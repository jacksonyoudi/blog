---
title: 爬虫的"盗亦有道"-Robots协议
date: 2021-07-31 21:09:03
img: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/k6GFvZ.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/ik61te.jpg
toc: true
mathjax: false
summary: Robots协议（也称为爬虫协议、机器人协议等）的全称是“网络爬虫排除标准”（Robots ExclusionProtocol）
keywords:
-  爬虫
-  crawler
-  robots 
categories: 技术
tags:
-  crawler
-  爬虫
-  robots
---


## 网络爬虫的君子协议
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/3tqLVS.jpg)

### 网络爬虫的尺寸

|小规模，数量小，爬去速度不敏感，requests库|中规模，数据规模较大，爬取速度敏感scrapy库|大规模，搜索引擎,爬取速度关键定制开发|
|-|-|-|
|爬取网页 玩转网页|爬取网站 爬取系列网站|爬取全网|


### 网络爬虫引发的问题
- 性能骚扰
- 法律风险
- 隐私泄露

*网络爬虫的"性能骚扰"*
web服务器默认接受人类访问，受限于编写水平和目的，网络爬虫将会为web服务器带来巨大的资源的开销。
*网络爬虫的法律风险*
服务器上的数据有产权归属，网络爬虫获取数据后牟利将会带来法律的风险。
*网络爬虫的隐私泄露*
网络爬虫可能具备突破简单访问的控制能力，获取被保护的数据，从而泄露个人隐私。

### 网络爬虫的限制
* 来源审查：判断`User-Agent`进行限制，检查来访者HTTP协议头的User-Agent域，只响应浏览器或友好爬虫的访问
* 发布公告： `Robots`协议， 告知所有的爬虫网站的爬虫策略，要求爬虫遵守。

## Robots协议
>Robots协议（也称为爬虫协议、机器人协议等）的全称是“网络爬虫排除标准”（Robots ExclusionProtocol），网站通过Robots协议告诉搜索引擎哪些页面可以抓取，哪些页面不能抓取.

根据协议，网站管理员可以在网站域名的根目录下放一个robots.txt 文本文件，里面可以指定不同的网络爬虫能访问的页面和禁止访问的页面，指定的页面由正则表达式表示。网络爬虫在采集这个网站之前，首先获取到这个文件，然后解析到其中的规则，然后根据规则来采集网站的数据。

注意，这个协议的存在更多的是需要网络爬虫去遵守，而起不到防止爬虫的功能。

### 为什么需要Robots协议
互联网上的网页是通过超级链接互相关联起来的，从而形成了网页的网状结构。爬虫的工作方式就像蜘蛛在网上沿着链接爬来爬去，最基本的流程可以简化如下：

1. 喂给爬虫一堆url，我们称之为种子(seeds)；
2. 爬虫抓取seeds，解析html网页，抽取其中的超级链接；
3. 爬虫接着抓取这些新发现的链接指向的网页。

步骤2和步骤3循环往复。

了解了上面的流程就能发现：对爬虫来说网站非常被动，只有老老实实被抓取的份。

所以，对于网站的管理者来说，就存在这样的需求：

某些路径下是个人隐私或者网站管理使用，不想被搜索引擎抓取，比如说日本爱情动作片；
不喜欢某个搜索引擎，不愿意被他抓取，最有名的就是之前淘宝不希望被百度抓取；
小网站使用的是公用的虚拟主机，流量有限或者需要付费，希望搜索引擎抓的温柔点；
某些网页是动态生成的，没有直接的链接指向，但是希望内容被搜索引擎抓取和索引。

网站内容的所有者是网站管理员，搜索引擎应该尊重所有者的意愿，为了满足以上等等，就需要提供一种网站和爬虫进行沟通的途径，给网站管理员表达自己意愿的机会。有需求就有供应，robots协议就此诞生。

#### 案例
京东的Robots协议
`https://www.jd.com/robots.txt`
```javascript
User-agent: * 
Disallow: /?* 
Disallow: /pop/*.html 
Disallow: /pinpai/*.html?* 
User-agent: EtaoSpider 
Disallow: / 
User-agent: HuihuiSpider 
Disallow: / 
User-agent: GwdangSpider 
Disallow: / 
User-agent: WochachaSpider 
Disallow: /
```
百度的Robots协议
`https://www.baidu.com/robots.txt`
```bash
User-agent: Baiduspider
Disallow: /baidu
Disallow: /s?
Disallow: /ulink?
Disallow: /link?

User-agent: Googlebot
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: MSNBot
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Baiduspider-image
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: YoudaoBot
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou web spider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou inst spider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou spider2
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou blog
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou News Spider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sogou Orion spider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: ChinasoSpider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: Sosospider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?


User-agent: yisouspider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: EasouSpider
Disallow: /baidu
Disallow: /s?
Disallow: /shifen/
Disallow: /homepage/
Disallow: /cpro
Disallow: /ulink?
Disallow: /link?

User-agent: *
Disallow: /
```

