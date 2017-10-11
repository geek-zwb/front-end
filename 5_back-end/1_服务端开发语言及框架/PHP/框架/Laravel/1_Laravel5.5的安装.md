## 服务器要求
- PHP >= 7.0.0
- PHP OpenSSL 扩展
- PHP PDO 扩展
- PHP Mbstring 扩展
- PHP Tokenizer 扩展
- PHP XML 扩展

按照 [1_php安装与基础环境配置](https://github.com/geek-zwb/front-end/blob/master/5_back-end/1_%E6%9C%8D%E5%8A%A1%E7%AB%AF%E5%BC%80%E5%8F%91%E8%AF%AD%E8%A8%80%E5%8F%8A%E6%A1%86%E6%9E%B6/PHP/%E5%9F%BA%E7%A1%80/1_php%E5%AE%89%E8%A3%85%E4%B8%8E%E5%9F%BA%E7%A1%80%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE.md) 安装的 php 环境符合上述全部要求。 

## 安装 Composer
### Globally
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
mv composer.phar /usr/local/bin/composer
```

ok, now, you can run `composer` in order to run Composer

## 通过 Composer 创建项目
```
composer create-project --prefer-dist laravel/laravel blog
```