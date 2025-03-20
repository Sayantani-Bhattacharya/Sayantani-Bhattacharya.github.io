+++
title = 'Collaborative Hetrogeneous Quadruped Fleet'
date = 2025-03-14
draft = false
+++
<!-- <div style="text-align:center;">Author: Sayantani Bhattacharya</div> -->
<!-- <div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="200" height="auto">
</div>  -->

## Overview

<!-- Explanation of the system -->
In times of disaster, every second counts, and reaching survivors in hazardous terrains poses significant challenges. 
Imagine a coordinated team of agile, four-legged robots, working together to navigate treacherous environments like dense forests or mines. 
These quadrupedal robots autonomously perform simultaneous localization and mapping (SLAM), creating real-time detailed maps of their 
surroundings. By employing decentralized collaborative system, these robots can share and merge their individual maps, 
creating a comprehensive understanding of the complete area without relying on a central system. 
This approach enhances the robustness and speed of search operations, as the failure of a single unit does not compromise the entire mission. 
Quadrupeds inherently work well in uneven terrains, and harnessing the strengths of SLAM to explore unmapped areas with LIDAR and Visual-Inertial sensor data, these robotic swarms represent
a leap forward in disaster response, offering hope and assistance when it's needed most.
By all means this is just the first iteration and needs good work for being deployable onsite.

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="300" height="auto">
</div>

<div style="text-align: center; font-size: 20px; margin-top: 0px; margin-bottom: 10px"> 
<!-- font-weight: bold;  -->
    The Main Idea 
</div>

<!-- The block diagram -->
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/system.png" alt="Robot Fleet" width="900" height="auto">
</div> 

<!-- _________________________________________________________________________________________________________ -->

## Unitree-GO2

Hardware Setup: Connecting laptop with Ethernet cable to Unitree. Due to time constraints I did not configure Jetson for GO2.

<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go2.png" alt="Robot Fleet" width="900" height="auto">
</div>

#### Manual Navigation
Implemented high level controls that interacts with the Unitree GO2 ROS2 SDK.

Implemented manual navigation package with following features in ROS2 Jazzy and C++:
- Integrated with high level control pkg to operate on real robot.
- Transform publishers.
- Robot State publishers.
- Rtabmap pkgs generating odom tf and occupancy grid using GO2's 4d Lidar.
- Rviz visulaizations with modified URDF.
- Nav-to-pose to enable high level control with the GO2's APIs.
- Nav2 based manual goal subscription.
- Footprint based obstacle avoidance,


<!-- Manual Nav: video. -->
<!-- Rviz: video/img -->

#### Autonomous Navigation
Implemented autonomous navigation package with following features in ROS2 Jazzy and C++:
- Integrated with the manual nav package to operate on real robot.
- Nearest frontier based exploration
- State machine based goal assignment
- Services to start, and emergency stop
- Markers for visualization.


<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go2AutNav.gif" alt="Robot Fleet" width="auto" height="400">
</div> 

<!-- _________________________________________________________________________________________________________ -->


## Unitree-GO1
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go1.png" alt="Robot Fleet" width="900" height="auto">
</div> 

Hardware Setup: Jetson Orin Nano, powered by Unitree Power port, connected to buck converter (24V->12V). Zed 2i camera and a Display Screen is connected and powered by Jetson. And Jetson is connected to the same Wifi Network as Unitree GO1 (Password: 00000000)

<!-- Rviz: -->
<!-- jetson pics/ videos: -->
<!-- Aut Nav, obs avoidance: video -->

#### Autonomous Navigation Demo
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go1AutNav.gif" alt="Robot Fleet" width="auto" height="400">
</div> 

#### Obstacle Avoidance Demo
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go1ObsAvoid.gif" alt="Robot Fleet" width="auto" height="400">
</div> 


<!-- _________________________________________________________________________________________________________ -->

## Lidar-SLAM

Simultaneous Localization and Mapping (SLAM) is a fundamental technology in autonomous systems, enabling devices to perform real-time mapping while determining their position within an environment. LiDAR (Light Detection and Ranging) SLAM utilizes laser sensor technology to generate a highly accurate 3D map of the surrounding environment. By emitting laser pulses and calculating the Time of Flight (ToF), it can measure distances and map complex areas with precision.

Unitree GO2 has an inbuild 4D Lidar which publishes pointCloud data, which encodes the ToF in form of intensity values. And am using the ROS2 RTabMap package, feeding in the Odom data from Unitree topic and filtered pointcloud, to generate map. I also plan to use the RTabMap ICP (Iterative Closest Point) algorithm to improve the mapping accuracy. Also GO2 has a highly mismatched frequency rate of publishing different sensor datas, so ensure that all packages use a sync or queuing mechanism.

