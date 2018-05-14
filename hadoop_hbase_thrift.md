## 安装hadoop和hbase

### 查看版本兼容性

http://hbase.apache.org/book.html#configuration

|                  | HBase-1.2.x | HBase-1.3.x | HBase-2.0.x |
| ---------------- | ----------- | ----------- | ----------- |
| Hadoop-2.4.x     | S           | S           | X           |
| Hadoop-2.5.x     | S           | S           | X           |
| Hadoop-2.6.0     | X           | X           | X           |
| Hadoop-2.6.1+    | S           | S           | S           |
| Hadoop-2.7.0     | X           | X           | X           |
| Hadoop-2.7.1+    | S           | S           | S           |
| Hadoop-2.8.[0-1] | X           | X           | X           |
| Hadoop-2.8.2     | NT          | NT          | NT          |
| Hadoop-2.8.3+    | NT          | NT          | S           |
| Hadoop-2.9.0     | X           | X           | X           |
| Hadoop-3.0.0     | NT          | NT          | NT          |

选择hadoop-2.7.6和hbase-2.0.0



### 安装hadoop

cd ~

mkdir hadoop

cd hadoop

wget http://mirrors.shu.edu.cn/apache/hadoop/common/hadoop-2.7.6/hadoop-2.7.6.tar.gz

tar -zxvf hadoop-2.7.6.tar.gz

vi ~/.bashrc

```shell
export HADOOP_HOME=/root/hadoop/hadoop-2.7.6
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

source ~/.bashrc

cd hadoop-2.7.6/etc/hadoop

vi /etc/hosts

```
192.168.2.80 hadoop-master
```

vi hadoop-env.sh

```shell
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export JAVA_HOME=/home/jdk1.8.0_151
```

vi core-site.xml

```xml
<configuration>  
    <!-- 指定HDFS老大（namenode）的通信地址 -->  
    <property>  
        <name>fs.defaultFS</name>  
        <value>hdfs://hadoop-master:9000</value>  
    </property>  
    <!-- 指定hadoop运行时产生文件的存储路径,需要与hdfs-site.xml中的dfs.data.dir一致 -->  
    <property>  
        <name>hadoop.tmp.dir</name>  
        <value>/root/hadoop/hdfs/data</value>  
    </property>  
</configuration>
```

vi hdfs-site.xml

```xml
<configuration>  
  <property>
      <name>dfs.namenode.secondary.http-address</name>
      <value>hadoop-master:50090</value>
    </property>
    <property>
      <name>dfs.blocksize</name>
      <value>67108864</value>
    </property>
    <property>  
        <name>dfs.name.dir</name>  
        <value>/root/hadoop/hdfs/name</value>  
        <description>namenode上存储hdfs名字空间元数据 </description>   
    </property>  
  
    <property>  
        <name>dfs.data.dir</name>  
        <value>/root/hadoop/hdfs/data</value>  
        <description>datanode上数据块的物理存储位置</description>  
    </property>  
  
    <!-- 设置hdfs副本数量 -->  
    <property>  
        <name>dfs.replication</name>  
        <value>1</value>  
    </property>  
</configuration>
```

第一次需要格式化：

hdfs namenode -format  

启动hdfs：

start-dfs.sh

找到NameNode的pid：

jps

找到NameNode的端口：

netstat -anp | grep {$pid}

开放端口：

firewall-cmd --add-port {$port}/tcp

查看：（默认为50070端口）

![](C:\Users\tianren.zhang\Desktop\file\hadoop.png)



### 安装hbase

cd ~/hadoop

wget http://mirrors.hust.edu.cn/apache/hbase/2.0.0/hbase-2.0.0-bin.tar.gz

tar -zxvf hbase-2.0.0-bin.tar.gz

cd hbase-2.0.0/conf

vi ~/.bashrc

```shell
export HBASE_HOME=/root/hadoop/hbase-2.0.0
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$HBASE_HOME/bin
```

vi hbase-env.sh

```shell
export JAVA_HOME=/home/jdk1.8.0_151
export HBASE_MANAGES_ZK=true
```

vi hbase-site.xml

```xml
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://hadoop-master:9000/hbase</value>
    </property>
    <property>
        <name>hbase.tmp.dir</name>
        <value>/root/hadoop/tmp</value>
    </property>
</configuration>
```

start-hbase.sh

找到HMaster的pid：

jps

找到HMaster的端口：

netstat -anp | grep {$pid}

开放端口：

firewall-cmd --add-port {$port}/tcp

查看：（默认为16010端口）

![](C:\Users\tianren.zhang\Desktop\file\hbase.png)



## Thrift

按照教程安装：http://thrift.apache.org/docs/install/centos

启动thrift服务（由于默认端口被占用，需要指定端口）：hbase-daemon.sh start thrift -p 9191

git clone https://github.com/apache/hbase.git

cd hbase/hbase-thrift/src/main/resources/org/apache/hadoop/hbase/thrift/

cp Hbase.thrift ~/hadoop

cd ~/hadoop

thrift --gen java Hbase.thrift

