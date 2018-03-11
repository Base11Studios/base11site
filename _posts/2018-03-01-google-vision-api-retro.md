---
date: 2018-03-01
title: What I Learned Using Google's Mobile Vision API
categories:
  - android
  - ios
  - google vision
author_staff_member: aj
featured_image: /octocats/filmtocat.png
image:
  path: /images/octocats/filmtocat.png
---

While Base11 Studios hasn't released any camera focused applications, I have spent significant time in the last year developing scanning features in Android applications that utilize the phone camera.  I want to take a minute to unpack what I've learned.

## Mobile Vision

Google's [Mobile Vision API](https://developers.google.com/vision/) enables users to easily use their Android phones or iPhones to scan or detect [faces](https://developers.google.com/vision/face-detection-concepts), [barcodes, QR codes, and other 2D code formats](https://developers.google.com/vision/barcodes-overview), and to [recognize text](https://developers.google.com/vision/android/text-overview).

![scanning text](https://lh3.googleusercontent.com/786zj8Cw9XIe2mwdmMcU2_ID7y7Y9CrVwZEtjTOI4NBQkLiEFwHA3Cgth3yDUxx9jedFkG8hGGEgVRgNLLK-_wRkllelbxI=s688)

> Mobile Vision is not to be confused with Google's [Cloud Vision API](https://cloud.google.com/vision/) which is an AI image analysis product offered by Google.

Google provides a lot of awesome reference materials including their [API reference](https://developers.google.com/vision/introduction), Code Labs ([barcode](https://codelabs.developers.google.com/codelabs/bar-codes)), and full samples on **[GitHub](https://github.com/googlesamples/android-vision)**.

That's a lot of information to throw at you, and contains pretty much all you need to get started with the Mobile Vision API, but what might be more valuable from my experience is the gotcha's and oh-shit moments I experienced in implementing both OCR and Barcode detection.

## What You Might Need To Know

A few of the things I didn't realize when I started implementing the API but want to make clear for any would-be users of these API's.

### 1. You don't need the Android Camera

This should come as a relief to you.  The Android Camera API's (yes, multiple) are unecessarily confusing. For instance if you're implementing an app that supports anything before API 21 you'll have to implement the Camera1 and Camera2 API in your app to make sure everything works.  Not only that, but even the Camera2 implementation has different requirements between certain API levels.

| API Level | Camera API | Preview View |
|:---------:|------------|--------------|
| 9-13      | Camera1    | SurfaceView  |
| 14-20     | Camera1    | TextureView  |
| 21-23     | Camera2    | TextureView  |
| 24        | Camera2    | SurfaceView  |


Instead of the Android Camera, you're going to be using a `CameraSource` to manage the camera and `CameraSourcePreview` to manage the camera preview.  The [android-vision sample](https://github.com/googlesamples/android-vision) on GitHub includes all the sample files you need to manage the camera UI in the Barcode sample [here](https://github.com/googlesamples/android-vision/tree/master/visionSamples/barcode-reader/app/src/main/java/com/google/android/gms/samples/vision/barcodereader/ui/camera).  

*[The rest of the Barcode sample classes](https://github.com/googlesamples/android-vision/tree/master/visionSamples/barcode-reader/app/src/main/java/com/google/android/gms/samples/vision/barcodereader)*

This includes an implementation of the `CameraSource`.  The other samples use the `CameraSource` from Google Play Services `com.google.android.gms.vision.CameraSource`.

### 2. Re-Sizing The View

If you're an Android developer, you understand that your code is going to be running on any number of device sizes and shapes.  In my experience with the `CameraSourcePreview` not every device responded kindly to the preview size - leaving me with weird squished or stretched previews.

After some Googling and searching the Issues in the repo I came across a widely accepted solution that worked [in this comment](https://github.com/googlesamples/android-vision/issues/23#issuecomment-203534913).

The gist, we need to re-size the view to be slightly beyond the view of the screen while maintaining aspect ratio and then crop it to screen size:

```java
...

// To fill the view with the camera preview, while also preserving the correct aspect ratio,
// it is usually necessary to slightly oversize the child and to crop off portions along one
// of the dimensions.  We scale up based on the dimension requiring the most correction, and
// compute a crop offset for the other dimension.
if (widthRatio > heightRatio) {
    childWidth = viewWidth;
    childHeight = (int) ((float) previewHeight * widthRatio);
    childYOffset = (childHeight - viewHeight) / 2;
} else {
    childWidth = (int) ((float) previewWidth * heightRatio);
    childHeight = viewHeight;
    childXOffset = (childWidth - viewWidth) / 2;
}

...
```

Compare that to the `onLayout` method in the `CameraSourcePreview` [in the sample app](https://github.com/googlesamples/android-vision/blob/2ce3132c959e76e7dbe1d8d3332abe87c246b22a/visionSamples/FaceTracker/app/src/main/java/com/google/android/gms/samples/vision/face/facetracker/ui/camera/CameraSourcePreview.java#L126).

### 3. You Depend on Google Play Services

One of the most frustrating bugs I had to hunt down in one of the projects I worked on was why my Barcode Detector would randomly just not work when I was testing.  After killing my app and re-starting things started to work again.

It was really tough to reproduce, but eventually I was able to get the error on a fresh build and see an error looping in the console:

```
com.google.android.gms.dynamite.DynamiteModule$zza: No acceptable module found. Local version is 0 and remote version is 0.
       at com.google.android.gms.dynamite.DynamiteModule.zza(Unknown Source)
       at com.google.android.gms.internal.zzbjz.zzTS(Unknown Source)
       at com.google.android.gms.internal.zzbjz.isOperational(Unknown Source)
       at com.google.android.gms.vision.barcode.BarcodeDetector.isOperational(Unknown Source)
       at com.google.android.gms.vision.Detector.receiveFrame(Unknown Source)
       at com.google.android.gms.vision.CameraSource$zzb.run(Unknown Source)
       at java.lang.Thread.run(Thread.java:762)
```

After some more searching on Google and through the project Issues, I found out that it was potentially a data issue in Google Play Services.

It turns out in older versions of the API the user had to have at least 10% of storage available on their device to install the library (an issue if you have a 128GB drive trying to download a tiny library way less than 12GB) - but that issue was corrected in the version I was using (no only allowing if there is at least 500MB).

After determining the possibility of it being a data issue, I was finally able to reliably reproduce the issue by deleting all cached and stored data from Google Play Services before trying to scan in my app.

But the issue for me was that my devices had tons of available storage.  So why was this low-storage issue affecting me?  And what did that have to do with clearing out Google Play Services on my device?

Well, the truth is always in the source.  At [line 84 of the `PhotoViewerActivity`](https://github.com/googlesamples/android-vision/blob/2ce3132c959e76e7dbe1d8d3332abe87c246b22a/visionSamples/photo-demo/app/src/main/java/com/google/android/gms/samples/vision/face/photo/PhotoViewerActivity.java#L84) in the photo-demo sample.

```java
if (!safeDetector.isOperational()) {
    // Note: The first time that an app using face API is installed on a device, GMS will
    // download a native library to the device in order to do detection.  Usually this
    // completes before the app is run for the first time.  But if that download has not yet
    // completed, then the above call will not detect any faces.
    //
    // isOperational() can be used to check if the required native library is currently
    // available.  The detector will automatically become operational once the library
    // download completes on device.
    Log.w(TAG, "Face detector dependencies are not yet available.");

    // Check for low storage.  If there is low storage, the native library will not be
    // downloaded, so detection will not become operational.
    IntentFilter lowstorageFilter = new IntentFilter(Intent.ACTION_DEVICE_STORAGE_LOW);
    boolean hasLowStorage = registerReceiver(null, lowstorageFilter) != null;

    if (hasLowStorage) {
        Toast.makeText(this, R.string.low_storage_error, Toast.LENGTH_LONG).show();
        Log.w(TAG, getString(R.string.low_storage_error));
    }
}
```

Makes sense.  Clearing the GMS data and then fresh installing my app means I could beat Google Play Services in the race to download the library and end up with an unresponsive scanner.

So be wary as a dev.  Unless you want to package all of the Vision API with your app, it's possible that you're users beat a sluggish Google Play Services to your scanner before the source you need is available.  Luckily this is rare in the wild - but hopefully I can save you some time.

## That's it for now

I hope these couple of gotcha's can help you in your next implementation of Google's Android Vision API.  As I continue to use this powerful library I'll be sure to come back to you with any new info I can share.  Keep hacking.
