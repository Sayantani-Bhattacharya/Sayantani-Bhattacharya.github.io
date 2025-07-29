+++
title = 'Apex Putter'
date = 2025-07-26
draft = false
+++
<div style="text-align:center; margin-top: -40px; margin-bottom: 30px;">Authors: Sayantani Bhattacharya, Andrew Kwolek, Kyle Puckett, Jueun Kwon, Zhengyang Kris Weng</div>

<!-- <div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="200" height="auto">
</div> -->


## Overview

<div style="text-align: center; margin-bottom: 15px; margin-top: -20px;">
    <img src="/images/projects/golf/apex_comp.gif" alt="SealBot" width="700" height="auto">
</div>

Apex Putter system integrates a Franka Emika Panda arm to play mini-golf. The goal is to have the robot sense a golf ball and hole, plan a putt, and execute it precisely. What a human child can do by “eyeballing” a putt requires precision control of position and timing in a robot. The project turns a simple game into a challenging control problem requiring integrated sensing (camera-based vision) and trajectory planning.

## System Architechture 

<div style="text-align: center; margin-bottom: 25px; margin-top: 10px;">
    <img src="/images/projects/golf/AP_SA.png" alt="SealBot" width="600" height="auto">
</div>


## Motion-Planning

The Motion subsystem computes the necessary trajectory for the arm to hit the ball toward the hole. Its main steps are:

#### Target Vector Calculation: 
From the detected ball and hole positions (both expressed in the robot’s base frame via TF), the code computes a target vector – typically the straight-line direction the putter should move. This vector represents the desired direction of the putt.

#### Pose Calculation with Compensation: 
Using the target direction, the system calculates a desired end-effector pose for the putter. This includes orienting the putter “face” toward the ball-hole line (putter-face compensation) and applying any mechanical offsets (e.g. to account for the putter’s geometry).

#### MoveIt Planning: 
The final pose is fed into MoveIt as a goal. MoveIt computes a collision-free joint-space trajectory for the entire arm from its current configuration to the target pose. The Franka’s moveit config (loaded in the launch file) specifies joint limits and allowed collisions. Once a plan is found, the node invokes a planning callback to execute it on the robot. The planning generally produces a smooth trajectory that brings the putter down onto the ball and then follows through.

### Trajectory Planning

The trajectory generation works in two parts:

#### 1. Joint-Space trajectory:

During setup while moving the arm from its home position to a pre-strike pose, the robot uses joint-space planning. This means that:

- The planner computes a trajectory in terms of the robot’s joint angles (q₁ to q₇ for the Franka).
- The end-effector’s trajectory can be non-linear in Cartesian (3D) space as the path is optimised for feasibility, joint-limits and smoothness. 
- But the robot safely reaches its desired configuration accurately.
- This method is robust, avoids singularities, and is faster to compute.
- Also used for resetting the putts.

#### 2. Cartesian trajectory:

When executing the actual putting stroke, the system uses a Cartesian trajectory. This ensures the end-effector (the putter head) moves in a precise, straight-line motion in 3D space.
- The trajectory is defined in Cartesian coordinates (x, y, z + orientation).
- MoveIt computes waypoints along this straight-line path and solves inverse kinematics at each step.
- The waypoints lie along the intended ball-hole vector.
- The result is a controlled forward stroke with minimal deviation, ideal for striking the golf ball towards hole.

This combination of joint-space and Cartesian planning enables the system to balance general repositioning flexibility with precise task execution.

## Vision

#### Detection(YOLO)

A YOLOv8-based object detector locates the golf ball and hole in the camera image, in camera frame. It was then trained on a custom dataset emulating the same environment conditions to improve prediction accuracy. And OpenCV was used to process the raw RealSense image frames.

#### AprilTag Localization

April Tag mounted at the base of franka, is used for calculating the robot base transformation with respect to the camera.

#### TF Broadcasting

The ball and hole locations, tag poses and camera-to-base transform are published via ROS2 TF frames. Once the base tag’s transform is known, the vision node continuously broadcasts a TF transform so that the ball’s position can be converted into the robot’s base frame. This TF-based setup keeps all parts of the system in sync with a common coordinate frame.

### Vision-Pipeline

1. YOLO detects ball in 2D image → get center pixel (u, v)
2. Use depth image + camera intrinsics → get 3D point in camera frame
3. Adjust for ball radius → get contact point on the ground
4. Use camera-to-robot transform → get ball position in robot base frame

## Robot-State

The Franka’s behavior is organized as a simple state machine controlled by ROS2 services. These states are invoked in sequence by the user (as mentioned in the README), effectively forming a simple state transition: Home → Ready → Putt. Each transition is implemented as a service callback in the motion controller node. The use of explicit services ensures modularity, and are coulped in the demo launches.


## Integration and Synchronization

Vision and Motion Planning are integrated via ROS2 TF transforms and service-based synchronization. The vision node continuously detects and publishes the ball and target poses in the robot frame, and broadcasts TF transforms. The motion control node queries the latest ball and hole positions using the TF buffer before planning. This ensures that motion planning always uses up-to-date, real-world geometry. Synchronization is handled through a state-based service architecture. The Motion Control Node maintains a state variable ("HOMING", "READY", "PUTT") that changes in response to ROS2 service calls. Each callback executes logic only if the system is in the correct prior state. This ensures the robot never plans or moves without valid, synchronized vision data. Vision and motion remain modular but tightly coupled through shared TF frames and state-driven service logic.

<!-- ## Demo tasks -->


## Awknowledgements

I would like to thank [Prof. Matthew Elwin](https://robotics.northwestern.edu/people/profiles/faculty/elwin-matt.html) and the other authors.

<div style="text-align: center; margin-bottom: 30px; margin-top: 30px;">
    🚀 <a href="https://github.com/Sayantani-Bhattacharya/Apex_Putter" style="font-weight:;">Link to Project Github</a>
</div>

