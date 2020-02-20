### flume自定义开发入门（三）
现场遇到的问题解决办法，当目录内文件数太多，而且文件写入速度很快，导致目录内某些文件不被消费，可以尝试设置maxBatchCount参数解决问题




|Property Name	      | Default	       | Description    |
| :-----------:       | -------------- | ------------------------------------------------------------ |
| maxBatchCount       | Long.MAX_VALUE | Controls the number of batches being read consecutively from the same file. If the source is tailing multiple files and one of them is written at a fast rate, it can prevent other files to be processed, because the busy file would be read in an endless loop. In this case lower this value. |

```
a1.sources.ri.maxBatchCount = 1000  //控制从同一文件连续读取的批次数
```



