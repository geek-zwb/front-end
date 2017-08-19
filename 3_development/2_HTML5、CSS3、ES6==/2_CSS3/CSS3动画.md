# CSS3 动画
> css 的动画出来这么久了， 虽然还未成为标准， 但是主流浏览器都已经支持。说来忏愧， 在写这篇文章之前，很少使用这个新特性更别说研究了。或许是出于对新生事物的恐惧， 或许是因为畏难情绪，总之，它们就是使你到今天还不出众的主要因素。



## animations
> CSS3 animations allows animation of most HTML elements without using JavaScript or Flash!

animation 的本质是他把一个盒子的css样式根据配置(时间，效果等)转到了另一个CSS样式。

## 。。。

整理，查询笔记时看到 ruanyifeng 的 [CSS动画简介](http://www.ruanyifeng.com/blog/2014/02/css_transition_and_animation.html)。 自觉写不到这么清晰，所以就不班门弄斧了。

但是将其转过来， 作为知识整理，好供以后自己查阅。

---
---

# CSS动画简介
本文介绍CSS动画的两大组成部分：transition和animation。我不打算给出每一条属性的详尽介绍，那样可以写一本书。这篇文章只是一个简介，帮助初学者了解全貌，同时又是一个快速指南，当你想不起某一个用法的时候，能够快速地找到提示。
我的主要参考资料是，2013年10月，Lea Verou在JSConf.Asia上面的演讲《[CSS in the 4th Dimension》](http://2013.jsconf.asia/blog/2013/10/31/jsconfasia-2013-lea-verou-css-in-the-4th-dimension-not-your-daddys-css-animations)。那是一个非常棒的演讲，有视频和幻灯片，强烈推荐。

## 第一部分：CSS Transition

### 1.1 基本用法
在CSS 3引入Transition（过渡）这个概念之前，CSS是没有时间轴的。也就是说，所有的状态变化，都是即时完成。

[Have a try](https://jsfiddle.net/ruanyf/XfG4Y/1/embedded/result/)

https://jsfiddle.net/ruanyf/XfG4Y/1/embedded/result/

上面是一个演示，当鼠标放置于缩略图之上，缩略图会迅速变大。注意，缩略图的变大是瞬间实现的。下面是代码，相当简单。
```
img{
    height:15px;
    width:15px;
}

img:hover{
    height: 450px;
    width: 450px;
}
```

`transition`的作用在于，指定状态变化所需要的时间。
```
img{
    transition: 1s;
}
```
上面代码指定，图片放大的过程需要1秒，效果如下。

[Have a try](http://jsfiddle.net/ruanyf/mWaM7/3/embedded/result)

http://jsfiddle.net/ruanyf/mWaM7/3/embedded/result

我们还可以指定transition适用的属性，比如只适用于`height`。
```css
img{
    transition: 1s height;
}
```
这样一来，只有height的变化需要1秒实现，其他变化（主要是width）依然瞬间实现，效果如下。

[Have a try](http://jsfiddle.net/ruanyf/mWaM7/2/embedded/result)

http://jsfiddle.net/ruanyf/mWaM7/2/embedded/result

### transition-delay
在同一行transition语句中，可以分别指定多个属性。
```css
img {
    transition: 1s height, 1s width;
}
```
但是，这样一来，height和width的变化是同时进行的，跟不指定它们没有差别，效果如下。

http://jsfiddle.net/ruanyf/mWaM7/4/embedded/result

我们希望，让`height`先发生变化，等结束以后，再让`width`发生变化。实现这一点很容易，就是为`width`指定一个delay参数。
```
transition: 1s height, 1s 1s width;
```

上面代码指定，width在1秒之后，再开始变化，也就是延迟（delay）1秒，效果如下。

http://jsfiddle.net/ruanyf/mWaM7/5/embedded/result

delay的真正意义在于，它指定了动画发生的顺序，使得多个不同的transition可以连在一起，形成复杂效果。
