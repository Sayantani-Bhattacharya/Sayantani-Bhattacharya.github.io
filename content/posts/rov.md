+++
title = 'Bio-Inspired Underwater ROV'
date = 2025-06-21
draft = false
+++
<!-- <div style="text-align:center;">Author: Sayantani Bhattacharya</div>  -->
<!-- <div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/Quadruped_Fleet/QuadrupedFleet.gif" alt="Robot Fleet" width="200" height="auto">
</div> -->

## Overview

This is an ongoing research project in collaboration with the **[SeNSE Lab at Northwestern University](https://sense-lab.github.io/)** with the aim to embody the sensory-motor loop of seal to understand the cognitive effects of the percieved data from the wiskers. 

So, I am developing an Underwater ROV system with seal-inspired whiskers' array mounting, to emulate the perception information observed by Seals in real life and develop decision making model based on that information. The final objective is to build an ROV capable of autonomously persuing a moving object, underwater, based solely on wiskers' perception.

<div style="text-align: center; margin-bottom: 10px;">
    <img src="/images/projects/SealBot/sealbot.gif" alt="SealBot" width="300" height="auto">
</div>

## Hardware Setup

<div style="text-align: center; font-size: 20px; font-weight: bold;">The Rover</div>
<!-- images of the rover and Label parts -->
<div style="text-align: center; margin-top: 20px; margin-bottom: 40px;">
    <img src="/images/projects/SealBot/ROVLabelled.png" alt="SealBot" width="1100" height="auto">
</div>

<div style="text-align: center; font-size: 20px; font-weight: bold;">The Soft-Robotic wisker array system mimicking real-life seal wiskers</div>

<div style="display: flex; justify-content: center; gap: 50px; margin-top: 10px; margin-bottom: 30px;">
    <div>
        <img src="/images/projects/SealBot/seal2.png" alt="Real" width="auto" height="300">
        <p style="text-align: center;">Real</p>
    </div>
    <div>
        <img src="/images/projects/SealBot/wisker.gif" alt="Real" width="auto" height="300">
        <p style="text-align: center;">Robotic System</p>
    </div>
</div>


## Perception Stack

### Ground-Truth
<!-- Write about the experiments with detections -->
There is two main parts of this subsection. First is the ground truth extracted from the External April Tag system mounted on the ROV using a external Real-Sense RGBD. This is the 6-Dimentional trajectory information of the Rover's movement in physical world, with respect to a static tag outside the pool.


<div style="display: flex; justify-content: center; gap: 50px; margin-top: 10px; margin-bottom: 30px;">
    <div>
        <img src="/images/projects/SealBot/RealStatic.png" alt="Real" width="auto" height="300">
        <p style="text-align: center;">Observed by Ext RealSense D435i</p>
    </div>
    <div>
        <img src="/images/projects/SealBot/6d_pose_paths.png" alt="Real" width="auto" height="300">
        <p style="text-align: center;">Plot</p>
    </div>
</div>


The above plot is the 3-D pose trajectory, with the black lines determining the orientation of the tag, at an discrete time interval. And this plot is for each of the on-board 5Tags, so the Rover pose is known at all times. And the orange trajectory is of the static tag out of pool, to calculate relative pose.

### Wisker-based Measurements
The second part of the perception stack is the information extracted from the april tags attached on the Wisker Array system, mounted on the Seal Head, as viewed by the Monocular Camera of the ROV. This is submerged in water, and are prone to issues caused by water reflections, waves, varring lighting conditions, etc. To Note the plot is of a single array and the image(left) is the complete observed frame.

<!-- the image as seen in pool test and the plot -->

<div style="display: flex; justify-content: center; gap: 50px; margin-top: 10px; margin-bottom: 30px;">
    <div>
        <img src="/images/projects/SealBot/RealArray.png" alt="As observed by ROV Camera" width="auto" height="300">
        <p style="text-align: center;">Observed by BlueOS Cockpit Interface of Onboard Monocular Camera</p>
    </div>
    <div>
        <img src="/images/projects/SealBot/ArrayPlot.png" alt="Real" width="auto" height="300">
        <p style="text-align: center;">Plot</p>
    </div>
</div>

In the next phase of the project I would be working on modelling this data into a predictable output.

## Motion Stack

<div style="text-align: center; margin-bottom: 30px;">
    <img src="/images/projects/SealBot/systemArchROV.png" alt="SealBot" width="900" height="auto">
</div>


## Mechnical Design

### Seal Head 

<div style="text-align: left; font-size: 20px; font-weight: bold; margin-right: 370px;">Version 1:</div>

<div style="text-align: left; margin-top: 10px; margin-bottom: 20px; margin-left: 100px;">
    <img src="/images/projects/SealBot/SealHead-v1.png" alt="SealBot" width="350" height="auto">
</div>

V1 -> V2 Improvements:
1. The tag slots are inclined towards the ROV cam, giving it a much better viewing angle.
2. Increased length to ensure the camera captures focused images.
3. Thread inserts for better screwing mechanism.
4. Screwing mechanism from the top, as the lower section is inaccessible most of the time. 
5. Covered top layer, improves support to the complete head and prevents external lighting and reflection issues.
6. Removed mirrors, as tilting ensures the work now, and eliminates inversion step for detection.

<div style="text-align: left; font-size: 20px; font-weight: bold;">Version 2:</div>

<div style="text-align: center; margin-bottom: 10px;">
    <div style="display: flex; justify-content: center; align-items: right; gap: 20px;">
        <img src="/images/projects/SealBot/SealHead-render.png" alt="SealBot" width="275" height="auto" style="margin-right: 0px;">
        <img src="/images/projects/SealBot/SealHead-render2.png" alt="SealBot" width="320" height="auto" style="margin-left: 0px;">
    </div>
</div>

### External April Tag Mount

<div style="text-align: left; font-size: 20px; font-weight: bold; ">Version 1:</div>
<div style="text-align: center; margin-bottom: 10px;">
    <div style="display: flex; justify-content: center; align-items: center; gap: 30px;">
        <img src="/images/projects/SealBot/v1_tag-render.png" alt="SealBot" width="250" height="auto">
        <img src="/images/projects/SealBot/v1_tag2.jpeg" alt="SealBot" width="180" height="auto">
        <img src="/images/projects/SealBot/v1_tag.jpeg" alt="SealBot" width="180" height="auto">
    </div>
</div>

<div style="text-align: right; font-size: 20px; font-weight: bold; margin-right: 130px; margin-botton: 15px; ">Version 2:</div>
<div style="text-align: right; margin-bottom: -80px;">
    <img src="/images/projects/SealBot/v2_tag.jpeg" alt="SealBot" width="200" height="auto">
</div>

<div style="text-align: left; font-size: 20px; font-weight: bold; margin-botton: 15px; ">Version 3:</div>
<div style="text-align: left; margin-bottom: 30px;">
    <div style="display: flex; align-items: center; gap: 20px;">
        <img src="/images/projects/SealBot/v3_tag_render.png" alt="SealBot" width="250" height="auto">
        <img src="/images/projects/SealBot/v3_tag2.jpeg" alt="SealBot" width="180" height="auto">
        <img src="/images/projects/SealBot/v3_tag.jpeg" alt="SealBot" width="180" height="auto">
    </div>
</div>



## Next Quarter Plans:

1. Develop perceptual models that ideally reflect the cognitive relation between the seal whisker’s sensory perception with its physical environment.
    - Try learning based methods (AutoEncoder could be an option).
    - Try classical methods (like Principal Component Analysis).
2. Develop a method (maybe already exists, research that)  to create a latent/embedding space for the 6D pose data of all the 20 tags from april tag to be used as input to the model. 
3. Understand  or develop a quantitative metric to analyse the accuracy of the models.
4. Stretch goal would be to have real-time feedback from the model to control the SealBot movement to follow a dynamic moving object in front of it.  This however does not affect the main goal of developing the Model but can be used as a metric to understand the accuracy of the model. To note: When I tried real-time detection for the static tags, I faced topic synchronisation issues and the frame stream lags at times.[not sure how to fix it yet] [and the camera info publisher may need to be calibrated in water].  

## Awknowledgements

I would like to thank [Prof. Matthew Elwin](https://robotics.northwestern.edu/people/profiles/faculty/elwin-matt.html), [Dr. Mitra Hartmann](https://sense-lab.github.io/people.html) and [Kevin Kleczka](https://sense-lab.github.io/people.html) for their advice and guidance throughout the project. And [Ishani Narwankar](https://ishani-narwankar.github.io/) and [James Oubre](https://oubrejames.github.io/) for their previous work building the Rover.