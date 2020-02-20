nohup  java -Xms1g -Xmx1g   -XX:+HeapDumpOnOutOfMemoryError  -jar hbase.put-0.0.1-SNAPSHOT.jar 5 flumeToTopic latest kafka_group_2019 10.159.178.35:20310,10.159.178.36:20310,10.159.178.37:20310,10.159.178.38:20310,10.159.178.39:20310 &

Kafka/kafka/bin/kafka-topics.sh --list --zookeeper 10.154.147.165:24002/kafka

-Djava.security.auth.login.config=../conf/jaas.conf?-Djava.security.krb5.conf=../conf/krb5.conf
-Djava.security.auth.login.config=/app/hnivory/huawei_hbase_kafka/conf/jaas.conf -Djava.security.krb5.conf=/app/hnivory/huawei_hbase_kafka/conf/krb5.conf

java -jar hbase-huawei-maven20191125.jar -Djava.security.auth.login.config=/app/hnivory/hbase-huawei-maven/conf/jaas.conf -Djava.security.krb5.conf=/app/hnivory/hbase-huawei-maven/conf/krb5.conf -Dzookeeper.server.principal=zookeeper/hadoop.hadoop.com -Djavax.security.auth.useSubjectCredsOnly=false

{NAME => 'S', DATA_BLOCK_ENCODING => 'FAST_DIFF', TTL => '86400', DATA_BLOCK_ENCODING => 'FAST_DIFF', COMPRESSION => 'SNAPPY'} 

[hnivory@sncweb hadoopclient]$ source bigdata_env 
[hnivory@sncweb hadoopclient]$ kinit -kt /app/hnivory/hadoopclient/user.keytab xjuser
[hnivory@sncweb hadoopclient]$ hbase shell

create 'xjuser:shsnc_domp',{NAME => 'S', TTL=> 2592000,DATA_BLOCK_ENCODING => 'PREFIX'},{NUMREGIONS => 256, SPLITALGO=>'UniformSplit'}
scan 'xjuser:shsnc_domp',{LIMIT =>10}
scan 'xjuser:capopenplatform-capopen',{LIMIT =>10, 'TIMERANGE' => [1575362436939,1575362435939]}
scan 'xjuser:capopenplatform-capopen',{LIMIT =>2,STARTROW=> '1575352434939',STOPROW => '1575362435939'}

scan 'xjuser:capopenplatform-capopen',{LIMIT =>2,STARTROW=> '1575440262000'} 14
scan 'xjuser:capopenplatform-capopen',{LIMIT =>2,STARTROW=> '1575443862000'} 15
scan 'xjuser:capopenplatform-capopen',{LIMIT =>2,STARTROW=> '1575447462000'} 16

scan 'xjuser:centerapp-app_error',{LIMIT =>10, 'TIMERANGE' => [1575276035000,1575362435939]}
scan 'xjuser:centerapp-app_error',{LIMIT =>2,STARTROW=> '1575348035000',STOPROW => '1575362435939'}

nohup java -Xms1g -Xmx1g -XX:+HeapDumpOnOutOfMemoryError -jar hbase.put-0.0.1-SNAPSHOT.jar 32 flumeToTopic latest kafka_group_2019 10.159.178.35:20310,10.159.178.36:20310,10.159.178.37:20310,10.159.178.38:20310,10.159.178.39:20310 &
-Djava.security.auth.login.config=./config/kafka-jaas.conf -Djava.security.krb5.conf=./config/krb5.conf -Dzookeeper.server.principal=zookeeper/hadoop.hadoop.com -Djavax.security.auth.useSubjectCredsOnly=false 
disable 'xjuser:shsnc_domp'
drop 'xjuser:shsnc_domp'
create 'xjuser:shsnc_domp', { NAME => 'S', TTL => 2592000, DATA_BLOCK_ENCODING => 'PREFIX' }, {NUMREGIONS => 256, SPLITALGO => 'UniformSplit'}
前缀查询
scan 'xjuser:shsnc_domp' ,{LIMIT=>1, FILTER=> "PrefixFilter('179a5b6b5030')"}

