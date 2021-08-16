# Elasticsearch最佳实践

来源：斗鱼ElasticSearch集群运维实践

### 集群设置
#### 硬件设置
CPU 32核，内存 128GB
对传统硬盘采用6~8块盘组成RAID 0提高IO写入速度
对延迟要求低的APM日志、网关日志等，使用SSD存储热索引
服务器预留大约一半内存作为系统缓存提升查询性能
#### 集群配置
版本：大部分 Java 1.8.0_162，Elasticsearch 6.2.4
Heap：Master节点 4~6GB，Client节点 10~15GB，Data节点 30GB
一台物理机上启动两个Data节点
开启cluster.routing.allocation.same_shard.host选项
写入的Client节点 和 读取的Client节点 分开
对外提供查询服务的cross-search小集群分开，专门提供跨集群搜索服务

### 索引设置
◼索引刷新间隔 index.refresh_interval 为30s或更大
◼事务日志持久化模式 index.translog.durability 为 async
◼推迟分片分配 index.unassigned.node_left.delayed_timeout 为 10m
◼合并线程数 index.merge.scheduler.max_thread_count 对机械硬盘为1
◼限制节点分片数 index.routing.allocation.total_shards_per_node 一般为1或2
◼Mapping
 norms 不需要计算字段的评分，将该参数设为false
 ignore_malformed 忽略这个字段错误并保留该文档中的其他字段，设为ture
 _source 文档原始内容，可以考虑删除
 dynamic_templates 动态模板将默认的字符串类型映射为keyword
 如果不需要按数字大小排序或过来，不使用int类型，使用keyword

### 索引管理
◼索引生命周期
 定时提前新建下一天用的空索引
 冷热数据分离，48小时以前的索引数据搬移到冷节点
 索引变冷后（无写入后），进行强制合并（ForceMerge）
 定时关闭和删除索引
◼合理切分索引和制定分片大小，避免节点故障后索引恢复速度慢的问题
◼日志收集场景下写请求远远大于读请求，尽量均匀分配分片在每个节点上，分摊写压力
◼节点磁盘独立，避免节点间产生IO竞争



### 索引优化 
#### 合理设置字段索引参数 
      1. 不需要过滤时可以禁用索引 "index"：false 
            2. 不需要text字段的score，可以禁用 "norms"：false 
            3. 不需要短语查询可以不索引positions "index_options" : "freqs" 
                  4. 禁用全文搜索功能 _all   "enabled" :  false 
#### 采用多线程批量提交数据 
               1.使用 multiple workers/threads发送数据到ES 
               2. 每个bulk请求不宜过大，避免导致OOM 
                    3. 遇到EsRejectedExecutionException则说明IO压力过大，需要调整线程或bulk size 
#### 增加Refresh间隔，减少副本数量 
                    1. 增加refresh时间间隔可以避免生成过多的segment，从而减少合并压力 
                              2. 同步完成后再加副本可以避免主副分片同步带来的压力 
#### 硬件升级 
                              1. 使用SSD硬盘，提高读写性能 
                                    2. 使用性能更好的CPU，高并发 
                                    3. 使用大内存，索引缓冲默认会占用JVM 10%的内存空间 

###  查询优化 
#### 尽量避免使用script 
      1. 尽量避免使用script，如要使用则可以选择painless & experssions引擎 
      2. 避免大量动态脚本产生，因为脚本需要编译才能执行。 
#### 避免大查询和大聚合 
      1. 大查询或者大聚合会导致ES响应慢，还会占用大量JVM内存，从而导致其他任务堆压 
      2. 建议在服务层通过程序来组装业务，并及时阻断查询size或者笛卡尔积过大的请求 
#### 避免深度分页 
      1. 深度分页导致大批数据返回到协调节点，协同节点一共会受到N * (From + Size) 条数据，然后进行排序 
      2. 使用 Elasticsearch scroll 高效滚动的方式来解决深度分页问题 
#### 使用routing 
      可直接根据 routing 信息定位到某个分配查询，避免查询所有的分片，以及在协调节点上无需排序 
#### 加大线程池阻塞队列长度 
      修改thread_pool.bulk.queue_size，默认为500，可适度调整到1000-2000 
