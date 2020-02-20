ES命令
kibnan
http://10.159.114.5:9200
head
http://10.159.114.5:9201/?base_uri=http://10.159.114.6:9202&auth_user=admin&auth_password=shsnc2019flink

 查看kibana进程
 netstat -tunlp|grep 9203 
 tcp        0      0 10.159.114.183:9203     0.0.0.0:*               LISTEN      1576/bin/../node/bi 
 kill -9 1576
 nohup  bin/kibana &
 sql查询
curl -u admin:shsnc2019flink -X GET "10.159.114.214:9202/_sql?pretty" -H 'Content-Type: application/json' -d"select createTime,op_time from startnetworkfault-startnetworkfault-20191127 order by op_time desc limit 0,10"
select createTime,count(*) from markettask-crm_crontab-* where startLine = '20190916091301202000047' and  createTime between '2019-09-11 17:07:58.070' and '2019-09-18 17:07:58.070' group by date_histogram(field='createTime','interval'='1M')
 curl -u admin:shsnc2019flink -X GET "10.159.114.6:9202/_sql" -H 'Content-Type: application/json' -d"select * from centerweb-crm_web-20191114   ORDER by op_time desc  limit 0,10"
 手动插入数据
curl -u admin:shsnc2019flink  -H 'Content-Type:application/json' -XPOST 10.159.114.6:9202/yxd_rac-yxd_rac-20991127/yxd_rac-yxd_rac -d '{"createTime":"2019-05-21 09:55:18.000"}'
删除索引，支持模糊删除
curl -u admin:shsnc2019flink -XDELETE 10.154.147.202:9200/asiainfo-tracesql-20190901

统计节点数
curl -u admin:shsnc2019flink -XGET 10.159.102.43:9202/_cat/nodes  |wc-l
curl -u admin:shsnc2019flink -XGET 10.159.102.43:9202/_nodes/stats?pretty
统计新增或某一天数据
curl -u admin:shsnc2019 -s http://10.159.114.183:9201/_cat/indices?h=index,store.size\&bytes=kb  |grep $(date --date='1 days ago' +%Y%m%d)  | awk '{sum+=$2}END{printf("昨天新增据量（G）：%.2f\n",sum/1024/1024)}'
curl -u admin:shsnc2019 -s http://10.159.114.183:9201/_cat/indices/asiainfo*?h=index,store.size\&bytes=kb  |grep 20190916  | awk '{sum+=$2}END{printf("增据量（G）：%.2f\n",sum/1024/1024)}'
curl -k -u 'admin:admin!@#' http://10.159.114.182:9202/_cat/indices?h=index,store.size\&bytes=kb

curl -u admin:admin -s http://localhost:9201/_cat/indices?h=index,store.size\&bytes=kb  | awk '{sum+=$2}END{printf("量（G）：%.2f\n",sum/1024/1024)}'
查看索引分片
curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cat/shards/verifiedforword-verifiedforword-20191123
设置节点离开重新分配时间
  _all/_settings PUT
  {
  "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
  }
}
curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cat/nodes?v
查看该节点任务
  curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cat/tasks
  查看集群待处理任务
   curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cluster/pending_tasks
   curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cat/indices |grep yellow

    curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_cat/shards |grep UNASSIGNED
    最大文件描述符
    curl -u admin:shsnc2019flink -XGET http://10.159.114.214:9202/_nodes/stats/process?filter_path=**.max_file_descriptors |grep 

关闭开启监控
 curl -u admin:admin -XPUT http://192.168.199.101:9200/_cluster/settings   -H 'Content-Type: application/json' -d'{"persistent": {"xpack.monitoring.collection.enabled": false}}'
