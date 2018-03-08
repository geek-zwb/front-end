[原文地址](http://blog.nsfocus.net/xss-advance/)

> “XSS是跨站脚本攻击(Cross Site Scripting)，为不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。” ——《百度百科》

## 一、XSS概述

按形式分 | 按介质分 | 按接口分
---|---|---
反射型XSS | JSXSS | DOM base XSS
存储型XSS | FlashXSS | 非DOM XSS

XSS的实质其实是HTML代码与Javscript代码的注入。但由于XSS的攻击对象是与客户对等的Browser端，因此常常不被开发者所重视。

一般意义上的XSS通常可以用简单的方法检测出来：当用户输入中某个参数的全部或其中一部分，原封不动地在源代码里出现时，我们就可以认为这个参数存在XSS漏洞。

![通过服务器但不仅过处理](http://s5.51cto.com/wyfs02/M00/06/F8/wKiom1nBKAnTRTi6AACeURcMiLg547.png)

我们可以在扫描器的帮助下铲除绝大多数的常规XSS，因为扫描器所擅长的模式识别很容易从HTML代码里找出自己刚刚输入的东西…

![常规XSS](http://s2.51cto.com/wyfs02/M00/A5/A8/wKioL1nBJASCpMftAAI371xMrx4523.jpg)

直到某一天，出现了一类不会在源代码中回显的XSS…此时我们才意识到，原来XSS并不像想象中的那么简单

![](http://s5.51cto.com/wyfs02/M01/A5/A8/wKioL1nBJBDxopHQAAG4Avpd6Ys210.jpg)


## 二、文件对象模型简介
### 2.1 DOM简介

文档对象模型(Document Object Model)，即大名鼎鼎的DOM。

DOM可以被认为是一种通过将页面元素以对象的树形方式表现，以便由Javascript组织处理的实现方法。

![](http://s4.51cto.com/wyfs02/M02/A5/A8/wKioL1nBJEHwDgRXAADRJU2LLAw747.jpg)

### 2.2 常用的DOM方法

用户可通过 JavaScript (以及其他编程语言)对 HTML DOM 进行访问。所有 HTML 元素被定义为对象，而编程接口则是对象方法和对象属性。

![常用DOM方法](http://s5.51cto.com/wyfs02/M01/06/F8/wKiom1nBJIzjHGvyAAK7-vBYyO8428.jpg)

### 2.3 四个重要的DOM属性

nodeName 属性：规定节点的名称。

- nodeName 是只读的
- 元素节点的 nodeName 与标签名相同
- 属性节点的 nodeName 与属性名相同
- 文本节点的 nodeName 始终是 #text
- 文档节点的 nodeName 始终是 #document


 nodeValue 属性：规定节点的值。
- 元素节点的 nodeValue 是 undefined 或 null
- 文本节点的 nodeValue 是文本本身
- 属性节点的 nodeValue 是属性值

nodeType 属性：返回节点的类型。
- nodeType 是只读的。

innerHTML 属性：获取元素内容，如：
```JavaScript
<p id="test">Test</p>
<script>
    var txt = document.getElementById('test').innerHTML;
    // txt Test
</script>
```

`innerHTML`可以被赋值，也可读，因此是被引用次数最多的对象属性，同时也是最容易产生安全问题的对象属性。

### 2.4 输入一般在哪里

Location 对象属性

属性 | 描述
--- | ---
hash | 设置或返回从#号开始的URL（锚）
host | 设置或返回主机名和当前URL的端口号
hostname | 设置或返回当前URL的主机名
href | 设置或返回完整的URL
pathname | 设置或返回当前URL的路径部分
port | 设置或返回当前URL的端口号
protocol | 设置或返回当前 URL 的协议
search | 设置或返回从 ? 开始的URL（查询部分）

JS通常会通过调用DOM内置对象location来获得用户输入，如引用参数切分可使用location.search，引用完整URL可使用location.href等。

## 三、DOM base XSS

### 3.1 两个典型的DOM过程

1)反射型DOM base XSS
用户输入带有参数的URL -> JavaScript处理URL并获取参数 -> 通过DOM调用参数对页面进行排版 -> 通过DOM动态输出到页面上

2）存储型DOM base XSS
用户输入带有参数的 URL 或 BODY 域数据 -> 服务器将参数存入数据库 -> 通过JSON格式返回参数到页面 -> 通过DOM调用参数进行排版 -> 通过DOM动态输出到页面上

### 3.2 需要了解的知识点
知识点 | 常规XSS | DOM base XSS
JavaScript | 了解语法即可 | 熟练掌握
HTML | 了解语法即可 | 熟练掌握
CSS | 不需了解 | 熟练掌握
DOM | - | 熟练掌握
Encode/Decode | 简单了解即可 | 熟练掌握
Other | -- | 突变、优先级、宽字节、输入识别、模式识别...

比起常规XSS， DOM base XSS 的漏洞发现过程更像是代码审计..

### 3.3 mxss
如果用户输入“出入”DOM两次以上，则有可能触发更高级别的DOM base XSS：mXSS
![](http://s2.51cto.com/wyfs02/M02/06/F8/wKiom1nBJfbihobeAAFG8GdEyeU735.jpg)

容易出现mXSS的场景
```
a.innerHTML = b.innerHTML;
a.innerHTML += 'additional content';
a.insertAdjacentHTML('beforebegin', b.innerHTML);
document.write(b.innerHTML);
```
由于innerHTML会将HTML实体，CSS转义字符，ANSI编码等进行反转义，因此原来被转义之后认为安全的用户输入很有可能在绕过过滤器之后被反转回去。

## 四、过滤器绕过

### 4.1 常规XSS的过滤器绕过

### 4.2 过滤器绕过方法
![](http://s3.51cto.com/wyfs02/M00/A5/A8/wKioL1nBJjXiKZNoAAHdeX5deTU430.jpg)

### 4.3 XSS修复建议

(1)不含有富文本编辑器(自定义样式)且没有使用DOM的站点：

输入：过滤双引号，单引号，左右尖括号，分号。
输出：对上述字符进行HTML实体编码即可。

(2)不含有富文本编辑器(自定义样式)但使用DOM的站点：

输入：在DOM中转义双引号，单引号，左右尖括号，分号。
输出：在输出之前进行编码，如：innerHTML=encodeHTML(output)

(3)含有富文本编辑器(自定义样式)但没有使用DOM的站点：

输入：过滤双引号，单引号，分号。
输出：对上述字符进行HTML实体编码即可。

(4)含有富文本编辑器(自定义样式)且使用DOM的站点：

没办法…指哪儿修哪儿…

## 其他
写得比较详细的关于XSS的文章: [浅谈XSS攻击的那些事](https://zhuanlan.zhihu.com/p/26177815)