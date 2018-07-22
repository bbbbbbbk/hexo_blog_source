---
title: linux系统下mysql忘记密码处理
date: 2018-07-22 17:24:17
categories:
- 旧文搬运
tags:
- 旧文搬运
- mysql
---

最近开始重新拾掇自己优惠时贪便宜买的一台京东云主机，然而早已经将当年集成环境一键安装时设置的mysql密码给忘了。

于是度娘了解决办法，大致分为以下步骤：

```
#停止mysqld

service mysqld stop

#以不检查权限的方式启动mysql

mysqld --skip-grant-tables&

#用root帐号以空密码的形式登录mysql

mysql -u root

#进入mysql命令行界面之后，执行以下命令 分别是 修改root用户密码、重新加载权限、退出mysql命令行界面

mysql> update mysql.user set password=PASSWORD('新密码') where user='root';  
mysql> flush privileges;  
mysql> quit 
```

结果执行之后报这个错误 “Unknown column 'password' in 'field list'”。

{% asset_img 1.png  报错 %}

后查询得知mysql在5.7版本下已经没有password这个字段了，同一字段换成了authentication_string,因此对之前的步骤进行修改：

```
#修改密码的命令改为：
mysql> update mysql.user set authentication_string=password(新密码) where user='root';#修改密码
mysql> flush priviledge;#重载权限
mysql> quit;#退出mysql命令行
```

密码修改成功
