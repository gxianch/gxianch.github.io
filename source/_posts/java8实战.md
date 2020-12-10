---
title: Java 8实战
date: 2020-12-08 18:19:55
categories: [Java]
toc: true
mathjax: true
top: 98
tags:
  - ✰✰✰✰✰
  - Manning
---

Java 8函数式及流的新特性编程

{% asset_img label cover.jpg %}

![](java8实战/cover.jpg)

github:https://github.com/java8/Java8InAction

<!-- more -->

### 51 Collection主要是为了存储和访问数据，而Stream则主要用于描述对数据的计算。

{% asset_img label 1.jpg %}
![](java8实战/1.jpg)

### 74 行为参数化

{% asset_img label 2.jpg %}
![](java8实战/2.jpg)

{% asset_img label 3.jpg %}
![](java8实战/3.jpg)

### 77 开启线程(() -> System.out.println("Hello world")) == new Runnable()

{% asset_img label 4.jpg %}
![](java8实战/4.jpg)

### 83 Lambda表达式的例

{% asset_img label 5.jpg %}
![](java8实战/5.jpg)

{% asset_img label 6.jpg %}
![](java8实战/6.jpg)

### 86 函数式接口就是只定义一个抽象方法的接口

{% asset_img label 7.jpg %}
![](java8实战/7.jpg)

{% asset_img label 8.jpg %}
![](java8实战/8.jpg)

### 88 是函数式接口一个具体实现的实例

{% asset_img label 9.jpg %}
![](java8实战/9.jpg)

### 89 函数式接口的抽象方法的签名基本上就是Lambda表达式的签名。

{% asset_img label 10.jpg %}
![](java8实战/10.jpg)

{% asset_img label 11.jpg %}
![](java8实战/11.jpg)

### 90 @FunctionalInterface

{% asset_img label 12.jpg %}
![](java8实战/12.jpg)

### 97 function.Consumer<T>

为什么能用foreach,因为有Consumer函数接口，调用的是lambda中Consumer的accept方法，
Consumer签名是传入一个对象，但是没有返回，符合这个签名的操作都可以，不需要调用accept方法
foreach传入一个对象，没有返回

{% asset_img label 13.jpg %}
![](java8实战/13.jpg)

### 98 function.Function<T, R>

{% asset_img label 14.jpg %}
![](java8实战/14.jpg)

{% asset_img label 15.jpg %}
![](java8实战/15.jpg)

### 99 Java 8中的常用函数式接口

{% asset_img label 16.jpg %}
![](java8实战/16.jpg)

{% asset_img label 17.jpg %}
![](java8实战/17.jpg)

### 102 Lambdas及函数式接口的例子

{% asset_img label 18.jpg %}
![](java8实战/18.jpg)

### 103 任何函数式接口都不允许抛出受检异常（checked

异常主要分为种：Exception，RuntimeException以及Error。这三类异常都是Throwable的子类。直接从Exception派生的各个异常类型就是我们刚刚提到的Checked Exception。它的一个比较特殊的地方就是强制调用方对该异常进行处理。就以我们常见的用于读取一个文件内容的FileReader类为例。在该类的构造函数声明中声明了其可能会抛出FileNotFoundException：
 ，可以抛出RuntimeException以及Error

{% asset_img label 19.jpg %}
![](java8实战/19.jpg)



{% asset_img label 20.jpg %}
![](java8实战/20.jpg)

{% asset_img label 21.jpg %}
![](java8实战/21.jpg)

### 104 Lambda的类型是从使用Lambda的上下文推断出来的

{% asset_img label 22.jpg %}
![](java8实战/22.jpg)

{% asset_img label 23.jpg %}
![](java8实战/23.jpg)

### 107 特殊的void兼容规则

{% asset_img label 24.jpg %}
![](java8实战/24.jpg)

返回boolean,但是Consumer不需要boolean,void也可以
但是Predicate需要的boolean就不能返回void替换

### 109 局部变量必须显式声明为final

{% asset_img label 25.jpg %}
![](java8实战/25.jpg)

{% asset_img label 26.jpg %}
![](java8实战/26.jpg)

### 110 闭包

{% asset_img label 27.jpg %}
![](java8实战/27.jpg)

### 112 方法引用

{% asset_img label 28.jpg %}
![](java8实战/28.jpg)

{% asset_img label 29.jpg %}
![](java8实战/29.jpg)

{% asset_img label 30.jpg %}
![](java8实战/30.jpg)

### 123 函数复合

{% asset_img label 31.jpg %}
![](java8实战/31.jpg)

### 149 流的延迟性质

{% asset_img label 32.jpg %}
![](java8实战/32.jpg)

### 151 常见中间流操作和终端流操作

{% asset_img label 33.jpg %}
![](java8实战/33.jpg)

### 164 由map到flatmap,流的扁平化

{% asset_img label 34.jpg %}
![](java8实战/34.jpg)

{% asset_img label 35.jpg %}
![](java8实战/35.jpg)

{% asset_img label 36.jpg %}
![](java8实战/36.jpg)

{% asset_img label 37.jpg %}
![](java8实战/37.jpg)

{% asset_img label 38.jpg %}
![](java8实战/38.jpg)

{% asset_img label 39.jpg %}
![](java8实战/39.jpg)

### 291 Lambda表达式优化设计模式

{% asset_img label 40.jpg %}
![](java8实战/40.jpg)