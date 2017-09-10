诞生于1995，是由Netscape公司的 Brendan Eich 所开发的程序设计语言，命名 LiveScript. Netscape 2.0 发布之后，为了抱上 java 的大腿，更名为 JavaScript。

没过多久， 浏览器的竞争厂商 Microsoft 公司发布了 Jscript (在JavaScript的继承之上引入了 IE 独有的特性。)

为了使语言的实现更趋向标准化， ECMAScript 应运而生。（ECMA: 欧洲计算机制造商协会）

~= JavaScript 这个术语通常涵盖了：
- ECMAScript — 语言的核心部分（变量，函数，循环等），独立于浏览器之外，并可以在其他环境中使用。
- 文档对象模型（DOM）: 参考万维网联盟（W3C）专门创建的 DOM 标准。 该标准是一种独立的（并不依赖于 JavaScript）操作结构化文档的方式。
- 浏览器对象模型（BOM）: 与浏览器环境有关的对象集合。

---

安利一个小技巧：

Mac 上的 JavaScriptCore:

打开终端，输入
```
# 别名
alias jsc='/System/Library/Frameworks/JavaScriptCore.framework/Versions/A/Resources/jsc'

# 启动 JavaScriptCore
jsc
```

或者将别名写入 `~/.profile`文件
```
vi ~/.profile
alias jsc='/System/Library/Frameworks/JavaScriptCore.framework/Versions/A/Resources/jsc'

# 生效
source ~/.profile
```

