---
title: "AI/ML Model for Soccer Analysis using YOLO & Computer Vision"
layout: single-portfolio
excerpt: "<img src='../images/projects/vision_soccer/static.png' alt=''>"
collection: projects
order_number: 40
---

This project explores the integration of computer vision with machine learning to analyze soccer matches. Inspired by a [YouTube tutorial](https://www.youtube.com/watch?v=neBZ6huolkg&t=79s), the project aimed to refine and extend established techniques to create a custom, end-to-end analytical pipeline.

<div align="center">
<img src="../../images/projects/vision_soccer/static.png">
</div>

## Overall Summary

The primary goal was to leverage a pre-trained YOLO model for real-time, frame-by-frame detection of objects and individuals within a video stream. The system was engineered to:

1. Detect and differentiate players, referees, and pitch boundaries.
2. Track the soccer ball throughout the match.
3. Utilize an additional machine learning model to distinguish team affiliations via color detection.
4. Generate key analytical metrics, such as calculating player speed and the total distance covered by tracking pixel displacement between frames.

<div align="center">
  <img src="../../images/projects/vision_soccer/soccer.gif" width="80%">
</div>

## Takeaways
Working on this computer vision-based ML tutorial improved my understanding of the importance of a well-structured, modular pipeline. One key takeaway was realizing how critical preprocessing is: extracting video frames and preparing the data before model inference is essential for accurate object detection and tracking. By integrating a pre-trained inference model with complementary components—such as camera movement estimation, view transformation, and position interpolation—I discovered how raw images can be transformed into actionable insights, like measuring speed, distance, and ball possession.

Another valuable learning outcome was mastering the post-processing and integration steps needed to convert raw model outputs into meaningful athlete metrics. This process required not only applying the inference model but also coordinating multiple specialized modules—such as team assignment and ball control detection—to produce a comprehensive analysis. Overall, I learned how to design and implement a scalable ML pipeline, streamline iteration cycles for faster development, and generate clear, annotated output videos that effectively communicate complex analytics.

## Contact Me
Feel free to send me an email if you have any questions or comments: kbhakta96@gmail.com


## References
[1] [Youtube Reference Video] (https://www.youtube.com/watch?v=neBZ6huolkg&t=79s)





