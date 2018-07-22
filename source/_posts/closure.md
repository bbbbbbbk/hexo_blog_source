---
title: PHP 闭包（匿名函数）
date: 2018-07-22 16:46:01
categories:
- 旧文搬运
tags:
- 旧文搬运
- php
---

PHP 在 5.3引入了匿名函数closure的概念，这个也就是俗称的闭包，指的是包含有未绑定到特定对象的变量（自由变量）的代码块

创建closure对象：

```php
$foo = function(){
};//因为相当于将一个closure对象赋值给对象，要遵循对象赋值语法，语句结束需要添加分号
$foo();
```

闭包声明参数以及调用外部变量：

```php
$value = 'hello';
$foo = function($bar) use ($value){
    echo $value . $bar;
};
$foo('world');//输出helloworld
/*PHP版本5.4以上$this才可用于匿名函数*/
```

使用引用和不使用引用，根据官方文档：

```php
<?php
$result = 0;

$one = function()
{ var_dump($result); };

$two = function() use ($result)
{ var_dump($result); };

$three = function() use (&$result)
{ var_dump($result); };

$result++;

$one();    // outputs NULL: $result is not in scope
$two();    // outputs int(0): $result was copied
$three();    // outputs int(1)
?>
```

不使用引用时，在闭包内部的$result实际是声明时候对外部变量$result的复制,因此闭包内部对$result的处理不会影响到外部变量result，而使用引用则闭包内$result直接指向外部变量$result的内存地址，因此会对外部$result造成影响：

```php
<?php 
$x = 0;
$foo = function() use ($x){
    $x++;
    echo $x;
};

$bar = function() use (&$x){
    $x++;
    echo $x;
};
$foo();
$foo();
$bar();
$bar();
//输出 11 12
?>
```

因为闭包的存在，我们便可以在不需要在外部声明回调函数的情况下使用一些类似于array_walk的方法，比如官方手册中提供的购物车的方法：

```php
<?php
// 一个基本的购物车，包括一些已经添加的商品和每种商品的数量。
// 其中有一个方法用来计算购物车中所有商品的总价格，该方法使
// 用了一个 closure 作为回调函数。
class Cart
{
    const PRICE_BUTTER  = 1.00;
    const PRICE_MILK    = 3.00;
    const PRICE_EGGS    = 6.95;

    protected   $products = array();
    
    public function add($product, $quantity)
    {
        $this->products[$product] = $quantity;
    }
    
    public function getQuantity($product)
    {
        return isset($this->products[$product]) ? $this->products[$product] :
               FALSE;
    }
    
    public function getTotal($tax)
    {
        $total = 0.00;
        
        $callback =
            function ($quantity, $product) use ($tax, &$total)
            {
                $pricePerItem = constant(__CLASS__ . "::PRICE_" .
                    strtoupper($product));
                $total += ($pricePerItem * $quantity) * ($tax + 1.0);
            };
        
        array_walk($this->products, $callback);
        return round($total, 2);;
    }
}

$my_cart = new Cart;

// 往购物车里添加条目
$my_cart->add('butter', 1);
$my_cart->add('milk', 3);
$my_cart->add('eggs', 6);

// 打出出总价格，其中有 5% 的销售税.
print $my_cart->getTotal(0.05) . "\n";
// 最后结果是 54.29
?>
```