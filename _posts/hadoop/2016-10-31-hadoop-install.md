---
layout: post
title: "Hadoop Single Node Cluster安装"
date: 2016-10-31 22:12:00 +0800
categories: hadoop
---
### Prerequisites
安装[JAVA JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) （Hadoop 3.0要求Java最低版本为1.8）

#### Java环境变量配置
修改/etc/profile，在末尾添加如下配置：

```bash
export JAVA_HOME=/usr/java/jdk.1.8.0_101
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```

### 下载Hadoop安装包
目前为止，hadoop有两个版本，hadoop 1.x和hadoop 2.x，hadoop 3.x在2016.09出了alpha1版本。 [Hadoop安装包下载地址](http://mirrors.hust.edu.cn/apache/hadoop/common)

### 准备安装Hadoop Cluster
1. 解压下载的Hadoop distribution. `tar zxvf apache-hadoop-3.0.0-alpha1.tar.gz`
2. 修改解压的文件夹下的etc/hadoop/hadoop-env.sh文件，将其中的JAVA_HOME改为Java安装的路径
3. 运行bin/hadoop命令，查看Hadoop的使用文档

### Hadoop环境变量配置
在/etc/profile文件末尾添加如下配置：

```bash
export HADOOP_HOME=/home/hadoop-3.0.0-alpha1
export PATH=$HADOOP_HOME/bin:$PATH
```

### Standalone Operation
Hadoop的默认配置是以一个Java单进程运行在非分布式模式下。

```bash
mkdir input
cp etc/hadoop/*.xml input
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.0-alpha1.jar grep input output 'dfs[a-z.]+'
cat output/*
```

### Pseudo-Distributed Operation

* 配置`etc/hadoop/core-site.xml`

```bash
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

* 配置`etc/hadoop/hdfs-site.xml`

```bash
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

* 配置无密码访问

```bash
ssh-keygen -t ras -P '' -f ~/.ssh/id_rsa
cat `/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

### Execution
在本地运行一个MapReduce Job:
1. bin/hdfs namenode -format  # 初始化文件系统
2. sbin/start-dfs.sh          # 启动NameNode daemon和DataNode daemon
3. 访问http://localhost:9870，查看NameNode的web接口
4. bin/hdfs dfs -mkdir /user
5. bin/hdfs dfs -mkdir /user/root
6. bin/hdfs dfs -mkdir input
7. bin/hdfs dfs -put etc/hadoop/\*.xml input
8. bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.0-alpha1.jar grep input output 'dfs[a-z.]+'
9. bin/hdfs dfs -get output output
10. cat output/\*
11. sbin/stop-dfs.sh
