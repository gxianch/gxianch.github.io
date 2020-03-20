---
title: Oracle数据泵使用
date: 2020-03-20 08:46:01
categories: [oracle]
toc: true
mathjax: true
top: 98
tags:
  - datapump
  - 数据泵
---

数据泵的全表，条件及分区导出导入执行pl/sql, 并且实现重新映射用户和表

<!-- more -->

##### 官方文档

https://docs.oracle.com/cd/E11882_01/server.112/e22490/dp_overview.htm#SUTIL802

#### 创建测试单表T
```
create table USER01.T as select * from all_objects;
```
#### 创建测试分区表
https://blog.csdn.net/lizhangyong1989/article/details/26592113
```
CREATE TABLESPACE TBSP_1 DATAFILE '/data/oracle/oradata/orcl/TBSP_1.dbf' SIZE 100M;
create table USER01.ware_retail_part
(
    id integer primary key,
    retail_date date,
    ware_name varchar2(50)
)
partition by range(retail_date)
(
   --2011年第一季度为par_1分区
   partition par_01 values less than(to_date('2011-04-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第二季度为par_2分区
   partition par_02 values less than(to_date('2011-07-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第三季度为par_3分区
   partition par_03 values less than(to_date('2011-10-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第四季度为par_4分区
   partition par_04 values less than(to_date('2012-01-01','yyyy-mm-dd')) tablespace TBSP_1
)

insert into USER01.ware_retail_part values(1,to_date('2011-01-20','yyyy-mm-dd'),'平板电脑');
insert into USER01.ware_retail_part values(2,to_date('2011-04-15','yyyy-mm-dd'),'小米3手机');
insert into USER01.ware_retail_part values(3,to_date('2011-07-25','yyyy-mm-dd'),'iWatch');
insert into USER01.ware_retail_part values(4,to_date('2011-12-17','yyyy-mm-dd'),'华硕笔记本');

create table USER01.ware_part
(
    id integer primary key,
    ware_date date,
    ware_name varchar2(50)
)
partition by range(ware_date)
(
   --2011年第一季度为par_6分区
   partition par_06 values less than(to_date('2011-04-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第二季度为par_7分区
   partition par_07 values less than(to_date('2011-07-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第三季度为par_8分区
   partition par_08 values less than(to_date('2011-10-01','yyyy-mm-dd')) tablespace TBSP_1,
   --2011年第四季度为par_9分区
   partition par_09 values less than(to_date('2012-01-01','yyyy-mm-dd')) tablespace TBSP_1
)

insert into USER01.ware_part values(6,to_date('2011-01-20','yyyy-mm-dd'),'平板电脑');
insert into USER01.ware_part values(7,to_date('2011-04-15','yyyy-mm-dd'),'小米3手机');
insert into USER01.ware_part values(8,to_date('2011-07-25','yyyy-mm-dd'),'iWatch');
insert into USER01.ware_part values(9,to_date('2011-12-17','yyyy-mm-dd'),'华硕笔记本');
```


