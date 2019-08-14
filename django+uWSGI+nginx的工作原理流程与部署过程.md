## django+uWSGI+nginx的工作原理流程与部署过程

### 一、前言

知识的分享，不应该只是展示出来，还应该解释这样做是为什么...

献给和我一样懵懂中不断汲取知识，进步的人们。

**授人与鱼，不如授人以渔！**

------

### 二、必要的前提知识

#### 2.1 准备知识

1. **Django**

   ```
   .基于python的一个web框架，同类框架有Flask、Tornado...
   ```

2. **uWSGI**

   ```
   .实现了WSGI协议的一个web服务器
   ```

   **web服务器和web框架**

   ```
   .在理解uWSGI和WSGI之前，先要弄清楚web开发的两大块，web服务器和web框架。
   .web服务器即用来接受客户端请求，建立连接，转发响应的程序。至于转发的内容是什么，交由web框架来处理，即处理这些业务逻辑。如查询数据库、生成实时信息等。
   .Nginx就是一个web服务器，Django或flask就是web框架。
   ```

   **uWSGI和WSGI**

   ```
   .那么如何实现uWSGI和WSGI的配合呢？如何做到任意一个web服务器，都能搭配任意一个框架呢？
   这就产生了WSGI协议。只要web服务器和web框架满足WSGI协议，它们就能相互搭配。
   所以WSGI只是一个协议，一个约定。而不是python的模块、框架等具体的功能。
   .而uWSGI则是实现WSGI协议的一个web服务器。即用来接受请求，转发响应的程序。
   实际上，一个uWSGI的web服务器，再加上Django这样的框架，就已经可以实现网站功能了，那么为何还需要Nginx呢？
   ```

   **需要Nginx的原因**

   ```
   一个普通的个人网站，访问量不大的时候，用uWSGI和Django部署是没问题的，但是一旦访问量过大，客户的请求连接就要进行长时间的等待。
   这个时候就出现了分布式服务器，也就是说我用多台服务器，这些服务器都能处理请求，但是谁来分配客户的的请求连接和web服务器呢？Nginx就是这样一个管家的存在，由他分配，这其实就是Nginx的反向代理，即代理服务器。
   ```

3. **Nginx简单介绍**

   > ```
   > .Nginx是俄罗斯人编写的十分轻量级的HTTP服务器,Nginx以事件驱动的方式编写，所以有非常好的性能，同时也是一个非常高效的反向代理、负载平衡。其拥有匹配Lighttpd的性能，同时还没有Lighttpd的内存泄漏问题。
   > .nginx做为HTTP服务器，有以下几项基本特性：
   >     处理静态文件，索引文件以及自动索引；打开文件描述符缓冲．
   >     无缓存的反向代理加速，简单的负载均衡和容错．
   >     FastCGI，简单的负载均衡和容错．
   >     模块化的结构。包括gzipping, byte ranges, chunked responses,以及 SSI-filter等filter。如果由FastCGI或其它代理服务器处理单页中存在的多个SSI，则这项处理可以并行运行，而不需要相互等待。
   >     支持SSL 和 TLSSNI．
   > .Nginx专为性能优化而开发，性能是其最重要的考量,实现上非常注重效率 。它支持内核Poll模型，能经受高负载的考验,有报告表明能支持高达 50,000个并发连接数。
   > .Nginx具有很高的稳定性。其它HTTP服务器，当遇到访问的峰值，或者有人恶意发起慢速连接时，也很可能会导致服务器物理内存耗尽频繁交换，失去响应，只能重启服务器。例如当前apache一旦上到200个以上进程，web响应速度就明显非常缓慢了。而Nginx采取了分阶段资源分配技术，使得它的CPU与内存占用率非常低。nginx官方表示保持10,000个没有活动的连接，它只占2.5M内存，所以类似DOS这样的攻击对nginx来说基本上是毫无用处的。就稳定性而言,nginx比lighthttpd更胜一筹。
   > .Nginx支持热部署。它的启动特别容易, 并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够在不间断服务的情况下，对软件版本进行进行升级。
   > .Nginx采用master-slave模型,能够充分利用SMP的优势，且能够减少工作进程在磁盘I/O的阻塞延迟。当采用select()/poll()调用时，还可以限制每个进程的连接数。
   > .Nginx代码质量非常高，代码很规范，手法成熟， 模块扩展也很容易。特别值得一提的是强大的Upstream与Filter链。Upstream为诸如reverse proxy,与其他服务器通信模块的编写奠定了很好的基础。而Filter链最酷的部分就是各个filter不必等待前一个filter执行完毕。它可以把前一个filter的输出做为当前filter的输入，这有点像Unix的管线。这意味着，一个模块可以开始压缩从后端服务器发送过来的请求，且可以在模块接收完后端服务器的整个请求之前把压缩流转向客户端。
   > ```

