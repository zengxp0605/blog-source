---
title: CentOS6.8 LNMP安装与配置
comments: true
date: 2017-8-11 14:21
categories: [LNMP]
tags: [LNMP]
keywords: LNMP
---

### Nginx安装
```sh
wget http://nginx.org/download/nginx-1.10.1.tar.gz
tar -zxvf nginx-1.10.1.tar.gz
cd nginx-1.10.1
useradd -s /sbin/nologin www #添加用户,不能登录
yum install pcre pcre-devel openssl openssl-devel #安装依赖
./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-ipv6

make && make install
# 安装在 /usr/local/nginx 
```

### 编译安装php-fpm
1. 安装
```sh
#下载
wget http://sg2.php.net/get/php-5.5.38.tar.gz/from/this/mirror
mv mirror php-5.5.38.tar.gz
cd php-5.5.38

#编译
./configure --prefix=/usr/local/php-fpm \
--with-mysql=mysqlnd \
--enable-mysqlnd \
--with-gd \
--enable-gd-native-ttf \
--enable-gd-jis-conv \
--enable-fpm

make  && make install

#安装依赖
yum install libxml2 libxml2-devel openssl openssl-devel curl-devel  gd gd-devel 
yum install php-gd*

# 如果提示缺少 libmcrypt, 则下载安装
下载安装  libmcrypt-2.xxx []
# 安装依赖
yum install gcc gcc-c++
./configure --prefix=/usr/local
make && make install

## 更多安装项参考, 这里的`/usr/local/mysql` 需要根据mysql安装目录调整
## 如果是yum 安装的mysql, 则不指定--with-mysql --with-mysqli 的目录即可
./configure --prefix=/usr/local/php-fpm \
--with-mysql=/usr/local/mysql \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-curl --enable-mbregex --enable-fpm --enable-mbstring --with-mcrypt=/usrl/local \
--with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc \
--enable-zip --enable-soap --enable-opcache --with-pdo-mysql --enable-maintainer-zts

```

<!--more-->


2. 配置
```sh 

cd php-5.5.38 #解压安装包所在目录
cp php.ini-production /usr/local/php-fpm/lib/php.ini
cp sapi/fpm/init.d.php-fpm  /etc/init.d/php-fpm 
cd /usr/local/php-fpm/etc
cp php-fpm.conf.default php-fpm.conf
chmod 755 /etc/init.d/php-fpm
chkconfig --add php-fpm
chkconfig php-fpm on
service php-fpm start #启动服务

```
3. 配置nginx 
- 修改nginx.conf 中如下配置项
```conf
    location ~ \.php$ {
        root           html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

```

4. 常见错误及处理方式
- 重新编译PHP时出现的
```
/home/king/php-5.2.13/ext/iconv/iconv.c:2491: undefined reference to `libiconv_open'
collect2: ld returned 1 exit status
make: *** [sapi/cli/php] Error 1
```
- 使用:
```
make ZEND_EXTRA_LIBS='-liconv'
```


### 安装mysql5.5.57
1. 安装

```
wget ftp://ftp.jaist.ac.jp/pub/mysql/Downloads/MySQL-5.5/mysql-5.5.57.tar.gz
tar zxvf mysql-5.5.57.tar.gz 
cd mysql-5.5.57

cmake -DMYSQL_USER=mysql -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DINSTALL_DATADIR=/usr/local/mysql/data -DMYSQL_UNIX_ADDR=/tmp/mysqld.sock -DDEFAULT_CHARSET=utf8  -DDEFAULT_COLLATION=utf8_general_ci -DEXTRA_CHARSETS=all -DWITH_EMBEDDED_SERVER=1 -DENABLED_LOCAL_INFILE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1

make && make install 
```

2. 配置
```
chown -R mysql:mysql /usr/local/mysql/

cp /root/mysql-5.5.57/support-files/my-large.cnf /etc/my.cnf

cp /root/mysql-5.5.57/support-files/mysql.server /etc/init.d/mysqld

chmod a+x /etc/init.d/mysqld

开启启动  chkconfig --level 345 mysqld on
修改环境变量 echo "export PATH=/usr/local/mysql/bin/:$PATH" >> /etc/profile
source /etc/profile
初始化数据库   /usr/local/mysql/scripts/mysql_install_db --user=mysql --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
启动   service mysqld start
修改root 用户密码  /usr/local/mysql/bin/mysqladmin -u root password 'root'
连接数据库  mysql -hlocalhost -uroot -proot -P3306

配置文件修改: vi /etc/my.cnf
    [mysqld] 段添加
        basedir=/usr/local/mysql
        datadir=/usr/local/mysql/data
        character-set-server=utf8
    [client]
        default-character-set=utf8
```

### redis 扩展
1. 安装redis 
```sh
wget https://github.com/antirez/redis/archive/2.8.21.tar.gz
tar zxvf  2.8.21.tar.gz
cd redis-2.8.21.tar.gz
# make之后即可使用,可以移动到/usr/local/ 目录下
make 

# 运行服务端 (加 & 表示后台启动)
/usr/local/redis-2.8.21/src/redis-server &
# 运行客户端
/usr/local/redis-2.8.21/src/redis-cli
```

- 脚本启动redis服务的方式
> 参考: [http://blog.csdn.net/lzwglory/article/details/70261427](http://blog.csdn.net/lzwglory/article/details/70261427)


2. php 扩展
- 安装扩展
```sh
wget https://github.com/phpredis/phpredis/archive/develop.zip
unzip develop.zip
cd phpredis-develop
/usr/local/php-fpm/bin/phpize
./configure --with-php-config=/usr/local/php-fpm/bin/php-config
make && make install

```
- 配置php.ini
    - `vi /usr/local/php-fpm/lib/php.ini`
    - 加入 `extension=/usr/local/php-fpm/lib/php/extensions/no-debug-zts-20121212/redis.so`