## 定制Rpm包

2019-07-11 分类：[网站架构](https://www.increase93.com/?cat=6) 阅读(47) 评论(0)

### FPM安装 



**01.fpm是ruby写的，因此系统环境需要ruby，且ruby版本号大于1.8.5。**

**02.安装ruby模块**yum -y install ruby rubygems ruby-devel
**03.添加阿里云的Rubygems仓库，国外资源会影响下载速度**gem sources -a http://mirrors.aliyun.com/rubygems/ http://mirrors.aliyun.com/rubygems/added to sources
**04.移除原有的ruby仓库**

gem sources --remove http://rubygems.org/
**05.安装fpm，gem从rubygem仓库安装软件类似yum从yum仓库安装软件。首先安装低版本的json，高版本的json需要ruby2.0以上，然后安装低版本的fpm，够用。**

gem install json -v 1.8.3

gem install ffi -v 1.9.18

gem install fpm -v 1.3.3
\#此处如果安装不上,先安装gem install fpm 然后在安装gem install fpm -v 1.3.3

\#上面的安装仅适合CentOS6系统

\#如果fpm安装不上先将fpm版本去掉安装 gem install fpm然后在进行安装对应版本gem install fpm -v 1.3.3

**06.CentOS7系统安装一步搞定，即**

gem install fpm

### FPM参数 



详细使用见fpm -help

常用参数-s 指定源类型-t 指定目标类型，即想要制作为什么包-n 指定包的名字-v 指定包的版本号-C 指定打包的相对路径 Change directory to here before searchingforfiles-d 指定依赖于哪些包-f 第二次打包时目录下如果有同名安装包存在，则覆盖它-p 输出的安装包的目录，不想放在当前目录下就需要指定--post-install 软件包安装完成之后所要运行的脚本；同--after-install--pre-install 软件包安装完成之前所要运行的脚本；同--before-install--post-uninstall 软件包卸载完成之后所要运行的脚本；同--after-remove--pre-uninstall 软件包卸载完成之前所要运行的脚本；同--before-remove



### 制作软件依赖包 



1.开启yum缓存

sed -i 's#keepcache=0#keepcache=1#g' /etc/yum.conf2.清除yum缓存find /var/cache/ -type f -name '*rpm'|xargs rm -f3.下载需要安装软件的依赖包，然后统一打成一个压缩包。find /var/cache/ -type f -name '*rpm'



### 制作Nginx包 



1.编写脚本，这是安装完rpm包要执行的脚本

vim /server/scripts/nginx_rpm.sh#!/bin/shNGINXversion=1.10.2##判断www用户存在不存在id www >/dev/null2>&1if [ $? -eq0 ];thenecho "OK"elseuseradd -s /sbin/nologin -M www -g 666fi##判断nginx有没有软链接ls -l /application/nginx &>/dev/nullif [ $? -eq0 ];thenecho "OK"else/bin/ln -s /application/nginx-$NGINXversion /application/nginxfi
\#启动nginx/application/nginx/sbin/nginx#设置环境变量
echo 'export PATH="/application/nginx/sbin/:$PATH"' >>/etc/profile.d/nginx.sh#将其生效
source /etc/profile
打包fpm -s dir -t rpm -nnginx -v 1.10.2 -d 'pcre-devel,openssl-devel' --post-install/server/scripts/nginx_rpm.sh -f /application/nginx-1.10.2/



### 制作PHP包 



1.编写脚本

vim /server/scripts/php-install.sh#!/bin/shPHPXversion=5.5.32##判断www用户存在不存在id www >/dev/null2>&1if [ $? -eq0 ];thenecho "OK"elseuseradd -s /sbin/nologin -M www -g 666fi##判断php有没有软链接ls -l /application/nginx &>/dev/nullif [ $? -eq0 ];thenecho "OK"elseln -s/application/php-$PHPXversion/ /application/phpfi
\#启动PHP/application/php/sbin/php-fpm#设置环境变量
echo 'export PATH="/application/php/sbin/:$PATH"' >>/etc/profile.d/php.sh#将其生效
source /etc/profile
打包fpm -s dir-t rpm -n php -v 5.5.32 -d'zlib-devel,libxml2-devel,libjpeg-devel,libjpeg-turbo-devel,libiconv,freetype-devel,libpng-devel,gd-devel,libcurl-devel,libxslt-devel,libmcrypt-devel,mhash,mcrypt'--post-install /server/scripts/php-rpm.sh -f /application/php-5.5.32/ /usr/local/libiconv



### 制作MySQL包 



1.编写脚本

vim /root/mysql_install.sh
\#!/bin/shMySQLversion=5.6.40#判断用户是否存在
id mysql &>/dev/null
if [ $? -eq 0 ];then
echo "OK" &>/dev/null
else
useradd mysql -s /sbin/nologin -M
fi##判断mysql有没有软链接ls -l /application/mysql &>/dev/nullif [ $? -eq 0 ];then
echo "OK" &>/dev/null
else
ln -s /application/mysql-$MySQLversion/ /application/mysql
fi
\#判断/etc/my.cnf文件是否存在，存在就将其移动走
[ -f /etc/my.cnf ] && mv /etc/my.cnf /tmp#拷贝配置文件
/bin/cp /application/mysql/support-files/my*.cnf /etc/my.cnf#初始化
/application/mysql/scripts/mysql_install_db --basedir=/application/mysql/ --datadir=/application/mysql/data --user=mysql#创建socket文件存放目录
mkdir /application/mysql-5.6.40/tmp -p#授权
chown -R mysql.mysql /application/mysql*#拷贝启动脚本
/bin/cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld#赋予执行权限
chmod +x /etc/init.d/mysqld#启动数据库
/etc/init.d/mysqld start#设置环境变量
echo 'export PATH="/application/mysql/bin:$PATH"' >>/etc/profile.d/mysql.sh#将其生效
source /etc/profile
2.打包
fpm -s dir -t rpm -n mysql -v 5.6.40 -d 'ncurses-devel,libaio-devel,cmake,gcc,gcc-c++,glibc,autoconf' --post-install /root/mysql_install.sh -f /application/mysql-5.6.40/