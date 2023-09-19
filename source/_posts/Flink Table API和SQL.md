---
title: Flink  Table API & SQL实现的三种方式demo
date: 2023-08-22 08:51:57
categories: [Flink]
tags:
  - 笔记
---

## Flink Table API & SQL实现

Apache Flink 有两种关系型 API 来做流批统一处理：Table API 和 SQL。Table API 是用于 Scala 和 Java 语言的查询API，它可以用一种非常直观的方式来组合使用选取、过滤、join 等关系型算子。Flink SQL 是基于 [Apache Calcite]来实现的标准 SQL。这两种 API 中的查询对于批（DataSet）和流（DataStream）的输入有相同的语义，也会产生同样的计算结果。

Table API 和 SQL 两种 API 是紧密集成的，以及 DataStream 和 DataSet API。你可以在这些 API 之间，以及一些基于这些 API 的库之间轻松的切换。



写入kafka的测试数据:

{"ip":"192.168.199.104","port":"1521","metric":20}
{"ip":"192.168.199.104","port":"1521","metric":21}
{"ip":"192.168.199.104","port":"1521","metric":22}
{"ip":"192.168.199.104","port":"1522","metric":23}
{"ip":"192.168.199.104","port":"1521","metric":24}
{"ip":"192.168.199.105","port":"1521","metric":25}
{"ip":"192.168.199.105","port":"1521","metric":26}
{"ip":"192.168.199.105","port":"1522","metric":27}

### Table API实现求平均值

创建表方式2种，由DataStream来创建表和DDL创建表

核心代码如下：

```
 StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        EnvironmentSettings settings =                    EnvironmentSettings.newInstance().inStreamingMode().useBlinkPlanner().build();
        StreamTableEnvironment  tEnv = StreamTableEnvironment.create(env, settings);
    	Properties sourceProperties = new Properties();
		sourceProperties.setProperty("bootstrap.servers", "ip:port");
		sourceProperties.setProperty("group.id", "test");
		FlinkKafkaConsumer<KafkaEntity> kafkaConsumer = new FlinkKafkaConsumer<KafkaEntity>(
				"flinksql", new KafkaEntityDeserializationSchema(), sourceProperties);
		kafkaConsumer.setStartFromEarliest();
		DataStream<KafkaEntity> kafkaStream = env.addSource(kafkaConsumer);
		
		//方式一  fromDataStream创建表
		 Table kafkaTable = tEnv.fromDataStream(kafkaStream,Expressions.$("ip"),Expressions.$("port"),Expressions.$("metric"));
	     Table tab1 = tEnv.sqlQuery("select ip, avg(metric) as avgvalue from "+kafkaTable+" group by ip");
		 tab1.execute().print();
		 
		 
		//方式二   createTemporaryView临时视图模式创建表
		 tEnv.createTemporaryView("flinksql", kafkaStream,Expressions.$("ip"),Expressions.$("port"),Expressions.$("metric"));
	     Table tab2 = tEnv.sqlQuery("select ip, avg(metric) as avgvalue from flinksql group by ip");
	     tab2.execute().print();
	     
	     //方式三  DDL语句创建表
	     tEnv.executeSql(ddl);
		 tEnv.sqlQuery(sql);
		
 		env.execute("sql");
```



### SQL实现求平均值

创建Flink Table的DDL语句：

```sql
CREATE TABLE flinksql (
	ip STRING,
	PORT STRING,
	metric BIGINT,
	proctime AS PROCTIME () 
	) WITH (
	'connector' = 'kafka',
	'topic' = 'flinksql',
	'properties.bootstrap.servers' = 'ip:port',
	'properties.group.id' = 'test1',
	'format' = 'json',
'scan.startup.mode' = 'earliest-offset' 
)
```
根据ip聚合查指标平均值的sql语句:
```sql
select ip,  avg(metric)  from flinksql GROUP BY ip
```
返回结果:
| ip              | avg(metric) |
| :-------------: | :-------: |
| 192.168.199.104 | 20        |
| 192.168.199.104 | 20        |
| 192.168.199.104 | 21        |
| 192.168.199.104 | 21        |
| **192.168.199.104** | **22**    |
| 192.168.199.105 | 25        |
| 192.168.199.105 | 25    |
| **192.168.199.105** | **26** |

Flink SQL从kafka中获取到新数据就会触发一次平均值计算，只有最后一个值是最终值（粗体标识）



根据ip，port 聚合查指标平均值的sql语句

```sql
select ip, port, avg(metric)  from flinksql GROUP BY ip,port 
```



| ip                  | port     | avg(metric) |
| ------------------- | -------- | ----------- |
| 192.168.199.104     | 1521     | 20          |
| 192.168.199.104     | 1521     | 20          |
| **192.168.199.104** | **1521** | **21**      |
| **192.168.199.105** | **1521** | **25**      |
| **192.168.199.105** | **1522** | **27**      |
| **192.168.199.104** | **1522** | **23**      |


