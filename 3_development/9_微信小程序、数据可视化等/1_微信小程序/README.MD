#  微信小程序
 [官方的开发文档](https://mp.weixin.qq.com/debug/wxadoc/dev/)已经很清晰了。如果会`react` or `vue` or `angular` 的话，对着文档就能直接撸了。

 这里仅做一些开发中遇到的问题记录。

 [微信小程序 pratice](https://github.com/geek-zwb/303YY)

 1. 图片高度自适应问题
 ```
 .image {
    width: 100%;
    height: auto;
 }
 // 类似上面的这种样式代码是不能工作的。 如果要实现高度自适应，需要对 image 设置 mode。
 # index.wxml
 <image class="image" src="{{imgPath}}" mode="widthFix" />
 # index.wxss
 .image {
    width: 100%;
 }
```
**充分说明认真看文档的重要性**

2. 引用本地图片地址设置问题

![项目结构](https://github.com/geek-zwb/303YY/blob/develop/screenshots/WechatIMG11.jpeg?raw=true)

如： 在`pages/home`中，想要使用`image`下的`avatar.png`。有以下方法：
- home.wxss
```
// 在 .wxss 中是不能使用本地图片的。所以你可以：引入外部网址；使用base64;
// 不过，我测试结果是本地开发环境 base64 ok, 微信线上却不行，大概我的 base64有错？
```

- home.wxml
```
// 路径是相对于项目根目录的。用如"../../image/avatar.png", 本地开发环境正常，线上gg
<image src="/image/avatar.png">
```

- require
```
// 做梦呢你。Maybe on the way? 要不你去微信团队里整一波。
```