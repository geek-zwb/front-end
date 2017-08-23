> 这里做一个备忘。以下内容来自[css box-shadow](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-shadow)

## box-shadow
```
box-shadow: 水平阴影的位置 垂直阴影的位置 模糊距离 阴影的大小 阴影的颜色 阴影开始方向（默认是从里往外，设置inset就是从外往里）;
```

### 摘要
```
初始值                         none
适用元素                     all elements. It also applies to ::first-letter
是否是继承属性	          否
适用媒体	          visual
计算值	          any length made absolute; any specified color computed; otherwise as specified
Animation type	 a shadow list
正规顺序	          the unique non-ambiguous order defined by the formal grammar
```

### 语法
```css
/* offset-x | offset-y | color */
box-shadow: 60px -16px teal;

/* offset-x | offset-y | blur-radius | color */
box-shadow: 10px 5px 5px black;

/* offset-x | offset-y | blur-radius | spread-radius | color */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

/* inset | offset-x | offset-y | color */
box-shadow: inset 5em 1em gold;

/* 两组取值阴影方向相同可叠加，如都为inset 或都不为 inset. Any number of shadows, separated by commas */
box-shadow: 3px 3px red, -1em 0 0.4em olive;
```

### 取值
```
inset
默认阴影在边框外。
使用inset后，阴影在边框内（即使是透明边框），背景之上内容之下。

<offset-x> <offset-y>
这是头两个 <length> 值，用来设置阴影偏移量。<offset-x> 设置水平偏移量，如果是负值则阴影位于元素左边。 <offset-y> 设置垂直偏移量，如果是负值则阴影位于元素上面。可用单位请查看 <length> 。
如果两者都是0，那么阴影位于元素后面。这时如果设置了<blur-radius> 或<spread-radius> 则有模糊效果。

<blur-radius>
这是第三个 <length> 值。值越大，模糊面积越大，阴影就越大越淡。 不能为负值。默认为0，此时阴影边缘锐利。

<spread-radius>
这是第四个 <length> 值。取正值时，阴影扩大；取负值时，阴影.收缩。默认为0，此时阴影与元素同样大。

<color>
相关事项查看 <color> 。如果没有指定，则由浏览器决定——通常是color的值，不过目前Safari取透明。
```