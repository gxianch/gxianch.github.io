### flume自定义开发入门
flume开发属于插件型开发，需要在已有架构上，利用继承抽象类或接口进行自定义开发
本周学习如何搭建flume自定义开发环境和部署，并编写flume开发demo，记录此实现过程和步骤，
为下一步熟悉flume和开发打下基础

#### 1. 新建maven项目flume-source-test  引入flume依赖

```
<dependency>
            <groupId>org.apache.flume</groupId>
            <artifactId>flume-ng-core</artifactId>
            <version>1.9.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.flume</groupId>
            <artifactId>flume-ng-configuration</artifactId>
            <version>1.9.0</version>
</dependency>
```

#### 2. 创建自定义MySource，继承AbstractSource抽象类，主要自定义实现方法为process方法

```
 package flume.plugin;

 import java.nio.charset.Charset;
 import java.util.HashMap;
 import java.util.Random;

 import org.apache.flume.Context;
 import org.apache.flume.EventDeliveryException;
 import org.apache.flume.PollableSource;
 import org.apache.flume.conf.Configurable;
 import org.apache.flume.event.EventBuilder;
 import org.apache.flume.source.AbstractSource;

 public class MySource extends AbstractSource implements Configurable, PollableSource {
     public long getBackOffSleepIncrement() {
         return 0;
     }
     public long getMaxBackOffSleepInterval() {
         return 0;
     }
     public Status process() throws EventDeliveryException {
         Random random = new Random();
         int randomNum = random.nextInt(100);
         String text = "Hello World :" + random.nextInt(100);  //实际需要传的内容
         HashMap<String, String> header = new HashMap<String, String>();
         header.put("id", Integer.toString(randomNum));   //将id--value放入到header中
         this.getChannelProcessor()
           .processEvent(EventBuilder.withBody(text, Charset.forName("UTF-8"), header)); //prcessEvent()将数据传上去
         return Status.READY;
     }
    
     public void configure(Context arg0) {
    
     }

 }
```

#### 3. 导出jar包

Export->JAR file->仅勾选flume-source-test 下src/main/java  设置jar包保存路径和包名C:\Users\Administrator\Desktop\flume-source-test.jar 点击 Finish

#### 4. 将flume-source-test.jar放入apache-flume-1.9.0-bin/lib下

#### 5. 配置conf/flume-conf.properties
```
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = flume.plugin.MySource
#a1.sources.r1.type = flume.plugin.MyFlumeSource
a1.sources.r1.intervalMillis= 50

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 6. 启动flume命令
```
bin/flume-ng  agent -name a1 –conf conf -f conf/flume-conf.properties  -Dflume.root.logger=INFO,console
```
#### 7. 显示运行结果
```
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=83} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 38       Hello World :8 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=26} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 32 30    Hello World :20 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=6} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 39 37    Hello World :97 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=50} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 34 38    Hello World :48 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=24} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 32 37    Hello World :27 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=59} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 37 37    Hello World :77 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=23} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 37 37    Hello World :77 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=52} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 37 37    Hello World :77 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=47} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 32 32    Hello World :22 }
20/01/07 16:47:23 INFO sink.LoggerSink: Event: { headers:{id=2} body: 48 65 6C 6C 6F 20 57 6F 72 6C 64 20 3A 38 30    Hello World :80 }
```