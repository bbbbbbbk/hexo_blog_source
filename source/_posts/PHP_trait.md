---
title: PHP trait特性
date: 2018-07-22 17:18:28
cateogries:
- 旧文搬运
tags:
- 旧文搬运
- php
---

trait是PHP自5.4版本之后加入的一种新的代码复用机制，是一种细粒度代码复用的方法。官方文档对于trait给出的解释是：

>自 PHP 5.4.0 起，PHP 实现了一种代码复用的方法，称为 trait。
>Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。
>Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。

trait 单词的释义为特性，很好的解释了trait这种方法的特点，可以在不同层次结构内独立的类中复用方法和属性，以一种易于理解的方式来举例，在网络游戏《魔兽世界》当中，法师，潜行者，战士是三个玩家可以操控的职业,三个职业是平等的，可以理解为继承自“角色”这一父类, 因为不管玩家使用什么职业，都能够使用坐骑，所以，父类“Character”中便可以添加一个ride()方法，表示使用坐骑，这样所有继承自“Character”的职业便都有了使用坐骑的能力。而不同的职业又有各自的特点，比如说，法师能够使用暴风雪，潜行者能够潜行，战士能够冲锋，因此，“mage”类中可以增加“blizzard”方法表示法师使用暴风雪技能，“rogue”类中加入“stealth”方法表示潜行者使用潜行技能，“worrior”类中可以增加“charge”方法表示战士使用冲锋技能，这样各职业的特点便能体现出来。而当分配装备时，问题就来了，众所周知《魔兽世界》的装备系统十分庞大，护甲分为布甲、皮甲、锁甲、板甲四种不同的种类，而不同的职业能够装备的护甲类型不完全相同，有些职业只可以装备一种类型的护甲，而有的职业可以装备大部分类型的护甲，这种时候，为了避免重复定义，trait就能够派上用场了。我们在这里定义“布甲”（cloth armour）、“皮甲”（leather armour）、“板甲”（plate armour）三个不同的trait，表示一个职业可以装备某一种护甲，这样我们定义一个职业的时候，便可以通过插入trait来定义这个职业能够装备的护甲类型，而这样一个特性又是独立于职业之外的，多个职业可以共同享有一个特性，比如，战士既可以装备板甲，也可以装备布甲，这样在声明战士这个类的时候，便可以插入多条特性，来表示战士是具有作为一个全需党的条件的：

```php
trait ClothArmour
{
    public function putOnClothArmour()
    {
    //穿上一件布甲
    }
}
trait LeatherArmour
{
    public function putOnLeatherArmour()
    {
    //穿上一件皮甲
    }
}
trait PlateArmour
{
    public function putOnPlateArmour()
    {
    //穿上一件板甲
    }
}
class Character
{
    public function ride(){
    //召唤坐骑
    }
}
//法师
class Mage extends Character
{
    use ClothArmour;
    public function blizzard(){
    //使用“暴风雪”
    }
}
//战士
class Worrior extends Character
{
    use ClothArmour,LeatherArmour,PlateArmour;
    public function charge(){
    //使用“冲锋”
    }
}
//潜行者
class rogue extends Character
{
    use ClothArmour,LeatherArmour;
    public function stealth(){
    //使用“潜行”
    }
}
$worrior = new Worrior();
//继承自父类的方法，召唤坐骑
$worrior->ride();
//自身的方法，使用冲锋技能
$worrior->charge();
//战士能够装备布甲
$worrior->putOnClothArmour();
//战士能够装备皮甲
$worrior->putOnLeatherArmour();
 //战士能够装备板甲
$worrior->putOnPlateArmour();
```

在这个事例中，就可以看到trait最基础的使用方法，trait其实是一组描述了某个特性的一些属性和方法的集合，能够很方便地进行组合与应用，耦合性低，易于维护，同时特性的引入也在某些程度上减少了层层继承之后必须不断追溯父类寻找某些方法的情况，提高的代码的可读性，对于提升开发工作的效率以及开发团队合作是非常好的。
