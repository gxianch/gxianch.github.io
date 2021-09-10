## 日常重启

### 滚动重启：适应于日常维护升级

1. 可能的话，停止写入新的数据，停止写入可以帮助后续提高恢复速度。停止写入数据后, 将数据刷到磁盘，执行命令如下，可能需要几分钟，等待完成后再执行后续步骤。
```
curl -u admin: admin -XPOST http:// localhost:9200/_flush/synced
```
2. 临时禁止分片分配，执行命令如下：
```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable" : "none"}}'
```
3. 关闭单个节点。
4. 执行维护/升级。
5. 重启节点，然后确认它加入到集群了。
```
_cat/health; _cat/nodes
```

6. 用如下命令重启分片分配：
```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable" : "all"}}'
```
分片再平衡会花一些时间。一直等到集群变成 green 绿色 状态后再继续。

7. 重复第 2 到 6 步操作剩余节点。
8. 所有节点重启完成并green, 再次开始写入。

 

### 全量重启：适应于故障恢复

1.永久禁止分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{" persistent":{"cluster.routing.allocation.enable" : "none"}}'
```

2.内存数据写到磁盘
```
curl -u admin: admin -XPOST http:// localhost:9200/_flush/synced
```
3.在所有机器上执行关闭节点命令
```
for i in `ps -ef |grep 'Elasticsearch -d'  |grep -v grep |awk '{print $2}'`; do kill $i; done
```

3.检查是否关闭 
```
ps -ef |grep 'Elasticsearch -d'
```

4.确认所有节点关闭后，依次重启所有节点
```
bin/elasticsearch –d
```

5.检测集群状态和节点 

```
_cat/health; _cat/nodes
```

6.等待集群恢复，等所有节点加入到集群并恢复yellow状态后才进行下一步操作

7. 所有节点加入到集群并恢复yellow状态后开启分片移动
```
curl -u admin:admin -XPUT 192.168.199.101:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"persistent":{"cluster.routing.allocation.enable":"all"}}'
```

8.继续观察集群状态和节点
```
_cat/health; _cat/nodes
```





# ES场景应急预案

##  场景1 -单节点故障

故障现象及影响：

命令_cat/nodes查不到该节点，如果不能在设置的时间内恢复该节点，则该节点的数据在其它节点上的另一个副本会变成主分片，同时主分片会再次生成副本，会增加集群压力，以目前总数据54T来计算， 6台机器24个节点，平均每台机器8T数据，单个节点上有2T数据， 单节点故障会造成2T数据在其它节点重新生成，同时集群总节点数减少1个， 在集群总体压力不大时，单节点故障性能会有所降低，但是不影响使用， 如果集群总体压力已经很大， 如果其它节点不能承担该节点的数据，会造成其它节点不响应，发生连锁反应以致集群不可用。

处理措施：
1.临时停止分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"none"}}'
```

2.临时停止往该节点写入数据，执行命令如下

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude. _name":"node1"}}'
```
**3.观察集群压力及集群响应情况，如果出现响应超时等现象应立即停止业务侧数据写入，防止故障扩大化**

4.在集群正常的情况下，恢复节点，并等待节点加入集群
5.节点加入集群后开启分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"all"}}'
```
6.允许往该节点写入数据，执行命令如下：

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._name":""}}'
```


## 场景2 -单机器故障

故障现象及影响：

目前一台机器上已部署4个节点，所以如果该机器故障，则4个节点将不可用，命令_cat/nodes查不到该4个节点，如果不能在设置的时间内恢复该机器，同单节点故障一样，单机器故障会造成8T数据在其它节点重新生成，同时集群总节点数减少4个，同单节点故障一样，对集群的影响程度视目前集群压力而定，集群压力越小，造成的影响就越小，集群压力越大，造成的影响就大， 最终集群不可用。

处理措施：
1.临时停止分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"none"}}'
```

2.临时停止往该机器ip写入数据，执行命令如下
```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._ip":"10.159.102.43"}}'
```
**3.观察集群压力及集群响应情况，如果出现响应超时等现象应立即停止业务侧数据写入，防止故障扩大化**

4.在集群正常的情况下，恢复节点，并等待节点加入集群

5.节点加入集群后开启分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"all"}}'
```
6.允许往该ip写入数据，执行命令如下：

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._ip":""}}'
```


## 场景3 –多机器故障

故障现象及影响：

目前6台机器6个master备选节点，设置的是要求至少4个备选节点存在集群才可用，因此最多允许2台机器8个节点发生故障，不过按目前54T总数据来算， 4台机器需要承担2台机器故障额外的16T数据，现有集群抗不住

处理措施：

在集群压力小时同单机器故障处理措施相同

**按目前的集群数据量和集群压力应立即停止写入**

 然后临时停止分片移动

```
curl -u admin:admin -XPUT localhost:9200/_cluster/settings -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"none"}}'
```



## 场景4 –整套ES集群故障

故障现象及影响：

此时命令已没有响应，也无法做任何操作，只能重启节点或切换，针对这种情况，只能在主机或单节点出现故障时要做到早发现早告警早处理。

处理措施：

重启节点或切换备用



## 2.5  场景5 –集群缓慢

### **查询缓慢**

故障现象及影响：

查询响应超时或无响应

处理措施：

造成延时的原因分3个层面，一是集群层面，集群数据量大，分片数量多，正处于大量数据写入中， 查询较多，二是索引层面，索引太大，索引分片数据太大。三是查询层面，大查询或者大聚合会导致ES响应慢，还会占用大量JVM内存，从而导致其他任务堆压，所以应该尽量避免大查询或者大聚合，优化查询缓慢需要从这3个层面着手

### **写入缓慢**

故障现象及影响：

写入超时或写不进去

处理措施：

造成超时的原因分3个层面，一是集群层面，集群数据量大，分片数量多，大量数据同时写入，二是索引层面，索引太大，索引分片数据太大。三是写入层面，写入时关闭副本，空闲时再开启副本，延长或关闭刷新时间。

 

**造成集群缓慢的两个共同因素是集群层面，数据太多，集群一直处于高位运行，不仅会影响查询和写入，而且容易造成节点之间响应超时，丢失节点，二是索引层面，索引大小，分片数设置不合理，索引需要应用侧进行合理优化。**



预案总结：

分布式的特点是单机压力都不会处于高位，在某个或多个节点发生故障时，其它节点有能力支撑已发生故障的节点，这样才能保障了集群的稳定性，也就是说集群稳定性和每个节点的压力息息相关，每个节点压力越低，集群总体容错率越高，也越稳定。

目前集群总数据量54个T，单台机器有8T数据，堆内存使用率长期处于高位状态，单机器故障就会引发8T数据移动, 消耗大量i/o, 磁盘，cpu资源，同时其它节点更加不堪重负甚至出现无法响应。

为了提高集群的容错性和稳定性，建议降低数据量或扩充节点。