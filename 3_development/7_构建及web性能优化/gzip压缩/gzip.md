> gzip 压缩率能达到62%， 可以加快服务端和浏览器的传输。如，1Mb 的 bundle.js 在gzip压缩后变成300Kb左右。

## 动态压缩
### Apache 设置
```
// httpd.conf 增加配置如下， 当然deflate模块需要打开
<IfModule mod_deflate.c>
# 压缩等级（1~9） 6 均衡压缩率和CPU
# 其实并没有卵用，CPU唰的狂飙
DeflateCompressionLevel 6
# 压缩类型 html、xml、php、css、js
SetOutputFilter DEFLATE
AddOutputFilterByType DEFLATE text/html text/plain text/xml application/x-javascript application/x-httpd-php
AddOutputFilter DEFLATE js css
</IfModule>
```

>（其他，没试过）

## 静态压缩（推荐）
### 1. 将静态文件进行gzip压缩
方法一(推荐)： 使用构建工具webpack
```
var CompressionPlugin = require('compression-webpack-plugin');

// 使用该插件进行gzip压缩
module.exports = {
 plugins: [
    new CompressionPlugin({
      asset:
      //'[path].gz[query]', // express使用这个
      '[path].[query]', // apache 这里直接覆盖原js, css
      algorithm: 'gzip',
      test: /\.(js|css)$/, // 压缩js 和 css
      threshold: 10240,
      minRatio: 0.8
    }),
    // ...
    ]
// ...
```
方法二： window上使用gzip.exe。 linux、OS有gzip命令。

### 2.改写路由规则
方法一： 使用express
```
const app = express();
app.use(express.static(path.join(__dirname, 'build')));
```
在构建过程中，我们同时生成了`xx.js`, `xx.css` 及对应的`xx.js.gz`, `xx.css.gz`, 使用express创建一个服务器，将会自动返回压缩后的文件内容。（需要注意的是，原文件和对应的`.gz`文件缺一不可，如果`.gz`文件不存在，则返回原文件，如果只有`.gz`文件，则会发生404错误）。

方法二：apache
```
# 修改项目根目录下的 .hatccess
vim .hatccess

# 增加规则如下
<FilesMatch "\.(js|css)$">
Header set Vary "Accept-Encoding"
Header set Content-Encoding "gzip"
</FilesMatch>

# 下面是我完整的 htaccess (react + browserHistory)
RewriteEngine On
<FilesMatch "^((?!config).)*(js|css)$">
Header set Vary "Accept-Encoding"
Header set Content-Encoding "gzip"
</FilesMatch>
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-l
RewriteRule ^ /index.html [L,QSA]
```