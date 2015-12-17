---
layout: project
title: Object Detection and Grasping with youBot
date: April 8, 2015
image: youbot_detect_and_grasp.jpg
---

## Overview
This project uses the KUKA youBot and an ASUS Pro Xtion Live to detect a block on the ground and pick it up.  The ASUS is mounted on the end-effector and is initially used to find the location of the block relative to the base by finding it in the scene using template matching.  The youBot then navigates next to the block and puts the arm into a pose that allows the RGB stream to be used to align the youBot with the block for grasping.  It then grasps the block, returns to the starting location, drops off the block, and navigates back to the pickup point.  The process then begins again with the block in the new location.

This project uses two ROS packages called [youbot_object_grasp](https://github.com/mattmongeon/youbot_object_grasp) and [pcl_auto_seg](https://github.com/mattmongeon/pcl_auto_seg).  The youbot_object_grasp package contains the code for the state machine, arm control, and base control.  It is the main package of the project.

The pcl_auto_seg project contains all of the image processing code.  One node handles picking up the block from the RGBD stream using PCL, and another node uses OpenCV to find the block in the RGB stream to allow the youBot to position itself relative to the block to prepare for grasping.

The video below shows the robot picking up the block and returning to the start position.

<iframe src="https://player.vimeo.com/video/126804484" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<p><a href="https://vimeo.com/126804484">youBot Block Detection and Grasping</a> from <a href="https://vimeo.com/user39869467">Matt Mongeon</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

