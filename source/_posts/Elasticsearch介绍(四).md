---
title: Elasticsearch介绍(四)
date: 2020-01-03 08:46:01
categories: [elasticseach,命令,安装]
toc: true
mathjax: true
tags:
  - elasticseach
  - 命令
  - 安装
---

上一节介绍elasticsearch的插件，包括head, kibnan, ik分词,searchguard。

本节主要介绍elasticsearch的系统环境配置，es配置和一些操作和优化命令

<!-- more -->

### 系统环境配置
1. /etc/sysctl.conf
#增加以下参数
```
vm.max_map_count=655360
```
#永久关闭内存交换或设置为1
```
vm.swappiness = 1	
```
执行以下命令，确保生效配置生效：
sysctl -p

2. /etc/security/limits.conf
#增加以下参数
```
* soft nofile 655360
* hard nofile 655360
* soft nproc unlimited
* hard nproc unlimited
* soft memlock unlimited
* hard memlock unlimited
```
3. /etc/security/limits.d/20-nproc.conf
#增加以下参数
```
* soft nproc 65536
```

### elasticsearch.yml配置
```
cluster.name: shsnces
node.name: node1
http.cors.enabled: true 
http.cors.allow-origin: "*"
network.host: 0.0.0.0
http.port: 9200
transport.tcp.port: 9300
http.cors.allow-headers: Authorization,X-Requested-With,Content-Length,Content-Type
xpack.security.enabled: false
searchguard.enterprise_modules_enabled: false
searchguard.restapi.roles_enabled: ["sg_all_access"]
searchguard.ssl.transport.enabled: true
searchguard.ssl.transport.keystore_filepath: node-0-keystore.jks
searchguard.ssl.transport.keystore_password: xxxxx
searchguard.ssl.transport.truststore_filepath: truststore.jks
searchguard.ssl.transport.truststore_password: xxxxx
searchguard.ssl.transport.enforce_hostname_verification: false
searchguard.ssl.transport.resolve_hostname: false

searchguard.ssl.http.enabled: false
#searchguard.ssl.http.keystore_filepath: node-0-keystore.jks
#searchguard.ssl.http.keystore_password: xxxxx
#searchguard.ssl.http.truststore_filepath: truststore.jks
#searchguard.ssl.http.truststore_password: xxxxx

searchguard.authcz.admin_dn:
 - CN=kirk,OU=client,O=client,L=test,C=DE

#关闭searchguard,测试用
searchguard.disabled: true
indices.fielddata.cache.size: 40%
node.max_local_storage_nodes: 2
cluster.routing.allocation.same_shard.host: true
bootstrap.memory_lock: true
xpack.ml.enabled: false
#action.destructive_requires_name: true
discovery.zen.ping.unicast.hosts: ["192.168.8.101:9300", "192.168.8.103:9300", "192.168.8.104:9300"]  
#这个参数控制的是，一个节点需要看到的具有master节点资格的最小数量，然后才能在集群中做操作。官方的推荐值是(N/2)+1，其中N是具有master资格的节点的数量
discovery.zen.minimum_master_nodes: 2 
#控制集群在达到多少个节点之后才会开始数据恢复,通过这个设置可以避免集群自动相互发现的初期,shard分片不全的问题,假如es集群内一共有5个节点,就可以设置为5,那么这个集群必须有5个节点启动后才会开始数据分片,如果设置为3,就有可能另外两个节点没存储数据分片
#gateway.recover_after_nodes: 1
#初始化数据恢复的超时时间,假如gateway.recover_after_nodes参数设置为5,就是5个节点全部启动后,再过5分钟开始数据恢复
#gateway.recover_after_time: 5m
#启动几个节点后开始数据恢复,假如gateway.recover_after_nodes这个参数设置为5,那么等到这5个节点全部启动后直接可以数据恢复,不用等待gateway.recover_after_time设置的时间
#gateway.expected_nodes: 2
举例来说，对于一个有10个data node的集群，如果有以下的设置:
#gateway.expected_data_nodes: 10
#gateway.recover_after_time: 5m
#gateway.recover_after_data_nodes: 8
那么集群5分钟以内10个data node都加入了，或者5分钟以后8个以上的data node加入了，都会立即启动recovery过程。
#配置在每轮ping操作中等待DNS查找的时间。这被指定为时间单位，默认为5秒。。
#discovery.zen.ping.unicast.resolve_timeout
#（默认为30s）确定节点将多久决定开始选举或加入现有的群集之前等待
# discovery.zen.ping_timeout: 30s
#How often a node gets pinged. Defaults to 1s.
#ping_interval
#How long to wait for a ping response, defaults to 30s.
#ping_timeout
#How many ping failures / timeouts cause a node to be considered failed. Defaults to 3.
#ping_retries
#控制fielddata允许内存大小，达到HEAP 40% 自动清理旧cache
#indices.fielddata.cache.size: 40%
#防止同一个shard的主副本存在同一个物理机上（因为如果存在一个机器上，副本的高可用性就没有了
#cluster.routing.allocation.same_shard.host:true
#但仍允许os在紧急情况下发生交换。对于大部分Linux操作系统，可以在sysctl 中这样配置：#vm.swappiness = 1，
#临时修改sysctl vm.swappiness=1 ， 永久修改，vi /etc/sysctl.conf  在这个文档的最后加上这样一行:vm.swappiness=1,保存，重启
#如果上面的方法都不能做到，你需要打开配置文件中的mlockall开关，它的作用就是运行JVM锁住内存，禁止OS交换出去。在elasticsearch.yml配置如下：bootstrap.mlockall: true
```

### 集群参数设置和优化
停止和开启分片分配
```
curl -u admin:admin -X PUT 192.168.199.101:9200/_cluster/settings {"persistent":{"cluster.routing.allocation.enable":"none"}}
curl -u admin:admin -X PUT 192.168.199.101:9200/_cluster/settings {"persistent":{"cluster.routing.allocation.enable":"all"}}
```
同步副本分片，刷新到磁盘
```
curl -XPOST http://127.0.0.1:9200/_flush/synced
```
索引段强制合并为1个(*通配符表示全部，也可以具体的索引和其它通配符索引)，该操作会占用大量资源，慎重操作
```
 curl -u admin:admin -XPOST http://192.168.199.101:9200/*/_forcemerge?max_num_segments=1
```

```
curl -u admin:admin -XPUT 127.0.0.1:9201/twitter/_settings   -H 'Content-Type: application/json' -d '{"index":{"refresh_interval":"30s"}‘
```
 ik_smart: 会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,国歌”，适合 Phrase 查询。
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