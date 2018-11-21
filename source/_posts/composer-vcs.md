---
title: composer 更新项目使用VCS源
date: 2018-11-21 17:57:55
categories:
- 学习笔记
tags:
- php
---

我们在PHP开发当中难免会遇到这种情况，在用composer做包管理工具的时候，项目依赖的某个开源组件的部分代码需要根据整个项目的需求进行修改，这种时候可以通过修改vendor包里面的组件源码来实现，然而修改vendor包容易导致一个问题，那就是版本不容易进行管理，如果进行composer update操作很容易就把修改过的代码给覆盖了。将composer的repository管理设置为vcs源可以很好地解决这个问题。

VCS全称Version Control System，意为版本管理系统，根据composer官方文档，现在composer支持`Git`、`Subversion`、`Mercurial`和`Fossil`等版本管理系统，其中如果使用`Github`的git源，`Bitbucket`的git和mercurial源，composer是可以直接通过API获取到zip包的，如果是其他源，则需要本地有对应的客户端支持。

假设有这么个场景，在开发的过程中使用了`authorA`的`projectA`包，然后我需要对包里面某个部分的代码进行一些跟本地项目环境更加适配的更改，就可以先将`projectA`的项目代码fork到自己的github目录下，这样就可以对项目源码进行修改了（请遵循相应的开源协议），然后在工程目录的composer.json当中只需要加入这几行代码：

```php
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/myAcount/projectA"
        }
    ],
    "require": {
        "authorA/projectA": "~x.x"
    }
}
```

将本地修改后的代码push到自己的仓库中，注意要打tag，然后在项目目录下执行`composer update authorA/projectA`  命令就可以使用用自己仓库做源的`projectA`包了
