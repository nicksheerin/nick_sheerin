---
title: "Intent Recognition"
layout: single-portfolio
excerpt: "<img src='../images/projects/intent/dissertation.png' alt=''>"
collection: projects
order_number: 10
---

# Improving Intelligence of Robotic Lower-Limb Prostheses to Enhance Mobility for Individuals with Limb Loss

<div align="center">
<img src="../../images/projects/intent/dissertation.png">
</div>

## Overall Summary

The field of wearable robotics is an emerging field that seeks to create smarter and intuitive devices that can assist users improve their overall quality of life. Specifically, individuals with lower limb amputation tend to have significantly impaired mobility and asymmetric gait patterns that result in increased energy expenditure than able-bodied individuals over a variety of tasks. Unfortunately, most of the commercial devices are passive and lack the ability to easily adapt to changing environmental contexts. Powered prostheses have shown promise to help restore the necessary power needed to walk in common ambulatory tasks. However, there is a need to infer/detect the user’s movement to appropriately provide seamless and natural assistance. To achieve this behavior, a better understanding is required of adding intelligence to powered prostheses. My [dissertation](https://www.youtube.com/watch?v=rJzv68i8Mf4) focused on three key research objectives: 

1) Development & enhancement of offline intent recognition systems for both classification and regression tasks using embedded prosthetic mechanical sensors and machine learning

2) Deployment of intelligent controllers in real-time to directly modulate assistive torque in a knee and ankle prosthetic device

3) Quantification of the biomechanical and clinical effects of a powered prosthesis compared to a passive device. 

Through multiple research studies & publications, we showed that powered prostheses could better enhance mobility for individuals with transfemoral amputation and was a step forward towards clinical acceptance.

<div align="center">
<img src="../../images/projects/intent/legs.png" width = "50%">
</div>

## General Overview

In order for robotic technology to go from research lab settings to community use, there are several steps that are needed for these devices to be useful: <u> hardware improvement, development of intelligent controls, biomechanical comparison, and clinical validation/acceptance</u>. 

From a hardware perspective, you need to have a device that can provide stable and high enough toques while maintaining a low weight profile and sustaining loads for a long periods of time. Next from a controls perspective, you need to have intuitive and adaptive strategies that can dynamically adjust to the user. Lastly, it is useful to understand the impact of the prosthetic leg on intact joints and whether these active devices  are improving clinical measures such as gait symmetry. With the development of these three key areas,  clinical acceptance can be reached. 

In the end, the overarching goal is to have a device that is  easy to use, robust to different walking patterns,  and durable. 

## Control Paradigm

<div align="center">
<img src="../../images/projects/intent/control_flow.png">
</div>

The control of these devices is critical to the deployment of these devices in real-world situations. Typically, the control architecture can be categorized into 3 different tiers: high, mid, and low level controllers. 

The high-level controller is concerned with detecting user intent  — such as walking in different modes or estimating other environmental variables such as walking speed or ground slope angle (focus on my dissertation). This informs the mid-level controller which is responsible for generating the desired torque profiles. It uses an impedance control law that has tunable parameters coupled with a finite state machine (FSM) at each joint. We can break down the cyclic behavior of walking into 4 distinct phases, where each phase has adjustable parameters. Lastly, the low-level controller is responsible for matching the desired torque and actual torque outputted by our actuators.

## Results

Over the span of my Ph.D., I was fortunate enough to publish some findings that can be found [here](https://kbhakt.github.io/krishan_bhakta//publications/). Overall, by conducting multiple studies that looked at offline machine learning analysis of wearable sensors to validating the performance of these devices, I hoped to create a framework that would allow others to build upon this work. From my dissertation, the main research contributions included:

1) Developing the 1<sup>st</sup> user-independent (IND) real-time intent recognition system for both continuous and discrete estimation

2) Creating scalable control strategies across common ambulation modes and terrain grades

3) Performed biomechanical validations of powered prostheses compared to passive devices

The [video](https://youtube.com/shorts/t9WFBW-oOTE?feature=share) shows one of the first times, my team and I were able to implement a real-time intent recognition system for detecting locomotion mode changes embedded onto a Raspberry Pi that controlled the powered prosthesis.

## Acknowledgements

I cannot thank all the subjects enough for participating in our studies over the years and all the commitment and patience you gave us!

<div align="center">
<img src="../../images/projects/intent/subjects.png">
</div>