#### 导出多个全表及带条件
```
DECLARE
ind NUMBER;
spos NUMBER;
slen NUMBER;
h1 NUMBER;
percent_done NUMBER;
job_state VARCHAR2(30);
le ku$_LogEntry;
js ku$_JobStatus;
jd ku$_JobDesc;
sts ku$_Status;
BEGIN
h1:=dbms_datapump.open('EXPORT','TABLE',NULL,'JOB76c8b83602f8499H','11.0.0');
dbms_datapump.add_file(h1,'JOB76c8b83602f8499H_%U.DMP','DATA_PUMP_DIR');
DBMS_DATAPUMP.add_file(handle=>h1,filename=>'JOB76c8b83602f8499H.log',directory=>'DATA_PUMP_DIR',filetype=>DBMS_DATAPUMP.KU$_FILE_TYPE_LOG_FILE);
DBMS_DATAPUMP.set_parallel(h1,1);
dbms_datapump.set_parameter(handle=>h1,name=>'COMPRESSION',value =>'ALL');
DBMS_DATAPUMP.metadata_filter(handle=>h1,name=>'SCHEMA_EXPR',VALUE=>'IN(''USER01'')');
DBMS_DATAPUMP.metadata_filter(handle=>h1,name=>'NAME_EXPR',VALUE=>'IN(''T'',''WARE_RETAIL_PART'')');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''INDEX''');
dbms_datapump.data_filter(h1,'SUBQUERY','where OBJECT_ID=20','T','SYSTEM');
dbms_datapump.data_filter(h1,'SUBQUERY','where ID=2','WARE_RETAIL_PART','SYSTEM');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''CONSTRAINT''');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''GRANT''');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''TABLE_STATISTICS''');
DBMS_DATAPUMP.START_JOB(handle => h1,cluster_ok=>0);
dbms_datapump.detach(h1);
END;
```
#### 导出日志
```
[root@bigdata-05 dpdump]# cat JOB76691edf695e4235.log
启动 "SYSTEM"."JOB76691edf695e4235":  
正在使用 BLOCKS 方法进行估计...
处理对象类型 TABLE_EXPORT/TABLE/TABLE_DATA
使用 BLOCKS 方法的总估计: 50 MB
处理对象类型 TABLE_EXPORT/TABLE/TABLE
. . 导出了 "USER01"."T"                                7.920 MB   81583 行
. . 导出了 "USER01"."WARE_RETAIL_PART":"PAR_01"        5.859 KB       1 行
. . 导出了 "USER01"."WARE_RETAIL_PART":"PAR_02"        5.859 KB       1 行
. . 导出了 "USER01"."WARE_RETAIL_PART":"PAR_03"        5.851 KB       1 行
. . 导出了 "USER01"."WARE_RETAIL_PART":"PAR_04"        5.859 KB       1 行
. . 导出了 "USER01"."WARE_RETAIL_PART":"PAR_05"        5.859 KB       1 行
已成功加载/卸载了主表 "USER01"."JOB76691edf695e4235" 
******************************************************************************
SYSTEM.JOB76691edf695e4235 的转储文件集为:
  /data/oracle/admin/orcl/dpdump/JOB76691edf695e4235_01.DMP
作业 "USER01"."JOB76691edf695e4235" 已于 星期二 3月 10 18:35:40 2020 elapsed 0 00:00:03 成功完成
```



#### 多个分区表导出测试
多个分区表分区名合并写在一起
dbms_datapump.data_filter(handle=>h1,name=>'PARTITION_LIST',value=>'''PAR_02'',''PAR_03'',''PAR_04'',''PAR_06'',''PAR_07''');
```
DECLARE
ind NUMBER;
spos NUMBER;
slen NUMBER;
h1 NUMBER;
percent_done NUMBER;
job_state VARCHAR2(30);
le ku$_LogEntry;
js ku$_JobStatus;
jd ku$_JobDesc;
sts ku$_Status;
BEGIN
h1:=dbms_datapump.open('EXPORT','TABLE',NULL,'JOB200211160036ad5846a','11.0.0');
dbms_datapump.add_file(h1,'JOB200211160036ad5846a_%U.DMP','DATA_PUMP_DIR');
DBMS_DATAPUMP.add_file(handle=>h1,filename=>'JOB200211160036ad5846a.log',directory=>'DATA_PUMP_DIR',filetype=>DBMS_DATAPUMP.KU$_FILE_TYPE_LOG_FILE);
DBMS_DATAPUMP.set_parallel(h1,1);
DBMS_DATAPUMP.metadata_filter(handle=>h1,name=>'SCHEMA_EXPR',VALUE=>'IN(''USER01'')');
DBMS_DATAPUMP.metadata_filter(handle=>h1,name=>'NAME_EXPR',VALUE=>'IN(''WARE_RETAIL_PART'',''WARE_PART'')');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''INDEX''');
dbms_datapump.data_filter(handle=>h1,name=>'PARTITION_LIST',value=>'''PAR_02'',''PAR_03'',''PAR_04'',''PAR_06'',''PAR_07''');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''CONSTRAINT''');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''GRANT''');
DBMS_DATAPUMP.METADATA_FILTER(handle=>h1,name=>'EXCLUDE_PATH_EXPR',value=>'=''TABLE_STATISTICS''');
DBMS_DATAPUMP.START_JOB(handle => h1,cluster_ok=>0);
dbms_datapump.detach(h1);
END;
```
#### 导入不分全表和分区
```
DECLARE
  ind NUMBER;
  spos NUMBER;
  slen NUMBER;
  h1 NUMBER;
  percent_done NUMBER;
  job_state VARCHAR2(30);
  le ku$_LogEntry;
  js ku$_JobStatus;
  jd ku$_JobDesc;
  sts ku$_Status;
BEGIN
  h1 := DBMS_DATAPUMP.OPEN('IMPORT','FULL',NULL,'JOB76691edf695e4215');
  DBMS_DATAPUMP.ADD_FILE(h1,'JOB200211160036ad5847aa_%U.DMP','MYDUMP');
  DBMS_DATAPUMP.add_file(handle => h1,filename  => 'JOB76691edf695e4215.log',directory => 'MYDUMP',filetype  => DBMS_DATAPUMP.KU$_FILE_TYPE_LOG_FILE);
  DBMS_DATAPUMP.set_parallel (h1, 1);
  DBMS_DATAPUMP.METADATA_REMAP(h1,'REMAP_SCHEMA','USER01','SYSTEM'); 
  
--  DBMS_DATAPUMP.METADATA_REMAP(h1,'REMAP_TABLE','WROW','WROW_3'); 
  DBMS_DATAPUMP.METADATA_TRANSFORM(h1,'SEGMENT_ATTRIBUTES',1);
  DBMS_DATAPUMP.SET_PARAMETER(h1,'TABLE_EXISTS_ACTION','REPLACE');
  DBMS_DATAPUMP.START_JOB(handle => h1,cluster_ok=>0);
  dbms_datapump.detach(h1);
END;
```

