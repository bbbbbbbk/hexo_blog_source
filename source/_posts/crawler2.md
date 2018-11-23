---
title: PHP 爬虫体验（二） - 爬取cnblog单篇文章并且自动生成markdown格式hexo文章
date: 2018-11-22 11:26
categories:
- 学习笔记
tags:
- 爬虫
- php
---
自从使用hexo在github page更新博客之后，我每次在cnblog上发布文章，需要手动再更新hexo。hexo使用markdown格式来写文章，手动更新需要对文章本身内容进行转化，做成md文件再进行上传，后来就想到，本身爬虫就可以对页面中的各种元素进行提取，同时markdown使用的是标记语法，那么使用爬虫分析文章元素，提取主要内容并且根据模板自动生成对应的md文件理论上是可行的。

由于我的hexo博客使用的是默认布局，所以在hexo目录下直接执行:

```
hexo new model
```

这样就生成了一个model.md文件，接下来就是把这个文件改装成所需的模板，打开model.md，能看到默认布局hexo文章只有一个简单的front-matter区域用来进行文章变量的指定：

```
---
title: model
date: 2018-11-22 09:49:22
tags:
---
```

按照官方文档，front-matter用于文章变量的指定，本身不会作为markdown被解析，所以模板布局可以分为两个部分：

```
---
{{front-matter}}
---
{{markdown}}
```

接下来就是对文章元素的提取并且转化成相应的hexo文章内容，并且拼接填充至模板当中了。

这里我简单封装了一个MarkdownGenerator类用来把文章转换成markdown文件：

```php
namespace Root;

use GuzzleHttp\Client;
use Symfony\Component\DomCrawler\Crawler;

Class MarkdownGenerator
{
    private $client;

    private $crawler;

    private $url;

    private $assetPath;

    private $contentsArray = [];

    private $categories = [];

    private $tags = [];

    private $title;

    private $dateString;

    private $documentName;

    /** 初始化，会在同目录下生成一个和文档同名的文件夹用来装静态资源
     * MarkdownGenerator constructor.
     * @param $documentName
     */
    public function __construct($documentName)
    {
        $this->documentName = $documentName;
        $this->assetPath = __DIR__ . "/{$this->documentName}/";
        if(!is_dir($this->assetPath)){
            mkdir($this->assetPath, 755);
        }
        $headers = [
            'user-agent' => 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
        ];
        $this->client = new Client([
            'timeout' => 20,
            'headers' => $headers
        ]);
        $this->crawler = new Crawler();
    }

    /** 设置文章url
     * @param string $url
     */
    public function setUrl($url = '')
    {
        $this->url = $url;
    }

    /** 设置文章tags
     * @param array $tags
     */
    public function setTags($tags = [])
    {
        $this->tags = $tags;
    }

    /** 设置文章categories
     * @param array $categories
     */
    public function setCategories($categories = [])
    {
        $this->categories = $categories;
    }

    /**
     * 生成markdown文件
     */
    public function generate()
    {
        $responseContent = $this->client->request('GET', $this->url)->getBody()->getContents();
        $this->crawler->addHtmlContent($responseContent);
        try{
            //获取文章title
            $this->title = trim($this->crawler->filterXPath('//h1[@class="postTitle"]')->text());
            //获取文章发布时间
            $this->dateString = trim($this->crawler->filterXPath('//span[@id="post-date"]')->text());
            //处理文章正文部分
            $this->crawler->filterXPath('//div[@id="cnblogs_post_body"]')->children()->each(function(Crawler $node) {
                $this->contentsArray[] = $this->parseParagraph($node);
            });
            //组装markdown正文部分
            $markdownContent = $this->makeContent();
            $frontMatter = $this->makeFrontMatter();
            $tmplate = file_get_contents('model.md');
            $content = str_replace(['{{front-matter}}','{{markdown}}'],[$frontMatter, $markdownContent], $tmplate);
            file_put_contents($this->documentName.'.md', $content);
        }catch (\Throwable $e){
            print_r($e->getMessage());
        }
    }

    /** 处理段落
     * @param Crawler $node
     * @return mixed|string
     */
    private function parseParagraph(Crawler $node)
    {
        $res = $node->html();
        //替换a标签,替换成markdown当中的格式
        $linkPattern = '#<a\b[^>]+\bhref=\"([^\"]*)\"[^>]*>([\s\S]*?)<\/a>#';
        preg_match_all($linkPattern, $res, $links);
        if(!empty($links[0])){
            foreach ($links[2] as $k => $link){
                $l = "[{$link}]({$links[1][$k]})";
                $res = str_replace($links[0][$k], $l, $res);
            }
        }

        //处理图片，图片这里使用hexo的图片标签,格式为{% asset_img name.format alt %}, 对应图片放在和post目录下和md文件同名文件夹中
        $imgPattern = '#<img\b[^>]+\bsrc="([^"]*)"[^>]+\balt="([^"]*)"[^>]*>#';
        preg_match_all($imgPattern, $res, $imgs);
        if(!empty($imgs[0])){
            foreach ($imgs[2] as $k => $img){
                $imageUrl = $imgs[1][$k];
                //下载图片并放入对应的文件夹内
                $imageName = pathinfo($imageUrl)['basename'];
                $fileName = $this->assetPath . $imageName;
                $image = $this->client->get($imageUrl)->getBody()->getContents();
                file_put_contents($fileName, $image);
                $i = "{% asset_img {$imageName} {$imgs[2][$k]} %}";
                $res = str_replace($imgs[0][$k], $i, $res);
            }
        }
        //处理内嵌代码
        if($node->attr('class') === 'cnblogs_code'){
            $plainCodes = trim($node->text());
            $res = htmlspecialchars("```") ."\n{$plainCodes}\n" . htmlspecialchars("```");
        }
        return $res;
    }

    /** 组装markdown正文部分
     * @return string
     */
    private function makeContent()
    {
        return (implode($this->contentsArray, "\n\n"));
    }

    /** 组装frontMatter部分
     * @return string
     */
    private function makeFrontMatter()
    {
        $res = <<<FM
title: {$this->title}
date: {$this->dateString}
FM;
        if(!empty($this->categories)){
            $res .= "\ncategories:\n- " . implode($this->categories, "\n- ");
        }
        if(!empty($this->tags)){
            $res .= "\ntags:\n- " . implode($this->tags, "\n- ");
        }
        return $res;
    }
}
```

接下来只用实例化generator类，然后设置各项属性，调用generate方法就能够抓取生成markdown文件了：

```php
$generator = new MarkdownGenerator('test');
$generator->setUrl('https://www.cnblogs.com/jackiebao/p/8466232.html');
$generator->setTags(['test1','test2']);
$generator->setCategories(['test_cat']);
$generator->generate();
```

 P.S.本篇文章hexo版本即为该脚本生成。

