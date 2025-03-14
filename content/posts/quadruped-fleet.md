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

<!-- The block diagram -->
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/system.png" alt="Robot Fleet" width="900" height="auto">
</div> 

## Unitree-GO2
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go2.png" alt="Robot Fleet" width="900" height="auto">
</div> 

<!-- A. hardware of both
    B. block diagram of both / rqt_graph 
    C. manual nav, filetring data and high level controls.
    D. Rviz videos both both. -->

## Unitree-GO1
<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/go1.png" alt="Robot Fleet" width="900" height="auto">
</div> 
<!-- A. hardware of both
    A. JEtson- edge computing
    B. block diagram of both.
    C. manual nav, filetring data and high level controls.
    D. Rviz videos both both. -->

## Visual-SLAM
 <!-- A. Theory
        B. videos, point cloud, mapping.
        C. sterio theory, Zed 2i cam -->

## Lidar SLAM
<!-- A. theory
        B.  videos, point cloud, mapping.
        C. point cloud theory, 4D lidar stuff -->

## Map-Merging 
<!-- a. theory
        b. simulation video,... later complete thing -->


## Future Work

## Points to take of while working on it:

<!-- Feel free to raise pr. Most issues, and operational stuff is fresh in my mind, so feel free to ask and add issues.

Link to github. || Detailed setup instruction and running procedure is mentioned in the ReadMe files of each package. -->


<!--  Doubts
-> All node/ launch file descriptions?
-> include that as another project? 
