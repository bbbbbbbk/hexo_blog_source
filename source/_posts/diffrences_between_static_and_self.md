---
title: PHP static关键字和self关键字的区别
date: 2018-07-22 17:28:02
categories:
- 旧文搬运
tags:
- php
- 旧文搬运
---

在PHP的一个类中，带有static关键字的方法和属性被称为静态方法和静态属性，这样的方法和属性可以通过类直接访问，而不需要通过类对应的实例来进行访问，在类中访问静态变量以及静态属性的时候，可以使用self关键字和static关键字，两种访问方式看起来似乎没有区别，但是实际上还是不一样的

```php
abstract class Person
{
    public static $_className = 'Person';

    public static function getIntro()
    {
        return 'this is a Person';
    }

    public function say()
    {
        return self::getIntro();
    }

    public function staticSay()
    {
        return static::getIntro();
    }

    public function getClassName()
    {
        return self::$_className;
    }

    public function staticGetClassName()
    {
        return static::$_className;
    }
}

class Driver extends Person
{
    public static $_className = 'Driver';

    public static function getIntro()
    {
        return 'this is a Driver';
    }
}

$temp = new Driver;

echo $temp->getClassName().'<br>';
echo $temp->say().'<br>';
//static
echo $temp->staticGetClassName().'<br>';
echo $temp->staticSay().'<br>';
```

运行之后的结果为:

Person
this is a Person
Driver
this is a Driver

由此可看出，在类中使用self关键字访问的静态方法以及静态变量时，self被解析为定义方法和变量的类，在使用static关键字访问的时候，static指的是被调用的类

在抽象类中加入方法print打印一个类的实例：

```php
abstract class Person
{
    public static $_className = 'Person';

    public static function getIntro()
    {
        return 'this is a Person';
    }

    public function say()
    {
        return self::getIntro();
    }

    public function staticSay()
    {
        return static::getIntro();
    }

    public function getClassName()
    {
        return self::$_className;
    }

    public function staticGetClassName()
    {
        return static::$_className;
    }
//print方法用于打印一个类的实例
    public function print()
    {
        print_r(new self());
    }
}

$temp = new Driver;
$temp->print();
```

运行后会报“Cannot instantiate abstract class Person”错误，原因是此时$temp->print()方法中new self()指的是定义的Person这个类，而抽象类是无法实例化的，此时将方法改为：

```php
public function print()
{
    print_r(new static());
}
```

即可成功运行，输出Driver Object ( )，说明此时打印出来的实例为Driver类的实例

