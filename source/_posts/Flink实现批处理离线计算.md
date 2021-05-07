---
title: Flink实现批处理离线计算
categories: [Flink]
tags:
  - 笔记
---

### 引言

笔者的项目，一直是用flink进行流处理，为什么这次写flink批处理离线计算呢，因为客户提的新需求更适合用批处理离线计算:

1. 客户不需要实时计算的结果，认为实时结果对他们没有参考意义

2. 客户要求计算一个月(或更长时间)内历史数据的基准值，认为一个月(或更长时间)历史数据得到的基准值才能更加准确的评估和预测未来的趋势

   

## 一 批处理介绍
大数据计算分为离线计算和实时计算，离线计算就是我们通常说的批计算，代表是Hadoop MapReduce、Hive等大数据技术，实时计算也被称作流计算，代表是Storm、Spark Streaming、Flink等大数据技术。
批处理在大数据世界有着悠久的历史。批处理主要操作大容量静态数据集，并在计算过程完成后返回结果。
批处理模式中使用的数据集通常符合下列特征
有界：批处理数据集代表数据的有限集合
持久：数据通常始终存储在某种类型的持久存储位置中
大量：海量数据集
批处理非常适合需要访问全套记录才能完成的计算工作。例如在计算总数和平均数时，必须将数据集作为一个整体加以处理，而不能将其视作多条记录的集合。这些操作要求在计算进行过程中数据维持自己的状态。
需要处理大量数据的任务通常最适合用批处理操作进行处理。无论直接从持久存储设备处理数据集，或首先将数据集载入内存，批处理系统在设计过程中就充分考虑了数据的量，可提供充足的处理资源。由于批处理在应对大量持久数据方面的表现极为出色，因此经常被用于对历史数据进行分析。

## 二 批处理vs流处理
相信各位已经看过诸多流计算优点的文章，流计算的优点笔者就略过, 下面说一下批处理相比流处理的优点。

### 1 批处理的吞吐量大、资源利用率高
由于批量和流式处理数据粒度不一样，批量每次处理一定大小的数据块（输入一般采用文件系统），一个任务处理完一个数据块之后，才将处理好的中间数据发送给下游。流式计算则是以单条记录为单位，任务在处理完一条记录之后，然后发送给下游进行处理。流式计算来一条记录就计算一次，计算量巨大，当不需要中间值的时候，这种计算属实浪费，因此**批处理的吞吐量更大、资源利用率更高、系统的开销更小**。

### 2 批处理容易实现精准计算
#### 2.1 流处理数据丢失和重复处理
精确一次(exactly once)是指数据处理没有数据丢失和重复处理的现象。
流处理的数据来源一般是消息队列，是无界的，数据是一条一条获取，在加载数据时可能会出现网络连接等问题，所以流处理需要解决数据丢失和重复处理的问题，实现精确一次(exactly once)的语义相对复杂，目前storm流框架目前不支持(exactly once)，spark为了支持(exactly once)引入预写日志(AWL)并且offset由Spark自身管理  ，flink为了支持(exactly once)引入快照(snapshot)机制， 虽然流处理能够解决数据丢失和重复计算问题，但需要引入各种机制，而这增加了系统消耗的资源。
**批处理的数据源是静态块，比如文件，hdfs文件，批处理一次性加载一批数据，基本不会出现数据丢失和重复计算的情况。**

#### 2.2 流处理水印(watermark)忽略数据
如果说流处理引入各种机制增加资源消耗可以解决数据丢失和重复处理问题，那么对于乱序数据流则存在忽略数据的可能。
流处理数据没有边界，需要窗口(window)的概念，根据窗口来汇总计算。窗口(window)类型有很多种, 滚动窗口(Tumbling Window)、滑动窗口(Sliding Window)和会话窗口(Session window)等，窗口(window)中需要定义时间，流处理中存在事件时间(event time)和处理时间(process time)。对于乱序的数据，为此又引入了水印(watermark)机制。具体概念读者自行查阅。
水印(watermark)有一个允许延时(allow lateness)的参数, 窗口(window)接收到水印(watermark)后，再等待一段时间才会关闭窗口，如果这段时间有些数据依然没有发送过来，那就只能忽略它们了。允许延时(allow lateness)参数设置的大，系统占用的资源就多，而且允许延时(allow lateness)的参数不能设置无限大，因此如果数据源异常乱序，流处理的窗口就等不到延时数据过来就进行汇总计算，导致延时数据未处理。
**批处理数据有界，所有的数据全部都会加载，不用考虑数据源的顺序，不会出现忽略数据的情况，也不需要窗口(window) ，时间，水印等机制。**

## 三 Flink实现批处理离线计算
通过上面简单的介绍和对比，发现客户的需求更适合用批处理离线计算，由于Flink是一个流处理框架， 可以处理有边界和无边界的数据流。无边界的数据就是流数据，有边界的数据就是批数据，因此Flink也是支持批处理的。所以笔者采用Flink进行批处理计算。
以下是核心代码:
flink执行环境：

```java
ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();
```

从kafka中获取DataSet数据源，DataSet表示批处理的数据 (流处理是DataStream)：

```java
	DataSet<ConsumerRecords<String, String>> recordsDataSetDataSet = env.createInput(KafkaInputFormat
				.buildKafkaInputFormat().setBootstrapServers(KafkaServers)
				.setGroupId(xx).setTopic(sourceTopic).finish());
```

继承GenericInputFormat类实现自定义获取kafka数据源 KafkaInputFormat：

```java
public class KafkaInputFormat  extends GenericInputFormat<ConsumerRecords<String, String>> {
	@Override
	public void open(GenericInputSplit split) throws IOException {
        consumer = new KafkaConsumer<String,String>(props);
        initPartionMap();
	}
	//获取kafka topic每个分区的偏移量,用做kafka消费结束的标识
	void initPartionMap(){
		Collection<PartitionInfo> partitionInfos = consumer.partitionsFor(topic);
        List<TopicPartition> tp =new ArrayList<TopicPartition>();
        partitionInfos.forEach(partitionInfo -> {
            tp.add(new TopicPartition(topic,partitionInfo.partition()));
            consumer.assign(tp);
            consumer.seekToEnd(tp);
            partionOffsetMap.put(partitionInfo.partition(),consumer.position(new TopicPartition(topic, partitionInfo.partition())));
            partionBooleanMap.put(partitionInfo.partition(), false);     
            //获取参数值后返回最初
            consumer.seekToBeginning(tp);
        });
	}
	//消费kafka是否结束
	@Override
	public boolean reachedEnd() throws IOException {
		return !partionBooleanMap.containsValue(false);
	}
	@Override
	public ConsumerRecords<String, String> nextRecord(ConsumerRecords<String, String> reuse) {
	   //从kafka中获取一批数据
		 final ConsumerRecords<String, String> records consumer.poll(Duration.ofMillis(pollTime));
        for (ConsumerRecord<String, String> record : records) {
        	Integer partion=record.partition();
        	Long offset=	record.offset();
        	//表示已有分区已经消费完
        	if(offset+1==partionOffsetMap.get(partion)) { 
        		partionBooleanMap.put(partion, true);
        	}
        }
		 return records;
	}

```

## 四 总结

以前的流处理计算过程经过批处理改造后，计算时间大大缩短，也不需要设置窗口(window)、等待时间(allow lateness)和水印(watermark)，而且计算完成程序自动退出，不再占用系统资源。
流处理和批处理都有各自的优缺点和应用场景，应该根据项目需求选择合适的。