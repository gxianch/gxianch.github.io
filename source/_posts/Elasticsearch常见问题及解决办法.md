
## 常见问题及解决办法

##### 打开文件数过多Too many open files

解决办法：
 1.修改系统参数，增加最大文件数

##### 出现red索引

解决办法：
 1.查看unassigned分片(没有生成副本导致unassigned状态不在此列），获取unassigned的索引和分片

```
curl -u admin:admin http://localhost:9200/_cat/shards |grep UNASSIGNED
shsnc-snc_redis_rule_data 0 p UNASSIGNED                             
shsnc-snc_redis_rule_data 0 r UNASSIGNED 

索引是shsnc-snc_redis_rule_data,分片0主副分片未分配
```

2.查询得到master节点的唯一标识符

```
curl -u admin:admin  http://localhost:9200/_cat/master?v
id                     host            ip              node
WACuWE4UTcSWWxAEKHbwUg 192.168.199.101 192.168.199.101 es192.168.199.101_1
```

3重新路由reroute

3.1根据索引index和分片shard 先尝试恢复数据

```
curl –u admin:admin -H 'Content-Type:application/json' -XPOST  'http://localhost:9200/_cluster/reroute?pretty' -d '{"commands":[{"allocate_stale_primary":{"index":"shsnc-snc_redis_rule_data","shard":0,"node":"WACuWE4UTcSWWxAEKHbwUg","accept_data_loss":true}}]}'
```

3.2如果不能恢复，则创建空主分片，使索引恢复GREEN状态

```
curl –u admin:admin -H 'Content-Type:application/json' -XPOST  'http://localhost:9200/_cluster/reroute?pretty' -d '{"commands":[{"allocate_empty_primary":{"index":"shsnc-snc_redis_rule_data","shard":0,"node":"WACuWE4UTcSWWxAEKHbwUg","accept_data_loss":true}}]}'
```



##### 超过单个字段最大长度32766
```
ava.lang.IllegalArgumentException: Document contains at least one immense term in field="groupTemplateValue" 
(whose UTF8 encoding is longer than the max length 32766), all of which were skipped.  Please correct the analyzer 
to not produce such terms.  The prefix of the first immense term is: '[102, 114, 111, 109, 32, 58, 32, 42, 32, 44, 10, 
42, 32, 58, 32, 36, 78, 85, 77, 32, 44, 10, 98, 111, 111, 108, 32, 58, 32, 123]...', original message: bytes can be at most 
32766 in length; got 102464
```
分析：此问题仅对keyword类型字段
解决办法：
1.更换字段类型为text,
2.添加ignore_above设置

```
"raw_message": {
"ignore_above": 10000,
"type": "keyword"
}
```
##### 任务超出队列,拒绝请求
```
error":{"root_cause":[{"type":"es_rejected_execution_exception","reason":"rejected execution of org.elasticsearch.transport.TransportService$7@215f2351 on EsThreadPoolExecutor[name = node-0/bulk, queue capacity = 200, org.elasticsearch.common.util.concurrent.EsThreadPoolExecutor@587b75c[Running, pool size = 32, active threads = 32, queued tasks = 200, completed tasks = 98211]]"}],"type":"es_rejected_execution_exception","reason":"rejected execution of org.elasticsearch.transport.TransportService$7@215f2351 on EsThreadPoolExecutor[name = node-0/bulk, queue capacity = 200,
```
默认值是200，可设置参数
thread_pool.write.queue_size: 1000
调参数是临时解决办法，需要扩容集群
##### 新加入机器到集群时，写入倾斜到新集群
```
The cluster-level shard allocator tries to spread the shards of a single index across as many nodes as possible. However, depending on how many shards and indices you have, and how big they are, it may not always be possible to spread shards evenly.

The following *dynamic* setting allows you to specify a hard limit on the total number of shards from a single index allowed per node:

    index.routing.allocation.total_shards_per_node

  The maximum number of shards (replicas and primaries) that will be allocated to a single node. Defaults to unbounded.

You can also limit the amount of shards a node can have regardless of the index:

    cluster.routing.allocation.total_shards_per_node

  The maximum number of shards (replicas and primaries) that will be allocated to a single node globally. Defaults to unbounded (-1).

These settings impose a hard limit which can result in some shards not being allocated.
  Use with caution.
```
索引级别设置：单个机器上分配该索引多少个分片，默认无限制
集群级别设置：单个机器上总分片数，默认无限制
注意：设置不合理，可能导致分片无法分配，比如索引有10个分片，只有2个es节点，如果设置单个节点该索引最大值为3，就会导致该索引分片无法分配

https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-total-shards.html



##### 模糊查询导致堆栈溢出
```sql
select * from asiainfo-ceshizhibiao-* where cpu like '%在迪士尼乐园，点亮心中奇梦。它是一个充满创造力、冒险精神与无穷精彩的快地。您可在此游览全球最大的迪士%' 
DSL
{"from":0,"size":1000,"query":{"bool":{"filter":[{"bool":{"must":[{"wildcard":{"cpu":{"wildcard":"*在迪士尼乐园，点亮心中奇梦。它是一个充满创造力、冒险精神与无穷精彩的快地。您可在此游览全球最大的迪士*","boost":1.0}}}],"adjust_pure_negative":true,"boost":1.0}}],"adjust_pure_negative":true,"boost":1.0}}}

```
like中字段太长，导致堆栈溢出，目前like限制在30个字节
```
[2017-06-14T21:06:39,330][ERROR][o.e.b.ElasticsearchUncaughtExceptionHandler] [xx.xx.xx.xx] fatal error in thread [elasticsearch[xx.xx.xx.xx][search][T#29]], exiting
java.lang.StackOverflowError
at org.apache.lucene.util.automaton.Operations.isFinite(Operations.java:1053) ~[lucene-core-6.2.1.jar:6.2.1 43ab70147eb494324a1410f7a9f16a896a59bc6f - shalin - 2016-09-15 05:15:20]
at org.apache.lucene.util.automaton.Operations.isFinite(Operations.java:1053) ~[lucene-core-6.2.1.jar:6.2.1 43ab70147eb494324a1410f7a9f16a896a59bc6f - shalin - 2016-09-15 05:15:20]
at org.apache.lucene.util.automaton.Operations.isFinite(Operations.java:1053) ~[lucene-core-6.2.1.jar:6.2.1 43ab70147eb494324a1410f7a9f16a896a59bc6f - shalin - 2016-09-15 05:15:20]
at org.apache.lucene.util.automaton.Operations.isFinite(Operations.java:1053) ~[lucene-core-6.2.1.jar:6.2.1 43ab70147eb494324a1410f7a9f16a896a59bc6f - shalin - 2016-09-15 05:15:20]
```
##### 出现Can not be imported as a dangling index 异常

https://discuss.elastic.co/t/how-to-resolve-dangling-indices-error-on-each-index/130609/11
1.找到索引对应的uuid，
2.在报错的es节点上删除对应的本地存储nodes/0/indices/{uuid}文件



![](/images/Elasticsearch介绍/elasticsearch9.bmp)

![](Elasticsearch介绍/elasticsearch9.bmp)