#### 附相关sql
##### 查找dblink路径
```
select * from dba_db_links;
select count(1) count from dba_db_links where owner in ('PUBLIC','SYSTEM') and db_link IN ('ORCL_DBLINK','ORCL_DBLINK.LOCAL')
```
##### 查找用户名
```
select USERNAME from dba_users where lower(username) like '%sys%';
```
##### 查找任务状态
```
select STATE from dba_datapump_jobs where JOB_NAME='JOBd3d222dda2894120';
```
##### 根据用户查找表名
```
select table_name tname from dba_tables  where owner='SYSTEM' and lower(table_name) like '%%' 
select count(1)  from dba_tables
SELECT * FROM (SELECT ROWNUM AS rowno, t.table_name FROM dba_tables t  WHERE  ROWNUM <= 20) table_alias WHERE table_alias.rowno >= 10;
//分页处理
int page=1 rows=20
select tname from (select table_name tname,rownum myrow from dba_tables  where rownum <= 20 and lower(owner)='system' and lower(table_name) like '%t%' ) mytable where myrow>=1
int page=2 rows=20
select tname from (select table_name tname,rownum myrow from dba_tables  where rownum <= 40 and lower(owner)='system' and lower(table_name) like '%t%' ) mytable where myrow>=21
		 select table_name tname from dba_tables  where owner='SYSTEM' and lower(table_name) like '%t%' 
```
##### 查找数据泵目录
```
select * from dba_directories 
```
##### ？？查找失败任务
```
SELECT o.status,
       o.object_id,
       o.object_type,
       o.owner || '.' || object_name "OWNER.OBJECT"
  FROM dba_objects o, dba_datapump_jobs j
 WHERE o.owner = j.owner_name
   AND o.object_name = j.job_name
 ORDER BY 4, 2;
 
status object_id object_type OWNER.OBJECT
VALID	85358	TABLE	SYSTEM.JOBd3d222dda2894120
VALID	85388	TABLE	SYSTEM.JOBd3d222dda2894121
```
##### ？？也和失败任务有关
```
SELECT * FROM DBA_DATAPUMP_SESSIONS;

SYSTEM	JOBd3d222dda2894120	1	000000014A47A7F0	DBMS_DATAPUMP
SYSTEM	JOBd3d222dda2894120	1	000000014743C3E0	MASTER
SYSTEM	JOBd3d222dda2894121	1	000000014A47A7F0	DBMS_DATAPUMP
SYSTEM	JOBd3d222dda2894121	1	000000014749AF00	MASTER
```
##### ？？数据库文件？
```
SELECT * FROM DBA_DATA_FILES;
/data/oracle/oradata/orcl/system01.dbf	1	SYSTEM	744488960	90880	AVAILABLE	1	YES	34359721984	4194302	1280	743440384	90752	SYSTEM
```
##### 查询命令空间
```
select t.tablespace_name from dba_tablespaces t where t.contents<>'UNDO' and t.contents<>'TEMPORARY' group by t.tablespace_name order by t.tablespace_name asc 
```

