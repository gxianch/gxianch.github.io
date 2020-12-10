---
title: Learning-Spark-Streaming
date: 2020-12-10 09:03:18
categories: [Spark]
toc: true
mathjax: true
top: 98
tags:
  - Streaming
  - OReilly

---
OReilly出口，Spark流式计算解读
{% asset_img label cover.jpg %}
![](Learning-Spark-Streaming/cover.jpg)
<!-- more -->

### 160 Spark Streaming’s output is often envisioned jointly with Dataframes
{% asset_img label 1.jpg %}
![](Learning-Spark-Streaming/1.jpg)
### 160 将dstream转成dataframe后按时间存储
{% asset_img label 2.jpg %}
![](Learning-Spark-Streaming/2.jpg)
### 206 checkpoint时间点设置
{% asset_img label 3.jpg %}
![](Learning-Spark-Streaming/3.jpg)
{% asset_img label 4.jpg %}
![](Learning-Spark-Streaming/4.jpg)
### 233 结果存储到hdfs 考虑其窗口数据接近hdfs分区数据128M
{% asset_img label 5.jpg %}
![](Learning-Spark-Streaming/5.jpg)
### 233 foreachRDD
{% asset_img label 6.jpg %}
![](Learning-Spark-Streaming/6.jpg)
{% asset_img label 7.jpg %}
![](Learning-Spark-Streaming/7.jpg)
{% asset_img label 8.jpg %}
![](Learning-Spark-Streaming/8.jpg)
{% asset_img label 9.jpg %}
![](Learning-Spark-Streaming/9.jpg)
### 235 foreachRDD示例
{% asset_img label 10.jpg %}
![](Learning-Spark-Streaming/10.jpg)
{% asset_img label 11.jpg %}
![](Learning-Spark-Streaming/11.jpg)
### 236 支持存储到kafka,es
{% asset_img label 12.jpg %}
![](Learning-Spark-Streaming/12.jpg)
### 246 join操作，broadcast join
{% asset_img label 13.jpg %}
![](Learning-Spark-Streaming/13.jpg)