1. LESS (<)
2. LESS_OR_EQUAL (<=)
3. EQUAL (=)
4. NOT_EQUAL (!=)
5. GREATER_OR_EQUAL (>=)
6. GREATER (>)
7. NO_OP (no operation)（不知道这个怎么用）
包含字符串
import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
import org.apache.hadoop.hbase.filter.RowFilter
scan 'xjuser:shsnc_domp', {FILTER => RowFilter.new(CompareFilter::CompareOp.valueOf('EQUAL'), SubstringComparator.new('92233704614153'))}

hbase shell脚本
scan 'xjuser:test_confpath','info:address',{'TIMERANGE' => [1574069509073,1574069509075]}

scan 'xjuser:test_confpath',{'TIMERANGE' => [1574161546017,1574161546019]}
get 'xjuser:test_confpath','info'

#查询时间范围内的数据
scan 'xjuser:test_confpath',{'TIMERANGE' => [1574161546016,1574161546019]}

#查询起始行数据
scan 'xjuser:test_confpath', {LIMIT => 10, STARTROW => '1574071036000'}

#存一个表,实现查询
scan 'xjuser:test_confpath', {LIMIT => 10, STARTROW => 'table_aa1574161564935',STOPROW  => 'table_aa1574162452938'}


#1574071036000   2019-11-18 17:57:16

 scan 'xjuser:test_confpath', {LIMIT => 10, STARTROW => 'table_aa1574071523332'}

  scan 'xjuser:test_confpath', {LIMIT => 10, STARTROW => 'table_dd1574071523332'}

 scan 'xjuser:test_confpath', {LIMIT => 10, STARTROW => '1574047755000'}.

scan 'xjuser:test_confpath',{STARTROW=>'1574047755000',STOPROW=>'1574071029148'}

hbase shell创表
create 'xjuser:TraceV2', { NAME => 'S', TTL => 432000, DATA_BLOCK_ENCODING => 'PREFIX' }, {NUMREGIONS => 256, SPLITALGO => 'UniformSplit'}
create 'xjuser:TraceV2', { NAME => 'S', TTL => 432000, DATA_BLOCK_ENCODING => 'FAST_DIFF' }
#进入hbase shell,删掉表
disable 'xjuser:shsnc_domp'
drop 'xjuser:shsnc_domp'
#hbase数据过期时间改为10s
create 'xjuser:shsnc_domp', { NAME => 'S', TTL => 10}
#检查数据是否有录入
scan 'xjuser:shsnc_domp'