##### 湖北原impdp命令
```
nohup impdp dbauser/dba_2014 tables=USERINFO.ENTITY_SYNC_QUEUE_HIS_929:PART_713_201709 directory=HIS_EXP dumpfile=ENTITY_SYNC_QUEUE_HIS_929_%U.dmp logfile=IMP_ENTITY_SYNC_QUEUE_HIS_929.log cluster=no parallel=8 table_exists_action=append remap_table=ENTITY_SYNC_QUEUE_HIS_929:ENTITY_SYNC_QUEUE_HIS_929_ZWA &
```

##### 环境检查，目标库磁盘信息，百分比已用：已用
[INFO][2020-01-19 16:57:52,867][com.snc.system.utils.BaseSqlHelper][http-bio-8080-exec-8]执行 SQL：
```
select a.TABLESPACE_NAME, a.TOTAL_SIZEM, round(a.total_sizem-b.free_sizem) USED_SIZEM,round((a.total_sizem-b.free_sizem)*100/a.total_sizem,2) USED_PCT,round(b.free_sizem) FREE_SIZEM from (select tablespace_name,sum(bytes)/1024/1024 total_sizem from dba_data_files group by tablespace_name) a,(select tablespace_name,sum(bytes)/1024/1024 free_sizem from dba_free_space group by tablespace_name) b where a.tablespace_name=b.tablespace_name order by used_pct desc
```
[INFO][2020-01-19 16:57:52,906][com.snc.system.utils.BaseSqlHelper][http-bio-8080-exec-8]执行SQL 耗时：39毫秒
大小：4[{USED_SIZEM=748, TABLESPACE_NAME=UNDOTBS1, FREE_SIZEM=2, USED_PCT=99.73, TOTAL_SIZEM=750}, {USED_SIZEM=687, TABLESPACE_NAME=SYSTEM, FREE_SIZEM=13, USED_PCT=98.16, TOTAL_SIZEM=700}, {USED_SIZEM=402, TABLESPACE_NAME=SYSAUX, FREE_SIZEM=198, USED_PCT=66.94, TOTAL_SIZEM=600}, {USED_SIZEM=1, TABLESPACE_NAME=USERS, FREE_SIZEM=4, USED_PCT=20, TOTAL_SIZEM=5}]


将表名加进去:T
返回值:[T]
检查：
```
select index_name from dba_indexes where table_owner='SYSTEM' and table_name like '%T%'
```
##### (???sql什么用)
[INFO][2020-01-19 17:16:05,192][com.snc.system.utils.BaseSqlHelper][http-bio-8080-exec-19]执行 SQL：
```
select partition_name, sum(BYTES) BYTES from (SELECT a.PARTITION_NAME,SUM(a.BYTES) as BYTES FROM DBA_segments a where a.SEGMENT_TYPE like 'TABLE%' AND a.OWNER = 'SYSTEM' AND a.SEGMENT_NAME = 'T'group by a.partition_name union all select s.partition_name, sum(s.bytes) as BYTES from dba_segments s, dba_indexes i where s.segment_type like 'INDEX%' and i.table_owner = 'SYSTEM' and i.table_name = 'T' and s.owner = i.owner and s.segment_name = i.index_name group by s.partition_name union all select b. partition_name, sum(b.bytes) as BYTES from dba_lobs a, dba_segments b where a.segment_name = b.segment_name and a.owner = 'SYSTEM' and a.table_name = 'T'group by b.partition_name) group by partition_name

```
##### 广州周期项目根据字段值获取分区名称
 2020/02/11 16:00:36 -  - 备份表的where条件，计算是否过滤....  
 2020/02/11 16:00:36 -  - 过滤表达式： where RETAIL_DATE <  to_date(''2019-01-01'',''yyyy-mm-dd'')   
 2020/02/11 16:00:36 -  - 当前策略类型为： 2 (按时间分区) ，验证是否符合high_value获取的条件 。   
 2020/02/11 16:00:36 -  - 符合high_value获取的条件，正在使用效率更高的获取方式，计算分区名。   
 2020/02/11 16:00:36 -  - 获取high_value的分区的sql语句：SELECT TABLE_OWNER,TABLE_NAME,PARTITION_NAME,HIGH_VALUE,PARTITION_POSITION FROM DBA_TAB_PARTITIONS WHERE TABLE_OWNER ='SYSTEM' AND TABLE_NAME='WARE_RETAIL_PART'  ORDER BY PARTITION_POSITION ASC   
 2020/02/11 16:00:37 -  - 4：检测high_value的时间，已大于保留的时间边界  PAR_05  
 2020/02/11 16:00:37 -  - 符合备份条件的分区名为： [PAR_01, PAR_02, PAR_03, PAR_04]  
 2020/02/11 16:00:37 -  - 构建完成。  
 2020/02/11 16:00:37 -  - datapump脚本：

