## Cobbler无人值守安装

2019-07-12 分类：[网站架构](https://www.increase93.com/?cat=6), [运维基础](https://www.increase93.com/?cat=5) 阅读(83) 评论(0)

## 一、背景介绍 

​    作为运维，在公司经常遇到一些机械性重复工作要做，例如：为新机器装系统，一台两台机器装系统，可以用光盘、U盘等介质安装，1小时也完成了，但是如果有成百台的服务器还要用光盘、U盘去安装，就显得有些力不从心了。PXE技术就能很好的解决这个问题，本文将会对PXE的工作原理有所介绍，而cobbler则是基于PXE技术的工作原理的二次封装，通过命令的方式简化了PXE配置过程。





## 二、 安装系统的方法 

　　    光盘（ISO文件，光盘的镜像文件）===>>每一台物理机都得给一个光驱，如果用外置光驱的话，是不是每台机器都需要插一下
    
　      U盘：ISO镜像刻录到U盘==>>需要每台机器都需要插一下

　      并行安装==>>网络安装　　    自动化安装



## 三、PXE说明 



### 说明 

　　PXE，全名Pre-boot Execution Environment，预启动执行环境；

　　通过网络接口启动计算机，不依赖本地存储设备（如硬盘）或本地已安装的操作系统；　　由Intel和Systemsoft公司于1999年9月20日公布的技术；　　客户端/Server的工作模式；　　PXE客户端会调用网际协议(IP)、用户数据报协议(UDP)、动态主机设定协议(DHCP)、小型文件传输协议(TFTP)等网络协议；　　PXE客户端(客户端)这个术语是指机器在PXE启动过程中的角色。一个PXE客户端可以是一台服务器、笔记本电脑或者其他装有PXE启动代码的机器（我们电脑的网卡）



### PXE+cobbler工作步骤图 

![img](D:\Notes\md_img\071219_0722_Cobbler1.png)

1.PXE客户端发送UDP广播请求　　PXE 客户端从自己的PXE网卡启动，通过PXE BootROM(自启动芯片)会以UDP(简单用户数据报协议)发送一个广播请求，向本网络中的DHCP服务器索取IP。2.DHCP服务器提供信息　　DHCP服务器收到客户端的请求，验证是否来至合法的PXE 客户端的请求，验证通过它将给客户端一个"提供"响应，这个"提供"响应中包含了为客户端分配的IP地址、pxelinux启动程序(TFTP)位置，以及配置文件所在位置。3.PXE客户端请求下载启动文件　　客户端收到服务器的"回应"后，会回应一个帧，以请求传送启动所需文件。这些启动文件包括：pxelinux.0、pxelinux.cfg/default、vmlinuz、initrd.img等文件。4.TETP服务器响应客户端请求并传送文件　　当服务器收到客户端的请求后，他们之间之后将有更多的信息在客户端与服务器之间作应答, 用以决定启动参数。BootROM由TFTP通讯协议从tftp服务器
下载启动安装程序所必须的文件(pxelinux.0、pxelinux.cfg/default)。default文件下载完成后，会根据该文件中定义的引导顺序，启动Linux安装程序的引导内核。5.请求下载自动应答文件　　客户端通过pxelinux.cfg/default文件成功的引导Linux安装内核后，安装程序首先必须确定你通过什么安装介质来安装linux，如果是通过网络安装(NFS, FTP, HTTP)，则会在这个时候初始化网络，并定位安装源位置。接着会读取default文件中指定的自动应答文件ks.cfg所在位置，根据该位置请求下载该文件。6.客户端安装操作系统　　将ks.cfg文件下载回来后，通过该文件找到http镜像，并按照该文件的配置请求下载安装过程需要的软件包。　　http镜像和客户端建立连接后，将开始传输软件包，客户端将开始安装操作系统。　　安装完成后，将提示重新引导计算机。



## 四、cobbler介绍 



### cobbler概念 

​    Cobbler是一个Linux服务器快速网络安装的服务，由python开发，小巧轻便（15k行python代码），可以通过PXE的方式来快速安装、重装物理服务器和虚拟机，同时还可以管理DHCP，DNS，TFTP、RSYNC以及yum仓库、构造系统ISO镜像。

​    Cobbler可以使用命令行方式管理，也提供了基于Web的界面管理工具(cobbler-web)，还提供了API接口，可以方便二次开发使用。



### Cobbler集成的服务 

PXE服务支持

DHCP服务管理DNS服务管理(可选bind,dnsmasq)电源管理Kickstart服务支持YUM仓库管理TFTP(PXE启动时需要)Apache(提供kickstart的安装源，并提供定制化的kickstart配置)



### 配置目录及文件介绍 

配置文件目录：
/etc/cobbler
/etc/cobbler/settings :         #cobbler 主配置文件
/etc/cobbler/iso/:                 #iso模板配置文件
/etc/cobbler/pxe:                 #pxe模板文件
/etc/cobbler/power:             #电源配置文件
/etc/cobbler/user.conf:         #web服务授权配置文件
/etc/cobbler/users.digest:         #web访问的用户名密码配置文件
/etc/cobbler/dhcp.template :     #dhcp服务器的的配置末班
/etc/cobbler/dnsmasq.template : #dns服务器的配置模板
/etc/cobbler/tftpd.template :     #tftp服务的配置模板
/etc/cobbler/modules.conf :     #模块的配置文件
数据目录：
/var/lib/cobbler/config/:         #用于存放distros，system，profiles 等信
息配置文件
/var/lib/cobbler/triggers/:     #用于存放用户定义的cobbler命令
/var/lib/cobbler/kickstart/:     #默认存放kickstart文件
/var/lib/cobbler/loaders/:         #存放各种引导程序  镜像目录
/var/www/cobbler/ks_mirror/:     #导入的发行版系统的所有数据
/var/www/cobbler/images/ :         #导入发行版的kernel和initrd镜像用于
远程网络启动
/var/www/cobbler/repo_mirror/:     #yum 仓库存储目录
日志目录：
/var/log/cobbler/installing:     #客户端安装日志
/var/log/cobbler/cobbler.log :     #cobbler日志





### 命令介绍 

cobbler commands介绍
cobbler check         #核对当前设置是否有问题
cobbler list         #列出所有的cobbler元素
cobbler report         #列出元素的详细信息
cobbler sync         #同步配置到数据目录,更改配置最好都要执行下
cobbler reposync     #同步yum仓库
cobbler distro         #查看导入的发行版系统信息
cobbler system         #查看添加的系统信息
cobbler profile     #查看配置信息





## 五、cobbler安装系统实践 



### 环境准备 

[root@cobbler ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)
[root@cobbler ~]# uname -r
3.10.0-862.el7.x86_64
[root@cobbler ~]# hostname
cobbler
[root@cobbler ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
Active: inactive (dead)
Docs: man:firewalld(1)
[root@cobbler ~]# getenforce
Disabled
[root@cobbler ~]# hostname -I
10.0.0.202 172.16.1.202





### 安装cobbler 

**安装cobbler相关服务软件信息（前提必须部署好epel源）**

[root@cobbler ~]# yum install -y cobbler cobbler-web dhcp tftp-server pykickstart httpd python-django
**启动服务**
[root@cobbler ~]# systemctl start httpd.service cobblerd.service
**对cobbler默认配置进行检查测试**
[root@cobbler ~]# cobbler check
![img](D:\Notes\md_img\071219_0722_Cobbler2.png)
检查提示了8个信息，需要修改**解释**1 : The 'server' field in /etc/cobbler/settings must be set to something other than localhost, or kickstarting features will not work.
This should be a resolvable hostname or IP for the boot server as reachable by all machines that will use it.
\#cobbler配置文件中server参数信息要改为相应的指定信息,不能使用默认localhosts2 : For PXE to be functional, the 'next_server' field in /etc/cobbler/settings must be set to something other than 127.0.0.1,
and should match the IP of the boot server on the PXE network.
\#cobbler配置文件中next_server参数信息要改为相应指定的tftp服务器地址信息,不能使用默认的127.0.0.13 : change 'disable' to 'no' in /etc/xinetd.d/tftp
\#让tftp服务可以被xinetd服务管理
4 : Some network boot-loaders are missing from /var/lib/cobbler/loaders, you may run 'cobbler get-loaders' to download them, or,
if you only want to handle x86/x86_64 netbooting, you may ensure that you have installed a *recent* version of the syslinux package
     installed and can ignore this message entirely.
     Files in this directory, should you want to support all architectures, should include pxelinux.0, menu.c32, elilo.efi,
     and yaboot. The 'cobbler get-loaders' command is the easiest way to resolve these requirements.
\#需要进行下载系统启动时所需使用的启动引导文件信息,使用'cobbler get-loaders'命令
5 : enable and start rsyncd.service with systemctl
\#需要启动rsync服务,并且设置开机自启动
6 : debmirror package is not installed, it will be required to manage debian deployments and repositories
\#debian系统的一个管理软件包需要安装 debmirror7 : The default password used by the sample templates for newly installed machines (default_password_crypted in /etc/cobbler/settings)
is still set to 'cobbler' and should be changed, try: "openssl passwd -1 -salt 'random-phrase-here' 'your-password-here'" to generate new one
\#cobbler配置文件中default_password_crypted参数信息要改为相应指定的密码信息,不能使用默认
    
8 : fencing tools were not found, and are required to use the (optional) power management features. install cman or fence-agents to use them
\#智能电源管理工具没有找到
**解决相关检查问题**
\01. 解决问题一
[root@cobbler ~]# cp /etc/cobbler/settings{,.ori} #备份配置文件
[root@cobbler ~]# vim +384 /etc/cobbler/settings
[root@cobbler ~]# sed -i 's/server: 127.0.0.1/server: 172.16.1.202/' /etc/cobbler/settings02. 解决问题二[root@cobbler ~]# sed -i 's/next_server: 127.0.0.1/next_server: 172.16.1.202/' /etc/cobbler/settings03. 解决问题三
[root@cobbler ~]# sed -i '/disabled/s#yes#no#' /etc/xinetd.d/tftp04. 解决问题四
[root@cobbler ~]# cobbler get-loaders
[root@cobbler ~]# tree /var/lib/cobbler/loaders/05. 解决问题五
[root@cobbler ~]# systemctl start rsyncd
[root@cobbler ~]# systemctl enable rsyncd
[root@cobbler ~]# systemctl status rsyncd06. 解决问题六
debian系统才需要安装相应软件包, 课程使用centos系统无需安装07. 解决问题七
[root@cobbler ~]# sed -ri "/default_password_crypted/s#(.*: ).*#\1\"`openssl passwd -1 -salt 'oldboy' '123456'`\"#" /etc/cobbler/settings
[root@cobbler ~]# openssl passwd -1 -salt 'oldboy' '123456'08. 解决问题八
需要解决一些脑裂问题,需要安装智能电源软件(暂时无需处理)解决完成后，再次的进行配置检查
[root@cobbler ~]# cobbler check
The following are potential configuration items that you may want to fix:1 : debmirror package is not installed, it will be required to manage debian deployments and repositories
2 : fencing tools were not found, and are required to use the (optional) power management features. install cman or fence-agents to use themRestart cobblerd and then run 'cobbler sync' to apply changes.
**修改dhcp服务配置文件信息**
\01. 修改配置文件信息
[root@cobbler ~]# vim /etc/cobbler/dhcp.template
22 # option routers 172.16.1.5;
23 # option domain-name-servers 172.16.1.1;
:%s#192.168.1#172.16.1#g
\#说明: 修改模板配置文件的时候,即可把/etc/dhcp/dhcpd.conf文件进行修改
[root@cobbler ~]#grep 172.16.1 /etc/cobbler/dhcp.template
subnet 172.16.1.0 netmask 255.255.255.0 {
\# option routers 172.16.1.5;
\# option domain-name-servers 172.16.1.1;
range dynamic-bootp 172.16.1.100 172.16.1.254;    02. 使用cobbler服务管理dhcp服务    
[root@cobbler ~]# sed -i 's/manage_dhcp: 0/manage_dhcp: 1/' /etc/cobbler/settings
[root@cobbler ~]# vim /etc/cobbler/settings
242 manage_dhcp: 1        #将默认数值0改为1,即表示使用cobbler服务管理dhcp服务
**启动cobbler相关服务程序**
[root@cobbler ~]# systemctl restart httpd.service rsyncd.service tftp.socket cobblerd.service
**应用cobbler相关的所有服务**
[root@cobbler ~]# cobbler sync
task started: 2019-04-01_123634_sync
task started (id=Sync, time=Mon Apr 1 12:36:34 2019)
running pre-sync triggers
cleaning trees
removing: /var/lib/tftpboot/pxelinux.cfg/default
removing: /var/lib/tftpboot/grub/images
removing: /var/lib/tftpboot/grub/grub-x86.efi
removing: /var/lib/tftpboot/grub/grub-x86_64.efi
removing: /var/lib/tftpboot/grub/efidefault
removing: /var/lib/tftpboot/s390x/profile_list
copying bootloaders
trying hardlink /var/lib/cobbler/loaders/grub-x86.efi -> /var/lib/tftpboot/grub/grub-x86.efi
trying hardlink /var/lib/cobbler/loaders/grub-x86_64.efi -> /var/lib/tftpboot/grub/grub-x86_64.efi
copying distros to tftpboot
copying images
generating PXE configuration files
generating PXE menu structure
rendering DHCP files
generating /etc/dhcp/dhcpd.conf
rendering TFTPD files
generating /etc/xinetd.d/tftp
cleaning link caches
running post-sync triggers
running python triggers from /var/lib/cobbler/triggers/sync/post/*
running python trigger cobbler.modules.sync_post_restart_services
running: dhcpd -t -q
received on stdout:
received on stderr:
running: service dhcpd restart
received on stdout:
received on stderr: Redirecting to /bin/systemctl restart dhcpd.servicerunning shell triggers from /var/lib/cobbler/triggers/sync/post/*
running python triggers from /var/lib/cobbler/triggers/change/*
running python trigger cobbler.modules.manage_genders
running python trigger cobbler.modules.scm_track
running shell triggers from /var/lib/cobbler/triggers/change/*
*** TASK COMPLETE ***
\#至此cobbler服务端命令行配置完成



### cobbler软件web页面配置 

**加载cobbler网页页面信息**

https://10.0.0.202/cobbler_web/
![img](D:\Notes\md_img\071219_0722_Cobbler3.png)
在这一步的过程中，肯定是有同学会报错这个错误的![img](D:\Notes\md_img\071219_0722_Cobbler4.png)
解决方法：[root@cobbler ~]# tailf /var/log/httpd/ssl_error_log #查看日志
![img](D:\Notes\md_img\071219_0722_Cobbler5.png)
初步判断应该是pythone-django版本问题
\#下载pip.py
[root@cobbler ~]# wget https://bootstrap.pypa.io/get-pip.py#安装pip
[root@cobbler ~]# yum install python-pip -y#调用本地python运行pip.py脚本
[root@cobbler ~]# python get-pip.py#安装Django
[root@cobbler ~]# pip install Django==1.8.9#查看Django版本号
[root@cobbler ~]# python -c "import django; print(django.get_version())"#重启httpd
[root@cobbler ~]# systemctl restart httpd
web界面再重新加载
**登录cobbler**
默认用户名：cobbler 默认密码 ：cobbler![img](D:\Notes\md_img\071219_0722_Cobbler6.png)



### 将光盘导入到系统当中 

**将光盘插入到服务器中（注意：是服务端）**

![img](D:\Notes\md_img\071219_0722_Cobbler7.png)
![img](D:\Notes\md_img\071219_0722_Cobbler8.png)
**进行挂载光盘镜像**[root@cobbler ~]# mount /dev/cdrom /mnt
mount: /dev/sr0 is write-protected, mounting read-only
[root@cobbler ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/sda3 19G 1.7G 18G 9% /
devtmpfs 477M 0 477M 0% /dev
tmpfs 488M 0 488M 0% /dev/shm
tmpfs 488M 7.7M 480M 2% /run
tmpfs 488M 0 488M 0% /sys/fs/cgroup
/dev/sda1 197M 102M 95M 52% /boot
tmpfs 98M 0 98M 0% /run/user/0
/dev/sr0 4.2G 4.2G 0 100% /mnt
**web界面进行导入**
![img](D:\Notes\md_img\071219_0722_Cobbler9.png)
Actions --- DVD Importer --- 进行以下配置 --- run
Prefix --- 定义导入镜像的名称（注意：中间不能有空格）
Arch --- 定义系统镜像的架构
Breed --- 指定使用系统发现版本
Path --- 指定本地挂载光驱镜像的挂载点路径检查方法:
web页面检查: cobbler -- events -- 显示镜像正在导入事件信息
命令查看: [root@cobbler ~]# ps -ef|grep rsync
正在进行rsync同步
![img](D:\Notes\md_img\071219_0722_Cobbler10.png)
已经同步成功了
[root@cobbler ~]# du -sh /var/www/cobbler/ks_mirror/centos7.5_x86_64bit-x86_64/
4.2G    /var/www/cobbler/ks_mirror/centos7.5_x86_64bit-x86_64/
![img](D:\Notes\md_img\071219_0722_Cobbler11.png)



### 系统安装过程配置 

**修改网络安装系统后主机网卡名称信息**

![img](D:\Notes\md_img\071219_0722_Cobbler12.png)
![img](D:\Notes\md_img\071219_0722_Cobbler13.png)
configuration -- Distros -- 编辑指定系统镜像 -- save
Kernel Options: net.ifnames=0 biosdevname=0
**编写网络安装系统时的自动应答文件信息**
![img](D:\Notes\md_img\071219_0722_Cobbler14.png)
![img](D:\Notes\md_img\071219_0722_Cobbler15.png)
\# Cobbler for Kickstart Configurator for CentOS 7 by yao zhang
install
url --url=$tree
text
lang en_US.UTF-8
keyboard us
zerombr
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"
\#Network information
$SNIPPET('network_config')
\#network --bootproto=dhcp --device=eth0 --onboot=yes --noipv6 --hostname=CentOS7
timezone --utc Asia/Shanghai
authconfig --enableshadow --passalgo=sha512
rootpw --iscrypted $default_password_crypted
clearpart --all --initlabel
part /boot --fstype xfs --size 1024
part swap --size 1024
part / --fstype xfs --size 1 --grow
firstboot --disable
selinux --disabled
firewall --disabled
logging --level=info
reboot%pre
$SNIPPET('log_ks_pre')
$SNIPPET('kickstart_start')
$SNIPPET('pre_install_network_config')
\# Enable installation monitoring
$SNIPPET('pre_anamon')
%end%packages
@^minimal
@compat-libraries
@core
@debugging
@development
bash-completion
chrony
net-tools
lrzsz
nmap
sysstat
telnet
tree
vim
wget
%end%post
systemctl disable postfix.service
%endconfiguration -- kickstart Templates -- save
Create New Kickstart File
说明: 将之前编写好的自动应答文件信息进行复制进去，并定义一个名称
cfg文件说明信息
install #告知安装程序，这是一次全新安装，而不是升级
url --url="http://172.16.1.201/Centos-7/" #通过http下载安装镜像
text #以文本格式安装
lang en_US.UTF-8 #设置字符集格式
keyboard us #设置键盘类型
zerombr #清除mbr引导
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet" #指定引导记录被写入的位置
\#Network information
$SNIPPET('network_config') #配置网卡
\#network --bootproto=dhcp --device=eth1 --onboot=yes --noipv6 --hostname=CentOS7
timezone --utc Asia/Shanghai #可以使用dhcp方式设置网络
authconfig --enableshadow --passalgo=sha512 #设置密码格式
rootpw --iscrypted $default_password_crypted #密文密码
clearpart --all --initlabel #清空分区
part /boot --fstype xfs --size 1024 #/boot分区
part swap --size 1024 #swap分区
part / --fstype xfs --size 1 --grow #/分区
firstboot --disable #负责协助配置redhat一些重要的信息
selinux --disabled #关闭selinux
firewall --disabled      #关闭防火墙
logging --level=info #设置日志级别
reboot #安装完成重启%packages #包组段 @表示包组
@^minimal
@compat-libraries
@debugging
@development
tree
bash-completion
%end%post #脚本段，可以放脚本或命令
%end
**加载配置完成的自动应答配置文件**
![img](D:\Notes\md_img\071219_0722_Cobbler16.png)
![img](D:\Notes\md_img\071219_0722_Cobbler17.png)
configuration -- Profiles -- 编辑相应的镜像系统 -- save
Kickstart :     指定使用之前编辑好的应答文件
**配置主机安装系统完成后获取的IP地址信息**
![img](D:\Notes\md_img\071219_0722_Cobbler18.png)
![img](D:\Notes\md_img\071219_0722_Cobbler19.png)
configuration -- Systems -- Create New System -- General
Name centos7.5_x86_64bit #镜像名称
Profile centos7.5-x86_64
Status production
Kickstart 选择你的应答文件
**配置主机名**
![img](D:\Notes\md_img\071219_0722_Cobbler20.png)
configuration -- Systems -- Create New System -- Networking(Global)
Hostname oldboy
Name Servers 114.114.114.114
**配置网卡eth0**
![img](D:\Notes\md_img\071219_0722_Cobbler21.png)
![img](D:\Notes\md_img\071219_0722_Cobbler22.png)
![img](D:\Notes\md_img\071219_0722_Cobbler23.png)
configuration -- Systems -- Create New System -- Networking
Add Interface eth0
edit Interface eth0
MAC Address 对应的网卡生成生成
IP Address
Static
Subnet Mask
Per-Interface Gateway
**配置网卡eth1**
![img](D:\Notes\md_img\071219_0722_Cobbler24.png)
![img](D:\Notes\md_img\071219_0722_Cobbler25.png)
Add Interface eth1
edit Interface eth1
MAC Address 对应的网卡生成生成
IP Address
Static
Subnet Mask
save 保存
**配置完成**
![img](D:\Notes\md_img\071219_0722_Cobbler26.png)
**使之所有配置信息生效**![img](D:\Notes\md_img\071219_0722_Cobbler27.png)
![img](D:\Notes\md_img\071219_0722_Cobbler28.png)
**配置完成之后就可以创建一个虚拟主机了，注意网卡是要有两块的，一块是LAN区段的，内存是大于2G的。**![img](D:\Notes\md_img\071219_0722_Cobbler29.png)
![img](D:\Notes\md_img\071219_0722_Cobbler30.png)
安装成功![img](D:\Notes\md_img\071219_0722_Cobbler31.png)



### 批量化进行安装系统 

[root@cobbler ~]# cobbler system add --name=cobbler01 --mac=00:50:56:34:79:DD --profile=centos7.5-x86_64 --kickstart=/var/lib/cobbler/kickstarts/centos7.5.cfg --ip-address=10.0.0.111 --subnet=255.255.255.0 --gateway=10.0.0.254 --interface=eth0 --static=1 --hostname=cobbler01 --name-servers="114.114.114.114"
[root@cobbler ~]# cobbler system edit --name=cobbler01 --mac=00:50:56:29:E2:E1 --profile=centos7.5-x86_64 --kickstart=/var/lib/cobbler/kickstarts/centos7.5.cfg --ip-address=172.16.1.111 --subnet=255.255.255.0 --interface=eth1 --static=1
[root@cobbler ~]# cobbler sync

把这些命令写成一个脚本，定义变量。
比较麻烦的问题是如果服务器有很多，Mac地址怎样统计呢？
![img](D:\Notes\md_img\071219_0722_Cobbler32.png)
![img](D:\Notes\md_img\071219_0722_Cobbler33.png)
