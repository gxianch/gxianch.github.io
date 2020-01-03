---
title: Elasticsearch介绍(三)
date: 2020-01-03 08:46:01
categories: elasticseach
toc: true
mathjax: true
tags:
  - elasticseach
  - 插件
---

上一节讲述了elasticsearch的查询方式，索引的增删改查，dsl语句查询，dsl分为两种，一种是Query，一种是filter。

本节主要介绍elasticsearch的插件，包括head, kibnan, ik分词,searchguard

<!-- more -->
### Ik中文分词
> ik_smart: 会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,国歌”，适合 Phrase 查询。
ik_max_word: 会将文本做最细粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,中华人民,中华,华人,人民共和国,人民,人,民,共和国,共和,和,国国,国歌”，会穷尽各种可能的组合，适合 Term Query；

![elasticsearch5](/images/elasticsearch5.png)

链接:https://github.com/medcl/elasticsearch-analysis-ik

### head
![elasticsearch6](/images/elasticsearch6.png)

### kibnan
![elasticsearch7](/images/elasticsearch7.png)

### searchgurad
基于用户，角色，权限的配置，实现对集群，索引细粒度的访问控制。通过控制索引名称，实现了多租户功能。
> 安装searchgurad
bin/elasticsearch-plugin install -b file:///path/to/search-guard-6-6.7.0-24.3.zip
bin/elasticsearch-plugin install -b com.floragunn:search-guard-6:6.7.0-24.3
> 手动刷新
./sgadmin.sh -ts ../sgconfig/truststore.jks -tspass ba6f8fb3081380fc3ba3 -ks ../sgconfig/kirk-keystore.jks -kspass 079f95446ba0ea022598 -cd ../sgconfig/ -cn yaxines0 -nhnv -p 9300 --accept-red-cluster
![elasticsearch8](/images/elasticsearch8.png)
链接:
https://docs.search-guard.com/latest/authentication-authorization
https://search-guard.com/searchguard-elasicsearch-transport-clients/
https://search-guard.com/elasticsearch-security-first-steps/
https://search-guard.com/transport-client-authentication-authorization/
https://search-guard.com/search-guard-ssl-tls/
https://search-guard.com/elasticsearch-custom-auth-modules/
https://search-guard.com/jwt-secure-elasticsearch/