---
title: Learning Storm
date: 2020-12-08 12:32:31
categories: [Storm]
toc: true
mathjax: true
top: 98
tags:
  - ✰✰✰
---

Storm基础知识，由浅入深，学习storm不错的书

{% asset_img label cover.jpg %}
![](Learning-Storm/cover.jpg)

<!-- more -->

### 26 如果没有emit a tuple，storm会等待1毫秒重新调用

![](/images/Learning-Storm/2.jpg)
![](Learning-Storm/2.jpg)

### 26 storm nexttuple,ack,fail占用一个线程，不能block阻塞

![](/images/Learning-Storm/1.jpg)
![](Learning-Storm/1.jpg)

### 27 2个流时你可以等待另一个tuple过来后再emit

![](/images/Learning-Storm/3.jpg)
![](Learning-Storm/3.jpg)

### 27 prepare方法调用时间

![](/images/Learning-Storm/4.jpg)
![](Learning-Storm/4.jpg)

### 47 zookeeper只有主节点负责写，从节点只读

![](/images/Learning-Storm/5.jpg)
![](Learning-Storm/5.jpg)

### 59 rebalance command

![](/images/Learning-Storm/6.jpg)
![](Learning-Storm/6.jpg)

![](/images/Learning-Storm/7.jpg)
![](Learning-Storm/7.jpg)

### 66 Global Grouping

![](/images/Learning-Storm/8.jpg)
![](Learning-Storm/8.jpg)

### 69 kafka消费组

![](/images/Learning-Storm/9.jpg)
![](Learning-Storm/9.jpg)

### 113 为什么使用Trident

![](/images/Learning-Storm/15.jpg)
![](Learning-Storm/15.jpg)

### 113 Trident引申阅读 http://www.flyne.org/article/216 

![](/images/Learning-Storm/10.jpg)
![](Learning-Storm/10.jpg)

![](/images/Learning-Storm/11.jpg)
![](Learning-Storm/11.jpg)

![](/images/Learning-Storm/12.jpg)
![](Learning-Storm/12.jpg)

![](/images/Learning-Storm/13.jpg)
![](Learning-Storm/13.jpg)

![](/images/Learning-Storm/14.jpg)
![](Learning-Storm/14.jpg)



### 129 non-transactional topology包含最多一次和最少一次处理2种情况

![](/images/Learning-Storm/16.jpg)
![](Learning-Storm/16.jpg)

### 138 如果spout读取的数据来源不能容错，则spout也无法保证事务

![](/images/Learning-Storm/17.jpg)
![](Learning-Storm/17.jpg)

### 143 什么时候使用Trident, Trident不适合高速应用，因为Trident增加复杂性和状态管理

![](/images/Learning-Storm/18.jpg)
![](Learning-Storm/18.jpg)

### 143 什么时候使用Trident,仅需要处理exactly once (仅处理一次)的情况

![](/images/Learning-Storm/19.jpg)
![](Learning-Storm/19.jpg)

### 144 storm应用通过storm-yarn部署在已经存在的hadoop集群

![](/images/Learning-Storm/20.jpg)
![](Learning-Storm/20.jpg)

![](/images/Learning-Storm/21.jpg)
![](Learning-Storm/21.jpg)