上面，`*`代表所有，`/`代表根目录

### Robots协议的写法
既然网络爬虫在爬取一个网站之前，要先获取到这个文件，然后解析到其中的规则，那么，Robots就必须要有一套通用的语法规则。

最简单的robots.txt只有两条规则：

User-agent：指定对哪些爬虫生效
Disallow：指定要屏蔽的网址
先说User-agent，爬虫抓取时会声明自己的身份，这就是User-agent，没错，就是http协议里的User-agent。robots.txt利用User-agent来区分各个引擎的爬虫，比如说google网页搜索爬虫的User-agent为Googlebot。

可能有读者要问了，我怎么知道爬虫的User-agent是什么?你还可以查相关搜索引擎的资料得到官方的数据，比如说百度的爬虫列表是这样的：

|产品名称|对应User-Agent|
|-|-|
|网页搜索|Baiduspider|
|移动搜索|Baiduspider|
|图片搜索|Baiduspider-image|
|视频搜索|Baiduspider-video|
|新闻搜索|Baiduspider-news|
|百度搜索|Baiduspider-favo|
|百度联盟|Baiduspider-cpro|
|商务搜索|Baiduspider-ads|


Disallow 行列出的是要拦截的网页，以正斜线 (/) 开头，可以列出特定的网址或模式。要屏蔽整个网站，使用正斜线即可;要屏蔽某一目录以及其中的所有内容，在目录名后添加正斜线;要屏蔽某个具体的网页，就指出这个网页。

下面介绍一些实例:
允许所有的robot访问
```bash
User-agent: *
Disallow:
```

或者也可以建一个空文件 "/robots.txt" file。

禁止爬虫访问所有目录
```bash
User-agent: *
Disallow: /
```
禁止爬虫访问某些目录
```bash
User-agent: *
Disallow: /a/
Disallow: /b/
Disallow: /c/
```
禁止某些爬虫访问
```bash
User-agent: BadBot
Disallow: /
```
只允许某个爬虫访问
```bash
User-agent: MangCrawler
Disallow:
User-agent: *
Disallow: /
```
我们再来结合两个真实的范例来学习一下。先看这个例子：
```bash
User-agent: Baiduspider
Disallow: /
User-agent: baiduspider
Disallow: /
```
这个是淘宝网的Robots协议内容，相信你已经看出来了，淘宝网禁止百度的爬虫访问。

再来看一个例子：
```bash
User-agent: *
Disallow: /?*
Disallow: /pop/*.html
User-agent: EtaoSpider
Disallow: /
```
这个稍微复杂点，京东有2个目录不希望所有的爬虫来抓。同时，京东完全屏蔽了一淘网的蜘蛛（EtaoSpider是一淘网的蜘蛛）。

## Robots协议进阶知识
**sitemap**
爬虫会通过网页内部的链接发现新的网页。但是如果没有连接指向的网页怎么办?或者用户输入条件生成的动态网页怎么办?能否让网站管理员通知搜索引擎他们网站上有哪些可供抓取的网页?这就是sitemap，最简单的 Sitepmap 形式就是 XML 文件，在其中列出网站中的网址以及关于每个网址的其他数据(上次更新的时间、更改的频率以及相对于网站上其他网址的重要程度等等)，利用这些信息搜索引擎可以更加智能地抓取网站内容。

新的问题来了，爬虫怎么知道这个网站有没有提供sitemap文件，或者说网站管理员生成了sitemap，(可能是多个文件)，爬虫怎么知道放在哪里呢?

由于robots.txt的位置是固定的，于是大家就想到了把sitemap的位置信息放在robots.txt里。这就成为robots.txt里的新成员了。

节选一段google robots.txt：
```bash
Sitemap: http://www.gstatic.com/cultur...
Sitemap: http://www.google.com/hostedn...
```
插一句，考虑到一个网站的网页众多，sitemap人工维护不太靠谱，google提供了工具可以自动生成sitemap。

**meta tag**
其实严格来说这部分内容不属于robots.txt。

robots.txt的初衷是为了让网站管理员管理可以出现在搜索引擎里的网站内容。但是，即使使用 robots.txt 文件让爬虫无法抓取这些内容，搜索引擎也可以通过其他方式找到这些网页并将它添加到索引中。例如，其他网站仍可能链接到该网站。因此，网页网址及其他公开的信息(如指向相关网站的链接中的定位文字或开放式目录管理系统中的标题)有可能会出现在引擎的搜索结果中。如果想彻底对搜索引擎隐身那咋整呢?答案是：元标记，即meta tag。

