---
layout: post
category: "reading_notes"
title:  "ADH tracker算法"
tags: [auto-driving,track]
---

论文链接：
[Robust Real-Time Tracking Combining 3D Shape, Color, and Motion](http://davheld.github.io/DavidHeld_files/ijrr_tracking.pdf)

### Abstract
目前实时的物体跟踪算法在遇到各种不同的真实数据时,准确率和鲁棒性有待提高.本文提出了一种新的track算法,结合了目标的形状,颜色以及运动信息,效果更佳.该tracker基于后验分布的形状分配计算量.从一个粗略的后验分布开始,tracker持续提高后验分布的精度,从而提高track的准确率.即使运行很短的时间,该tracker的效果也优于其他算法.运行时间越长,精度越高.可以根据精度需求进行track.结合形状,颜色以及运动等信息,该tracker可以很好的处理视角变化,遮挡,光照变化等.

### Introduction
track对机器人来说非常重要.以无人驾驶汽车为例,需要知道周边车辆或人的运动趋势.然而由于严重的遮挡和视角变换，目前的track算法给出的物体速度估计噪声很大。其他的机器人也面临同样的问题。
本文提出的track算法，将物体shape引入了概率框架，提出了新的基于网格的方法来预估速度，作者称之为ADH( annealed dynamic histograms).开始时状态空间精度低，后验分布不准确，随着精度提高，对后验分布进行退火，最后接近真实的分布。

传统的kalman滤波，将物体作为一个点来处理，由于遮挡和视角原因，每一帧的点云可能相差很大，求得的中心点误差会很大，导致速度估计误差会很大。另外kalman滤波存在比较大的延迟，实际应用中发现大概有2s的延迟，对于无人驾驶系统来说，这个延迟还是非常大的。ADHtracker引入了物体的形状信息，可以更好的确定物体的位置，从而更好的估计速度。本文的重点，我觉得一个是看作者如何构建概率模型，另一个是如何用退火的方式求物体的状态。

***

### tracking pipeline
tracker的输入是经过segment和associate（匈牙利算法）点云，然后tracker进行速度估计。
### 概率模型                                                        
定义物体状态 Xt = （Xtp， Xv），Xtp是物体的位置，Xv是物体的速度。Xtp是指当前帧相对于上一帧的位置.忽略角速度，因为相对于10hz的lidar转速，物体的角速度可以忽略。为了性能考虑，同样不考虑Z轴方向的速度。
<img src="/images/posts/2017-03-07/fig1.png">

