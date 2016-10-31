---
layout: post
title: "Apache Atlas安装使用说明文档"
date: 2016-10-31 15:47:00 +0800
categories: atlas
---
### 简介
Apache Atlas是Apache基金会的孵化项目，是Hadoop生态圈的数据治理和元数据框架（Data Governance and Metadata framework）。Atlas是一套核心基础治理服务的集合，有很好的伸缩性和可扩展性，能够满足企业对Hadoop生态系统的多样性需求，并能和企业的数据生态系统集成。

### 特性
* Data Classification
  *  定义、注释并自动获取数据集与底层元素（source, target, derivation processes）之间的关系
  *  能够为第三方系统导出元数据

* Centralized Auditing
  * 捕捉每一个应用、进程和数据交互的安全访问信息
  * 捕捉execution, steps和activities的操作信息

* Search & Lineage
  * 通过预定义的导航路径浏览数据分类和审计信息
  * 通过基于文本的搜索特性在Data Lake中快速、准确地定位相关数据和审计事件
  * 可视化浏览数据集的血缘关系，能够允许用户深入探讨操作、安全和数据起源信息 

### 安装(Standalone)
目前[Atlas官网](atlas.incubator.apache.org)没有可下载的包，如果要安装Atlas，必须下载源码。

#### 安装前置条件
* JDK 1.7及以上
* Maven 3.3.9

#### Building Atlas

```bash
git clone https://git-wip-us.apache.org/repos/asf/incubator-atlas.git atlas
cd atlas
export MAVEN_OPTS="-Xmx1536m -XX:MaxPermSize=512m" && mvn clean install
```

##### Atlas使用的数据库和搜索有两种方案
1. HBase + Solr
2. BerkeleyDB + ElasticSearch

这里仅使用HBase + Solr的方案，打包命令如下：

```
mvn clean package -Pdist,embedded-hbase-solr
```
* embedded-hbase-solr会将HBase和Solr打包进Atlas
* 打包结束后，tar包会放在**atlas/distro/target/apache-atlas-${project.version}-bin.tar.gz**

##### Atlas文件结构

```
|- bin
   |- atlas_start.py
   |- atlas_stop.py
   |- atlas_config.py
   |- quick_start.py
   |- cputil.py
|- conf
   |- atlas-application.properties
   |- atlas-env.sh
   |- hbase
      |- hbase-site.xml.template
   |- log4j.xml
   |- solr
      |- currency.xml
      |- lang
         |- stopwords_en.txt
      |- protowords.txt
      |- schema.xml
      |- solrconfig.xml
      |- stopwords.txt
      |- synonyms.txt
|- docs
|- hbase
   |- bin
   |- conf
   ...
|- server
   |- webapp
      |- atlas.war
|- solr
   |- bin
   ...
|- README
|- NOTICE
|- LICENSE
|- DISCLAIMER.txt
|- CHANGES.txt
   
```

#### 安装&运行Atlas

```bash
tar zxvf apache-atlas-${project.version}-bin.tar.gz
cd atlas-${project.version}
```

##### 配置Atlas
* 打开conf/atlas-application.properties，将***atlas.kafka.zookeeper.connect***改为localhost:2181

##### 启动HBase

```bash
cd hbase/bin
./start_hbase.sh
tail -f ../logs/hbase-***.lan.log  
# 查看HBase有没有成功启动
```
* 默认情况下，HBase会自动启动内嵌的zookeeper，端口号2181

##### 启动Solr

```bash
cd solr/bin
# standalone的zookeeper_host:port 为localhost:2181
./solr start -c -z <zookeeper_host:port> -p 8983
# 使用solr create命令创建Atlas使用的indexes
# SOLR_CONF代表路径：atlas/conf/solr
./solr create -c vertex_index -d SOLR_CONF
./solr create -c edge_index -d SOLR_CONF
./solr create -c fulltext_index -d SOLR_CONF
```

##### 启动Atlas

```bash
bin/atlas_start.sh
```

###### 验证Atlas是否启动

```bash
curl -v http://localhost:21000/api/atlas/admin/version
```
