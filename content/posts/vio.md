+++
title = 'Real-Time Pose Graph Estimation'
date = 2025-07-05
draft = false
+++
<div style="text-align:center;">Author: Sayantani Bhattacharya, Sharwin Patil</div> 

<!-- <div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="200" height="auto">
</div> -->

## Overview

We implemented a ROS2 Package that extracts synchronized stereo or monocular raw image frames from an Intel RealSense Camera. Upon initialization, the node configures itself based on runtime parameters including camera mode (stereo/monocular), feature extraction method (ORB/SIFT), and optimization frequency. For stereo operation, it establishes synchronized subscribers for both infrared cameras and their calibration data, while monocular mode uses the color camera stream. 

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/VIO/poseGraph.gif" alt="SealBot" width="400" height="auto">
</div>

The overall modular design allows easy swapping of components - for instance, changing from g2o to GTSAM for optimization or integrating additional sensors like IMUs through the ROS 2 interface.Current performance achieves real-time operation (15-30Hz) when using ORB features, with the optimization step running asynchronously at a lower frequency (default 10Hz) to maintain responsiveness.


## System-Architechture 
<div style="text-align: center; margin-bottom: -40px; margin-top: -80px">
    <img src="/images/projects/VIO/VIO_BD.png" alt="SealBot" width="800" height="auto">
</div>



## Feature-Extraction
This forms the core frontend processing, where each incoming frame is analyzed using the selected algorithm (ORB by default for real-time performance). The implementation provides interchangeable feature detectors through OpenCV's features2D module, with extracted features including both keypoint locations and their descriptors. These features are then published as visualized overlays on the original images for debugging purposes. For stereo configurations, matching features between left and right images enables 3D point triangulation using the known baseline distance.
## ORB/SIFT
ORB seemed faster but thus better for long runs, like if the track was covering a large distance. But SIFT detects the feature better, and better for our use case.

Add image comparisions.

## Visual odometry estimation
This step occurs through feature matching between consecutive frames using FLANN-based matchers. The system computes relative camera motion by first finding the essential matrix with RANSAC outlier rejection, then decomposing it into rotation and translation components. In stereo mode, this is enhanced with direct 3D point triangulation from matched features across the stereo pair. Each estimated pose forms a node in the pose graph, with odometry constraints creating edges between consecutive nodes.


## The backend optimization system 
utilizes g2o's sparse optimizer with Levenberg-Marquardt solving. Nodes represent SE(3) camera poses while edges encode relative transformationsbetween them. The first node is fixed as a reference frame, with subsequent nodes added as new frames are processed. Loop closure detection, though not fully implemented in the current version, is designed to add additional constraints when revisited locations are identified through feature matching against previous keyframes.


## Visualization and debugging
the system maintains three main outputs: (1) A continuously updating camera trajectory published as a ROS Path message, (2) Transform frames broadcast through tf2 representing the current camera pose estimate, and (3) A pose-graph visualization showing nodes and edges as interactive markers in RViz. The implementation handles thread safety through mutex locks when accessing shared resources like the current pose estimate and
optimization graph.



## Hardware

## Comparing Our Visual-Pose estimation with ZED 2i VIO SDK:

Our visual-pose system matches ZED2i's relative positional accuracy but lacks its IMU fusion for robust tracking during motion. While both run at 30Hz, our CPU-based OpenCV/g2o pipeline offers research flexibility versus ZED2i's optimized hardware processing. ZED2i provides out-of-the-box Kalman filtering and confidence-based weighting and ROS2 wrappers making it easy to integrate to hardware devices. Thus, oursystem is good for customization and understanding the concepts and, ZED2i for plug-and-play performance.


Add gif for comparision......?


## ROS Package/Node, Concurrency stuff

## Stereo 
## RANSAC

## Mono/Stereo
Initially we implemented monocular feature extraction and pose estimation using the rect topic of realsense. Then we switched to using both the right and left raw image frames, intrinsic and extrinsic camera data, and geometry (baseline). Then we used the PnPRansac function of OpenCV to triangulate the feature keypoints and descriptors. And as you can see below the results (before tuning) show vast improvement with stereo based pose
estimation.

## Extended Used in another project
Link to embedded video of other .md file.

## Limitations
2. And our pose-estimation pipeline has a teleportation issue where the odom jumps to another location and on
both the locations the relative motion of poses remains correct.
3. The inherent noise of using only visual features and not considering the physics of the environment in our
pipeline.

## Futute Work:
1. Used ML Based techques like SuperPoint and use this as a benchmark to test.
2. Deploy on edge devices.

<!-- See the ppt -->
