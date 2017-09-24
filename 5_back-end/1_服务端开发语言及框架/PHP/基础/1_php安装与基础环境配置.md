> OS： macOS Sierra 10.12
> 工具 brew

先解决个 brew 下载慢的问题。

**法一：** 更换镜像源(自己找去)

**法二： 代理**
我有配 shadowsocks, 网速不错~ so 让 curl 走一波代理
```
vi ~/.curlrc

# content
socks5 = "127.0.0.1:1086"
```
不过这样容易误伤。记得改回去用完。

## 安装 MySQL
```shell
# install mysql
brew install mysql

# 启动 mysql
mysql.server start

# 设置密码
mysql -uroot
>update mysql.user set authentication_string="new password" where User='root';
>flush privileges;
>quit;
```

## 安装 php 7
```
# install php7.0
brew install homebrew/php/php70 homebrew/php/php70-mcrypt homebrew/php/php70-gmagick homebrew/php/php70-opcache homebrew/php/php70-xdebug

#
export PATH="/usr/local/sbin:$PATH"

# 写入 .bash_profile
echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.bash_profile

# 如果上面写入报错， 直接用 vi 把 export PATH="/usr/local/sbin:$PATH" 写进 ~/.bash_profile 文件

# 测试 php, 以下两条命令均得到 PHP 7.0.XX
php -v
php-fpm -v
```

### 启动 php-fpm
```
sudo php-fpm

# 下面是结束命令
sudo pkill php-fpm
```

注意在终端中启动php-fpm之后未结束前不要关闭窗口，若输入其他命令应该新建窗口，非正常关闭重新启动将显示端口被占用，此时需要修改默认的端口9000为其他端口。修改方法在介绍完Nginx的安装之后在说明，因为其中涉及到Nginx的相关设置。


## Nginx
install
```
brew install --with-http2 nginx
```

配置
```
vim /usr/local/etc/nginx/nginx.conf

# 修改有下：
server {
    listen       8080; #默认端口为8080
    server_name  localhost; #网站地址

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    #如果想使用80端口，需要给予权限，添加下面两行并参考下一个步骤（生成密钥），不需要则可以跳过
    ssl_certificate ssl/nginx.crt;
    ssl_certificate_key ssl/nginx.key;

    location / {
        root   html; #html为网站根目录，可以自己修改
        index  index.html index.htm; #添加index.php
    }
```

生成密钥给予权限以使用80端口
```
sudo mkdir /usr/local/etc/nginx/ssl/
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /usr/local/etc/nginx/ssl/nginx.key -out /usr/local/etc/nginx/ssl/nginx.crt
```

设置Nginx支持php
在刚才的配置文件中找到以下几行，去掉前面的井号，即使注释内容生效。并做相应修改
```
location ~ \.php$ {
    root           html; #此处的根目录应与上面的设置的一致
    fastcgi_pass   127.0.0.1:9000; #此处可以设置php-fpm默认端口
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name; #此处将/scripts$fastcgi_script_name修改为根目录$document_root$fastcgi_script_name
    include        fastcgi_params; #将此行代码与上一行对调位置
}
```

### 启动 Nginx
```
sudo nginx

# 下面这条是结束命令
sudo nginx -s stop
# 重载
sudo nginx -s reload
```

### 关于修改php-fpm默认端口的说明

在前面Nginx配置文件注释中已经有提到一处`php-fpm`默认端口的修改，这是在Nginx中进行设置，接下来只需在`php-fpm`的配置文件中另外修改即可。文件路径`/usr/local/etc/php/7.0/php-fpm.d/www.conf`，在文件中找到`listen = 127.0.0.1:9000`进行修改即可。


## 完工
nginx 的默认根目录地址：
```
/usr/local/var/www
```

## Reference
> [OS X EI Capitan 10.11.3上安装Nginx+MySQL+PHP](https://github.com/Xilesun/blog/issues/1)