#### Point-Cloud-Filtering

On passing the raw point cloud data to the RTabMap package, the occupancy grid generated did not have any free spaces. So, I had to incorprate certain standard techniques to filter the data, like deskewing(incorporating distortions caused by motion), Voxel filter(Downsampling scan data). I also utilized the point cloud library to cluster high density points and project them as obstacles and rest as free spaces to ensure a better occupancy grid is formed. 

<p style="text-align: center; font-size: 20px; font-weight: bold;">Before and After Filtering:</p>

<div style="display: flex; justify-content: center; gap: 20px; margin-bottom: 30px;">
    <div>
        <img src="/images/projects/Quadruped_Fleet/AwesomelyWrong.png" alt="Before Filtering" width="400" height="auto">
        <p style="text-align: center;">Before</p>
    </div>
    <div>
        <img src="/images/projects/Quadruped_Fleet/filteredGrid.png" alt="After Filtering" width="400" height="auto">
        <p style="text-align: center;">After</p>
    </div>
</div>
<!-- _________________________________________________________________________________________________________ -->

## Visual-SLAM
Visual SLAM uses camera sensors and computer vision algorithms to map environments and track a device’s movement in real-time. By identifying and tracking key visual features across multiple frames, Visual SLAM estimates the camera’s movement and builds a 3D map.

I am using a Zed 2i camera mounted on and powered by GO1, connected to Jetson Orin Nano. Zed 2i has ROS2 SDK and built in image processing and SLAM algorithms. I am using its [Visual-Inertial Odometry (VIO)**](https://www.stereolabs.com/docs/positional-tracking) for generating occupancy grid. The 3D map generated needs to be projected to 2D for Nav2 to use it. I am using the Map filtering node written by [**Aditya Nair**](https://github.com/GogiPuttar/Search-and-Rescue_Robot_Dog_Unitree_Go1/tree/main) specifically for Zed 2i. 

Stereo vision based Point Cloud generation by Zed:
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/point_cloud_zed.png" alt="After Filtering" width="600" height="auto">
</div>

Position Tracking (video from Zed Official site):
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/ZedPoseTracking.gif" alt="After Filtering" width="600" height="auto">
</div>


<!-- _________________________________________________________________________________________________________ -->

## Lidar vs Visual

This is just my opinion mainly based on the obsservations made while working on this project. 

1. Robust in Challenging Conditions: LiDAR performs well in adverse conditions like low light making it highly adaptable for autonomous systems operating in GPS-denied areas. Visual SLAM relies on well-lit environments.
2. High Processing Demand: LiDAR generates massive amounts of data, requiring powerful hardware and more advanced data processing.
3. Feature-Rich Environments Required: Visual SLAM performs better in environments rich in features (e.g., edges, corners), which can be a challenge in sparse or plain areas. 
4. Ease of debugging: Visual SLAM has camera data, which makes it easier to make sense of the data, compared to the heat-map obtained by the Lidars.
<!-- _________________________________________________________________________________________________________ -->

## Map-Merging 
The final step now, is to merge the occupancy grids generated by all the indivudual quadrupeds in a cluster.
The initial relative position of both agents are known and I also have the IMU data of the motion series, which can be stiched into a combined map.


I have it working in simulation based on the 🚀 [**research**](https://github.com/robo-friends/m-explore-ros2).


<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/mergedMapPOC.gif" alt="Robot Fleet" width="900" height="auto">
</div> 

I am currently trying to get it to work with the real GOs.
_________________________________________________________________________________________________________
<!-- <br> -->

## Awknowledgements

Unitree, Prof. Paul Umbanhowar, Davin Landry, Aditya Nair, Nick Morales , Marno Nel, Katie Hughes, David Dorf, the robofriends people, Cohort, and most importantly the Open Source Community!!








<!-- ## Future Work -->
<!-- ## Points to take of while working on it, Useful resources -->
<br>
<br>

If you get to work on it and add some segments to the codebase would love to know about it. Most issues, and operational stuff is fresh in my mind, so feel free add issues and raise PR. Detailed setup instruction and running procedure is mentioned in the ReadMe file of each package. 

🚀 [**Link to this Project Github**](https://github.com/Sayantani-Bhattacharya/Multi-Hetero-Agent-Exploration-on-UnitreeGOs).



<!--  Doubts
-> All node/ launch file descriptions?
-> include that as another project? 
