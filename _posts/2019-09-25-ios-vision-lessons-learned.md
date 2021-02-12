---
date: 2019-09-25
title: iOS Vision - Image Processing Lessons Learned
categories:
  - swift
  - image processing
  - ios
author_staff_member: aj
featured_image: /vision/vision-hero-lockup-large.png
image:
  path: /images/vision/vision-hero-lockup-large.png
---

There's something magical about computer vision. Point a camera at something and get data back. Not just the expected image data, but detailed metadata about what's in the photo. Object descriptions, locations of interesting shapes, faces, depth maps, etc. Every year image detection processing improves, and Apple continues to provide updated capabilities in approachable API's thorugh their iOS [Vision](https://developer.apple.com/documentation/vision) framework.

![vision hero image](/images/vision/vision-hero-lockup-large.png)

Over the course of the past year I've had the opportunity to get some experience building apps that utilize the Vision framework for image processing - primarily for the purpose of identifying and tracking objects. In that journey I've experienced some challenges, had some lightbulb moments, and learned how to utilize the latest API's to improve performance and accuracy.

This article is a loose walkthrough of how I used the [Vision](https://developer.apple.com/documentation/vision) framework to detect and track objects, used [AVFoundation](https://developer.apple.com/documentation/avfoundation) and [AVCapturePhotoOutput](https://developer.apple.com/documentation/avfoundation/avcapturephotooutput) to capture an output image, and used [Core Graphics](https://developer.apple.com/documentation/coregraphics) to do post-processing on that image to prepare it for further server-side processing.

We're going to assume that this project

## Setting up the camera

## Creating Detection Requests

## Creating Tracking Requests

## Running Detection and Handling Results

## Capturing Photos

## Image Post-Processing