4. **wsgi.py**

   ```
   django项目携带的一个wsgi接口文件
   如果项目名叫br_api的话，此文件就位于[br_api/br_api/wsgi.py]
   ```

5. **相关资料**

   ```
   .wsgi:一种实现python解析的通用接口标准/协议，俗称web服务网关接口
   	需要搞清楚一点：wsgi不是服务器、Python模块、框架、API或者任何软件，它只是一种规范，描述web服务器与web程序通信的规范。
   .uwsgi:同wsgi一样，也是一种通信协议
   	uwsgi协议是一个uWSGI服务器自有的协议，它用于定义传输信息的类型，它与wsgi相比是两样东西。
   .uWSGI:一种python web server或者可以这么称它：Server/Gatway
	uWSGI是实现了uWSGI和wsgi两种协议的Web服务器，负责相应python的web请求。
   	uWSGI实现了wsgi协议、uwsgi协议、http协议
   ```
   

#### 2.2项目流程分析

**流程**

```
1.首先客户端向服务器发送请求，请求资源，
2.nginx作为直接对外的服务接口，接收到客户端发送过来的http请求，会解包、分析，
	如果是静态文件请求就根据配置的静态文件目录，返回请求的资源，
	如果是动态请求，nginx就通过配置文件，将请求传递给uWSGI，uWSGI将接收到包进行处理，并转发给wsgi，3.wsgi根据返回值进行打包，转发给uSWGI，
	uWSGI接收后转发给nginx，nginx最终将返回值返回给客户端(如浏览器)
*.这里需要了解一点：不同的组件之间的信息传递涉及到数据格式和协议的转换
```

**分析**

```
1.首先，守在最外面的nginx并不是必须的，uwsgi完全可以完成整个与浏览器(前端)交互的流程；
2.在nginx上加上安全性或其他限制，可以达到保护程序的作用；
3.uWSGI本事是内网接口，开启多个work和processes可能都不够用，而nginx可以代理堕胎uWSGI完成uWSGI的	负载均衡；
4.django在某些情况下对静态文件的处理能力不是很好，二用nginx来处理会更加高效。
```

### 三、安装与配置

#### 3.1 基础环境安装

首先，项目是部署在服务器上，环境是centeos，linux系统环境

使用xshell远程连接服务器搭建项目环境

##### 3.1.1 更新系统软件包

```
yum update -y
```

##### 3.1.2 安装软件管理包和可能会使用到的依赖

```
yum -y groupinstall "Development tools"

yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel psmisc libffi-devel
```

##### 3.1.3 安装Python3.6.7

```
1）前往用户根目录cd ~
2）下载 或 上传 Python3.6.7
wget https://www.python.org/ftp/python/3.6.7/Python-3.6.7.tar.xz
3）解压安装包
tar -xf Python-3.6.7.tar.xz
4）进入目标文件
cd Python-3.6.7
5）配置安装路径
./configure --prefix=/usr/local/python3
6）编译并安装
make && sudo make install
7）建立软连接：终端命令 python3，pip3
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3.6 /usr/bin/pip3
8）删除安装包与文件（可选）
cd ..
rm -rf Python-3.6.7
rm -rf Python-3.6.7.tar.xz
```

##### 3.1.4 配置pip源加速(阿里和腾讯云服务器自带源加速，可不执行此步)

```
1）创建pip配置路径mkdir ~/.pip
2）进入目录编辑配置文件：填入下方内容
cd ~/.pip && vim pip.conf

[global]
index-url = http://pypi.douban.com/simple
[install]
use-mirrors =true
mirrors =http://pypi.douban.com/simple/
trusted-host =pypi.douban.com
```

##### 3.1.5 安装虚拟环境

**默认工作路径 ~/.virtualenvs**

```
1）安装依赖
pip3 install virtualenv
pip3 install virtualenvwrapper

2）建立虚拟环境软连接
ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv

3）配置虚拟环境服务默认启动：
vim ~/.bash_profile
添加下方内容：
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/python3/bin/virtualenvwrapper.sh
4）退出保存
esc >>> :wq
5）让配置文件生效
source ~/.bash_profile

使用虚拟环境部署项目
1.先创建一个虚拟环境：
mkvirtualenv 虚拟环境名称
2.创建完毕会自动进入该虚拟环境，第二次进入方法
workon 虚拟环境名称
3.查看已经存在的虚拟环境
workon
4.推出当前虚拟环境
deactivate
```

##### 3.1.6 安装Mysql 5.7