副本删除恢复:
1.curl -u admin:shsnc2019flink -XPUT http://10.159.114.214:9202/verifiedforword-verifiedforword-20191123/_settings -H 'Content-Type:application/json'  -d'{"index":{"number_of_replicas":0}}'		
2.curl -u admin:shsnc2019flink -XPOST http://10.159.114.214:9202/verifiedforword-verifiedforword-20191123/_forcemerge?max_num_segments=1
3.curl -u admin:shsnc2019flink -XPUT http://10.159.114.214:9202/verifiedforword-verifiedforword-20191123/_settings -H 'Content-Type:application/json'  -d'{"index":{"number_of_replicas":1}}'
模糊设置副本
curl -u admin:shsnc2019flink -XPUT http://10.159.114.214:9202/*-20191127/_settings -H 'Content-Type:application/json'  -d'{"index":{"number_of_replicas":0}}'	
#全部索引段合并成一个
curl -u admin:admin -XPOST http://localhost:9200/*/_forcemerge?max_num_segments=1

大索引
abilityfeedback-abilityfeedback  14G 4
audit_log-audit_log 54G 11
capopenplatform-capopen 160g 160/5=32  40
centerapp-app_erro   87g   20  
drecv-boss_drecv 11G  3
networkfault-networkfault 40  10
networkland-networkland 50 10
serviceopen-provisioning   15 5
yxd_rac-yxd_rac   12G  4


1.同步副本分片
curl -u admin:shsnc2019 -XPOST http://10.154.147.203:9200/_flush/synced
2.暂停分片分配
curl -u admin:shsnc2019  -XPUT http://10.154.147.203:9200/_cluster/settings  -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"none"}}'
3.关闭es kill espid 添加参数：node.max_local_storage_nodes: 2
4.启动es
5.分片分配
curl -u admin:shsnc2019  -XPUT http://10.154.147.203:9200/_cluster/settings  -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.enable":"all"}}'

curl -u admin:shsnc2019  -XDELETE http://10.154.147.203:9200/admin.*.2019061*?pretty' 


 curl -u admin:shsnc2019 -XGET http://10.154.147.203:9200/_cat/indices/asiainfo-bizlog-2014*?h=index 

curl -u admin:shsnc2019flink  -XDELETE http://10.159.102.43:9202/data_after-data_after-20191121


排除节点
curl -u admin:shsnc2019flink  -XPUT http://10.159.114.5:9202/_cluster/settings  -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._ip":"10.159.102.43"}}'
curl -u admin:shsnc2019flink  -XPUT http://10.159.114.5:9202/_cluster/settings  -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._ip":""}}'
停止排除节点
curl -u admin:shsnc2019  -XPUT http://10.154.147.203:9200/_cluster/settings  -H 'Content-Type: application/json' -d '{"transient":{"cluster.routing.allocation.exclude._name":""}}'

磁盘分片分配
curl -u admin:shsnc2019flink 10.159.114.6:9202/_cluster/allocation/explain?pretty


curl -u admin:admin 192.168.199.101:9200/_template/template_*
select topHits('size'=11) from shsnc-script_data-20191211  group by ip
select topHits('size'=100) from shsnc-script_data-20191211  group by ip , scriptId
curl -k -u admin:admin 192.168.199.101:9200/_template/template_*?pretty

7.执行sgadmin.sh
./sgadmin.sh -ts ../sgconfig/truststore.jks -tspass ba6f8fb3081380fc3ba3 -ks ../sgconfig/kirk-keystore.jks -kspass 079f95446ba0ea022598 -cd ../sgconfig/ -cn shsnccenter -nhnv -p 9301
强制执行
./sgadmin.sh -ts ../sgconfig/truststore.jks -tspass ba6f8fb3081380fc3ba3 -ks ../sgconfig/kirk-keystore.jks -kspass 079f95446ba0ea022598 -cd ../sgconfig/ -cn shsnccenter -nhnv -p 9301 --accept-red-cluster

10.154.147.203 elasticsearch-6.7.0/plugins/search-guard-6/tools 9200
./sgadmin.sh -ts ../sgconfig/truststore.jks -tspass ba6f8fb3081380fc3ba3 -ks ../sgconfig/kirk-keystore.jks -kspass 079f95446ba0ea022598 -cd ../sgconfig/ -cn shsnccenter -nhnv -p 9300 --accept-red-cluster