#### 设置Cache参数 
      1. QueryCache: 过滤查询过多则可以调大indices.queries.cache.size 
      2. FieldDataCache:聚类或排序场景较多则可以调大indices.fielddata.cache.size 

###  OS  & JVM 优化 
#### 禁用swapping 
      内存交换到磁盘对服务器性能来说是致命的，通过设置swappiness = 0来禁用该功能 
#### 文件描述符和MMap 
      Lucene使用了大量的文件，Elasticsearch 在节点和 HTTP 客户端之间进行通信也使用了大量的套接字， 
      这需要足够的文件描述符, 设置sysctl -w vm.max_map_count=262144 
#### 单个节点内存不要超过32G 
      1. JVM 在内存小于 32 GB 的时候会采用一个内存对象指针压缩技术 
      2. 大指针在主内存和各级缓存之间移动会变得缓慢 
      3. 机器内存大， 可以采用单机多节点部署 
#### 至少留一半内存给LUCENE 
      Lucene可以利用操作系统底层来缓存数据结构，以便快速访问，这些内存并不属于JVM内存 
#### 单机多节点部署避免主副分片被分配到同一物理机 
      设置cluster.routing.allocation.same_shard.host:true 
#### 使用G1垃圾回收器 
      1. 存在单个索引数据非常大的集群，可以考虑使用G1替代CMS 
      2. 设置MaxGCPauseMillis参数减少GC停顿时间，但如果过小则会带来CPU消耗 

#### 禁用numa模式，设置vm.zone_reclaim_mode=0 
Elasticsearch集群中，经常偶节点出现sys cpu占用过高问题， 出现问题期间，无法登录及操作,其它活跃进程占用
CPU都显示100%；压测集群过程中，出现了SYS 飙高问题，但是查看atop这个时间段的数据，也因为atop进程hang
住，导致了无法采集数据







## 排除故障节点

### 背景介绍

如果某个节点出现问题,需要立即排除该节点，避免数据进入该节点，等检查原因后再恢复

### 故障现象

节点有刷错误日志

### 解决办法

暂时排除该es节点,新数据不会进来该节点，旧数据会自动移走，等数据清空后，删掉节点数据和日志后重启该节点,z执行命令

```
curl -XPUT https://172.28.50.200:9200/_cluster/settings -d '{"transient":{"cluster.routing.allocation.exclude._ip":"172.29.50.131,172.29.50.129"}}'
```

 

## 优化索引分片数

### 背景介绍

ES中所有数据的文件块，也是数据的最小单元块

### 故障现象

某个索引入库延时比较大，搜索速度慢

### 解决办法

根据索引大小确定分片大小，分片不超 10GB，且单节点总分片不超 600，找到对应的表模板进行修改

1.先从head插件获取原来的模板 GET命令，复制右侧数据到左侧框内，

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image002.jpg)

2.修改number_of_shards值,发送PUT请求，

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image004.jpg)

3.再次使用GET请求，查看是否已经修改

## ES监控

### 背景介绍

Es监控对于观察集群状态和写入量非常有用，但是Kibnan的监控数据来源于es集群生成，数据量大，在es集群压力大时会影响数据入库

### 解决办法

关闭监控

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image005.png)

 

 

 

## 硬件选择

### 内存

如果有一种资源是最先被耗尽的，它可能是内存。排序和聚合都很耗内存，所以有足够的堆空间来应付它们是很重要的。即使堆空间是比较小的时候， 也能为操作系统文件缓存提供额外的内存。因为 Lucene 使用的许多数据结构是基于磁盘的格式，Elasticsearch 利用操作系统缓存能产生很大效果。

64 GB 内存的机器是非常理想的

### CPU

大多数 Elasticsearch 部署往往对 CPU 要求不高。因此，相对其它资源，具体配置多少个（CPU）不是那么关键。你应该选择具有多个内核的现代处理器，常见的集群使用两到八个核的机器。 

如果你要在更快的 CPUs 和更多的核心之间选择，选择更多的核心更好。多个内核提供的额外并发远胜过稍微快一点点的时钟频率。

### 硬盘

硬盘对所有的集群都很重要，对大量写入的集群更是加倍重要（例如那些存储日志数据的）。硬盘是服务器上最慢的子系统，这意味着那些写入量很大的集群很容易让硬盘饱和，使得它成为集群的瓶颈。

