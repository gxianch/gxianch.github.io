---
title: Storm Blueprints-Patterns for Distributed Real-time Computation
date: 2020-12-10 09:13:35
categories: [Storm]
toc: true
mathjax: true
top: 98
tags:
  - Packt
---

{% asset_img label cover.jpg %}
![](Storm-Blueprints-Patterns-for-Distributed-Real-time-Computation/cover.jpg)
<!-- more -->

### 83 真实系统用滑动窗口倾向，这里采用简单的去判断是否超过阈值

{% asset_img label 1.jpg %}
![](Storm-Blueprints-Patterns-for-Distributed-Real-time-Computation/1.jpg)

### 86  Trident spouts must emit tuples in batches.

{% asset_img label 2.jpg %}
![](Storm-Blueprints-Patterns-for-Distributed-Real-time-Computation/2.jpg)

### 95 The values emitted by functions are fields that are added to the tuple

通过Trident函数发射的字段是添加的，不会删除原来的字段

{% asset_img label 3.jpg %}
![](Storm-Blueprints-Patterns-for-Distributed-Real-time-Computation/3.jpg)