```
1）前往用户根目录cd ~
2）下载mysql57
wget http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
3）安装mysql57
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server
4）启动mysql57并查看启动状态
启动 systemctl start mysqld.service
状态 systemctl status mysqld.service
5）Mysql5.7在第一次启动会设置一个默认初始密码，需要查看root用户、登录并修改，然后退出，重新登录
查看 grep "password" /var/log/mysqld.log
登录 mysql -uroot -p
修改 ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
退出 exit
6）创建项目数据库和独立用户（注意本地Django项目settings文件需要配置对应数据库名称和密码）
创建数据库 
create database '数据库名' default charset=utf8;
创建用户并授予权限（注意密码必须包含字符、数字和特殊字符）
grant 权限(create, update) on 库.表 to '账号'@'host' identified by '密码'
grant all privileges on xxx.* to 'xxx'@'%' identified by 'xxx';
grant all privileges on xxx.* to 'xxx'@'localhost' identified by 'xxx';
刷新生效
flush privileges;
非root户查看数据库只能看到授权的数据库列表，而root可以看到全部
查看用户相关信息
select user,host,authentication_string from mysql.user;
```

##### 3.1.7 安装Redis

```
1）前往用户根目录
cd ~
2）下载redis-5.0.5
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
3）解压安装包
tar -xf redis-5.0.5.tar.gz
4）进入目标文件
cd redis-5.0.5
5）编译环境
make
6）复制环境到指定路径完成安装
cp -r ~/redis-5.0.5 /usr/local/redis
7）配置redis可以后台启动：修改下方内容
vim /usr/local/redis/redis.conf
daemonize yes
8）完成配置修改
esc0
:wq
9）建立软连接
ln -s /usr/local/redis/src/redis-server /usr/bin/redis-server
ln -s /usr/local/redis/src/redis-cli /usr/bin/redis-cli
10）后台运行redis
redis-server &
ctrl + c
11）测试redis环境
redis-cli
config set requirepass '12345'
ctrl + c
12）关闭redis服务
pkill -f redis -9
```

#### 3.2 安装配置nginx和uWSGI，部署django项目

##### 3.2.1 nginx安装

```
1）前往用户根目录
cd ~
2）下载nginx1.13.7
wget http://nginx.org/download/nginx-1.13.7.tar.gz
3）解压安装包
tar -xf nginx-1.13.7.tar.gz
4）进入目标文件
cd nginx-1.13.7
5）配置安装路径：/usr/local/nginx
./configure --prefix=/usr/local/nginx
6）编译并安装
make && sudo make install
7）建立软连接：终端命令 nginx
ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
8）删除安装包与文件：(非必须)
rm -rf nginx-1.13.7
rm -rf nginx-1.13.7.tar.xz
9）测试Nginx环境，服务器运行nginx，本地访问服务器ip
nginx
服务器绑定的域名 或 ip:80
```

**nginx操作命令**

```
1）启动
nginx
2）关闭nginx
nginx -s stop
3）重启nginx
nginx -s reload
4）查看端口，强行关闭
ps -aux|grep nginx
kill <进程编号>
查看端口占用
netstat -tunlp|grep 8000
```

##### 3.2.2 uwsgi安装

```
1).安装(如果是虚拟环境记得在虚拟环境下再安装一遍)
pip3 install uwsgi
2).建立软连接
ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi
3).测试一下：
在本地新建test.py文件，写入内容
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"]
然后保存复制到服务器root目录
cd ~
sudo uwsgi --http 0.0.0.0:8000 --wsgi-file test.py
本地浏览器输入http://118.25.94.126:8000/ (前面是我的公网ip)
tips：
如果端口被占用，使用
lsof -i :8000
列出占用端口的程序pid号，杀掉进程再执行一遍就可以了
sudo kill -9 pid
```

##### 3.2.3 配置nginx和uwsgi

配置之前需要环境满足要求

```
.python pip已安装
.django项目文件已经上传服务器，当然django已经安装完毕
.django项目所依赖的第三方模块都已经安装完毕(安装方法同与本地一样，通过pip install ***安装)
	django项目测试在项目目录下：python manage.py runserver 0.0.0.0:8000
```

**3.2.3_1 nginx配置**

建立工程单独的nginx配置文件 

查看nginx的默认配置文件目录(nginx.conf)的路径

```
nginx -t
```

显示结果参考：

```
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

所以nginx默认配置文件路径为：

```
/usr/local/nginx/conf/nginx.conf
```

创建nginx配置文件

```
.确保nginx.conf的同目录下有[uwsgi_params]文件
	使用命令cd /usr/local/nginx/conf/进行查看
