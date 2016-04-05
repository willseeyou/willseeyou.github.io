---
layout: post
title: "Maven配置Nginx代理"
date: 2016-03-31 23:48:00 +0800
categories: nginx Maven
---
### Maven Nexus 配置
* 配置文件：Edit $NEXUS_HOME/conf/nexus.properties. Change nexus-webapp-context-path=/nexus to nexus-webapp-context-path=/

### References
* [maven+nexus nginx 配置](http://blog.kainaodong.com/?p=43)
* [Running Behind a Reverse Proxy](http://books.sonatype.com/nexus-book/reference/install-sect-proxy.html)

### 1. 服务器环境
* CentOS 6.5（系统应该是minimal安装，工具极少，无 [`make`](http://www.gnu.org/software/make/manual/make.html),无[`gcc`](https://gcc.gnu.org/onlinedocs/gcc-5.3.0/gcc/)等常用工具）
* 默认无可用[`yum`](https://www.centos.org/docs/5/html/yum/index.html)源
* 无外网访问权限（目前已申请到）
* 服务器无法使用ssh连接，只能使用[`citrix`](https://www.citrix.com.cn/)
* 文件传输通过ftp服务器中转
* 处于内网中，只能通过vpn连接(吐槽一下公司总部的网络)

### 2. Maven私服Sonatype Nexus安装
* 下载 [nexus-latest-bundle.tar.gz](http://www.sonatype.org/nexus/go/)
* 使用`tar zxvf nexus-latest-bundle.tar.gz`解压到当前文件夹
* 进入$NEXUS_HOME/bin，执行./nexus start启动nexus服务，默认端口为8081
* 访问http://localhost:8081/nexus，默认用户名密码为admin/admin123
* 配置settings.xml文件，将repository指向http://localhost:8081/nexus/content/groups/public就可以使用私服了

##### 以上是启动nexus私服的最简单步骤，接下来是nexus的相关配置
* 修改默认端口号：打开$NEXUS_HOME/conf/nexus.properties， 修改application-port为指定端口
* nexus默认访问url为http://localhost:8081/nexus, 如果想直接通过http://localhost:8081 访问，则将$NEXUS_HOME/conf/nexus.properties中的`nexus-webapp-context-path=/nexus`修改为<br>`nexus-webapp-context-path=/`

### 3. Nginx安装
在有外网访问权限的情况下，安装nginx非常简单：
* 添加网易[yum源](http://mirrors.163.com/.help/centos.html)

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

将下载的yum源放入/etc/yum.repos.d/

yum clean all
yum makecache
```

* 安装nginx依赖的包

```
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```

* 安装[pcre](http://www.pcre.org/), 让nginx支持rewrite功能

```
wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz

tar zxvf pcre-8.35.tar.gz

cd pcre-8.35

./configure

make && make install
```

* 安装nginx

```
wget http://nginx.org/download/nginx-1.9.13.tar.gz

tar zxvf nginx-1.9.13.tar.gz

cd nginx-1.9.13

./configure

make && make install
```

在海尔没有外网访问权限的情况下，安装nginx非常痛苦：
1. 需要将依赖的包先下载到本地，然后put到ftp服务器
2. 使用citrix访问待搭建服务器，从ftp服务器将依赖包get到服务器
3. 安装依赖包，此时发现依赖包本身也有依赖包，于是下载依赖包的依赖包，循环往复。。。直到所有的依赖包都安装完成
4. ***不要浪费时间了，果断停止上述步骤，抓紧申请外网访问权限***

### TIP:
有时使用rpm命令安装包时，该包的依赖包已经存在，并且满足依赖条件。但是安装时却提示未找到依赖包。
<br>比如安装[rabbitmq-server-3.6.1-1.noarch.rpm](https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.1/rabbitmq-server-3.6.1-1.noarch.rpm)时，rabbitmq的依赖包esl-erlang的最低版本为R16B03。
<br>安装R16B03版本的esl-erlang，再安装rabbitmq时，提示依赖包不存在。
<br>安装18.3版本的esl-erlang，再安装rabbitmq时，依然提示依赖包不存在。

这种情况下，可以使用--nodeps --force参数强制安装。<br>
`rpm -ivh --nodeps --force rabbitmq-server-3.6.1-1.noarch.rpm`

### 4. Nexus配置Nginx反向代理

Nexus的Nginx配置为：

```
upstream repo.xx.com {
  127.0.0.1:8092;
}

server {
  listen 80;
  server_name repo.xx.com;
  location / {
    proxy_pass http://repo.xx.com/;
  }
}
```

### 5. Gitlab安装
* 下载bitnami封装好的一键安装包[bitnami-gitlab](https://bitnami.com/stack/gitlab/installer)
* 修改安装包权限为744 <br>
`chmod 744 bitnami-gitlab-8.5.7-0-linux-x64-installer.run`
* 修改Gitlab的host为git.xx.com，端口号为8091<br>

```
修改/opt/gitlab-8.5.7-0/apps/gitlab/htdocs/config/gitlab.yml的gitlab.host=git.xx.com和gitlab.port=8091

修改/opt/gitlab-8.5.7-0/apps/gitlab/gitlab-shell/config.yml的gitlab_url为http://git.xx.com/
```

### 6. Gitlab配置Nginx反向代理
Gitlab的Nginx配置为：

```
upstream git.xx.com {
  127.0.0.1:8091;
}

server {
  listen 80;
  server_name git.xx.com;

  location ~ ^/(.*)$ {
    proxy_pass http://git.xx.com;
  }
}
```

### 7. haier-link依赖包部署到Nexus
本来想把公司的仓库整体移植到海尔，但是一想还是算了，公司这么多私有仓库，怎么可能随便就给海尔，再说我也没有权限。。。

不过还是得说说Nexus的仓库迁移：
旧库迁移（Nexus 到 Nexus）

旧库迁移其实非常简单，只需要将旧的库文件拷贝到新库对应位置，然后repair index就可以了。如：

将旧库的“$NEXUS_HOME/sonatype-work/nexus/storage/releases”文件夹覆盖到新库的“$NEXUS_HOME/sonatype-work/nexus/storage/releases”，然后“repair index”（需要花费一点时间），就可以了。

<hr>
在deploy haier-link依赖包的时候被狠狠坑了一把。
我仅仅是把相关的jar包deploy到了Nexus中，忽略了pom文件。。。因为以前处理的大部分jar包的pom文件都是通过deploy命令由Nexus自动生成的，但是我们工程中大部分的基础依赖包都有各自的pom文件。
由于我忽略了这些文件，所以导致怎么都编译不过，只得一个一个解决。。。
