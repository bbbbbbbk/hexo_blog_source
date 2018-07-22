---
title: 注册免费试用12个月的亚马逊AWS云计算服务
date: 2018-07-22 17:38:37
categories:
- 旧文搬运
tags:
- 旧文搬运
---

注册：

[注册地址](https://aws.amazon.com/cn/free/)

点击页面中间的创建免费用户，进入下一步页面:

{% asset_img 1.png  注册地址 %}

然后就是填写各种个人信息的页面了:

{% asset_img 2.png 创建免费用户 %}

填写付款信息:

{% asset_img 3.png  填写个人信息 %}

付款信息会进行一个电话验证，这里需要先填写对应的电话号码和验证码，然后点立刻呼叫我，提示音之后从手机键盘上输入提示的验证码就可以完成验证

{% asset_img 4.png  填写付款信息 %}

验证登录完成后进入主界面：

{% asset_img 5.png  付款信息电话验证 %}

选择所有服务-》EC2，然后启动实例:

{% asset_img 6.png  进入主界面 %}

我选择的是Ubuntu Server 16.04 LTS (HVM), SSD Volume Type：

{% asset_img 7.png  启动实例 %}

 选择好实例类型然后点审核启动：

{% asset_img 8.png  实例类型 %}


下载保存密钥对：

{% asset_img 9.png  启动实例 %}

然后就能看到启动状态了：

{% asset_img 10.png  下载密钥对 %}

选择创建账单警报以免超过免费流量限制：

{% asset_img 11.png  查看启动状态 %}

在控制台可以看见实例已经在运行，接下来就是连接到服务器了：

{% asset_img 12.png  创建账单报警 %}

使用xshell连接：

打开xshell，选择工具->用户秘钥管理者，点导入秘钥，导入刚才创建的秘钥对

{% asset_img 13.png  连接服务器 %}

然后新建连接：

{% asset_img 14.png  打开xshell %}

设置用户身份验证方式：

{% asset_img 15.png  新建xshell链接 %}

点击确定后，会弹出会话窗口，第一次连接会弹出警告框，选择"接受并保存"：

{% asset_img 16.png  设置用户身份验证方式 %}

点击连接，出现以下界面，表示连接成功：

{% asset_img 17.png  弹出会话窗口 %}