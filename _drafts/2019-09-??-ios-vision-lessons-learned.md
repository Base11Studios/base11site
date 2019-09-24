---
date: 2019-09-??
title: iOS Vision - Image Processing Lessons Learned
categories:
  - swift
  - image processing
  - ios
author_staff_member: aj
featured_image: double-diamond/double_diamond_discover.png
image:
  path: /images/double-diamond/double_diamond_discover.png
---

There's something magical about computer vision. Point a camera at something and get data back. Not just simple image data, but detailed metadata about what's in the photo. Object descriptions, locations of interesting shapes, faces, depth maps, etc. Every year image detection processing improves, and Apple continues to provide updated capabilities in approachable API's thorugh their iOS [Vision](https://developer.apple.com/documentation/vision) framework.

Over the course of the past year I've had the opportunity to get some experience building apps that utilize the Vision framework for image processing - primarily for the purpose of identifying and tracking objects. In that journey I've experienced some challenges, had some lightbulb moments, and learned how to utilize the latest API's to improve performance and accuracy.

This article is a loose walkthrough of how I used the [Vision](https://developer.apple.com/documentation/vision) framework to detect and track objects, used [AVFoundation](https://developer.apple.com/documentation/avfoundation) and [AVCapturePhotoOutput](https://developer.apple.com/documentation/avfoundation/avcapturephotooutput) to capture an output image, and used [Core Graphics](https://developer.apple.com/documentation/coregraphics) to do post-processsing on that image to prepare it for further server-side processing.
