---
title: 解决nvm安装的node使用sudo npm报错的问题
date: 2019-03-08 12:25
categories:
- 学习笔记
tags:
- nodejs
- php
---
主要思路是通过软链将npm添加到usr/local/bin下面：

```
sudo ln -s "$NVM_DIR/versions/node/$(nvm version)/bin/node" "/usr/local/bin/node"
sudo ln -s "$NVM_DIR/versions/node/$(nvm version)/bin/npm" "/usr/local/bin/npm"
```

 

