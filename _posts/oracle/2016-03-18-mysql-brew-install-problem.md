---
layout: post
title: "brew安装mysql问题"
date: 2016-03-18 23:18:00 +0800
categories: mysql
---
### brew安装mysql问题
通过brew 安装mysql时遇到了问题，问题场景如下：
在海尔不稳定的网络环境下，使用武老师的bootstrap.sh脚本安装开发环境经常出现timeout的情况，由于不想再单独去安装由于timeout失败的组件，所有多次重复运行bootstrap.sh脚本。我`猜测有可能`是在这种情况下，多次运行了brew install mysql，导致在使用mysql.server start启动mysql时，报出

```
mysql git:(master) mysql.server start
Starting MySQL
.. ERROR! The server quit without updating PID file (/usr/local/var/mysql/ChuHongweideMacBook-Pro.local.pid).
```

通过查看err日志，发现如下ERROR信息：

```
Could not open unix socket lock file /tmp/mysql.sock.lock.
2016-03-18T05:37:51.820512Z 0 [ERROR] Unable to setup unix socket lock file.
2016-03-18T05:37:51.820515Z 0 [ERROR] Aborting
```

正常情况下，在/tmp目录下应该不会出现/tmp/mysql.sock.lock文件。我使用brew uninstall mysql卸载掉mysql，同时删除/tmp目录下的mysql.sock和mysql.sock.lock两个文件，重新安装mysql，结果一切正常。

**Tip: brew remove mysql 卸载当前版本   brew uninstall mysql 卸载全部版本**
