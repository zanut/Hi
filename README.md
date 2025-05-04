# Big data

# Create New User

```bash

sudo apt update
sudo apt install openjdk-8-jdk

sudo adduser {username}
sudo usermod -aG sudo {username}
sudo passwd {username}

# root user
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys

# Should be able to login without password required
ssh {username}@localhost
exit
```

```bash
# User
whoami

# Group Name
id -g -n
```

## .bashrc file

```bash
export userProfileName=hadoop

# HADOOP
export HADOOP_HOME=/home/$userProfileName/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

# HIVE
export HADOOP_USER_CLASSPATH_FIRST=true
export HIVE_HOME=/home/$userProfileName/hive
export PATH=$HIVE_HOME/bin:$PATH

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/

# PIG
export PATH=$PATH:/home/$userProfileName/pig/bin
export PIG_HOME=/home/$userProfileName/pig
export PYTHONPATH=$PYTHONPATH:$PIG_HOME/src/python/streaming
export CLASSPATH=$CLASSPATH:/home/$userProfileName/spark/jars

```

### Update .bashrc file

```bash
source ~/.bashrc
```

# Hadoop installing

```bash
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
tar xzf hadoop-3.2.1.tar.gz
mv hadoop-3.2.1 hadoop
```

### Update hadoop-env.sh

```bash
vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

```bash
vi $HADOOP_HOME/etc/hadoop/core-site.xml
vi $HADOOP_HOME/etc/hadoop/hdfs-site.xml
vi $HADOOP_HOME/etc/hadoop/mapred-site.xml
vi $HADOOP_HOME/etc/hadoop/yarn-site.xml

# after config all xml above then run
hdfs namenode -format
```

core-site.xml

```xml
<configuration>
<property>
  <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
</property>
</configuration>
```

hdfs-site.xml

```xml
<configuration>
<property>
 <name>dfs.replication</name>
 <value>1</value>
</property>

<property>
  <name>dfs.name.dir</name>
    <value>file:///home/{username}/hadoopdata/hdfs/namenode</value>
</property>

<property>
  <name>dfs.data.dir</name>
    <value>file:///home/{username}/hadoopdata/hdfs/datanode</value>