如果你负担得起 SSD，它将远远超出任何旋转介质（注：机械硬盘，磁带等）。 基于 SSD 的节点，查询和索引性能都有提升。如果你负担得起，SSD 是一个好的选择。

如果你使用旋转介质，尝试获取尽可能快的硬盘（高性能服务器硬盘，15k RPM 驱动器）。

使用 RAID 0 是提高硬盘速度的有效途径，对机械硬盘和 SSD 来说都是如此。没有必要使用镜像或其它 RAID 变体，因为高可用已经通过 replicas 内建于 Elasticsearch 之中。

最后，避免使用网络附加存储（NAS）。人们常声称他们的 NAS 解决方案比本地驱动器更快更可靠。除却这些声称， 我们从没看到 NAS 能配得上它的大肆宣传。NAS 常常很慢，显露出更大的延时和更宽的平均延时方差，而且它是单点故障的。

### 网络

快速可靠的网络显然对分布式系统的性能是很重要的。 低延时能帮助确保节点间能容易的通讯，大带宽能帮助分片移动和恢复。现代数据中心网络（1 GbE, 10 GbE）对绝大多数集群都是足够的。

即使数据中心们近在咫尺，也要避免集群跨越多个数据中心。绝对要避免集群跨越大的地理距离。

Elasticsearch 假定所有节点都是平等的—并不会因为有一半的节点在150ms 外的另一数据中心而有所不同。更大的延时会加重分布式系统中的问题而且使得调试和排错更困难。

和 NAS 的争论类似，每个人都声称他们的数据中心间的线路都是健壮和低延时的。这是真的—直到它不是时（网络失败终究是会发生的，你可以相信它）。 从我们的经验来看，处理跨数据中心集群的麻烦事是根本不值得的。

## 系统配置

ElasticSearch 理论上必须单独部署，并且会独占几乎所有系统资源，因此需要对系统进行配置，以保证运行 ElasticSearch 的用户可以使用足够多的资源。生产集群需要调整的配置如下：

### 设置 JVM 堆大小

某ElasticSearch 需要有足够的 JVM 堆支撑索引数据的加载，对于公司的机型来说，因为都是大于 128GB 的，所以推荐的配置是 32GB（如果 JVM 以不等的初始和最大堆大小启动，则在系统使用过程中可能会因为 JVM 堆的大小调整而容易中断。 为了避免这些调整大小的暂停，最好使用初始堆大小等于最大堆大小的 JVM 来启动），预留足够的 IO Cache 给 Lucene（官方建议超过一半的内存需要预留）。

设置方法（需要重启进程）

```
修改 ${PATH_TO_ElasticSearch_HOME}/config/jvm.options 中的 Xms 和 Xmx

-Xms31g

-Xmx31g
```



### 关闭 swap & 禁用交换

必须要关闭 swap，因为在物理内存不足时，如果发生 FGC，在回收虚拟内存的时候会造成长时间的 stop-the-world，最严重的后果是造成集群雪崩。公司的默认模板是关闭的，但是要巡检一遍，避免有些机器存在问题

设置方法：

```
# Step1. root 用户临时关闭

sudo swapoff -a

sudo sysctl vm.swappiness=0

# Step2. 修改 /etc/fstab，注释掉 swap 这行

# Step3. 修改 /etc/sysctl.conf，添加：

vm.swappiness = 0

\# Step4. 确认是否生效

sudo sysctl vm.swappiness
```

也可以通过修改 yml 配置文件的方式从 ElasticSearch 层面禁止物理内存和交换区之间交换内存：

 

Linux 把它的物理 RAM 分成多个内存块，称之为分页。内存交换（swapping）是这样一个过程，它把内存分页复制到预先设定的叫做交换区的硬盘空间上，以此释放内存分页。物理内存和交换区加起来的大小就是虚拟内存的可用额度。

 

内存交换有个缺点，跟内存比起来硬盘非常慢。内存的读写速度以纳秒来计算，而硬盘是以毫秒来计算，所以访问硬盘比访问内存要慢几万倍。交换次数越多，进程就越慢，所以应该不惜一切代价避免内存交换的发生。

 

