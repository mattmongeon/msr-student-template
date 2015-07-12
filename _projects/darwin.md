---
layout: project
title: Face Detection and Tracking with ROBOTIS-OP2
date: May 21, 2015
image: darwin.jpg
---

## Overview

This project was developed as part of the [Robot Revolution](http://www.msichicago.org/whats-here/exhibits/robot-revolution/) exhibit at the [Museum of Science and Industry](http://www.msichicago.org) in Chicago, IL.  The [ROBOTIS-OP2](http://en.robotis.com/index/product.php?cate_code=111310) stands in place and tracks faces in its camera view.  Before it has locked onto a face it will move its head up and down and side to side to look for faces.  Once it has found one or more in its camera view, it will randomly choose a face to lock onto.  At this point the ROBOTIS will follow the person's face as they move around by moving its head to keep their face centered in the camera's view.  The image stream is reproduced on a nearby monitor where the image is overlaid with current status.  When a face is being tracked, it is outlined with a green box to indicate which one is being followed.

Face detection and image processing are implemented using OpenCV.  Control of the head is handled using the ROBOTIS library for interfacing with its hardware.