.没有的话根据链接(https://github.com/nginx/nginx/blob/master/conf/uwsgi_params)获取，后面要用到。
.在自己的工程目录下(/home/project/br_api/)，建立如										br_api.conf（/home/project/br_api/br_api.conf）的配置文件；
复制nginx.conf里面全部的内容，全部写入br_api.conf中。
然后按照下面写的，把全部写入br_api.conf配置文件中的server段部分全部替换掉。
⬇⬇⬇⬇⬇⬇⬇⬇⬇⬇⬇
```

```
server {
		listen 80;
		server_name 127.0.0.1;
		charset     utf-8;
		access_log      /home/project/br_api/nginx_access.log;
		error_log       /home/project/br_api/nginx_error.log;
		client_max_body_size 75M;


		location /static {
			alias /home/project/br_api/static;
		}
		location /media {
			alias /home/project/br_api/media;
		}
		location / {
            root        /home/project/br_api;
			include     /usr/local/nginx/conf/uwsgi_params;
			uwsgi_pass  127.0.0.1:8001;
		}
	}
```

```
替换内容说明:
.listen 80 代表服务器开放80端口,也就是说外界可以通过服务器ip:80端口访问到nginx服务器,这里80端口默认可以不写
.access_log与error_log 定义nginx访问日志和错误日志的存放路径,这里我把他们全部放到项目br_api项目根目录下
.location /目录名 {} 代表项目路径的引导
.location /static {} 项目的静态文件定位配置
	django静态文件的绝对路径是根据自己的实际情况来确定的，一般在自己的django项目根目录的/static目录下,或自己python manage.py collectstatic后的路径下。
	像我的是在/home/project/br_api/static下。 
.location / 指访问项目根目录时，nginx要做的事。其中需要指定 uwsgi_params文件的绝对路径，上面已经提到了；如果还有media文件之类的静态目录，仿照static的写法，自己补充。
.uwsgi_pass 指uWSGI绑定的监听地址，这里使用了9090端口,这里需要和下面的uwsgi配置文件中的socket一致
```

**3.2.3_2 uwsgi配置**

项目根目录创建uwsgi.ini文件,写入以下内容

```
[uwsgi]
socket = 127.0.0.1:8001
chdir=/home/project/br_api
module=br_api.wsgi:application
master = true         
processes=2
threads=2
max-requests=2000
chmod-socket=664
vacuum=true
daemonize = /home/project/br_api/uwsgi.log
```

**3.2.3_3 配置路径总览**

| 项目配置           | 配置路径                              |
| :----------------- | ------------------------------------- |
| 项目路径           | /home/project/br_api                  |
| 项目静态文件路径   | /home/project/br_api/static           |
| 项目media文件路径  | /home/project/br_api/media            |
| wsgi.py的路径      | /home/project/br_api/br_api/wsgi.py   |
| uwsgi.ini的路径    | /home/project/br_api/uwsgi.ini        |
| uwsgi日志路径      | /home/project/br_api/uwsgi.log        |
| br_api.conf的路径  | /home/project/br_api/br_api.conf      |
| uwsgi_params的路径 | /usr/local/nginx/conf/uwsgi_params    |
| nginx访问日志路径  | /home/project/br_api/nginx_access.log |
| nginx错误日志路径  | /home/project/br_api/nginx_error.log  |

可以发现，我几乎把所有有关项目的配置文件和日志文件都放在该项目目录下了，方便后期维护与查错。 

### 四、启动

**启动 uWSGI**

```
sudo uwsgi --ini /home/project/br_api/uwsgi.ini
```

**启动 nginx** 

```
.在这之前，我们要先去nginx配置文件的根目录拷贝mime.types(/usr/local/nginx/conf/mime.types)到工程目录(/home/project/br_api/mime.types)，和br_api.conf放在一起。 否则启动时会报错
.当然，如果不想拷贝mime.types文件，也可以将配置文件中“include mime.types;”一项，改成绝对路径“include /usr/local/nginx/conf/mime.types;” 
如果nginx已经开启，先关闭nginx(service nginx stop或nginx -s stop)，再执行以下命令:
```

```
如果nginx已经开启，先关闭nginx(service nginx stop或nginx -s stop)，再执行以下命令:
nginx -c /home/project/br_api/br_api.conf
这里的-c 表示加载配置文件启动
nginx -s reload
```

**补充**

如果出现502错误，查看uwsgi.log文件发现缺少模块，如果是这个错误，可以在br_api/br_api/wsgi.py中添加环境变量参数：

```
import os, sys
sys.path.append('/home/project/br_api')
# 添加的2个环境变量，将第三方模块包所在目录添加进去
sys.path.append('/root/.virtualenvs/br_api/lib/python3.6/site-packages')


from django.core.wsgi import get_wsgi_application
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'br_api.dev')
application = get_wsgi_application()
```

