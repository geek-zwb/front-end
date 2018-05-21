apache 部署配置
`.htaccess 文件`
```
RewriteEngine On
# 除了config之外的js和css都加上gzip头部 同时强缓存
<FilesMatch "^((?!config).)*(js|css)$">
Header set Vary "Accept-Encoding"
Header set Content-Encoding "gzip"
#Header set Cache-Control "max-age=315360000"
</FilesMatch>
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-l
# 多入口路由规则重写
RewriteRule ^ /index.html [QSA]
RewriteRule ^(antiFraud\.html)/.* /$1
RewriteRule ^(riskControl\.html)/.* /$1
```