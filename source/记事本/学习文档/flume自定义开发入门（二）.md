### flume学习文档（二）
目前遇到的现场问题：当目录内文件数太多，而且文件写入速度很快，导致目录内某些文件不被消费

提出解决办法：设置maxBatchCount参数解决问题

|Property Name	      | Default	       | Description    |
| :-----------:       | -------------- | ------------------------------------------------------------ |
| maxBatchCount       | Long.MAX_VALUE | Controls the number of batches being read consecutively from the same file. If the source is tailing multiple files and one of them is written at a fast rate, it can prevent other files to be processed, because the busy file would be read in an endless loop. In this case lower this value. |

```
a1.sources.ri.maxBatchCount = 1000
```

本周熟悉采集客户端代码和流程，阅读代码熟悉自定义开发用到主要类和方法以及实现的主要功能。

参考内容：

flume源码：https://github.com/apache/flume

flume使用文档：https://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html

flume开发文档：https://flume.apache.org/releases/content/1.9.0/FlumeDeveloperGuide.html

#### 文件匹配类：com.snc.domp.agent.utils.TaildirMatcher

源码只支持监控一级目录下的文件，经过自定义添加getMatchingFilesNoCache2()方法，实现实现目录递归, 即可以同时监控子目录

#### 自定义拦截器类com.snc.domp.agent.interceptor.TaildirSourceInterceptor

根据需求，将数据分别发往不同的地方

intercept(Event event)方法：

拼接json字符串，判断当前采集对象的内容是否为空并且是否大于32kb，如果大于则把内容发到kafka

#### 入口类：com.snc.domp.agent.source.TaildirSource

TailDirSource继承了AbstractSource类，而AbstractSource类中channelProcessor属性负责将Source中的Event提交给Channel组件
TailDirSource类通过配置参数匹配日志文件，获取日志文件更新内容并且将已经读取的偏移量记录到特定的文件当中(position file)

configure()方法:
1.判断从配置文件加载的配置是否合法，其中包括了对filegroups,以及以filegroups为单位的文件路径是否存在等条件。
2.对batchSize,skipToEnd，writePosInterval，idleTimeout等变量进行初始化工作
batchSize定义了往Channel中发送Event的批量处理大小
skipToEnd定义了每次程序启动，对文件进行读取的时候，是否从文件尾部开始读取数据，或者从文件最开始读取。
writePosInterval，TaildirSource读取每个监控文件都在位置文件中记录监控文件的已经读取的偏移量，writePosInterval则是定义了更新位置文件的间隔。
idleTimeout日志文件在idleTimeout间隔时间，没有被修改，文件将被关闭

start()方法：
通过configure()初始化后的变量创建了ReliableTaildirEventReader对象，同时创建两个线程池idleFileChecker和positionWriter，分别用于监控日志文件和记录日志文件读取的偏移量。
idleFileChecker实现一个Runnable接口，遍历reader所有监控的文件，检查文件最后修改时间+idleTimeout是否小于当前时间，说明日志文件在idleTimeout时间内没有被修改，该文件将被关闭。

positionWriter主要作用是记录日志文件读取的偏移量，以json格式（"inode", inode, "pos", tf.getPos(), "file", tf.getPath()），其中inode是linux系统中特有属性，在适应其他系统（Windows等）日志采集时ReliableTaildirEventReader.getInode()方法需要修改(注意：在利用Linux系统上inode实现上，文件是通过inode记录日志读取偏移量。所以即使文件名改变了，也不影响日志读取，在我实现Window版本上，只采用了文件名对应日志读取偏移量，文件名改变影响日志读取)。pos则是记录的日志读取的偏移量，file记录了日志文件的路径

process()方法:
process方法记录了TailDirSource类中主要的逻辑，获取每个监控的日志文件，调用tailFileProcess获取每个日志文件的更新数据，并将每条记录转换为Event(具体细节要看ReliableTaildirEventReader的readEvents方法)

#### 读数据源类com.snc.domp.agent.event.ReliableTaildirEventReader2
构造ReliableTaildirEventReader对象的时候，首先会判断各种必须参数是否合法等，然后加载position file获取每个文件上次记录的日志文件读取的偏移量
loadPositionFile(String filePath) 不粘贴方法的具体代码，主要就是获取每个监控日志文件的读取偏移量
readEvents()的各个不同参数方法中，下面这个是最主要的，该方法获取当前日志文件的偏移量，调用TailFile.readEvents(numEvents, backoffWithoutNL, addByteOffset)方法将日志文件每行转换为Flume的消息对象Event，并循环将每个event添加header信息。

openFile(File file, Map<String, String> headers, long inode, long pos) 方法根据日志文件对象，headers，inode和偏移量pos创建一个TailFile对象

#### 采集信息类com.snc.domp.agent.utils.TailFile
TaildirSource通过TailFile类操作处理每个日志文件，包含了RandomAccessFile类，以及记录日志文件偏移量pos,最新更新时间lastUpdated等属性
RandomAccessFile完美的符合TaildirSource的应用场景，RandomAccessFile支持使用seek()方法随机访问文件，配合position file中记录的日志文件读取偏移量，能够轻松简单的seek到文件偏移量，然后向后读取日志内容，并重新将新的偏移量记录到position file中。

readEvent(boolean backoffWithoutNL, boolean addByteOffset)方法：
readEvent简单的理解就是将每行日志转为Event消息体，方法最终调用的是readFile()方法。

truncate(Event event) 方法：事件数据大小超过设定的值，将会被抛弃

#### 监控入口类com.snc.domp.agent.netty.LogAnalyzeAgent/LogAnalyzeServer

需实现MonitorService接口

采用jetty作为内置通讯工具，定时判断进程是否挂死，定时发送状态消息到kafka






