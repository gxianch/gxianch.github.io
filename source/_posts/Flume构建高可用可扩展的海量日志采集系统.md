---
title: Flume构建高可用可扩展的海量日志采集系统
date: 2020-01-06 14:40:03
categories: [flume]
toc: true
tags:
  - 书摘

---
{% asset_img label cover.jpg %}
![](Flume构建高可用可扩展的海量日志采集系统/cover.jpg)

**hdfs数据格式有两种：可拆分的和不可拆分的
Avro是可拆分的**
<!-- more-->

![](/images/Flume构建高可用可扩展的海量日志采集系统/1.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/1.bmp)

**29  channel的行为像队列**

![](/images/Flume构建高可用可扩展的海量日志采集系统/2.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/2.bmp)



42 flume持久性依赖于channel,自带两类Channel,Memory channel和File channel

![](/images/Flume构建高可用可扩展的海量日志采集系统/3.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/3.bmp)![flume](3.bmp)
![](/images/Flume构建高可用可扩展的海量日志采集系统/4.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/4.bmp)

**158  自定义拦截器intercept必须是线程安全的**
![](/images/Flume构建高可用可扩展的海量日志采集系统/5.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/5.bmp)
![](/images/Flume构建高可用可扩展的海量日志采集系统/6.bmp)
![](Flume构建高可用可扩展的海量日志采集系统/6.bmp)