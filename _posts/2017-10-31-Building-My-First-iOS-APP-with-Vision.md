---
layout: post
comments: true
title:  "Building My First iOS APP with Vision!"
date:   2018-3-10
excerpt: "How I built a facial detection app using the power of CoreML"
image: "/images/coreml.jpg"
---

## Background

This is indeed my first time working with iOS. I'd like to thank Udacity for their free online course on iOS development. If you want to get started with iOS development, I'd highly recommend taking a MOOC and practice on the side. It really is a lot of fun.

## Vision

iOS 11 comes with CoreML and with that, the Vision framework. The roadmap for my applicaiton in setting up and using Vision is as follow: setting up AVFoundation, configuring camera instrinsics, linking video input and output. Then get a preview layer for displaying video, and implement the capture output function to feed each frame to Vision for further processing on the main queue. After that, all you gotta do is tell vision to give you the detected face information. What to do with that information depends on you, but I grabbed the bounding box, translated that from the camera coordinate into the UI coordinate to display a box around each face on the preview layer. At the same time, a one second timer is dispatched onto the main queue to catch the actual pixels around the face in order to send the encoded64 information to the backend server. The server runs a flask backend on AWS EC2 and uses a wonderful python library for one-hot facial recognition. 

I'll post the actual code here later on but this should be enough information to get you started.