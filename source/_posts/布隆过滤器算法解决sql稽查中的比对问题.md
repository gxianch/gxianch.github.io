---
title: 布隆过滤器算法解决sql稽查中的比对问题
date: 2023-08-22 08:51:57
categories:
  - 算法
toc: true
mathjax: true
top: 98
tags:
  - BloomFilter
  - ibook
  - ✰✰
---



副本

 ![jpg](https://img1.doubanio.com/view/subject/l/public/s34026678.jpg){% asset_img label cover.jpg %}

https://book.douban.com/subject/35245517/
 <!-- more -->



布隆过滤器(BloomFilter)算法解决sql稽查中的比对问题

最近在做sql稽查项目时，需要拿一条sql去和标准库中通过审核的sql去比对，如果一样，说明这条sql符合条件。

需求其实很清晰，只要判断一个数据在集合中是否存在即可。

看起来很简单，用HashMap就可以解决，HashMap写入查询的效率都很高，



在内存有限的情况下我们不能使用这种方式。

实际情况也是如此；既然要判断一个数据是否存在于集合中，考虑的算法的效率以及准确性肯定是要把数据全部 `load` 到内存中的。

在标准库

## 算法讲述

布隆过滤器是基于Hash来实现的，在学习BloomFilter之前，也需要对Hash的原理有基本的了解。个人认为，BloomFilter的总体思想实际上和bitmap很像，但是比bitmap更节省空间，误判率也更低。

BloomFilter的整体思想并不复杂，主要是使用k个Hash函数将元素映射到位向量的k个位置上面，并将这k个位置全部置为1。当查找某元素是否存在时，查找该元素所对应的k位是否全部为1即可说明该元素是否存在。

![preview](https://segmentfault.com/img/remote/1460000024566951/view)

![preview](https://segmentfault.com/img/remote/1460000024566952/view)

## 算法流程

BloomFilter的整体算法流程可总结为如下步骤：

\1. BloomFilter初始化为m位长度的位向量，每一位均初始化为0

![preview](https://pic2.zhimg.com/v2-3febdc4c9f891491e76083288dae4c99_r.jpg)

\2. 使用k个相互独立的Hash函数，每个Hash函数将元素映射到{1..m}的范围内，并将对应的位置为1。

![preview](https://pic2.zhimg.com/v2-32eb6dead67a965b55ec7522fc6f5ae1_r.jpg)

如上图所示，元素x分别被三个Hash函数映射到了三个位置8、1、14，并将这三个位置从0变为1。

\3. 若检查一个元素y是否存在，首先第一步使用k个Hash函数将元素y映射到k位。分别检测每一位是否为0。若某一位为0，则元素y一定不存在，若全部为1，则有可能存在。

**空间复杂度**

BloomFilter 使用位向量来表示元素，而不存储本身，这样极大压缩了元素的存储空间。其空间复杂度为O(m)，m是位向量的长度。而m与插入总数量n的关系如下公式(1)所示

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bequation%7D++++m%3D-%7B%5Cfrac+%7Bn%5Cln+p%7D%7B%28%5Cln+2%29%5E%7B2%7D%7D%7D+%5Clabel%7Beq%3Amn%7D++%5Cend%7Bequation%7D)

我们可以利用这个公式来算一下需要抓取100万个URL时BloomFilter所占据的空间。

假设要求误判率为1%，因此该公式可转化为 ![[公式]](https://www.zhihu.com/equation?tex=m%3D9.6+%2A+n) 。故此时BloomFilter位向量的大小为 ![[公式]](https://www.zhihu.com/equation?tex=100w%2A9.6%3D+960w+bit) ，约1.1M内存空间。
只需要1.1M的内存空间，就可满足100万个url的去重需求，这个空间复杂度之低不可谓不惊人。 实际上，哪怕是1亿个URL，也仅需100M左右的内存空间即可满足BloomFilter的空间需求，这对于绝大部分爬虫的体量来说，是完全可行的。

**时间复杂度**

时间复杂度方面 BloomFilter的时间复杂度仅与Hash函数的个数k有关，即O(k)

## 误判率

为什么说，在查找元素时，即使某个元素所映射的k位全部位1，依然无法确定它一定存在？

这是因为当插入的元素很多的情况下，某个元素即使之前不存在，但是它所映射的k位已经被之前其他的元素置为1了，这样就会出现误判，BloomFilter会认为它已经存在了。

但是这个概率是非常小的。根据维基百科的推导公式来说，误判率的大小p满足以下公式(2):

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bequation%7D+++%5Cln+p+%3D+-%7B%5Cfrac+%7Bm%7D%7Bn%7D%7D%5Cleft%28%5Cln+2%5Cright%29%5E%7B2%7D+%5Clabel%7Beq%3Ap%7D+%5Cend%7Bequation%7D+)

其中m为位向量的长度，n为要插入元素的总数。当误判率为1%时, ![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac+mn%3D9.6) 即每个元素仅需要9.6个字节存储即可

Hash函数的个数k，与误判率大小p的关系为如下公式(3) 所示

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bequation%7D+++k+%3D+%7B-%7B%5Cln+p%7D+%5Cover+%7B%5Cln+2%7D%7D+%5Clabel%7Beq%3Ak1%7D+++%5Cend%7Bequation%7D+)

当误判率大小为0.1时，k为3。当误判率大小为0.01时，k为7

而，k与位向量的长度m和插入元素的总数n的关系为公式(4) 所示

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bequation%7D+++k+%3D+%7B%5Cfrac+%7Bm%7D%7Bn%7D%7D%5Cln+2+%5Clabel%7Beq%3Ak2%7D++%5Cend%7Bequation%7D)