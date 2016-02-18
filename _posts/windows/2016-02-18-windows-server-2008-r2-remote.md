---
layout: post
title: "由于没有远程桌面授权服务器可以提供许可证，远程会话被中断"
date: 2016-02-18 09:21:00 +0800
categories: windows-server-2008
---
### 问题产生原因
Windows Server 2008 R2安装了`远程桌面角色`，这个角色120天免费，需要购买授权

### 解决方案
1. 开始->运行(Win+R): mstsc /v:192.168.1.* /admin <br>
![mstsc-remote](/images/windows/mstsc-remote.png)

2. 登录后，在角色中删除“远程桌面服务”，重启即可 <br>
![remote-desktop-service](/images/windows/remote-desktop-service.png)
