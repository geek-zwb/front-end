Step1: 检测系统是否自带安装mysql
```
yum list installed | grep mysql
```

Step2: 删除系统自带的mysql及其依赖  
```
yum -y remove mysql-libs.i686
```

Step3: 给CentOS添加rpm源，并且选择较新的源
```
# wget dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
# yum localinstall mysql-community-release-el6-5.noarch.rpm
# yum repolist all | grep mysql
# yum-config-manager --disable mysql55-community
# yum-config-manager --disable mysql56-community
# yum-config-manager --enable mysql57-community-dmr
# yum repolist enabled | grep mysql
```

Step4:安装mysql 服务器
```
yum install mysql-community-server
```

Step5: 启动mysql
```
service mysqld start
```

Step6: 查看mysql是否自启动,并且设置开启自启动
```
chkconfig --list | grep mysqld

chkconfig mysqld on
```

step7: 查看MySQL的初始密码~ 在mysql 日志可查
```
cat /var/log/mysqld.log
```

Step8: mysql安全设置（重置密码等）
```
mysql_secure_installation
```

创建数据库
```
CREATE DATABASE erp DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

### Reference
[CentOS 6.5/6.6 安装（install）mysql 5.7](https://segmentfault.com/a/1190000003049498)