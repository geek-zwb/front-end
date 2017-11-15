before
```
yum install gcc gcc++ 
yum -y install openssl openssl-devel
```

其他所需更新模块
```
yum -y install libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel mysql pcre-devel curl curl-devel libxslt-devel
```

下载 php7 源码包
```
wget http://cn2.php.net/get/php-7.0.4.tar.gz/from/this/mirror
```

解压
```
tar -zxvf mirror
```

```
cd php-7.0.4
```

编译安装到 /usr/local/php
```
./configure --prefix=/usr/local/php --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-xmlrpc --with-xsl --with-zlib --enable-fpm --enable-bcmath --enable-libxml --enable-inline-optimization --enable-gd-native-ttf --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvsem --enable-xml --enable-zip
```

没有错误， 执行安装
```
make &&  make install
```

配置文件
```
# cp php.ini-development /usr/local/php/lib/php.ini
# cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
# cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
# cp -R ./sapi/fpm/php-fpm /etc/init.d/php-fpm
```

启动
```
/etc/init.d/php-fpm
```

check
```
ps aux | grep php
```

nginx 配置

`vi /etc/nginx/nginx.conf`
```
# ...

http {
    # ...
    
    server {
        listen 80;
        server_name localhost;
        root /var/www;
        index index.html index.htm index.php; # 加上 index.php
        chartset utf-8;
        
        # 解析 php
        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000; # php-fpm 的默认端口是 9000
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
    
    # ...
}
```

重启 nginx
```
/etc/init.d/nginx restart
```

配置 php 命令行
```
vi /etc/profile.d/useralias.sh

# 写入如下内容
alias php='/usr/local/php/bin/php'

# 保存退出，使其生效
source /etc/profile.d/useralias.sh

# 检验
php -v

PHP 7.0.4 (cli) (built: Nov 14 2017 01:58:25) ( NTS )
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
```

打开php 错误提示, 如果需要的话
```
vi /usr/local/php/etc/php-fpm.conf
// 最后加上一行
php_flag[display_errors] = on

vi /usr/local/php/lib/php.ini
// 检查错误信息是否打开
display_errors = On
error_reporting = E_ALL
```
修改完配置需要重启
```
/etc/init.d/php-fpm restart
/etc/init.d/nginx restart
```