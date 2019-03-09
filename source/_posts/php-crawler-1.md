---
title: PHP 爬虫体验（一） - 使用dom-crawler和guzzle实现基本的爬虫
date: 2018-10-15 10:36:18
categories:
- 学习笔记
tags:
- php
---

网络爬虫在大数据时代可以非常高效地自动进行数据的收集处理，而传统爬虫最简单也是最基本的功能实现原理即是下载网页，然后通过抽取页面元素来达到收集信息的目的。

PHP作为一门灵活易用的脚本语言，实现这些功能自然是不在话下的。

这里实现爬虫基于两个组件：

[guzzle](https://github.com/guzzle/guzzle)：最好用的PHP HTTP客户端，用来进行爬取页面的请求，异步请求和并发请求功能可以用来实现一些后期的扩展功能。

[dom-crawler](https://github.com/symfony/dom-crawler)：symphony的Dom分析组件，可以用来分析HTML页面Dom元素和XML文件，用来进行页面分析。

两个组件在项目中都可以很方便地使用composer进行安装，这里以博客园的文章为例，使用这两个组件实现最简单的页面抓取，抓取我个人博客园首页的文章摘要和链接。

代码如下：

```php
require_once __DIR__ . '/vendor/autoload.php';

use GuzzleHttp\Client;
use Symfony\Component\DomCrawler\Crawler;

run();
function run()
{
    //要爬取的页面地址为我的博客园主页
    $url = "http://www.cnblogs.com/jackiebao/";
    //伪造浏览器UA
    $headers = [
        'user-agent' => 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
    ];
    $client = new Client([
        'timeout' => 20,
        'headers' => $headers
    ]);
    //发送请求获取页面内容
    $response = $client->request('GET', $url)->getBody()->getContents();

    $data = [];
    $crawler = new Crawler();
    $crawler->addHtmlContent($response);

    //使用crawler进行页面内容分析
    try{
        //这里使用的是xpath语法，轮询forFlow子类day中的元素，既页面上每一篇文章的块状元素，并且进行内容获取
        $crawler->filterXPath('//div[contains(@class, "forFlow")]/div[contains(@class, "day")]')->each(function(Crawler $node, $i) use (&$data){
            $item = [
                'date' => $node->filterXPath('//div[contains(@class, "dayTitle")]/a')->text(),
                'title' => $node->filterXPath('//div[contains(@class, "postTitle")]/a')->text(),
                'abstract' => $node->filterXPath('//div[contains(@class, "postCon")]/div')->text(),
                'url' => $node->filterXPath('//div[contains(@class, "postCon")]/div/a')->attr('href'),
            ];
            $data[] = $item;
        });
    }catch (\Exception $e){
        echo $e->getMessage() . PHP_EOL;
    }
    //打印结果
    print_r($data);
}
```
打印出来的结果为：

```
Array
(
    [0] => Array
        (
            [date] => 2018年4月27日
            [title] => windows环境下给PHP增加rdkafka扩展
            [abstract] => 摘要: 因为工作需要kafka作为消息中间件，所以在本地开发环境进行测试的时候需要给PHP添加rdkafka扩展，使用PHP作为producer或者cosumer，在此纪录一下rdkafka的安装过程。 扩展下载地址：http://pecl.php.net/package/rdkafka 根据自身PHP版本阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/8962804.html
        )

    [1] => Array
        (
            [date] => 2018年2月24日
            [title] => 在亚马逊aws服务器上添加Google BBR支持
            [abstract] => 摘要: 参考文章： https://51.ruyo.net/2783.html http://blog.csdn.net/VgFengYe/article/details/78609040 官方 quick start文档：https://github.com/google/bbr/blob/master/阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/8466232.html
        )

    [2] => Array
        (
            [date] => 2018年2月22日
            [title] => PHP static关键字和self关键字的区别
            [abstract] => 摘要: 在PHP的一个类中，带有static关键字的方法和属性被称为静态方法和静态属性，这样的方法和属性可以通过类直接访问，而不需要通过类对应的实例来进行访问，在类中访问静态变量以及静态属性的时候，可以使用self关键字和static关键字，两种访问方式看起来似乎没有区别，但是实际上还是不一样的 运行之后的阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/8459899.html
        )

    [3] => Array
        (
            [date] => 2018年2月6日
            [title] => linux系统mysql忘记密码处理
            [abstract] => 摘要: 最近开始重新拾掇自己优惠时贪便宜买的一台京东云主机，然而早已经将当年集成环境一键安装时设置的mysql密码给忘了。 于是度娘了解决办法，大致分为以下步骤： 结果执行之后报这个错误 “Unknown column 'password' in 'field list'”。 后查询得知mysql在5.7版阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/8424672.html
        )

    [4] => Array
        (
            [date] => 2018年1月25日
            [title] => PHP7 新增加的两种运算符
            [abstract] => 摘要: 太空舱运算符: 空合并运算符：阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/8352383.html
        )

    [5] => Array
        (
            [date] => 2017年4月25日
            [title] => PHP trait 特性
            [abstract] => 摘要: trait是PHP自5.4版本之后加入的一种新的代码复用机制，是一种细粒度代码复用的方法。官方文档对于trait给出的解释是： 自 PHP 5.4.0 起，PHP 实现了一种代码复用的方法，称为 trait。 Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/6763388.html
        )

    [6] => Array
        (
            [date] => 2017年1月23日
            [title] => PHP 字符串拆分函数
            [abstract] => 摘要: function str_split_utf8($str) { $split = 1; $array = array(); for ($i = 0; $i 127) { if ($value >= 192 && $value = 224 && $value = 240 && $value <= 247) { ...阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/6344173.html
        )

    [7] => Array
        (
            [date] => 2016年10月25日
            [title] => Windows 环境下php安装openssl证书
            [abstract] => 摘要: 新的电脑安装了PHP、设置好环境变量之后安装了composer，想要通过composer安装Yii2，结果出现了如下报错： 检查发现php.ini里面的extension=php_openssl.dll已开启,如提示所说问题的原因是证书认证失败。 解决方法： http://curl.haxx.se/阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/5996770.html
        )

    [8] => Array
        (
            [date] => 2016年7月21日
            [title] => PHP 按照多个键值给数组分组合并
            [abstract] => 摘要: 简介： $array 为一堆数组，各数组键值为固定 $keys为分组依据，在$array中按照$keys所指定的键值将数组分组，并且将除$keys指定键值对应的值以外的值合并 输出：阅读全文
            [url] => https://www.cnblogs.com/jackiebao/p/5691094.html
        )

)
```

得到这样格式化的数据就很方便进行进一步的处理了，而最基础的爬虫功能也就实现了，实际上是非常简单的。

