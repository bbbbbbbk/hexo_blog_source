---
title: PHP 爬虫体验（三） - 使用PHP + puppeteer爬取js动态渲染的页面内容
date: 2019-03-14 17:19
categories:
- 学习笔记
tags:
- nodejs
- php
---
之前写的两篇爬虫体验基本上涵盖了一般的Html页面提取场景，但是有些时候，如果目标页面不是纯静态的页面，而是使用js动态渲染的页面（比如[one](http://wufazhuce.com/)），之前的爬虫就不好使了，这种时候就要借助一些其他工具来进行实现。

一般爬取动态页面的思路是通过软件模拟浏览器行为获取到渲染后的页面镜像，然后再对渲染后的页面进行分析，常用的工具有selenium，phantomJs，puppeteer等，通过对项目维护程度、对PHP友好度的对比，我选用的是puppeteer。

根据官方介绍，谷歌在2017年开发了自家Chrome浏览器的Headless特性，puppeteer便是这个时候诞生的，它的原理是通过调用Chrome DevTools开放的接口与Chrome通信，将浏览器开放接口进行封装，方便用户调用，可以很容易地实现浏览器行为的模拟。

尝试一下puppeteer，安装起来其实非常简单：

```bash
npm i puppeteer
```

 根据官方API写了example.js进行测试：

```javascript
async function start(){
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('http://wufazhuce.com');
  return page.content();
};

(async () => {
  const a = await start();
  process.stdout.write(a);
})();
```

执行node example.js便可以看到控制台输出了渲染完成之后的页面Html，这个时候便能够使用php的fopen读取stdout获取到html文本进行下一步处理了。

在github上面查找相关支持，发现有[spatie/browsershot](https://github.com/spatie/browsershot)这个项目直接把操作步骤封装好了，这样便可以使用puppeteer进行动态生成html内容的获取，然后继续使用dom-crawler来获取想要抓取的内容了：

```php
$this->crawler = new Crawler();
$html = Browsershot::url($this->url)
    ->setOption('args', [
        '--no-sandbox',
        '--disable-setuid-sandbox'
    ])
    ->bodyHtml();
$this->crawler->addHtmlContent($html);
```

 

 

