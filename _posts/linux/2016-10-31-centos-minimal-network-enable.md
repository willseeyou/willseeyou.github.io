---
layout: post
title: "CentOS7 Minimal版本网络配置"
date: 2016-10-31 21:19:00 +0800
categories: linux
---
### CentOS7 Minimal版本安装之后，网卡默认是处于禁用状态的。使用`nmcli d`查看当前网络连接情况。
![nmcli.png](/images/linux/nmcli.png)

#### 方法1： 
1. 修改`/etc/sysconfig/network-scripts/ifcfg-eno0`,将ONBOOT=no改为ONBOOT=yes
2. 使用`service network restart`重启网络
3. ping `www.bing.com`, 查看网络有没有联通

![ifcfg-eno0.png](/images/linux/ifcfg-eno0.png)

#### 方法2：
1. 在terminal输入`nmtui`, 打开网络管理器界面
2. 使用tab进行选择，选择Edit a connection，按enter键进入
3. 选择指定网卡，将Automatically connect勾上
4. 使用`service network restart`重启网络
5. ping `www.bing.com`, 查看网络有没有联通

![network-management-gui.png](/images/linux/network-management-gui.png)
![ethernet-choose.png](/images/linux/ethernet-choose.png)
![auto-connect.png](/images/linux/autoconnect.png)

***如果ifconfig command not found，则使用`yum install net-tools`安装***
