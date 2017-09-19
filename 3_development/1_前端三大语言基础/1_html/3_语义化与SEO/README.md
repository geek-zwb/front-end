# HTML 语义化

## 什么是HTML语义化
HTML语义化就是根据具体内容，选择合适的标签进行代码的编写。便于开发者阅读和写出更优雅的代码，同时让搜索引擎的爬虫能更好的识别。

## Why
1. 有利于SEO:搜索引擎的爬虫是读不懂无语义的span和div的，因此语义化标签能使爬虫抓取更多的有效信息。
2. CSS文件读取失败的准备：万一CSS文件挂了，语义化的HTML也能呈现较好的内容结构与代码结构。
3. 方便其它设备的解析(如屏幕阅读器、盲人阅读器、移动设备)。
4. 便于团队开发和维护。

## 语义标签有哪些？
这里并没有列出全部, 举例个别常用或者值得注意的。其余请查阅 [HTML 元素参考](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
### `a`
> 创建一个到其他网页，文件，同一页面内的位置，电子邮件地址或任何其他URL的超链接。注意`download`这个属性;

### `p`
> 表示文本的一个段落。

### `header` (HTML5)
> 通常被放置在页面或者页面中某个区块元素的顶部，包含整个页面或者区块的标题、简介等信息，起到引导与导航的作用。

**example**
```html
<header>
    <img src="images/logo.png" alt="**" />
    <h1>xxx 系统</h1>
    <nav>
        <!-- 导航 -->
    </nav>
</header>
```

header使用注意：
- 可以是“网页”或任意`section`的头部部分；
- 没有个数限制。
- 如果`hgroup`或`h1-h6`自己就能工作的很好，那就不要用`header`。

### `nav` (HTML5)
> 表示页面的导航，可以通过导航连接到网站的其他页面，或者当前页面的其它部分。

根据HTML5标准，<nav>标签只用于页面的主要导航部分。因为搜索引擎或者屏幕阅读器会根据<nav>标签来确定网站的主体内容，所以并不是任意一组超链接都适合放置在<nav>标签中,我们只要将主要的，基本的链接组放进<nav>即可,对于有辅助性的页脚链接则不推荐使用<nav>标签。

**example**
```html
<header>
   <img src="images/logo.png" alt="**科技" />
   <h1>**信息科技有限公司</h1>
   <nav>
       <li><a href="#">首页</a></li>
       <li><a href="example.html">客户案例</a></li>
       <li><a href="service_one.html">技术服务</a></li>
       <li><a href="aboutus_one.html">关于我们</a></li>
       <li><a href="connection.html">联系我们</a></li>
   </nav>
</header>
```

nav使用注意：
- 用在整个页面主要导航部分上，不合适就不要用nav元素
- HTML5 规范不允许将 `<nav>` 标签嵌套在`<address>`

### `article` (HTML5)
> 表示包含于一个文档、页面、应用程序或网站中的一段独立的内容，可以被独立的发布或者重新使用文章标记标签。

`<article>`元素应该使用在相对比较独立、完整的的内容区块，所以我们可以在一篇博客、一个论坛帖子、一篇新闻报道或者一个用户评论中使用`<article>`元素。通常情况下，一个`<article>`元素包括标题、正文和脚注。和`<nav>`标签一样，该标签同样不能用在`<address>`标签中；
`<article>`标签还可以嵌套使用，但是它们必须是部分与整体的关系。如文章与评论。
**example**
```html
<article>
   <h1>HTML5学习之语义化标签</h1>
   <p>....正文.....</p>
   <article>
       <header>
           <h2>读者评论</h2>
       </header>
       <article>
           <header>
               <h3>评论人：张三</h3>
               <p>评论时间：<time datetime="2017-01-17">2017-2-15 11:45:23</time></p>
           </header>
           <p>张三到此一游</p>
       </article>
   </article>
</article>
```

`article`使用注意：
- 自身独立的情况下：用`article`
- 是相关内容：用`section`
- 没有语义的：用`div`

### `section` (HTML5)
> `section` 标签定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分。

**example**
```html
<body>
    <section>
       <h1>章节一</h1>
        <p>详细内容...</p>
    </section>
    <section>
       <h1>章节二</h1>
        <p>详细内容...</p>
    </section>
</body>
```

attention:
- 一张页面可以用section划分为简介、文章条目和联系信息。不过在文章内页，最好用article。section不是一般意义上的容器元素，如果想作为样式展示和脚本的便利，可以用div。
- 表示文档中的节或者段；
- article、nav、aside可以理解为特殊的section，所以如果可以用article、nav、aside就不要用section，没实际意义的就用div

### `aside` (HTML5)
> 所包含的内容不是页面的主要内容、具有独立性，是对页面的补充。

`<aside>`一般使用在页面、文章的侧边栏、广告、友情链接等区域。

**example**
```html
<article>
   <h1>HTML5学习之语义化标签</h1>
   <p>....正文.....</p>
   <aside>
       <h2>什么是语义化标签</h2>
       <p>语义化标签就是......</p>
   </aside>
</article>
```

### `footer` (HTML5)
> 一般被放置在页面或者页面中某个区块的底部，包含版权信息、联系方式等信息。

跟`<header>`标签一样，`<footer>`标签的使用个数没有限制，可以在任意需要的区块底部使用。

**example**
```html
<footer>
    <small>
        版权所有 © 2017-2018 xxx
    </small>
</footer>
```

### `hgroup`
> 已经从 HTML5 规范中删除。

### `nav`
> HTML导航栏 (<nav>) 描绘一个含有多个超链接的区域，这个区域包含转到其他页面，或者页面内部其他部分的链接列表.

**example**
```html
<nav>
  <ul>
    <li><a href="#">Home</a></li>
    <li><a href="#">About</a></li>
    <li><a href="#">Contact</a></li>
  </ul>
</nav>
```

attention:
- 并不是所有的链接都必须使用<nav>元素,它只用来将一些热门的链接放入导航栏,例如<footer>元素就常用来在页面底部包含一个不常用到,没必要加入<nav>的链接列表.
- 一个网页也可能含有多个<nav>元素,例如一个是网站内的导航列表,另一个是本页面内的导航列表.
- 对于屏幕阅读障碍的人,可以使用这个元素来确定是否忽略初始内容.

### `figure` (HTML5)
> `<figure>`表示一段富文本，可以是一个文章插图、一段代码、一个表格，当这部分转移到附录中或者其他页面时不会影响到主体。通常搭配`<figcaption>`来表述这段富文本的描述/标题，当然，一个`<figure>`下只能有一个`<figcaption>`。

典型应用： 瀑布流
```html
<figure>
  <img
  src="https://developer.cdn.mozilla.net/media/img/mdn-logo-sm.png"
  alt="An awesome picture">
  <figcaption>Fig1. MDN Logo</figcaption>
</figure>
```

### `video` (HTML5)
> 视频

### `audio` (HTML5)
> 音频

### `address` (HTML5)
> HTML 的`<address>`元素可以让作者为它最近的`<article>`或者`<body>`祖先元素提供联系信息。在后一种情况下，它应用于整个文档。

attention
- 当表示一个和联系信息无关的任意的地址时，使用<p>元素而不是<address>元素。
- 这个元素不能包含除了联系信息之外的任何信息，比如出版日期（这应该包含在<time>元素中）。
- 通常，<address>元素可以放在当前section的<footer>元素中，如果存在的话。