##### 新建逻辑目录
最好以system等管理员创建逻辑目录，Oracle不会自动创建实际的物理目录“D:\oracleData”（务必手动创建此目录），仅仅是进行定义逻辑路径dump_dir；
例如： 
```
create directory mydata as '/data/oracle/oradata/mydata';
```
查看逻辑目录是否创建成功
select * from dba_directories

##### network_link使用

通过db_link查询另外一个库的数据

 select * from abb_comp_01@dataware_dblink;



```
表数据导入至历史库 network_link=TO_ACTDB1_12C
userid='to_his/xxxxx'
logfile=MV_ACTDB1_AD_20200204130611.log
directory=EXPDP_DIR
cluster=n
exclude=INDEX,GRANT,STATISTICS,CONSTRAINT
table_exists_action=APPEND
transform=segment_attributes:n
tables=(
AD.CA_NOTIFY_TASK_0746_3_202001
AD.CA_NOTIFY_TASK_0734_2_202001
AD.CA_NOTIFY_TASK_0746_0_202001
AD.CA_NOTIFY_TASK_0736_7_202001
AD.CA_NOTIFY_TASK_0735_0_202001
AD.CA_NOTIFY_TASK_0746_1_202001
AD.CA_NOTIFY_TASK_0732_8_202001
AD.CA_NOTIFY_TASK_0736_4_202001
AD.CA_NOTIFY_TASK_0736_5_202001
AD.CA_NOTIFY_TASK_0734_7_202001
AD.CA_NOTIFY_TASK_0739_9_202001
AD.CA_NOTIFY_TASK_0746_9_202001
AD.CA_NOTIFY_TASK_0732_0_202001
AD.CA_NOTIFY_TASK_0745_7_202001
AD.CA_NOTIFY_TASK_0732_7_202001                                    )

表分区导入至历史库脚本 network_link=TO_BOSSDB1_12C userid='to_his/xxxx'
logfile=MV_BOSSDB1_UCR_ACT11_202002030235.log1
remap_schema=UCR_ACT11:BAK_ACT11_2019 directory=EXPDP_DIR
exclude=INDEX,GRANT,STATISTICS,CONSTRAINT table_exists_action=APPEND
transform=segment_attributes:n tables=(
UCR_ACT11.TF_BHB_ACCESSLOG_2013:PAR_TF_B_ACCESSLOG_1
UCR_ACT11.TF_BHB_ACCESSLOG_2014:PAR_TF_B_ACCESSLOG_1
UCR_ACT11.TF_BHB_ACCESSLOG_2015:PAR_TF_B_ACCESSLOG_1
UCR_ACT11.TF_BHB_ACCESSLOG_2016:PAR_TF_B_ACCESSLOG_1
UCR_ACT11.TF_BHB_ACCESSLOG_2017:PAR_TF_B_ACCESSLOG_1
UCR_ACT11.TF_BHB_ACCESSLOG_2018:PAR_TF_B_ACCESSLOG_1 ) 
```