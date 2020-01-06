---
title: Flume构建高可用可扩展的海量日志采集系统
date: 2020-01-06 14:40:03
tags:
---

hdfs数据格式有两种：可拆分的和不可拆分的
Avro是可拆分的

<!-- more-->

![hdfs的数据格式](1.bmp)

29  channel的行为像队列

![channel的行为像队列](2.bmp)


42 flume持久性依赖于channel,自带两类Channel,Memory channel和File channel
![flume](3.bmp)
![flume](4.bmp)

158 自定义拦截器intercept必须是线程安全的
![flume](5.bmp)
![flume](6.bmp)