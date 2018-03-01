> 项目中需要多入口的情况，如博客(有前台和后台)，在前端逻辑上关联不大，或者是由不同的用户入口(又在一个域名下)

## 实际需求
现在有三个块，一、官网首页 二、会员(客户)入口 三、管理员后台。

项目前期在一个域名下。如果做成一个SPA，不同的用户进入的是不同的页面，则会加载很多不需要的资源。同时，官网一般需要SEO（seo需求直接用原生做，或者SSR，这是多入口外的另一个需要解决问题，这里不作讨论），而后台是不需要的。所以分成多个入口。

## 项目准备:

项目中我都是通过 create-react-app 脚手架生成的。将其rejet后可自行配置。

方法自行google。
```
webpack 版本：2.6.1
```

## 配置为多入口可用
### 1. 新建三个入口文件
```
# cd src
# touch index.js vip.js admin.js
```
    
### 2. 新建入口配置文件
```
// 在项目根目录
touch .env.local

// 写入内容
REACT_APP_ENTRY=["vip","admin","index"]
```

### 3. 修改 `webpack.config.dev.js`
- 在 `config/webpack.config.dev.js`中新增两个function:
```
// 配置多入口
function setEntryConfig(arrayString) {
  let entryArray = JSON.parse(arrayString);
  if (!Array.isArray(entryArray)) {
    console.log('请确保entry是一个数组');
    return {};
  }
  let entry = {};
  entryArray.map(function (item) {
    entry[item] = [
      require.resolve('./polyfills'),
      require.resolve('react-dev-utils/webpackHotDevClient'),
      //合成入口文件
      path.resolve(paths.appSrc, item + '.js')
    ];
  });
  return entry;
}
function setHtmlPluginConfig(arrayString) {
  let entryArray = JSON.parse(arrayString);
  if (!Array.isArray(entryArray)) {
    console.log('请确保entry是一个数组');
    return [];
  }
  let plugin = [];
  entryArray.map(function (item) {
    plugin.push(
      new HtmlWebpackPlugin({
        inject: true,
        chunks: [item],
        template: paths.appHtml,
        filename: item + '.html'
      })
    );
  });
  return plugin;
}
```
- 修改配置
```
module.exports = {
    // ...
    // entry 项修改
    entry: setEntryConfig(env.raw.REACT_APP_ENTRY),
    // ...
    
    plugins: [
        // ...
        /*new HtmlWebpackPlugin({
            inject: true,
            template: paths.appHtml,
            }),*/
        ...(setHtmlPluginConfig(env.raw.REACT_APP_ENTRY)),
        // ...
    ]
}
```

> `webpack.config.pro.js` 做同上修改

### 4. 修改`webpackDevServer.config.js`
通过上面三个步骤，在pro环境下已经OK了，nginx、Apache或者node相应做处理就👌，如在浏览器请求url`www.test.com/vip.html/login`， 对应返回`vip.html`

在dev环境，还需要修改`webpackDevServer.config.js`，如下:
```
// 改写historyApiFallback项，新增rewrites如下
historyApiFallback: {
      // Paths with dots should still use the history fallback.
      // See https://github.com/facebookincubator/create-react-app/issues/387.
      disableDotRule: true,
      // 指明哪些路径映射到哪个html
      rewrites: [
        // { from: /^\/$/, to: '/index.html' },
        { from: /^\/admin.html/, to: '/admin.html' },
        { from: /^\/vip.html/, to: '/vip.html' },
      ]
    },
```

## 注意项
- 注意目录层次是否正确
- 每个入口内的router格式。比如`vip`对应的`react-router`为`www.test,com/vip.html/xxx`