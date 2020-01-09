---
title: Elasticsearch介绍(二)
date: 2020-01-03 08:46:01
categories: [elasticseach]
toc: true
mathjax: true
tags:
  - dsl
---

上一节讲述了为什么选择elasticsearch, elasticsearch的基本概念和Lucene的基本原理，

本节主要介绍elasticsearch的查询方式，索引的增删改查，dsl语句查询，dsl分为两种，一种是Query，一种是filter

<!-- more -->

### 增删改查
> 创建索引，索引名称必须全部为**小写字母**
```
curl -XPUT localhost:9200/customer
```
> 向索引添加文档
```
curl -XPUT localhost:9200/customer/_doc/1?pretty -H 'Content-Type: application/json' -d '{"name": "John Doe"}‘
```
> 获取数据
```
curl -XGET localhost:9200/customer/_doc/1?pretty
```
> 更新数据
```
curl -XPOST localhost:9200/customer/_doc/1/_update?pretty -H 'Content-Type: application/json' -d'{"doc": { "name": "Jane Doe", "age": 20 }}'
```
> 删除索引
```
curl -XDELETE localhost:9200/customer?pretty
```

###  DSL 查询(Query和fliter)
Query DSL又叫查询表达式，是一种非常灵活又富有表现力的查询语言，采用JSON接口的方式实现丰富的查询，  并使你的查询语句更灵活、更精确、更易读且易调试
**查询Query**
语句在执行时既要计算文档是否匹配，还要计算文档相对于其他文档的匹配度有多高，匹配度越高，*_score* 分数就越高
> match_all 
> match 
> multi_match 
> bool 
> wildcards 
> regexp 
> prefix 
> Phrase Matching

示例:
curl -XGET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d'{"query": { "match": { "address": "mill" } }}'
curl -XGET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d'{"query": { "match_phrase": { "address": "mill lane" } }}'


**Filter 过滤**
过滤上下文中的语句在执行时只关心文档是否和查询匹配，不会计算匹配度，也就是得分
> term 
terms 
range 
exists 和 missing 
bool 

示例：
curl -XGET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d'{"query": {"bool": {"must": { "match_all": {} },"filter": {"range": {"balance": {"gte": 20000,"lte": 30000}}}}}}'
