---
title: php字符串拆分函数
date: 2018-07-22 17:15:43
categories:
- 旧文搬运
tags:
- 旧文搬运
- php
---

```php
function str_split_utf8($str)  
{  
    $split = 1;  
    $array = array();  
    for ($i = 0; $i < strlen($str);) {  
        $value = ord($str[$i]);  
        if ($value > 127) {  
            if ($value >= 192 && $value <= 223) {  
                $split = 2;  
            } elseif ($value >= 224 && $value <= 239) {  
                $split = 3;  
            } elseif ($value >= 240 && $value <= 247) {  
                $split = 4;  
            }  
        } else {  
            $split = 1;  
        }  
        $key = null;  
        for ($j = 0; $j < $split; $j++, $i++) {  
            $key .= $str[$i];  
        }  
        array_push($array, $key);  
    }  
    return $array;  
}  
$str = '飞流直下三千尺，疑是银河落九天';
print_r(str_split_utf8($str));
```

输出结果:

```
Array
(
    [0] => 飞
    [1] => 流
    [2] => 直
    [3] => 下
    [4] => 三
    [5] => 千
    [6] => 尺
    [7] => ，
    [8] => 疑
    [9] => 是
    [10] => 银
    [11] => 河
    [12] => 落
    [13] => 九
    [14] => 天
)
```
