---
title: PHP7新增加的两种运算符
date: 2018-07-22 17:22:44
categories:
- 旧文搬运
tags:
- 旧文搬运
- php
---

太空舱运算符:

```php
$a = 1 <=> 1;
//左边等于右边 $a=0
$b = 1 <=> 2;
//左边小于右边 $b=-1
$c = 2 <=> 1;
//左边大于右边 $c=1
```

```php
$a = null;
$b = null;
$c = 1;

$d = $a ?? $b ?? $c ;

//$d = 1, 空合并运算从左到右取第一个非null值
```