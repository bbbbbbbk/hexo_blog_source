---
title: 系统升级win10后，wampserver开启处于offline的解决方法
date: 2018-07-22 16:35:37
categories:
- 旧文搬运
tags:
- 旧文搬运
- windows
- wamp
---

系统升级win10之后，wampserver开启的时候一直处于黄灯offline的状态，查询之后得知win10操作系统会默认占用80端口，于是打开apache的httpd.conf，将listen 80 改为 8088，问题即解决

补充，通过修改httpd配置之后只能通过localhost：8088访问，不是特别方便，所以还是得从根本上解决系统默认占用80端口的问题

用系统管理员身份打开命令提示符，执行net stop http命令

{% asset_img 1.png  执行命令 %}

停止win10 http service 服务之后，再执行sc config http start=disabled

将apache httpd.conf设置端口号改回80，refresh，亮绿灯