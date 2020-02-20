##### 原dompserver启动

/home/dx/server1/domp-server/bin/jdk/bin/java -Xms8000m -Xmx8000m -Dflume.monitoring.type=com.snc.domp.agent.netty.LogAnalyzeServer -Dflume.monitoring.nettyPort=3031 -Dflume.monitoring.port=3035 -Dflume.monitoring.zkUrl=192.168.199.101:9092 -Dflume.monitoring.time=60000 -Dflume.root.logger=ERROR,console -cp /home/dx/server1/domp-server/conf:/home/dx/server1/domp-server/lib/*:/lib/* -Djava.library.path= org.apache.flume.node.Application -f /home/dx/server1/domp-server/conf/server.conf -name dompserver

##### 测试dompagent启动101

/home/ivory/domp-agent/bin/jdk/bin/java -Xms256m -Xmx256m -Dflume.monitoring.type=com.snc.domp.agent.netty.LogAnalyzeAgent  -Dflume.monitoring.nettyPort=3031 -Dflume.monitoring.port=3035 -Dflume.monitoring.kafkaAddress=192.168.199.101:9092 -Dflume.monitoring.time=60000 -Dflume.monitoring.topic=memory_status -Dflume.monitoring.serverIp=192.168.199.101 -Dflume.monitoring.maxCpu=90 -Dflume.monitoring.filePath=/data/ -Dflume.monitoring.maxInstallSpace=95 -Dflume.root.logger=INFO,console -cp /home/ivory/domp-agent/conf:/home/ivory/domp-agent/lib/*:/lib/* -Djava.library.path= org.apache.flume.node.Application -f /home/ivory/domp-agent/conf/agent.conf -name dompagent

#### domp-server配置

[root@bigdata-01 domp-server]# cat conf/server.conf 
dompserver.sources = r1 
dompserver.sinks = k1 
dompserver.channels = c1 

dompserver.sources.r1.type = avro 
dompserver.sources.r1.bind = 192.168.199.100 
dompserver.sources.r1.port = 4545 

dompserver.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink 
dompserver.sinks.k1.kafka.topic = flumeToTopic_dev 
dompserver.sinks.k1.kafka.bootstrap.servers = 192.168.199.101:8888 
dompserver.sinks.k1.kafka.flumeBatchSize = 100 
dompserver.sinks.k1.kafka.producer.acks = 1 
dompserver.sinks.k1.kafka.producer.linger.ms = 1 
dompserver.sinks.k1.kafka.producer.compression.type = snappy 
dompserver.sinks.k1.kafka.producer.max.request.size = 10240000 

dompserver.channels.c1.type = file 
dompserver.channels.c1.checkpointDir = /home/dx/server1/domp-server/checkpoint 
dompserver.channels.c1.dataDirs = /home/dx/server1/domp-server/data 

dompserver.channels.c1.useDualCheckpoints=true 
dompserver.channels.c1.backupCheckpointDir=/home/dx/server1/domp-server/backupCheckpointDir  

dompserver.channels.c1.transactionCapacity=100000 

dompserver.sources.r1.channels = c1 
dompserver.sinks.k1.channel = c1



#### agent.conf

[root@bigdata-02 domp-agent]# cat conf/agent.conf
dompagent.sources = r0 r1 r2 r3  
dompagent.channels = c1 
dompagent.sinks = k0 k1 k2 k3 k4  


dompagent.channels.c1.type = memory 
dompagent.channels.c1.capacity = 1000 
dompagent.channels.c1.transactionCapacity = 100 


dompagent.sinks.k0.type = logger
dompagent.sinks.k1.type = logger
dompagent.sinks.k2.type = logger
dompagent.sinks.k3.type = logger
dompagent.sinks.k4.type = logger
#dompagent.sinks.k0.type = file_roll
#dompagent.sinks.k1.type = file_roll
#dompagent.sinks.k2.type = file_roll
#dompagent.sinks.k3.type = file_roll
#dompagent.sinks.k4.type = file_roll
#dompagent.sinks.k0.sink.directory = /home/ivory/sink
#dompagent.sinks.k1.sink.directory = /home/ivory/sink
#dompagent.sinks.k2.sink.directory = /home/ivory/sink
#dompagent.sinks.k3.sink.directory = /home/ivory/sink
#dompagent.sinks.k4.sink.directory = /home/ivory/sink
dompagent.sinks.k0.channel = c1 
dompagent.sinks.k1.channel = c1
dompagent.sinks.k2.channel = c1
dompagent.sinks.k3.channel = c1
dompagent.sinks.k4.channel = c1

#dompagent.sinks.k0.type = avro 
#dompagent.sinks.k0.hostname = 192.168.199.103 
#dompagent.sinks.k0.port = 4545 
#dompagent.sinks.k0.channel = c1 
#dompagent.sinks.k0.batch-size = 100 

#dompagent.sinks.k1.type = avro 
#dompagent.sinks.k1.hostname = 192.168.199.102 
#dompagent.sinks.k1.port = 4545 
#dompagent.sinks.k1.channel = c1 
#dompagent.sinks.k1.batch-size = 100 

#dompagent.sinks.k2.type = avro 
#dompagent.sinks.k2.hostname = 192.168.199.102 
#dompagent.sinks.k2.port = 4747 
#dompagent.sinks.k2.channel = c1 
#dompagent.sinks.k2.batch-size = 100 

#dompagent.sinks.k3.type = avro 
#dompagent.sinks.k3.hostname = 192.168.199.100 
#dompagent.sinks.k3.port = 4545 
#dompagent.sinks.k3.channel = c1 
#dompagent.sinks.k3.batch-size = 100 

#dompagent.sinks.k4.type = avro 
#dompagent.sinks.k4.hostname = 192.168.199.102 
#dompagent.sinks.k4.port = 4646 
#dompagent.sinks.k4.channel = c1 
#dompagent.sinks.k4.batch-size = 100 

 


dompagent.sinkgroups = g1 
dompagent.sinkgroups.g1.sinks =k0 k1 k2 k3 k4  

dompagent.sinkgroups.g1.processor.type = load_balance 
dompagent.sinkgroups.g1.processor.selector = round_robin 


dompagent.sinkgroups.g1.processor.backoff = true 
dompagent.sinkgroups.g1.processor.selector.maxTimeOut = 10000 

 

 

 


dompagent.sources.r0.type = com.snc.domp.agent.source.TaildirSource 
dompagent.sources.r0.positionFile = ./taildir_position_r0.json 
dompagent.sources.r0.filegroups = f1 
dompagent.sources.r0.filegroups.f1 = /home/logs/status-sink.log 
dompagent.sources.r0.batchSize = 100 
dompagent.sources.r0.backoffSleepIncrement  = 1000 
dompagent.sources.r0.maxBackoffSleep  = 5000 
dompagent.sources.r0.recursiveDirectorySearch = true 
dompagent.sources.r0.fileHeader = true 
dompagent.sources.r0.cachePatternMatchingDuration = 10000 
dompagent.sources.r0.fileTimePath =  
dompagent.sources.r0.readDataTime =  

dompagent.sources.r0.multiline = false 
dompagent.sources.r0.multilinePattern =   
dompagent.sources.r0.multilinePatternBelong = previous 
dompagent.sources.r0.multilineMatched = false 
dompagent.sources.r0.multilineEventTimeoutSeconds = 1  
dompagent.sources.r0.multilineMaxBytes = 3145728 
dompagent.sources.r0.multilineMaxLines = 2000 
dompagent.sources.r0.acquisitionCycle = 1 

dompagent.sources.r0.serverIp= 192.168.199.101 
dompagent.sources.r0.kafkaAddress= 192.168.199.101:9092 
dompagent.sources.r0.kafkaTopic= flumeErrorTopic 

dompagent.sources.r0.interceptors=i1 i2 i3 i4 i5   i8 i9 i10 i11 i12 
dompagent.sources.r0.interceptors.i1.type=host 
dompagent.sources.r0.interceptors.i1.useIP=true 
dompagent.sources.r0.interceptors.i1.preserverExisting=false 

dompagent.sources.r0.interceptors.i2.type=static 
dompagent.sources.r0.interceptors.i2.key=tablename 
dompagent.sources.r0.interceptors.i2.value=asiainfo-test_tablename_cn 
dompagent.sources.r0.interceptors.i2.preserverExisting=false 

dompagent.sources.r0.interceptors.i3.type=static 
dompagent.sources.r0.interceptors.i3.key=job_paras 
dompagent.sources.r0.interceptors.i3.value={"es":true,"redis":false} 
dompagent.sources.r0.interceptors.i3.preserverExisting=false 

 

dompagent.sources.r0.interceptors.i4.type=static  
dompagent.sources.r0.interceptors.i4.key=charsetStr  
dompagent.sources.r0.interceptors.i4.value= UTF-8 
dompagent.sources.r0.interceptors.i4.preserverExisting=false 


dompagent.sources.r0.interceptors.i5.type=static 
dompagent.sources.r0.interceptors.i5.key=period_type 
dompagent.sources.r0.interceptors.i5.value=1 
dompagent.sources.r0.interceptors.i5.preserverExisting=false 

dompagent.sources.r0.interceptors.i6.type=static 
dompagent.sources.r0.interceptors.i6.key=slicingType 
dompagent.sources.r0.interceptors.i6.value= 
dompagent.sources.r0.interceptors.i6.preserverExisting=false 


dompagent.sources.r0.interceptors.i7.type=static  
dompagent.sources.r0.interceptors.i7.key=PatternStr  
dompagent.sources.r0.interceptors.i7.value= 
dompagent.sources.r0.interceptors.i7.preserverExisting=false 

dompagent.sources.r0.interceptors.i8.type=static  
dompagent.sources.r0.interceptors.i8.key=ipStr 
dompagent.sources.r0.interceptors.i8.value=192.168.199.101 
dompagent.sources.r0.interceptors.i8.preserverExisting=false 

dompagent.sources.r0.interceptors.i9.type=static  
dompagent.sources.r0.interceptors.i9.key=kafkaStr 
dompagent.sources.r0.interceptors.i9.value=192.168.199.101:9092 
dompagent.sources.r0.interceptors.i9.preserverExisting=false 

dompagent.sources.r0.interceptors.i10.type=static  
dompagent.sources.r0.interceptors.i10.key=kafkaTopic 
dompagent.sources.r0.interceptors.i10.value=bigDataTopic 
dompagent.sources.r0.interceptors.i10.preserverExisting=false 

dompagent.sources.r0.interceptors.i11.type=static  
dompagent.sources.r0.interceptors.i11.key=kafkaTopicStr 
dompagent.sources.r0.interceptors.i11.value=0 
dompagent.sources.r0.interceptors.i11.preserverExisting=false 

dompagent.sources.r0.interceptors.i12.type = com.snc.domp.agent.interceptor.TaildirSourceBuilder 

dompagent.sources.r0.channels = c1 


dompagent.sources.r1.type = com.snc.domp.agent.source.TaildirSource 
dompagent.sources.r1.positionFile = ./taildir_position_r1.json 
dompagent.sources.r1.filegroups = f1 
dompagent.sources.r1.filegroups.f1 = /home/tomcat-vcompass-dev/logs/catalina.out 
dompagent.sources.r1.batchSize = 100 
dompagent.sources.r1.backoffSleepIncrement  = 1000 
dompagent.sources.r1.maxBackoffSleep  = 5000 
dompagent.sources.r1.recursiveDirectorySearch = true 
dompagent.sources.r1.fileHeader = true 
dompagent.sources.r1.cachePatternMatchingDuration = 10000 
dompagent.sources.r1.fileTimePath =  
dompagent.sources.r1.readDataTime = 5 

dompagent.sources.r1.multiline = false 
dompagent.sources.r1.multilinePattern =   
dompagent.sources.r1.multilinePatternBelong = previous 
dompagent.sources.r1.multilineMatched = false 
dompagent.sources.r1.multilineEventTimeoutSeconds = 1  
dompagent.sources.r1.multilineMaxBytes = 3145728 
dompagent.sources.r1.multilineMaxLines = 2000 
dompagent.sources.r1.acquisitionCycle = 1 

dompagent.sources.r1.serverIp= 192.168.199.101 
dompagent.sources.r1.kafkaAddress= 192.168.199.101:9092 
dompagent.sources.r1.kafkaTopic= flumeErrorTopic 

dompagent.sources.r1.interceptors=i1 i2 i3 i4 i5   i8 i9 i10 i11 i12 
dompagent.sources.r1.interceptors.i1.type=host 
dompagent.sources.r1.interceptors.i1.useIP=true 
dompagent.sources.r1.interceptors.i1.preserverExisting=false 

dompagent.sources.r1.interceptors.i2.type=static 
dompagent.sources.r1.interceptors.i2.key=tablename 
dompagent.sources.r1.interceptors.i2.value=asiainfo-test_tablename_cn 
dompagent.sources.r1.interceptors.i2.preserverExisting=false 

dompagent.sources.r1.interceptors.i3.type=static 
dompagent.sources.r1.interceptors.i3.key=job_paras 
dompagent.sources.r1.interceptors.i3.value={"es":true,"hbase":false,"redis":false} 
dompagent.sources.r1.interceptors.i3.preserverExisting=false 

 

dompagent.sources.r1.interceptors.i4.type=static  
dompagent.sources.r1.interceptors.i4.key=charsetStr  
dompagent.sources.r1.interceptors.i4.value= UTF-8 
dompagent.sources.r1.interceptors.i4.preserverExisting=false 


dompagent.sources.r1.interceptors.i5.type=static 
dompagent.sources.r1.interceptors.i5.key=period_type 
dompagent.sources.r1.interceptors.i5.value=1 
dompagent.sources.r1.interceptors.i5.preserverExisting=false 

dompagent.sources.r1.interceptors.i6.type=static 
dompagent.sources.r1.interceptors.i6.key=slicingType 
dompagent.sources.r1.interceptors.i6.value= 
dompagent.sources.r1.interceptors.i6.preserverExisting=false 


dompagent.sources.r1.interceptors.i7.type=static  
dompagent.sources.r1.interceptors.i7.key=PatternStr  
dompagent.sources.r1.interceptors.i7.value= 
dompagent.sources.r1.interceptors.i7.preserverExisting=false 

dompagent.sources.r1.interceptors.i8.type=static  
dompagent.sources.r1.interceptors.i8.key=ipStr 
dompagent.sources.r1.interceptors.i8.value=192.168.199.101 
dompagent.sources.r1.interceptors.i8.preserverExisting=false 

dompagent.sources.r1.interceptors.i9.type=static  
dompagent.sources.r1.interceptors.i9.key=kafkaStr 
dompagent.sources.r1.interceptors.i9.value=192.168.199.101:9092 
dompagent.sources.r1.interceptors.i9.preserverExisting=false 

dompagent.sources.r1.interceptors.i10.type=static  
dompagent.sources.r1.interceptors.i10.key=kafkaTopic 
dompagent.sources.r1.interceptors.i10.value=bigDataTopic 
dompagent.sources.r1.interceptors.i10.preserverExisting=false 

dompagent.sources.r1.interceptors.i11.type=static  
dompagent.sources.r1.interceptors.i11.key=kafkaTopicStr 
dompagent.sources.r1.interceptors.i11.value=0 
dompagent.sources.r1.interceptors.i11.preserverExisting=false 

dompagent.sources.r1.interceptors.i12.type = com.snc.domp.agent.interceptor.TaildirSourceBuilder 

dompagent.sources.r1.channels = c1 


dompagent.sources.r2.type = syslogudp 
dompagent.sources.r2.port = 1234 
dompagent.sources.r2.host = 192.168.199.101 
dompagent.sources.r2.keepFields = true 
dompagent.sources.r2.interceptors=i2 i3 i4 i5  i8 i9 i10 i11 i12 

 

dompagent.sources.r2.interceptors.i2.type=static 
dompagent.sources.r2.interceptors.i2.key=tablename 
dompagent.sources.r2.interceptors.i2.value=asiainfo-test_tablename_cn 
dompagent.sources.r2.interceptors.i2.preserverExisting=false 

dompagent.sources.r2.interceptors.i3.type=static 
dompagent.sources.r2.interceptors.i3.key=job_paras 
dompagent.sources.r2.interceptors.i3.value={"es":true,"redis":false} 
dompagent.sources.r2.interceptors.i3.preserverExisting=false 

 

dompagent.sources.r2.interceptors.i4.type=static  
dompagent.sources.r2.interceptors.i4.key=charsetStr  
dompagent.sources.r2.interceptors.i4.value= UTF-8 
dompagent.sources.r2.interceptors.i4.preserverExisting=false 


dompagent.sources.r2.interceptors.i5.type=static 
dompagent.sources.r2.interceptors.i5.key=period_type 
dompagent.sources.r2.interceptors.i5.value=1 
dompagent.sources.r2.interceptors.i5.preserverExisting=false 

dompagent.sources.r2.interceptors.i6.type=static 
dompagent.sources.r2.interceptors.i6.key=slicingType 
dompagent.sources.r2.interceptors.i6.value= 
dompagent.sources.r2.interceptors.i6.preserverExisting=false 

 

dompagent.sources.r2.interceptors.i8.type=static  
dompagent.sources.r2.interceptors.i8.key=ipStr 
dompagent.sources.r2.interceptors.i8.value=192.168.199.101 
dompagent.sources.r2.interceptors.i8.preserverExisting=false 

dompagent.sources.r2.interceptors.i9.type=static  
dompagent.sources.r2.interceptors.i9.key=kafkaStr 
dompagent.sources.r2.interceptors.i9.value=192.168.199.101:9092 
dompagent.sources.r2.interceptors.i9.preserverExisting=false 

dompagent.sources.r2.interceptors.i10.type=static  
dompagent.sources.r2.interceptors.i10.key=kafkaTopic 
dompagent.sources.r2.interceptors.i10.value=bigDataTopic 
dompagent.sources.r2.interceptors.i10.preserverExisting=false 

dompagent.sources.r2.interceptors.i11.type=static  
dompagent.sources.r2.interceptors.i11.key=kafkaTopicStr 
dompagent.sources.r2.interceptors.i11.value=0 
dompagent.sources.r2.interceptors.i11.preserverExisting=false 

dompagent.sources.r2.interceptors.i12.type = com.snc.domp.agent.interceptor.SyslogSourceBuilder 

dompagent.sources.r2.channels = c1 


dompagent.sources.r3.type = com.snc.domp.agent.source.TaildirSource 
dompagent.sources.r3.positionFile = ./taildir_position_r3.json 
dompagent.sources.r3.filegroups = f1 
dompagent.sources.r3.filegroups.f1 = /data/domp-agent/logs/flume.log 
dompagent.sources.r3.batchSize = 100 
dompagent.sources.r3.backoffSleepIncrement  = 1000 
dompagent.sources.r3.maxBackoffSleep  = 5000 
dompagent.sources.r3.recursiveDirectorySearch = true 
dompagent.sources.r3.fileHeader = true 
dompagent.sources.r3.cachePatternMatchingDuration = 10000 
dompagent.sources.r3.fileTimePath =  
dompagent.sources.r3.readDataTime =  

dompagent.sources.r3.multiline = false 
dompagent.sources.r3.multilinePattern =   
dompagent.sources.r3.multilinePatternBelong = previous 
dompagent.sources.r3.multilineMatched = false 
dompagent.sources.r3.multilineEventTimeoutSeconds = 1  
dompagent.sources.r3.multilineMaxBytes = 3145728 
dompagent.sources.r3.multilineMaxLines = 2000 
dompagent.sources.r3.acquisitionCycle = 1 

dompagent.sources.r3.serverIp= 192.168.199.101 
dompagent.sources.r3.kafkaAddress= 192.168.199.101:9092 
dompagent.sources.r3.kafkaTopic= flumeErrorTopic 

dompagent.sources.r3.interceptors=i1 i2 i3 i4 i5   i8 i9 i10 i11 i12 
dompagent.sources.r3.interceptors.i1.type=host 
dompagent.sources.r3.interceptors.i1.useIP=true 
dompagent.sources.r3.interceptors.i1.preserverExisting=false 

dompagent.sources.r3.interceptors.i2.type=static 
dompagent.sources.r3.interceptors.i2.key=tablename 
dompagent.sources.r3.interceptors.i2.value=other_hostlog-flume_log 
dompagent.sources.r3.interceptors.i2.preserverExisting=false 

dompagent.sources.r3.interceptors.i3.type=static 
dompagent.sources.r3.interceptors.i3.key=job_paras 
dompagent.sources.r3.interceptors.i3.value={"es":true,"redis":false,"hbase":false} 
dompagent.sources.r3.interceptors.i3.preserverExisting=false 

 

dompagent.sources.r3.interceptors.i4.type=static  
dompagent.sources.r3.interceptors.i4.key=charsetStr  
dompagent.sources.r3.interceptors.i4.value= UTF-8 
dompagent.sources.r3.interceptors.i4.preserverExisting=false 


dompagent.sources.r3.interceptors.i5.type=static 
dompagent.sources.r3.interceptors.i5.key=period_type 
dompagent.sources.r3.interceptors.i5.value=1 
dompagent.sources.r3.interceptors.i5.preserverExisting=false 

dompagent.sources.r3.interceptors.i6.type=static 
dompagent.sources.r3.interceptors.i6.key=slicingType 
dompagent.sources.r3.interceptors.i6.value= 
dompagent.sources.r3.interceptors.i6.preserverExisting=false 


dompagent.sources.r3.interceptors.i7.type=static  
dompagent.sources.r3.interceptors.i7.key=PatternStr  
dompagent.sources.r3.interceptors.i7.value= 
dompagent.sources.r3.interceptors.i7.preserverExisting=false 

dompagent.sources.r3.interceptors.i8.type=static  
dompagent.sources.r3.interceptors.i8.key=ipStr 
dompagent.sources.r3.interceptors.i8.value=192.168.199.101 
dompagent.sources.r3.interceptors.i8.preserverExisting=false 

dompagent.sources.r3.interceptors.i9.type=static  
dompagent.sources.r3.interceptors.i9.key=kafkaStr 
dompagent.sources.r3.interceptors.i9.value=192.168.199.101:9092 
dompagent.sources.r3.interceptors.i9.preserverExisting=false 

dompagent.sources.r3.interceptors.i10.type=static  
dompagent.sources.r3.interceptors.i10.key=kafkaTopic 
dompagent.sources.r3.interceptors.i10.value=bigDataTopic 
dompagent.sources.r3.interceptors.i10.preserverExisting=false 

dompagent.sources.r3.interceptors.i11.type=static  
dompagent.sources.r3.interceptors.i11.key=kafkaTopicStr 
dompagent.sources.r3.interceptors.i11.value=0 
dompagent.sources.r3.interceptors.i11.preserverExisting=false 

dompagent.sources.r3.interceptors.i12.type = com.snc.domp.agent.interceptor.TaildirSourceBuilder 

dompagent.sources.r3.channels = c1