比如要完全阻止一个网页的内容列在搜索引擎索引中(即使有其他网站链接到此网页)，可使用 noindex 元标记。只要搜索引擎查看该网页，便会看到 noindex 元标记并阻止该网页显示在索引中，这里注意noindex元标记提供的是一种逐页控制对网站的访问的方式。

要防止所有搜索引擎将网站中的网页编入索引，在网页的部分添加：
```vbscript-html
<meta name="robots" content="noindex">
```
这里的name取值可以设置为某个搜索引擎的User-agent从而指定屏蔽某一个搜索引擎。

除了noindex外，还有其他元标记，比如说nofollow，禁止爬虫从此页面中跟踪链接。详细信息可以参考Google支持的元标记，这里提一句：noindex和nofollow在HTML 4.01规范里有描述，但是其他tag的在不同引擎支持到什么程度各不相同，还请读者自行查阅各个引擎的说明文档。

**Crawl-delay**
除了控制哪些可以抓哪些不能抓之外，robots.txt还可以用来控制爬虫抓取的速率。如何做到的呢?通过设置爬虫在两次抓取之间等待的秒数。这种操作可以进行缓解服务器压力。
```bash
Crawl-delay:5
```
表示本次抓取后下一次抓取前需要等待5秒。

注意：google已经不支持这种方式了，在webmaster tools里提供了一个功能可以更直观的控制抓取速率。

这里插一句题外话，几年前曾经有一段时间robots.txt还支持复杂的参数:Visit-time，只有在visit-time指定的时间段里，爬虫才可以访问;Request-rate: 用来限制URL的读取频率，用于控制不同的时间段采用不同的抓取速率。后来估计支持的人太少，就渐渐的废掉了，目前google和baidu都已经不支持这个规则了，其他小的引擎公司貌似从来都没有支持过。

### Robots协议的遵守方式
网络爬虫：
自动或人工识别rotbots.txt，再进行内容爬取
约束性:
Robots协议是建议但非约束性，网络爬虫可以不遵守，但存在法律风险。

### 对Robots协议的理解
|访问量小:可以遵守<br>访问量较大：建议遵守|非商业且偶尔:建议遵守<br>商业利益:必须遵守|必须遵守|
|-|-|-|
|爬取网页 玩转网页|爬取网站 爬取系列网站|爬取全网|
`原则`：类人行为可以不参考Robots协议。

### 防君子不防小人（君子协议）
Robots协议不是什么技术壁垒，而只是一种互相尊重的协议，好比私家花园的门口挂着“闲人免进”，尊重者绕道而行，不尊重者依然可以推门而入。目前，Robots协议在实际使用中，还存在一些问题。

### 缓存
robots.txt本身也是需要抓取的，出于效率考虑，一般爬虫不会每次抓取网站网页前都抓一下robots.txt，加上robots.txt更新不频繁，内容需要解析。通常爬虫的做法是先抓取一次，解析后缓存下来，而且是相当长的时间。假设网站管理员更新了robots.txt，修改了某些规则，但是对爬虫来说并不会立刻生效，只有当爬虫下次抓取robots.txt之后才能看到最新的内容。尴尬的是，爬虫下次抓取robots.txt的时间并不是由网站管理员控制的。当然，有些搜索引擎提供了web 工具可以让网站管理员通知搜索引擎那个url发生了变化，建议重新抓取。注意，此处是建议，即使你通知了搜索引擎，搜索引擎何时抓取仍然是不确定的，只是比完全不通知要好点。至于好多少，那就看搜索引擎的良心和技术能力了。

### ignore
不知是无意还是有意，反正有些爬虫不太遵守或者完全忽略robots.txt，不排除开发人员能力的问题，比如说根本不知道robots.txt。另外，本身robots.txt不是一种强制措施，如果网站有数据需要保密，必需采取技术措施，比如说：用户验证，加密，ip拦截，访问频率控制等。



![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/nGoP78.jpg)

### 恶意爬虫
在互联网世界中，每天都有不计其数的爬虫在日夜不息地爬取数据，其中恶意爬虫的数量甚至高于非恶意爬虫。遵守Robots协议的爬虫才是好爬虫，但是并不是每个爬虫都会主动遵守Robots协议。

恶意爬虫可以带来很多潜在威胁，比如电商网站的商品信息被爬取可能会被竞争对手利用，过多的爬虫还会占用带宽资源、甚至导致网站宕机。

反恶意爬虫是一件漫长而艰巨的任务，如果依靠自身实力难以解决，可以借助岂安科技的业务风险分析平台 WARDEN 来反恶意爬虫，根据自己的需求来定制功能。