ElasticSearch 的 memory_lock 属性允许 Elasticsearch 节点不交换内存。（注意只有Linux/Unix系统可设置。）这个属性可以在yml文件中设置。

```
# Step1. 修改 ${PATH_TO_ES_HOME}/config/elasticsearch.yml，添加：

bootstrap.memory_lock: true
```



### 增加文件描述符

单个用户可用的最大进程数量(软限制)&单个用户可用的最大进程数量(硬限制)，超过软限制会有警告，但是无法超过硬限制。 ElasticSearch 会使用大量的文件句柄，如果超过限制可能会造成宕机或者数据缺失。

 

文件描述符是用于跟踪打开“文件”的 Unix 结构体。在Unix中，一切都皆文件。 例如，“文件”可以是物理文件，虚拟文件（例如/proc/loadavg）或网络套接字。 ElasticSearch 需要大量的文件描述符（例如，每个 shard 由多个 segment 和其他文件组成，以及到其他节点的 socket 连接等）。

 

设置方法（假设是 hnivory用户启动的 ElasticSearch 进程）：

```
# Step1. 修改 /etc/security/limits.conf，添加：

Hnivory soft nofile 655360

Hnivory hard nofile 655360

# Step2. 确认是否生效

su – hnivory 

ulimit -n

# Step3. 通过 rest 确认是否生效

GET /_nodes/stats/process?filter_path=**.max_file_descriptors
```



### 保证足够的虚存

单进程最多可以占用的内存区域，默认为 65536。Elasticsearch 默认会使用 mmapfs 去存储 indices，默认的 65536 过少，会造成 OOM 异常。

 

设置方法：

```
# Step1. root 用户修改临时参数

sudo sysctl -w vm.max_map_count=655360

# Step2. 修改 /etc/sysctl.conf，在文末添加：

vm.max_map_count = 655360

# Step3. 确认是否生效

sudo sysctl vm.max_map_count
```



### 保证足够的线程

Elasticsearch 通过将请求分成几个阶段，并交给不同的线程池执行（Elasticsearch 中有各种不同的线程池执行器）。 因此，Elasticsearch 需要创建大量线程的能力。进程可创建线程的最大数量确保 Elasticsearch 进程有权在正常使用情况下创建足够的线程。 这可以通过 /etc/security/limits.conf 使用 nproc 设置来完成。

 

设置方法（假设是 admin 用户启动的 Elasticsearch 进程）：

```
# Step1. 修改 /etc/security/limits.d/90-nproc.conf，添加：

hnivory soft nproc 655360

hnivory hardnproc 655360

# Step1. 修改 /etc/security/limits.conf，添加：

hnivory soft nproc 655360

hnivory hardnproc 655360
```

 

 

### 暂时不建议使用G1GC

已知 JDK 8 附带的 HotSpot JVM 的早期版本在启用 G1GC 收集器时会导致索引损坏。受影响的版本是早于 JDK 8u40 附带的HotSpot 的版本，出于稳定性的考虑暂时不建议使用。

## 内存优化

ElasticSearch 自身对内存管理进行了大量优化，但对于持续增长的业务仍需进行一定程度的内存优化（而不是纯粹的添加节点和扩展物理内存），以防止 OOM 发生。ElasticSearch 使用的 JVM 堆中主要包括以下几类内存使用：

#### 减少 Segment Memory

##### 删除无用的历史索引

##### 关闭无需实时查询的历史索引，文件仍然存在于磁盘，只是释放掉内存，需要的时候可以重新打开

##### 定期对不再更新的索引做 force merge（会占用大量 IO，建议业务低峰期触发）

force merge 办法，使用 rest API：

```
# Step1. 在合并前需要对合并速度进行合理限制，默认是 20mb，SSD可以适当放宽到 80mb：

PUT /_cluster/settings -d '

{

  "persistent" : {

    "indices.store.throttle.max_bytes_per_sec" : "20mb"

  }

}'

 

# Step2. 强制合并 API，示例表示的是最终合并为一个 segment file：

# 对某个索引做合并

POST /${INDEX_NAME}/_forcemerge?max_num_segments=1

# 对某些索引做合并

POST /${INDEX_PATTERN}/_forcemerge?max_num_segments=1

 
```

