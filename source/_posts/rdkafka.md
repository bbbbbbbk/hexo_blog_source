---
title: windows环境下给PHP增加rdkafka扩展
date: 2018-07-22 18:35:50
categories:
- 旧文搬运
tags:
- 旧文搬运
---

因为工作需要kafka作为消息中间件，所以在本地开发环境进行测试的时候需要给PHP添加rdkafka扩展，使用PHP作为producer或者cosumer，在此纪录一下rdkafka的安装过程。

[扩展下载地址](http://pecl.php.net/package/rdkafka)

根据自身PHP版本选择相应的包，使用phpinfo()函数可以很方便的查看自己PHP版本：

{% asset_img 1.png  查看PHP版本 %}

我的是7.1.5 vc14 x64 nts版本的php 所以选择相应的扩展包进行下载：

{% asset_img 2.png 选择扩展包 %}

windows版的扩展包下载下来之后是一个压缩文件，解压之后得到一堆文件

将其中rdkafka.dll放入php目录下的ext文件夹内，librdkafka.dll放入php目录下，然后修改php.ini，添加：

```
extension=php_rdkafka.dll
```

然后重启服务器，再通过phpinfo查看，便能看到rdkafka扩展已经成功安装

{% asset_img 3.png  扩展成功安装 %}