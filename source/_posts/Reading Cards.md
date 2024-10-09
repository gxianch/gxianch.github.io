---
cssclasses:
  - cards
  - cards-cols-5
  - iframe-wide
  - cards-cover
  - cards-align-bottom
title: Reading Cards
categories: 
tags: 
date: 2024-10-08
time: 14:37
link:
---

## 待读
tag: #TODO 




## 微笑读书记录
```dataview
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, categories,tags,readingStatus,lastReadDate  where cover != null SORT lastReadDate desc
```



## Dataview使用

```dataview
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, category, readYear as Year
from #re
```
```
table without id("![]("+ cover + ")") as Cover, file.link as Name , author as Author, category, readYear as Year,readingStatus,reviewCount
from #阅读状态/阅读中  where cover != null and readYear=2022 
```


