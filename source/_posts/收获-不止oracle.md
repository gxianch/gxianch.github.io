---
title: 收获-不止oracle(第一版)
date: 2020-12-09 19:32:43
categories: [数据库]
toc: true
mathjax: true
top: 98
tags:
  - Oracle
  - ✰✰✰✰✰
---
国内写数据库写的最好的一本书，不像其它写数据库的书非常枯燥的教你写各种查询命令，而是从日常生活中引出数据库，通过示例一步一步带你走进数据库的世界，逻辑严谨，原理讲的非常透彻，同时文章生动幽默。
{% asset_img label cover.jpg %}
![](收获-不止oracle/cover.jpg)
<!-- more -->

### oracle体系结构图
{% asset_img label 1.jpg %}
![](收获-不止oracle/1.jpg)
{% asset_img label 2.jpg %}
![](收获-不止oracle/2.jpg)
{% asset_img label 3.jpg %}
![](收获-不止oracle/3.jpg)
{% asset_img label 4.jpg %}
![](收获-不止oracle/4.jpg)

### 2.1 物理体系
{% asset_img label 5.jpg %}
![](收获-不止oracle/5.jpg)

#### 2.2
{% asset_img label 6.jpg %}
![](收获-不止oracle/6.jpg)
#### 2.3 SQL指令描述
{% asset_img label 7.jpg %}
![](收获-不止oracle/7.jpg)
{% asset_img label 8.jpg %}
![](收获-不止oracle/8.jpg)
#### 2.4 日志缓存区
{% asset_img label 9.jpg %}
![](收获-不止oracle/9.jpg)
{% asset_img label 10.jpg %}
![](收获-不止oracle/10.jpg)
#### 2.5 commit逻辑
{% asset_img label 11.jpg %}
![](收获-不止oracle/11.jpg)
{% asset_img label 12.jpg %}
![](收获-不止oracle/12.jpg)
{% asset_img label 13.jpg %}
![](收获-不止oracle/13.jpg)
{% asset_img label 14.jpg %}
![](收获-不止oracle/14.jpg)
#### 2.6 一致读
{% asset_img label 15.jpg %}
![](收获-不止oracle/15.jpg)
#### 2.7 LWGR日志写入时机
{% asset_img label 16.jpg %}
![](收获-不止oracle/16.jpg)

### 3.0 逻辑体系
{% asset_img label 17.jpg %}
![](收获-不止oracle/17.jpg)
{% asset_img label 18.jpg %}
![](收获-不止oracle/18.jpg)
{% asset_img label 19.jpg %}
![](收获-不止oracle/19.jpg)
{% asset_img label 20.jpg %}
![](收获-不止oracle/20.jpg)
#### 回滚表空间可以建多个，并且自由切换，但是当前使用的只能有一个
{% asset_img label 21.jpg %}
![](收获-不止oracle/21.jpg)
{% asset_img label 22.jpg %}
![](收获-不止oracle/22.jpg)
{% asset_img label 23.jpg %}
![](收获-不止oracle/23.jpg)
#### oltp下，块太大，容易导致大量并发查询及更新操作都指向同一个数据库，从而产生热点块竞争
{% asset_img label 24.jpg %}
![](收获-不止oracle/24.jpg)
### 4.0 表设计之五朵金花
{% asset_img label 25.jpg %}
![](收获-不止oracle/25.jpg)
#### 普通堆表不足之处
{% asset_img label 26.jpg %}
![](收获-不止oracle/26.jpg)
#### truncate与delete对比
{% asset_img label 27.jpg %}
![](收获-不止oracle/27.jpg)
{% asset_img label 28.jpg %}
![](收获-不止oracle/28.jpg)
#### 分区表优势
{% asset_img label 29.jpg %}
![](收获-不止oracle/29.jpg)
#### 全局临时表
{% asset_img label 30.jpg %}
![](收获-不止oracle/30.jpg)
{% asset_img label 31.jpg %}
![](收获-不止oracle/31.jpg)
{% asset_img label 32.jpg %}
![](收获-不止oracle/32.jpg)
{% asset_img label 33.jpg %}
![](收获-不止oracle/33.jpg)
#### 分区表
{% asset_img label 34.jpg %}
![](收获-不止oracle/34.jpg)
{% asset_img label 35.jpg %}
![](收获-不止oracle/35.jpg)
{% asset_img label 36.jpg %}
![](收获-不止oracle/36.jpg)
{% asset_img label 37.jpg %}
![](收获-不止oracle/37.jpg)
{% asset_img label 38.jpg %}
![](收获-不止oracle/38.jpg)
{% asset_img label 39.jpg %}
![](收获-不止oracle/39.jpg)
{% asset_img label 40.jpg %}
![](收获-不止oracle/40.jpg)

#### 索引组织表
{% asset_img label 41.jpg %}
![](收获-不止oracle/41.jpg)
#### 簇表
{% asset_img label 42.jpg %}
![](收获-不止oracle/42.jpg)
#### 分区表的类型有范围分区，列表分区，hash分区及组合分区4种
{% asset_img label 43.jpg %}
![](收获-不止oracle/43.jpg)
#### 分区表的索引分为全局索引和局部索引
{% asset_img label 44.jpg %}
![](收获-不止oracle/44.jpg)

### 5.索引
{% asset_img label 45.jpg %}
![](收获-不止oracle/45.jpg)
#### 5.1索引结构图
{% asset_img label 46.jpg %}
![](收获-不止oracle/46.jpg)

#### 如果查询返回绝大多数数据不如全表扫描
{% asset_img label 47.jpg %}
![](收获-不止oracle/47.jpg)
#### 是否创建分区索引，看查询是否用到分区条件
{% asset_img label 48.jpg %}
![](收获-不止oracle/48.jpg)
#### 索引三种查询方式
{% asset_img label 49.jpg %}
![](收获-不止oracle/49.jpg)
{% asset_img label 50.jpg %}
![](收获-不止oracle/50.jpg)
#### 联合索引
{% asset_img label 51.jpg %}
![](收获-不止oracle/51.jpg)
{% asset_img label 52.jpg %}
![](收获-不止oracle/52.jpg)
#### 聚合因子
{% asset_img label 53.jpg %}
![](收获-不止oracle/53.jpg)
{% asset_img label 54.jpg %}
![](收获-不止oracle/54.jpg)
#### index fast full scan 和index full scan
{% asset_img label 55.jpg %}
![](收获-不止oracle/55.jpg)
#### 
{% asset_img label 56.jpg %}
![](收获-不止oracle/56.jpg)
#### 索引对三种语句的影响
{% asset_img label 57.jpg %}
![](收获-不止oracle/57.jpg)
#### 索引三大特点
{% asset_img label 58.jpg %}
![](收获-不止oracle/58.jpg)
### 6
{% asset_img label 59.jpg %}
![](收获-不止oracle/59.jpg)
{% asset_img label 60.jpg %}
![](收获-不止oracle/60.jpg)
{% asset_img label 61.jpg %}
![](收获-不止oracle/61.jpg)
### 7
{% asset_img label 62.jpg %}
![](收获-不止oracle/62.jpg)
{% asset_img label 63.jpg %}
![](收获-不止oracle/63.jpg)