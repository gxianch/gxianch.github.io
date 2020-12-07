---
title: flink消费kafka写入es_demo
date: 2020-01-08 16:54:38
categories: [flink]
toc: true
tags:
  - 笔记

---

#### 建立kafka数据源
```java
Properties properties = new Properties();
properties.setProperty("bootstrap.servers", "192.168.199.101:9092");
properties.setProperty("group.id", "test");
FlinkKafkaConsumer<String> flinkKafkaConsumer = new FlinkKafkaConsumer<String>(TOPIC, new SimpleStringSchema(),properties);
//flinkKafkaConsumer.setStartFromLatest();
flinkKafkaConsumer.setStartFromEarliest();
DataStream<String> kafkaStream = env.addSource(flinkKafkaConsumer)
```
#### 写入es
```java
List<HttpHost> httpHosts = new ArrayList<>();
httpHosts.add(new HttpHost("192.168.199.101", 9200, "http"));
ElasticsearchSink.Builder<Map<String, Object>> esSinkBuilder = new ElasticsearchSink.Builder<>(httpHosts,new ElasticsearchSinkFunction<Map<String, Object>>() {
     private IndexRequest createIndexRequest(Map<String, Object> element){
         .....
         return Requests.indexRequest()
               .index(index)
               .type(type)
               .source(element);
         }
         @Override
         public void process(Map<String, Object> element, RuntimeContext ctx, RequestIndexer indexer) {
         indexer.add(createIndexRequest(element));
         }
   }
);
```
##### 设置批次数和刷新时间
```java
esSinkBuilder.setBulkFlushMaxActions(1000);
esSinkBuilder.setBulkFlushInterval(3000);
```
#### 设置http认证
```java
esSinkBuilder.setRestClientFactory(
                restClientBuilder -> {

                    restClientBuilder.setMaxRetryTimeoutMillis(60000);

                    restClientBuilder.setHttpClientConfigCallback(new ESHttpAuth());
                }
        );
public class ESHttpAuth implements RestClientBuilder.HttpClientConfigCallback {
    @Override
    public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpClientBuilder) {
        CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials("admin",
                "admin"));
        return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
    }
}
```