HBase表和md5值
abilityfeedback-abilityfeedback:45c3f2de9ee2
aee-aee_server:179a5b6b5030
asiainfo-agentevent:e77783dbfe1e
asiainfo-agentinfo:44915dd77d28
asiainfo-agentstat:3493b0b4fb45
asiainfo-bizlog:683b8e34fdba
asiainfo-trace:1688f4d85de3
asiainfo-tracesql:023aed43a1f2
audit_log-audit_log:3717f8fc8db9
bigdata-universal:1ad5247605b3
boss-bill_details:ea700c8265e1
boss_adg-crs_alert:8cd79430d1fe
boss_adg-db_alert:c2a72e8d5860
boss_bes-boss_bes:577d49ba5dd6
boss_hostlog-boss_hostlog_oracle_hostlog:a15609a55fe2
boss_oracle-crs_alert:0e0f7b25f96a
boss_oracle-db_alert:2ae426aed5e0
capopenplatform-capopen:c5bf292de0f2
centerapp-app_error:210c4beb11c8
centerweb-crm_web:f1f0441f9855
channel-crm_channel:f66a3bb2a18a
crm-gateway:99cff98662dd
crm-market_back:8267affbd656
crm-market_front:640d8a9b3378
crm-qudaoyunying:c7e70c64616b
crm_adg-crs_alert:9932bc84aa58
crm_adg-db_alert:aa76b7f73a88
crm_bes-crm_bes:d2801ba0c4f9
crm_hostlog-crm_hostlog_oracle_hostlog:f5a8416f2ee9
crm_oracle-asm_alert:c248e2ffd0ac
crm_oracle-crs_alert:df19e6403ab3
crm_oracle-db_alert:994dcb83d6eb
data_after-data_after:f66812a61998
data_front-data_front:26182095ce2e
dianqu-net_request_log:4e9dc88379c1
dianqu-self_service_terminal_log:7c28e5c63346
dianqu-wap_request_log:bcb04d717582
dq_adg-crs_alert:6f7315701dab
dq_adg-db_alert:fa3802764a05
dq_bes-dq_bes:f2d53d3790eb
dq_hostlog-dq_hostlog_oracle_hostlog:4deb61f50c80
dq_oracle-crs_alert:823fd3931c80
dq_oracle-db_alert:bee0908be230
drecv-boss_drecv:8d2d228f5c98
elecinvoiceinit-elecinvoiceinit:86c2ef3a011c
elecinvoiceland-elecinvoiceland:3f2adc27b0f4
exadata_oracle-asm_alert:749d5a81013e
exadata_oracle-crs_alert:d7001c1d1f0e
exadata_oracle-db_alert:be6df2923930
flume_log-flume_log:aca45019285a
history_hostlog-history_hostlog_aix_messages:a58db0ba33d5
history_hostlog-history_hostlog_oracle_hostlog:d87b0b3ddb2c
history_oracle-asm_alert:45091bbb650e
history_oracle-crs_alert:124eb4947102
history_oracle-history_oracle_db:3e0400b7b622
iboss_bes-iboss_bes:06469c14bc60
iboss_hostlog-iboss_hostlog_aix_messages:42b9bd8776a5
iboss_hostlog-iboss_hostlog_oracle_hostlog:431d07a4ab86
icnd-icnd_firewall:c65a313fece8
icnd-icnd_ips:a80608d2f108
icnd-icnd_load_balance:d86018a44617
icnd-icnd_switch:30672dafa6dd
jf_db2-dbdiag:cb94f435a7be
jf_hostlog-jf_hostlog_oracle_hostlog:33f1ad8b0ac5
jf_oracle-crs_alert:44045ba4c144
jf_oracle-db_alert:853082ca7e75
jk_rac-jk_rac:08044f212a3f
markettask-crm_crontab:05eb86e3ce42
markettask-crm_markettask_after:8f68aa3ca875
markettask-crm_markettask_front:152871e5c0aa
networkfault-networkfault:7561d26b51f6
networkinit-networkinit:4dce455709d6
networkland-networkland:2494b48aa7cc
other_hostlog-other_hostlog_oracle_hostlog:3899d6217f4f
other_oracle-crs_alert:8ea0d2823eb9
other_oracle-db_alert:f9b7e6116d8c
portal-crm_portal:edf4097b8dab
portal_app-portal_app:0c282867b711
reward-receptionapp:48f1a2e70103
reward-reward_engine:f622bf038162
serviceopen-provisioning:a66926a3791c
shsnc-appcenterrelationship:8c09321ee597
shsnc-apprelationship:4b96a7e761eb
shsnc-logcentername:fd94d53d0324
shsnc-logmodename:85057bea2650
shsnc-lognodehostcount:ecbdecc94be5
shsnc-snc_redis_rule_data:3fd3b5c9fefa
shsnc-snc_redis_rule_warn_data:1539aaa630f5
shsnc-yewulian:421fe9f373c4
startnetworkfault-startnetworkfault:ac815991d083
startnetworkinit-startnetworkinit:37c399f36294
startnetworkland-startnetworkland:358e6bb02d56
suyan-suyan_log:399d32fa1098
task-crm_task:e0c4c10c2511
telcrm-telcrm:33b3cd6b0b60
telpayfault-telpayfault:9fc879c8603e
telpayland-telpayland:36f073920ed9
timer-timer_machine:5434c180b8c8
universal-universal_data:df6558976d98
verifiedforword-verifiedforword:c558687d009e
yxd_rac-yxd_rac:18834cf4ab54