shsnces
./sgadmin.sh -ts ../sgconfig/truststore.jks -tspass ba6f8fb3081380fc3ba3 -ks ../sgconfig/kirk-keystore.jks -kspass 079f95446ba0ea022598 -cd ../sgconfig/ -cn shsnces -nhnv -p 9300


修改分片数
 修改mysql中es_lifecycle表的shards值
http://10.154.51.3:20500/domp/lifecycle/field2template?tablename=startnetworkfault-startnetworkfault    //更新单个表es模版
http://10.154.51.3:20500/domp/lifecycle/field2templateall     //更新全部模版
查看节点任务
  curl -u admin:shsnc2019flink http://10.159.114.214:9202/_tasks?nodes=esnode_10.159.102.45_9202
    curl -u admin:shsnc2019flink http://10.159.114.214:9202/_tasks?nodes=esnode_10.159.114.5_9202
  {"nodes":{"YKhjmEw6TP2d-VDCyxCvNA":{"name":"esnode_10.159.102.45_9202","transport_address":"10.159.102.45:9302","host":"10.159.102.45","ip":"10.159.102.45:9302","roles":["master","data","ingest"],"attributes":{"xpack.installed":"true"},"tasks":{"YKhjmEw6TP2d-VDCyxCvNA:90113856":{"node":"YKhjmEw6TP2d-VDCyxCvNA","id":90113856,"type":"netty","action":"cluster:monitor/tasks/lists[n]","start_time_in_millis":1574855877228,"running_time_in_nanos":541054,"cancellable":false,"parent_task_id":"3WMnBj-pTNegdkT19ao5nQ:11035209","headers":{}}}}}}[hnivory@data01 ~]
  取消任务
  curl -u admin:shsnc2019flink http://10.159.114.214:9202/_tasks/YKhjmEw6TP2d-VDCyxCvNA:90113856/_cancel
  取消节点任务和某类型的任务
_tasks/_cancel?nodes=esnode_10.159.102.43_9202,esnode_10.159.102.43_9203,esnode_10.159.102.43_9204,esnode_10.159.102.43_9205,&actions=cluster:*
_tasks/_cancel?nodes=esnode_10.159.102.45_9202&actions=cluster:*   POST
_tasks/_cancel?nodes=esnode_10.159.102.45_9202&actions=*reindex		 POST
 查看节点任务和某类型的任务
 _tasks?nodes=esnode_10.159.102.45_9202&actions=cluster:*  GET


安装分词插件
elasticsearch-6.7.0-9203/bin/elasticsearch-plugin install -b file:/ivory01/elasticsearch-analysis-ik-6.7.0.zip
dsl查询
curl -u admin:shsnc2019 -X POST 10.159.102.43:9205/_search?pretty  -H 'Content-Type:application/json'  -d'{"query": {"bool": {"must": [{"term": {"bizStatus.keyword": "1"}}]}},"from": 0,"size": 10}'
{"query": {"bool": {"must": [{"term": {"bizStatus.keyword": "1"}}]}},"from": 0,"size": 10}

head 亚信
http://10.159.114.182:9203/?base_uri=http://10.154.147.202:9200&auth_user=admin&auth_password=shsnc2019
kibana亚信
http://10.159.114.183:9203
curl -u asiainfo:asiainfo2019 -X POST 10.154.147.202:9200/asiainfo-bizlog-20190923/_search?pretty  -H 'Content-Type:application/json' -d'{"query": {"bool": {"should": [{"term": {"bizStatus.keyword": "1"}},{"term": {"bizStatus.keyword": "2"}}]}},"from": 0,"size": 1}'
#字符串转Int聚合
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "a2": {
      "sum": {
        "script": {
          "lang": "painless",
          "source": "Integer.parseInt(doc['counter'][0])"
        }
      }
    }
  }
}
#更改字段类型
asiainfo-ceshizhibiao-20190818/asiainfo-ceshizhibiao/_update_by_query?conflicts=proceed   POST
{
  "query": {
    "match_all": {}
  },
  "script": {
    "lang": "painless",
    "inline": "ctx._source.counter=Integer.parseInt(ctx._source.counter)"
  }
}