---
title: 按照多个键值对数组进行分组合并
date: 2018-07-22 17:07:15
categories:
- 旧文搬运
tags:
- 旧文搬运
- php
---

简介：

$array 为一堆数组，各数组键值为固定

$keys为分组依据，在$array中按照$keys所指定的键值将数组分组，并且将除$keys指定键值对应的值以外的值合并

```php
function groupBy($array,$keys){
    $i = 0;
    while($i < sizeof($array)){
        $x = sizeof($array) - 1;
        while ($x > $i) {
            //取数组交集并返回交集，保留键名
            $temp = array_intersect_assoc($array[$i],$array[$x]);
            if(!empty($temp)){
                // 取键名交集，并做比较，如果相交等于分组依据的键名，则说明两个子数组可以合并
                if(array_intersect_assoc($keys,array_keys($temp)) == $keys){
                    foreach ($array[$i] as $k => $v) {
                        if(!in_array($k,$keys)){
                            $array[$i][$k] += $array[$x][$k];
                        }
                    }
                    //将合并部分移出数组
                    array_splice($array,$x,1);
                }
            }
            $x--;
        }
        $i++;
    }
    return $array;
}

//测试：
$arr = array(
        array(
            'province' => 'Guangdong',
            'city' => 'Guangzhou',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
        array(
            'province' => 'Guangdong',
            'city' => 'Guangzhou',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
        array(
            'province' => 'Guangdong',
            'city' => 'Guangzhou',
            'num1' => 33,
            'num2' => 24,
            'num3' => 32,
        ),
        array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 25,
            'num2' => 115,
            'num3' => 423,
        ),
        array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 0,
            'num2' => 15,
            'num3' => 1,
        ),
        array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 5,
            'num2' => 2,
            'num3' => 4,
        ),
        array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 5,
            'num2' => 2,
            'num3' => 4,
        ),
        array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 5,
            'num2' => 2,
            'num3' => 4,
        ),
        array(
            'province' => 'Guangdong',
            'city' => 'Guangzhou',
            'num1' => 1,
            'num2' => 2,
            'num3' => 3,
        ),
        array(
            'province' => 'Shanghai',
            'city' => 'Shanghai',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
                array(
            'province' => 'Jiangsu',
            'city' => 'Nanjing',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
        array(
            'province' => 'Henan',
            'city' => 'Zhengzhou',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
        array(
            'province' => 'Hunan',
            'city' => 'Changsha',
            'num1' => 25,
            'num2' => 15,
            'num3' => 43,
        ),
        array(
            'province' => 'Hunan',
            'city' => 'Changsha',
            'num1' => 3,
            'num2' => 4,
            'num3' => 5,
        ),
    );
$keys = array(
        'province',
        'city'
    );
$arr = groupBy($arr,$keys);
```

输出结果:

{% asset_img 1.png  计算结果 %}
