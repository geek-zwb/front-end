## 理解 CSS3 `transform`
`transform` 属性允许你修改CSS视觉格式模型的坐标空间。包括：
- `translate (转换)`
- `rotate (旋转)`
- `scale (缩放)`
- `skew (倾斜)`

在此基础上, 可以分别对`x`和`y`方向单独定义。如: `translatex()`、`translatey()`，以此类推。

> 高能预警： `transform` 只对 `block` 级元素生效！！！ `transform-origin` 对应着矩阵的中心点， 所有动作都围绕于此。

---


- `translate (转换)`
```css
<translate()> = translate( <length-percentage> [, <length-percentage> ]? )
<translateX()> = translateX( <length-percentage> )
<translateY()> = translateY( <length-percentage> )

// 3d 下才有
<translate3d()> = translate3d( <length-percentage> , <length-percentage> , <length> )
<translateZ()> = translateZ( <length> )

// 说明： `<length-percentage> = <length> | <percentage>`
```
第一个参数： 表示x轴方向上的移动， 长度单位值， 如 100px。 也可以是百分比数值，如 50%。
第二个参数(可选)： 表示y轴方向上的移动， 长度单位值， 如 100px。 也可以是百分比数值，如 50%。

> **Attention:**  负方向上的转换使用负值，如 -100px; 百分比值是相对盒子本身的宽度(对应x)与高度(对应y)。

- `rotate (旋转)`
```css
<rotate()> = rotate( <angle> )

// 3d 下才有
<rotate3d()> = rotate3d( <number> , <number> , <number> , <angle> )
<rotateX()> = rotateX( <angle> )
<rotateY()> = rotateY( <angle> )
<rotateZ()> = rotateZ( <angle> )
```
参数: 角度值， 如 `30deg` 。

- `scale (缩放)`
```css
<scale()> = scale( <number> [, <number> ]? )
<scaleX()> = scaleX( <number> )
<scaleY()> = scaleY( <number> )

// 3d 下才有
<scale3d()> = scale3d( <number> , <number> , <number> )
<scaleZ()> = scaleZ( <number> )
```
第一个参数： 表示x轴方向上的缩放， 缩放倍数， 如 `scale(2, 1)` 表示在x轴方向上放大2倍 。
第二个参数(可选)： 表示y轴方向上的缩放， 缩放倍数， 如 `scale(1, 2)` 表示在x轴方向上放大2倍 。

> **Attention: 省略第二个参数表示所有方向上同时缩放， 如 `scale(2)` 在2d 下表示 x 轴和 y 轴同时放大2倍**

- `skew (倾斜, 拉伸)`
```
<skew()> = skew( <angle> [, <angle> ]? )
<skewX()> = skewX( <angle> )
<skewY()> = skewY( <angle> )
```
第一个参数： 表示x轴方向上的倾斜， 角度， 如 `skew(90deg)` 表示在x轴方向上倾斜90度 。
第二个参数(可选)： 表示y轴方向上的倾斜， 角度， 如 `skew(0, 90deg)` 表示在y轴方向上倾斜90度。


## 放大招了
> 如果你明察秋毫的话，会发现对所有实例的解释都是错的，或者说不准确的。

深究一下原理，transform 有一个隐藏的 boss 一直没被发现。没错，就是 `matrix`。

- `matrix (矩阵)`
```
transform:  matrix(a, c, b, d, tx, ty)

/* a, b, c, d 创建了变形矩阵
   ┌     ┐
   │ a b │
   │ c d │
   └     ┘
   tx, ty是变形的值 .  */
```
关于二维矩阵
```
/* a, b, c, d 创建了变形矩阵
   ┌       ┐    ┌   ┐     ┌             ┐
   │ a b e │    | x |     │ ax + by + e │
   │ c d f │ *  | y | =   │ cx + dy + f │
   │ 0 0 1 │    | 1 |     │  0 +  0 + 1 │
   └       ┘    └   ┘     └             ┘
   tx, ty是变形的值 .  */
```

`translate`、`rotate`、`scale`、`skew` 这些动作都是经过矩阵变换来的。

`matrix`的几个参数中， `a` 和 `d` 决定了缩放， `c` 和 `b`决定了倾斜， `tx`、`ty`决定了移动。

具体的解析可以查看[理解CSS3 transform中的Matrix(矩阵)](http://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)

所以上面的实例应该解释成这样:
- `translate(100px)` x轴右移了100px。老大动了，小弟只能跟着跑。

- `rotate(45deg)` 绕着`transform-origin(不额外指定的话是正中心)`旋转了45°

- `scale(2, 1)` x轴长度被拉长了两倍。

- `skew(45deg)` x轴顺时针倾斜了45°

然而，还有一个问题： `skew`无法实现翻转！！！ `skew(-180deg)`并不能改变x轴的指向。这一点可以通过改变`matrix`中`c`,`b`领会。

所以，如果想要实现翻转可以通过改变a和d实现
```
// 左右翻转
matrix(-1, 0, 0, 1, 0, 0)

// 上下翻转
matrix(1, 0, 0, -1, 0, 0)
```

嗯，卧槽，还是很懵x 。。。我也很无奈啊~~~

### 参考
- [transform](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)
- [理解CSS3 transform中的Matrix(矩阵)](http://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)



