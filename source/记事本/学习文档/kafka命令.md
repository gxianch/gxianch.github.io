bootstrap.servers=10.159.107.27:21005,10.159.107.25:21005,10.159.107.30:21005,10.159.107.29:21005,10.159.107.28:21005,10.159.107.26:21005,10.159.107.24:21005,10.159.102.66:21005,10.159.102.64:21005,10.159.102.65:21005

topic=apm_span_log

cd /app/hnivory/hadoopclient/Kafka/kafka/bin
./kafka-console-consumer.sh --bootstrap-server 10.159.107.27:21005 --zookeeper 10.154.147.165:24002/kafka --topic apm_span_log

source bigdata_env
kinit -kt user.keytab xjuser
cd /app/hnivory/hadoopclient/Kafka/kafka/bin
bin/kafka-topics.sh --create --topic flumeToTopic --replication-factor 2 --partitions 32 --zookeeper 10.154.147.164:24002/kafka   

        /*        props.put("security.protocol", "SASL_PLAINTEXT");
       // props.put("sasl.mechanism", "GSSAPI");
        props.put("sasl.kerberos.service.name", "kafka");
        props.put("kerberos.domain.name","hadoop.hadoop.com");*/
      /*  System.setProperty("java.security.krb5.conf", "/app/apm/test/config/krb5.conf");
        System.setProperty("zookeeper.server.principal", "zookeeper/hadoop.hadoop.com");
        System.setProperty("java.security.auth.login.config", "/app/apm/test/config/jass.conf");*/         
1.每台使用的客户端需要安装华为的客户端。
2、修改启动参数
-Djava.security.auth.login.config=${current_path}/config/kafka-jaas.conf?-Djava.security.krb5.conf=${current_path}/config/krb5.conf  
-Djava.security.auth.login.config=./config/kafka-jaas.conf -Djava.security.krb5.conf=./config/krb5.conf -Dzookeeper.server.principal=zookeeper/hadoop.hadoop.com -Djavax.security.auth.useSubjectCredsOnly=false 