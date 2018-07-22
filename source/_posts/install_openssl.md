---
title: windows 环境下php安装openssl证书
date: 2018-07-22 17:11:14
categories:
- 旧文搬运
tags:
- 旧文搬运
- windows
---

新的电脑安装了PHP、设置好环境变量之后安装了composer，想要通过composer安装Yii2，结果出现了如下报错：

```
  [Composer\Downloader\TransportException]
  The "https://packagist.org/packages.json" file could not be downloaded: SSL
   operation failed with code 1. OpenSSL Error messages:
  error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify
  failed
  Failed to enable crypto
  failed to open stream: operation failed
```

检查发现php.ini里面的extension=php_openssl.dll已开启,如提示所说问题的原因是证书认证失败。

解决方法：

 http://curl.haxx.se/docs/caextract.html 从该网址下载证书文件cacert.pem，然后打开php.ini，找到 openssl.cafile= 

添加下载证书文件的位置（例如：openssl.cafile="E:/php-5.6.27/ext/cacert.pem"），问题解决。