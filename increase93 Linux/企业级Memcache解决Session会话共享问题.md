## 企业级Memcache解决Session会话共享问题

2019-07-12 分类：[网站架构](https://www.increase93.com/?cat=6) 阅读(62) 评论(0)

### 一、session共享问题介绍 

session主要用于服务端存储用户会话信息，cookie用于浏览器存储用户会话信息。单系统服务session都存在同一个web容器中，例如tomcat中，用户请求都只访问这个容器中的session信息，除非容器挂了，否者不存在session取不到的情况。随着业务的扩展，应用用户的增加，当个容器存放系统应用消耗服务的cup和内存会不断增加，导致应用性能下降。此时考虑用nginx集群做应用的负载均衡请求分发，假设用ngnix集群三个服务，分别用A、B、C表示。按照未做session共享，仍然使用Servlet中HttpSession情景，假设此时访问的是A服务，那么session将存储在A服务中，此处如果A服务宕机，ngnix会将用户的请求分发到B或者C服务，但是B和C服务中没有存A存放的Session信息，那么用户访问的数据将会丢失。为了解决session数据丢失，需要将session共享，主流做法是将session存储在nosql数据库中，例如memcache、redis等。也有很多人通过spring session 实现共享，原理大致一样，下面主要实现了memcache缓存session共享。





### 二、环境准备 



#### 1.三台或四台虚拟机 

[root@web01 ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)

web01            10.0.0.7     #web节点    web02            10.0.0.8             #web节点lb01         10.0.0.5             #负载均衡db01            10.0.0.51            #数据库服务和memcache服务端



#### 2.编译安装nginx 


**第一个里程：下载nginx程序软件包**

[root@web01 ~]# wget http://nginx.org/download/nginx-1.14.0.tar.gz
\#解压
[root@web01 ~]# tar xf nginx-1.14.0.tar.gz

**第二个里程：解决软件依赖问题**
[root@web01 ~]# yum install openssl-devel pcre-devel -y
openssl-devel         --- 为了让nginx服务可以实现https访问的功能
pcre-devel         --- 兼容perl语言的正则表达式（^ shell：以什么开头 perl：^/）
    nginx使用时会应用一个参数rewrite 正则表达式信息（perl）

**第三个里程：创建worker进程的管理用户组和用户**
[root@web01 ~]# groupadd -g 666 www[root@web01 ~]# useradd -u666 -g666 www

**第四个里程：编译安装软件**
\#编译之前，创建程序目录
[root@web01 ~]# mkdir /application
[root@web01 ~]# cd nginx-1.14.2
\#初始化
[root@web01 nginx-1.14.2]# ./configure --prefix=/application/nginx-1.14.2 --user=www --group=www --with-http_ssl_module --with-http_stub_status_module
\#进行软件的编译（将各个语言编写代码翻译成系统可以识别的二进制信息）
[root@web01 nginx-1.14.2]# make
\#进行编译安装（将软件最终安装到系统中）
[root@web01 nginx-1.14.2]# make install

**第五个里程：创建程序软链接**
[root@web01 nginx-1.14.2]# ln -s /application/nginx-1.14/ /application/nginx

**第六个里程：启动nginx服务**
[root@web01 nginx-1.14.2]# /application/nginx/sbin/nginx



#### 3.编译安装PHP 


**第一个里程：下载软件程序，并上传到虚拟机中**

\#下载地址
https://www.php.net/

**第二个里程：下载依赖包**
[root@web01 ~]# yum install zlib-devel libxml2-devel libjpeg-devel libjpeg-turbo-devel freetype-devel libpng-devel gd-devel libcurl-devel libxslt-devel libxslt-devel libmcrypt-devel mhash mcrypt -y

**第三个里程：解压和初始化**
\#解压并进入解压之后的目录
[root@web01 ~]# tar xf php-5.5.32.tar.gz
[root@web01 ~]# cd php-5.5.32
\#初始化
[root@web01 php-5.5.32]#./configure \
--prefix=/application/php-5.5.32 \
--with-mysql=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-iconv-dir=/usr/local/libiconv \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--disable-rpath \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--with-curl \
--enable-mbregex \
--enable-fpm \
--enable-mbstring \
--with-mcrypt \
--with-gd \
--enable-gd-native-ttf \
--with-openssl \
--with-mhash \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--enable-soap \
--enable-short-tags \
--enable-static \
--with-xsl \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-ftp \
--enable-opcache=no说明：LNMP架构部署时，如果mysql服务和web服务不在一台主机上，php配置参数需要进行调整
--with-mysql=/application/mysql/ => --with-mysql=mysqlnd

**第四个里程：编译安装**
[root@web01 php-5.5.32]# make
[root@web01 php-5.5.32]# make install

**第五个里程：创建PHP软件程序软链接**
[root@web01 php-5.5.32]# ln -s /application/php-5.5.32/ /application/php

**第六个里程：配置php解析文件和配置php-fpm配置文件**
[root@web01 php-5.5.32]# cp php.ini-production /application/php/lib/php.ini
[root@web01 php-5.5.32]# cd /application/php/etc/
[root@web01 etc]# cp php-fpm.conf.default php-fpm.conf

**第七个里程：启动PHP服务**
[root@web01 ~]# /application/php/sbin/php-fpm



#### 4.部署网站（phpMyAdmin） 


**第一个里程：创建一个虚拟主机配置文件保存目录**

[root@web01 ~]# mkdir -p /application/nginx/conf/extra

**第二个里程: 生成虚拟主机配置文件**
[root@web01 ~]# vim /application/nginx/conf/extra/phpmyadmin.oldboy.com.conf
server {
    listen 80;
    server_name phpmyadmin.oldboy.com;
    root html/phpmyadmin;
    client_max_body_size 100m;    location / {
        index index.php index.html;
    }    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

**第三个里程：编写nginx主配置文件，加载虚拟主机配置文件**
\#在http层最下面插入include extra/*;
[root@web01 ~]# vim /application/nginx/conf/nginx.conf
http {
...
include extra/*;
}

**第四个里程：检查语法和重启nginx服务**
[root@web01 ~]# /application/nginx/sbin/nginx -t[root@web01 ~]# /application/nginx/sbin/nginx -s reload

**第五个里程：部署网站站点并进行浏览器访问**
\#下载软件
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.4/phpMyAdmin-4.8.4-all-languages.zip#上传软件并进行解压
[root@web01 ~]# cd /application/nginx/html
[root@web01 html]# unzip phpMyAdmin-4.8.4-all-languages.zip
[root@web01 html]# mv phpMyAdmin-4.8.4-all-languages phpmyadmin#授权
[root@web01 html]# chown -R www.www phpmyadmin/#本地hosts文件域名解析
10.0.0.7 phpmyadmin.oldboy.com#浏览器访问
phpmyadmin.oldboy.com
![img](D:\Notes\md_img\071219_0732_MemcacheSe1.png)
注意：以上的环境web01和web02是一致的。部署站点的时候可以将web01的打包发给web02[root@web01 ~]# tar zcf phpmyadmin.tar.gz /application/nginx/html/phpmyadmin
[root@web01 ~]# scp -rp phpmyadmin.tar.gz root@10.0.0.8:/root
\#进入到web02主机
[root@web02 ~]# tar xf phpmyadmin.tar.gz -C /application/nginx/html/
\#重启php-fpm
[root@web02 ~]# killall php-fpm
php-fpm: no process found
[root@web02 ~]# /application/php/sbin/php-fpm



#### 5.部署数据库服务 


**第一个里程：下载安装Mariadb数据库**

[root@db01 ~]# yum install mariadb-server mariadb -y

**第二个里程：启动数据库**
[root@db01 ~]# systemctl start mariadb
[root@db01 ~]# systemctl enable mariadb

**第三个里程：设置数据库初始密码**
[root@db01 ~]# mysqladmin password 'oldboy123.com'

**第四个里程：登录数据库**
[root@db01 ~]# mysql -uroot -poldboy123.com

**第五个里程：创建一个可以远程连接的用户**
mysql> grant all privileges on *.* to oldboy@'10.0.0.%' identified by '123';

**第六个里程：配置phpmyadmin的配置文件（web主机）**
[root@web01 ~]# cd /application/nginx/html/phpmyadmin
[root@web01 phpmyadmin]# cp config.sample.inc.php config.inc.php
[root@web01 phpmyadmin]# vim config.inc.php
...
/* Server parameters */
$cfg['Servers'][$i]['host'] = '10.0.0.51';
....#重启php-fpm
[root@web02 ~]# killall php-fpm
php-fpm: no process found
[root@web02 ~]# /application/php/sbin/php-fpm
![img](D:\Notes\md_img\071219_0732_MemcacheSe2.png)
![img](D:\Notes\md_img\071219_0732_MemcacheSe3.png)
两台主机都可以访问之后，开始部署负载均衡。



#### 6.部署负载均衡 


**第一个里程：下载安装nginx，这里不用编译安装，直接从官网下载**

\#配置nginx官方源
[root@lb01 ~]# vim /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
\#下载nginx
[root@lb01 ~]# yum install nginx -y
\#查看版本
[root@lb01 ~]# nginx -v
nginx version: nginx/1.14.2
\#启动nginx
[root@lb01 ~]# systemctl start nginx
[root@lb01 ~]# systemctl enable nginx

**第二个里程：配置负载均衡配置文件**
[root@lb01 ~]# cat /etc/nginx/conf.d/node_proxy.conf
upstream node {
server 172.16.1.7:80;
server 172.16.1.8:80;
}
server {
listen 80;
server_name phpmyadmin.oldboy.com;
location / {
proxy_pass http://node;
proxy_set_header Host $host;
proxy_set_header X-Forwarded-For $remote_addr;
}
}
\#检查语法
[root@lb01 ~]# nginx -t
\#重启nginx
[root@lb01 ~]# systemctl restart nginx

**第三个里程：修改本地hosts解析**
10.0.0.5 phpmyadmin.oldboy.com

**第四个里程：浏览器登录访问，确认访问域名时走的是负载均衡。这时你会发现，在你用户和密码都正确的情况下，你是登录不成功的。所以，接下来要做的就是session会话共享。**



#### 7.部署memcache，实现session会话共享 

**服务端部署**

**第一个里程碑：安装依赖关系**Memcache用到了libevent这个库用于Socket的处理。[root@db01 ~]# yum install libevent libevent-devel nc -y

**第二个里程碑：安装memcache**
[root@db01 ~]# yum install memcached -y

**第三个里程碑：启动memcached**
[root@db01 ~]# systemctl start memcached
[root@db01 ~]# systemctl enable memcached

**第四个里程碑：查看是否启动成功**
[root@db01 ~]# netstat -lntp|grep memcached
tcp 0 0 0.0.0.0:11211 0.0.0.0:* LISTEN 1734/memcached
tcp6 0 0 :::11211 :::* LISTEN 1734/memcached
**客户端部署（web服务器）**
**第一个里程：安装PHP memcache**
**扩展插件**[root@web01 ~]# wget http://pecl.php.net/get/memcache-2.2.7.tgz
**第二个里程：解压并进入到目录中**
[root@web01 ~]# tar xf memcache-2.2.7.tgz
[root@web01 ~]# cd memcache-2.2.7
**第三个里程：执行这个命令**
[root@web01 memcache-2.2.7]# /application/php/bin/phpize
Configuring for:
PHP Api Version: 20121113
Zend Module Api No: 20121212
Zend Extension Api No: 220121212
**第四个里程：初始化和编译安装**
[root@web01 memcache-2.2.7]# ./configure -enable-memcache --with-php-config=/application/php/bin/php-config
[root@web01 memcache-2.2.7]# make
[root@web01 memcache-2.2.7]# make install
Installing shared extensions: /application/php-5.5.32/lib/php/extensions/no-debug-non-zts-20121212/
**第五个里程碑：配置memcache客户端使其生效**
[root@web01 memcache-2.2.7]# cd /application/php/lib/
\#添加如下两行内容
[root@web01 lib]# tail -2 php.ini
extension_dir = "/application/php/lib/php/extensions/no-debug-non-zts-20121212/"
extension = memcache.so

**第六个里程碑：检测语法，重启服务**
[root@web01 lib]# /application/php/sbin/php-fpm -t
[07-Nov-2017 10:20:44] NOTICE: configuration file /application/php-5.5.32/etc/php-fpm.conf test is successful
[root@web01 lib]# killall php-fpm
php-fpm: no process found
[root@web01 lib]# /application/php/sbin/php-fpm

**第七个里程碑：浏览器访问phpinfo页面出现memcache信息表示配置成功**
[root@web01 ~]# cd /application/nginx/html/phpmyadmin
[root@web01 phpmyadmin]# vim info.php
<?php
    phpinfo();
    ?>
[root@web01 phpmyadmin]# chown www.www info.php
![img](D:\Notes\md_img\071219_0732_MemcacheSe4.png)
再看下session的位置![img](D:\Notes\md_img\071219_0732_MemcacheSe5.png)
**第八个里程碑：编写测试memcache文件**cat>/application/nginx/html/phpmyadmin/test_memcache.php<<"EOF"
<?php
$memcache = new Memcache;
$memcache->connect('172.16.1.51', 11211) or die ("Could not connect server");
$memcache->set('key', 'Memcache connect OK');
$get = $memcache->get('key');
echo $get;
?>
EOF#进行测试
[root@web01 phpmyadmin]# /application/php/bin/php test.php
Memcache connect OK[root@web01 phpmyadmin]#
测试出现Memcache connect OK 表示连接成功

**第九个里程碑：修改php配置(设置session共享)**
[root@web01 ~]# vim /application/php/lib/php.ini
原配置
session.save_handler = files
session.save_path = "/tmp"
修改为：
session.save_handler = memcache
session.save_path = "tcp://172.16.1.51:11211"
**第十个里程碑：重启PHP服务**
[root@web01 ~]# killall php-fpm
php-fpm: no process found
[root@web01 ~]# /application/php/sbin/php-fpm
再重新看下session的位置已经改变了。到此企业级memcache(session共享)部署完毕。
![img](D:\Notes\md_img\071219_0732_MemcacheSe6.png)
这个时候你再去访问网站去登陆phpmyadmin，已经是可以正常登陆了。说明多台服务session会话共享已经实现。