</property>
</configuration>
```

mapred-site.xml

```xml
<!-- Put site-specific property overrides in this file. --> 
<configuration>  
<property>   
	<name>mapreduce.framework.name</name>  
	 <value>yarn</value> 
 </property>
 <property>     
	 <name>mapreduce.application.classpath</name>     
	 <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*,$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/common/*,$HADOOP_MAPRED_HOME/share/hadoop/common/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/yarn/*,$HADOOP_MAPRED_HOME/share/hadoop/yarn/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/hdfs/*,$HADOOP_MAPRED_HOME/share/hadoop/hdfs/lib/*</value> 
 </property>
 <property>  
	 <name>yarn.app.mapreduce.am.env</name> 
	 <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
 </property>
 <property>  
	 <name>mapreduce.map.env</name>  
	 <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value> 
 </property>
 <property>  
	 <name>mapreduce.reduce.env</name> 
	 <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value> 
</property> 
</configuration>
```

yarn-site.xml

```xml
<configuration>
 <property>
  <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
 </property>
</configuration>
```

```bash
cd $HADOOP_HOME/sbin/
./start-all.sh

# Should see all service that run
jps

# Check firewall status
sudo ufw status
# if status is enable
sudo ufw allow 9870/tcp
sudo ufw reload
```

## Tunnel setup for bypassing the firewall

```bash
# My Machine Terminal
ssh -L {local-port}:{vm-ip}:{service-port} {username}@{vm-ip}

# then we can access localhost:{local-port}
```

# Jupiter

https://github.com/cchantra/bigdata.github.io/wiki/Jupyter-Notebook-Setup/

# Kafka

```bash
pip3 install -U kafka-python
pip3 install lxml --user
pip3 install bs4 --user

wget https://archive.apache.org/dist/kafka/2.4.1/kafka_2.11-2.4.1.tgz
tar xvf kafka_2.11-2.4.1.tgz
mv kafka_2.11-2.4.1 kafka
cd kafka
vi config/zookeeper.properties
```

Make change to dataDir

`dataDir=./zookeeper`

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties # console 1
bin/kafka-server-start.sh config/server.properties  # console 2
```

## command generate topic

```bash
# Specific Topic name at the end
bin/kafka-topics.sh --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --create --topic {topic_name}

```

# Hbase

```bash
wget http://archive.apache.org/dist/hbase/1.4.13/hbase-1.4.13-bin.tar.gz
tar xvf hbase-1.4.13-bin.tar.gz
mv hbase-1.4.13-bin hbase
```

**Configure `hbase-env.sh`**

```bash
nano /home/$userProfileName/hbase/conf/hbase-env.sh
```

```bash
# HBASE
export JAVA_HOME="$(jrunscript -e 'java.lang.System.out.println(java.lang.System.getProperty("java.home"));')"
export HBASE_HOME=/home/$userProfileName/hbase
export PATH=$PATH:/home/$userProfileName/hbase/bin
export CLASSPATH=$CLASSPATH:/home/$userProfileName/hbase/lib
export HBASE_MANAGES_ZK=true

```

```bash
vi /home/$userProfileName/hbase/conf/hbase-site.xml
```

```xml
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>file:/home/hadoop/hbase/HFiles</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/home/hadoop/zookeeper</value>
  </property>
</configuration>
```

```bash
cd /home/$userProfileName/hbase/bin
./start-hbase.sh
```

# Hive Installing

```bash
wget https://archive.apache.org/dist/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
tar xzf apache-hive-2.3.9-bin.tar.gz
mv apache-hive-2.3.9-bin hive

$HADOOP_HOME/bin/hadoop fs -mkdir /user1
$HADOOP_HOME/bin/hadoop fs -mkdir /user1/hive
$HADOOP_HOME/bin/hadoop fs -mkdir /user1/hive/warehouse
$HADOOP_HOME/bin/hadoop fs -chmod g+w /tmp
$HADOOP_HOME/bin/hadoop fs -chmod g+w /user1/hive/warehouse

cp /home/$userProfileName/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar /home/$userProfileName/hive/lib
rm /home/$userProfileName/hive/lib/guava-14.0.1.jar
```

## mysql

```bash
sudo apt-get update
sudo apt-get install mysql-server
 
sudo ufw allow mysql
sudo systemctl start mysql
```

```bash
wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-java-5.1.48.tar.gz
tar xvf mysql-connector-java-5.1.48.tar.gz
cp mysql-connector-java-5.1.48/mysql-connector-java-5.1.48.jar /home/hadoop/hive/lib/
```

```bash
# id=root, pas=password
vi /home/$userProfileName/hive/conf/hive-site.xml
```

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

<property>
  <name>hive.exec.local.scratchdir</name>
  <value>/home/hadoop/hive/tmp</value>
</property>

<property>
    <name>hive.server2.active.passive.ha.enable</name>
    <value>true</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true&amp;useSSL=false&amp;allowPublicKeyRetrieval=true&amp;verifyServerCertificate=false&amp;requireSSL=false</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.jdbc.Driver</value>
</property>

<property>
  <name>hive.server2.enable.doAs</name>
  <value>false</value> 
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>root</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>password</value>
</property>

<property>
  <name>datanucleus.autoCreateSchema</name>
  <value>true</value>
</property>

<property>
  <name>datanucleus.fixedDatastore</name>
  <value>true</value>
</property>

<property>
 <name>datanucleus.autoCreateTables</name>
 <value>True</value>
</property>

<property>
 <name>hive.metastore.warehouse.dir</name>
 <value>hdfs://localhost:9000/user1/hive/warehouse</value>
</property>

</configuration>
```

```bash
vi /home/$userProfileName/hive/conf/core-site.xml
```

```xml
<?xml version="1.0"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at
      http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <!-- OOZIE proxy user setting -->
  <property>
    <name>hadoop.proxyuser.oozie.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.oozie.groups</name>
    <value>*</value>
  </property>

  <!-- HTTPFS proxy user setting -->
  <property>
    <name>hadoop.proxyuser.httpfs.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.httpfs.groups</name>
    <value>*</value>
  </property>

  <!-- HS2 proxy user setting -->
  <property>
    <name>hadoop.proxyuser.hive.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.hive.groups</name>
    <value>*</value>
  </property>

  <!-- metastore proxy user setting -->
  <property>
    <name>hadoop.proxyuser.superuser.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.superuser.groups</name>
    <value>*</value>
  </property>

</configuration>
```

### SQL set up

```bash
sudo mysql  # login root/password

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password'

SELECT user,authentication_string,plugin,host FROM mysql.user;

$HIVE_HOME/bin/schematool -initSchema -dbType mysql  # create metastore in mysql

exit
```

### Pyhive

```bash
sudo apt-get install libsasl2-dev
sudo pip install  sasl thrift
sudo pip install pyhive
sudo pip install  thrift_sasl

# export LC_ALL="en_US.UTF-8"
# export LC_CTYPE="en_US.UTF-8"

sudo dpkg-reconfigure locales
```

```
cd hive/
nohup bin/hive --service metastore &
bin/hiveserver2 &
```

# Install Kibana, Filebeat, Logstash

```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.12.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.12.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.12.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.12.0-linux-x86_64.tar.gz

tar xvf elasticsearch-7.12.0-linux-x86_64.tar.gz
tar xvf kibana-7.12.0-linux-x86_64.tar.gz
tar xvf logstash-7.12.0-linux-x86_64.tar.gz
tar xvf filebeat-7.12.0-linux-x86_64.tar.gz

mv kibana-7.12.0-linux-x86_64 kibana
mv elasticsearch-7.12.0-linux-x86_64 elasticsearch
mv logstash-7.12.0-linux-x86_64 logstash
mv filebeat-7.12.0-linux-x86_64 filebeat
```

## ElasticSearch installing

```bash
cd elasticsearch
bin/elasticsearch -d -p pid

vi /usr/lib/systemd/system/elastic.service
```

```xml
[Unit]

Description=Elastic

[Service]

RuntimeDirectory=/home/{username}/elasticsearch

Environment=ES_HOME=/home/{username}/elasticsearch

Environment=ES_PATH_CONF=/home/{username}/elasticsearch/config

Environment=ES_HEAP_SIZE=512M

Environment=ES_JAVA_OPTS=-Xmx2g -Xms2g

Type=simple

User={username}

Group={username}

#User=root

#Group=root

ExecStart=/home/{username}/elasticsearch/bin/elasticsearch

Restart=always

[Install]

WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable elastic.service
sudo systemctl start elastic.service
sudo systemctl status elastic.service
```

## Kibana installing

```bash
cd kibana
vi config/kibana.yml
```

```bash
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
server.ssl.enabled: false
```

```bash
sudo vi /usr/lib/systemd/system/kibana.service
```

```xml
[Unit]
Description=Kibana

[Service]
Type=simple
User={username}
Group={username}
#User=root
#Group=root
ExecStart=/home/{username}/kibana/bin/kibana -c /home/{username}/kibana/config/kibana.yml
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable kibana.service
sudo systemctl start kibana.service
sudo systemctl status kibana.service
```

```bash
sudo ufw allow 5601/tcp
# Tunnel to port 5601
ssh -N -L 5601:localhost:5601 {username}@{vm-ip} -vv

# https://github.com/cchantra/bigdata.github.io/wiki/Kibana-Reverse-Proxy
# nginx
```

## Firebeat yml config

filebeat.yml

```bash
# https://www.elastic.co/guide/en/beats/filebeat/7.12/configuration-filebeat-options.html
filebeat.inputs:
- type: log
 enabled: true 
 paths:
  - ./logstash-tutorial.log # Path to log can be *.log
 #- /home/{username}/logs/*.log

# https://www.elastic.co/guide/en/beats/filebeat/7.12/configuring-output.html  
output.logstash:
  hosts: ["localhost:5044"]
  
setup.kibana:
  host: "localhost:5601"
  
```

# Set up Firebeat to Kibana dashboard

 When have logstash between

```jsx
./filebeat setup -e \
  -E output.logstash.enabled=false \
  -E output.elasticsearch.hosts=['localhost:9200'] \
  -E output.elasticsearch.username={nginx username}\
  -E output.elasticsearch.password={nginx password}\
  -E setup.kibana.host=localhost:5601
```

# Logstash config

make .conf file 

```xml
# https://www.elastic.co/guide/en/logstash/7.12/input-plugins.html
input {
    beats {
        port => "5044"
    }
}

# https://www.elastic.co/guide/en/logstash/7.12/filter-plugins.html
filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG}"}
    }
    geoip {
        source => "clientip"
    }
}

# https://www.elastic.co/guide/en/logstash/7.12/output-plugins.html
output {
    elasticsearch {
        hosts => [ "localhost:9200" ]
    }
}
```

# Kafka for ELS

[https://risingwave.com/blog/how-to-send-data-from-kafka-to-elasticsearch-a-step-by-step-guide/](https://risingwave.com/blog/how-to-send-data-from-kafka-to-elasticsearch-a-step-by-step-guide/)

enable in filebeat.yml

```bash
#--------------------------------- Kafka Module ---------------------------------
- module: kafka
  # All logs
  log:
    enabled: true

    # Set custom paths for Kafka. If left empty,
    # Filebeat will look under /opt.
    var.kafka_home: /opt/kafka  <-- here

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    #var.paths:

    # Convert the timestamp to UTC. Requires Elasticsearch >= 6.1.
    #var.convert_timezone: false
```

```bash
mkdir /usr/share/filebeat/bin/module
sudo filebeat modules enable kafka
```