## KickStart无人值守安装

2019-07-11 分类：[网站架构](https://www.increase93.com/?cat=6) 阅读(31) 评论(0)

## 一、 安装系统的方法 



　　    光盘（ISO文件，光盘的镜像文件）===>>每一台物理机都得给一个光驱，如果用外置光驱的话，是不是每台机器都需要插一下
    
　      U盘：ISO镜像刻录到U盘==>>需要每台机器都需要插一下

　      并行安装==>>网络安装　　    自动化安装



## 二、 linux下批量安装系统 



​    kickstart是RedHat公司开源的软件，所以对CentOS兼容性最好。

原理：　　我们将手动安装的所有的详细步骤记录到一个文件中，然后kickstart通过读取这个文件就可以实现自动化安装系统。　　kickstart是一个项目的名称。没有这个软件。使用者水平是高中以上　　cobbler是对kickstart的所有组件的封装。使用者水平是初中以上。本质上就是网页版本的kickstart。



### 1.PXE说明 



　　PXE，全名Pre-boot Execution Environment，预启动执行环境；

　　通过网络接口启动计算机，不依赖本地存储设备（如硬盘）或本地已安装的操作系统；　　由Intel和Systemsoft公司于1999年9月20日公布的技术；　　客户端/Server的工作模式；　　PXE客户端会调用网际协议(IP)、用户数据报协议(UDP)、动态主机设定协议(DHCP)、小型文件传输协议(TFTP)等网络协议；　　PXE客户端(客户端)这个术语是指机器在PXE启动过程中的角色。一个PXE客户端可以是一台服务器、笔记本电脑或者其他装有PXE启动代码的机器（我们电脑的网卡）



### 2. kickstart原理 



![img](D:\Notes\md_img\071119_0959_KickStart1.png)





### 3. PXE请求顺序说明 



1.PXE客户端发送UDP广播请求

　　PXE 客户端从自己的PXE网卡启动，通过PXE BootROM(自启动芯片)会以UDP(简单用户数据报协议)发送一个广播请求，向本网络中的DHCP服务器索取IP。2.DHCP服务器提供信息　　DHCP服务器收到客户端的请求，验证是否来至合法的PXE 客户端的请求，验证通过它将给客户端一个"提供"响应，这个"提供"响应中包含了为客户端分配的IP地址、pxelinux启动程序(TFTP)位置，以及配置文件所在位置。3.PXE客户端请求下载启动文件　　客户端收到服务器的"回应"后，会回应一个帧，以请求传送启动所需文件。这些启动文件包括：pxelinux.0、pxelinux.cfg/default、vmlinuz、initrd.img等文件。4.TETP服务器响应客户端请求并传送文件　　当服务器收到客户端的请求后，他们之间之后将有更多的信息在客户端与服务器之间作应答, 用以决定启动参数。BootROM由TFTP通讯协议从tftp服务器
下载启动安装程序所必须的文件(pxelinux.0、pxelinux.cfg/default)。default文件下载完成后，会根据该文件中定义的引导顺序，启动Linux安装程序的引导内核。5.请求下载自动应答文件　　客户端通过pxelinux.cfg/default文件成功的引导Linux安装内核后，安装程序首先必须确定你通过什么安装介质来安装linux，如果是通过网络安装(NFS, FTP, HTTP)，则会在这个时候初始化网络，并定位安装源位置。接着会读取default文件中指定的自动应答文件ks.cfg所在位置，根据该位置请求下载该文件。6.客户端安装操作系统　　将ks.cfg文件下载回来后，通过该文件找到http镜像，并按照该文件的配置请求下载安装过程需要的软件包。　　http镜像和客户端建立连接后，将开始传输软件包，客户端将开始安装操作系统。　　安装完成后，将提示重新引导计算机。



## 三、 kickstart批量安装系统实践 



一般批量安装操作系统最好一次安装23台机器最佳，主要因为大部分交换机为24口，安装23台服务器没有太大的压力





### 1.环境说明 



[root@kickstart ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)
[root@kickstart ~]# uname -a
Linux kickstart 3.10.0-862.el7.x86_64 #1 SMP Fri Apr 20 16:44:24 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
[root@kickstart ~]# hostname
kickstart
[root@kickstart ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
Active: inactive (dead)
Docs: man:firewalld(1)
[root@kickstart ~]# getenforce
Disabled
[root@kickstart ~]# hostname -I
10.0.0.201 172.16.1.201





### 2.安装部署DHCP 



[root@kickstart ~]# yum -y install dhcp
[root@kickstart ~]# rpm -qc dhcp
/etc/dhcp/dhcpd.conf
/etc/dhcp/dhcpd6.conf
/etc/openldap/schema/dhcp.schema
/etc/sysconfig/dhcpd
/var/lib/dhcpd/dhcpd.leases
/var/lib/dhcpd/dhcpd6.leases

**编写配置文件**
cat >>/etc/dhcp/dhcpd.conf<<EOF
subnet 172.16.1.0 netmask 255.255.255.0 {
range 172.16.1.100 172.16.1.199;
option subnet-mask 255.255.255.0;
default-lease-time 21600;
max-lease-time 43200;
next-server 172.16.1.201;
filename "/pxelinux.0";
}
EOF
**配置文件解释说明**
range 172.16.1.100 172.16.1.199; # 可分配的起始IP-结束IP
option subnet-mask 255.255.255.0;      # 设定netmask
default-lease-time 21600;      # 设置默认的IP租用期限
max-lease-time 43200;      # 设置最大的IP租用期限
next-server 172.16.1.201; # 告知客户端TFTP服务器的ip
filename "/pxelinux.0";      # 告知客户端从TFTP根目录下载pxelinux.0文件
**启动服务**
[root@kickstart ~]# systemctl restart dhcpd.service
[root@kickstart ~]# systemctl enable dhcpd.service
Created symlink from /etc/systemd/system/multi-user.target.wants/dhcpd.service to /usr/lib/systemd/system/dhcpd.service.
[root@kickstart ~]# netstat -lntup|grep dhcpd
udp 0 0 0.0.0.0:67 0.0.0.0:* 2415/dhcpd
**查看其日志信息： (日志位置为/var/log/messages)**
[root@kickstart ~]# tailf /var/log/messages
Mar 29 16:12:42 oldboy dhcpd: ** Ignoring requests on eth1. If this is not what
Mar 29 16:12:42 oldboy dhcpd: you want, please write a subnet declaration
Mar 29 16:12:42 oldboy dhcpd: in your dhcpd.conf file for the network segment
Mar 29 16:12:42 oldboy dhcpd: to which interface eth1 is attached. **
Mar 29 16:12:42 oldboy dhcpd:
Mar 29 16:12:42 oldboy dhcpd: Listening on LPF/eth0/00:0c:29:a9:1b:37/10.0.0.0/24
Mar 29 16:12:42 oldboy dhcpd: Sending on LPF/eth0/00:0c:29:a9:1b:37/10.0.0.0/24
Mar 29 16:12:42 oldboy dhcpd: Sending on Socket/fallback/fallback-net
Mar 29 16:12:42 oldboy systemd: Started DHCPv4 Server Daemon.
Mar 29 16:12:48 oldboy systemd: Reloading.
注：配置dhcp的时候是可以配置监听的网卡，指定监听网卡
说明：此知识点与本文无关，只是作者用过这个功能，记于此。 **多网卡默认监听eth0，指定DHCP监听eth1网卡**[root@kickstart ~]# vim /etc/sysconfig/dhcpd
\# Command line options here
DHCPDARGS=eth1 # 指定监听网卡



### 3.安装配置TFTP服务 



**安装tftp服务**

[root@kickstart ~]# yum -y install tftp-server
**启动tftp服务**
[root@kickstart ~]# systemctl start tftp.socket
[root@kickstart ~]# systemctl enable tftp.socket
Created symlink from /etc/systemd/system/sockets.target.wants/tftp.socket to /usr/lib/systemd/system/tftp.socket.
**tftp根目录**
[root@kickstart ~]# cd /var/lib/tftpboot/



### 4.创建一个新的空白虚拟机 



**说明：Centos7.5安装系统至少需要2G内存，低于2G将无法安装，不要选择镜像，之后启动虚拟机**

![img](D:\Notes\md_img\071119_0959_KickStart2.png)
![img](D:\Notes\md_img\071119_0959_KickStart3.png)
启动发现又tftp但是没找到要加载的系统。上面报错是在TFTP服务的根目录找不到启动文件pxelinux.0



### 5.获取pxelinux.0系统 



syslinux是一个功能强大的引导加载程序，而且兼容各种介质。SYSLINUX是一个小型的Linux操作系统，它的目的是简化首次安装Linux的时间，并建立修护或其它特殊用途的启动盘。如果没有找到pxelinux.0这个文件,可以安装一下。

[root@kickstart tftpboot]# yum install -y syslinux
**复制pxelinux.0文件到tftp根目录**
[root@kickstart tftpboot]# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
[root@kickstart tftpboot]# ls
pxelinux.0
启动虚拟机
![img](D:\Notes\md_img\071119_0959_KickStart4.png)
首先排除最简单故障原因：selinux是否关闭，防火墙是否关闭上面的错误是因为pxelinux.0这个小系统的配置文件（default）不存在，或者文件名不对



### 6.添加pxelinux.0配置文件 



**给虚拟机添加上centos7的镜像，注意实在kickstart服务端添加**

![img](D:\Notes\md_img\071119_0959_KickStart5.png)
**将镜像挂载上****特别说明：**由于这是测试环境可以使用挂载，在生产环境中必须把镜像中的文件复制到硬盘上，光驱速度太慢。[root@kickstart ~]# mkdir -p /var/www/html/Centos-7/
[root@kickstart ~]# mount /dev/cdrom /var/www/html/Centos-7/
mount: /dev/sr0 is write-protected, mounting read-only
**将镜像中的相关文件复制到tftp根目录**
[root@kickstart ~]# cp -a /var/www/html/Centos-7//isolinux/* /var/lib/tftpboot/
[root@kickstart ~]# mkdir -p /var/lib/tftpboot/pxelinux.cfg
[root@kickstart ~]# cp /var/www/html/Centos-7/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
**修改default配置文件实现通过网络安装操作系统**
**CentOS7.X**
**网络安装的关键点,修改default文件**[root@kickstart tftpboot]# vim /var/lib/tftpboot/pxelinux.cfg/default
....
label linux
menu label ^Install CentOS 7
kernel vmlinuz
\#append initrd=initrd.img inst.stage2=hd:LABEL=CentOS\x207\x20x86_64 quiet
append initrd=initrd.img method=http://172.16.1.201/Centos-7/
....
修改完成后，重启pxe客户端，就会有安装界面，与使用光盘安装一致，这里就不是做详细的记录了。注意实现网路安装需要将下一步完成*(**配置http**服务*)。
![img](D:\Notes\md_img\071119_0959_KickStart6.png)
**CentOS7实现自动化安装的default文件**[root@kickstart ~]# cat /var/lib/tftpboot/pxelinux.cfg/default
default ks
prompt 0

label ks
kernel vmlinuz
append initrd=initrd.img ks=http://172.16.1.201/ks_config/CentOS7-ks.cfg net.ifnames=0 biosdevname=0 ksdevice=eth1

### 7.配置httpd服务 



**安装apache服务**

[root@kickstart tftpboot]# yum install httpd -y
**启动apache服务**
[root@kickstart tftpboot]# systemctl start httpd.service
[root@kickstart tftpboot]# systemctl enable httpd.service
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
[root@kickstart tftpboot]# netstat -lntup|grep httpd
tcp6 0 0 :::80 :::* LISTEN 17537/httpd
**通过浏览器访问，使用curl命令镜像访问。**
http://10.0.0.201/Centos-7/[root@kickstart tftpboot]# curl http://172.16.1.201/Centos-7/
![img](D:\Notes\md_img\071119_0959_KickStart7.png)



## 四、ks文件说明 





### 1. ks文件的作用 



　　　通常，我们在安装操作系统的过程中，需要大量的和服务器交互操作，为了减少这个交互过程，kickstart就诞生了。使用这种kickstart，只需事先定义好一个Kickstart自动应答配置文件ks.cfg（通常存放在安装服务器上），并让安装程序知道该配置文件的位置，在安装过程中安装程序就可以自己从该文件中读取安装配置，这样就避免了在安装过程中多次的人机交互，从而实现无人值守的自动化安装。





### 2. ks文件生成的三种方式 



**方法1**

　　每安装好一台Centos机器，Centos安装程序都会创建一个kickstart配置文件，记录你的真实安装配置。如果你希望实现和某系统类似的安装，可以基于该系统的kickstart配置文件来生成你自己的kickstart配置文件。（生成的文件名字叫anaconda-ks.cfg位于/root/anaconda-ks.cfg）　
**方法2**
　Centos提供了一个图形化的kickstart配置工具。在任何一个安装好的Linux系统上运行该工具，就可以很容易地创建你自己的kickstart配置文件。kickstart配置工具命令为redhat-config-kickstart（RHEL3）或system-config-kickstart（RHEL4，RHEL5）.网上有很多用CentOS桌面版生成ks文件的文章，如果有现成的系统就没什么可说。但没有现成的，也没有必要去用桌面版，命令行也很简单。
**方法3**
　阅读kickstart配置文件的手册。用任何一个文本编辑器都可以创建你自己的kickstart配置文件。



### 3. centos7.5自动应答文件(ks) 



创建ks文件存放目录

[root@kickstart ~]# mkdir /var/www/html/ks_config -p
编写ks文件
kickstart之中rootpw密码生成方法
https://www.cnblogs.com/panwenbin-logs/p/9340430.html
[root@kickstart ks_config]# vim /var/www/html/ks_config/CentOS7-ks.cfg
\# Kickstart Configurator for CentOS 7 by yao zhang #命令段
install #告知安装程序，这是一次全新安装，而不是升级
url --url="http://172.16.1.201/Centos-7/" #通过http下载安装镜像
text #以文本格式安装
lang en_US.UTF-8 #设置字符集格式
keyboard us #设置键盘类型
zerombr #清除mbr引导
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet" #指定引导记录被写入的位置
network --bootproto=static --device=eth0 --gateway=10.0.0.254 --ip=10.0.0.199 --nameserver=223.5.5.5 --netmask=255.255.255.0 --activate #配置eth0网卡
network --bootproto=static --device=eth1 --ip=172.16.1.199 --netmask=255.255.255.0 --activate #配置eth1网卡
network --hostname=oldboy #设置主机名
\#network --bootproto=dhcp --device=eth1 --onboot=yes --noipv6 --hostname=CentOS7
timezone --utc Asia/Shanghai #可以使用dhcp方式设置网络
authconfig --enableshadow --passalgo=sha512 #设置密码格式
rootpw --iscrypted $6$X20eRtuZhkHznTb4$dK0BJByOSAWSDD8jccLVFz0CscijS9ldMWwpoCw/ZEjYw2BTQYGWlgKsn945fFTjRC658UXjuocwJbAjVI5D6/ #密文密码
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
nmap
sysstat
lrzsz
dos2unix
telnet
wget
vim
bash-completion
%end%post #脚本段，可以放脚本或命令
systemctl disable postfix.service #关闭邮件服务开机自启动
%end
上面的事情做完以后就可以批量安装系统了，装了几台之后你就会发现，新装完系统的主机IP地址都是一样的，我们可不可以让其ip不重复呢？可不可以装完系统再对系统进行优化呢？
![img](D:\Notes\md_img\071119_0959_KickStart8.png)
优化一下ks文件和优化系统的及脚本[root@kickstart ks_config]# cat CentOS7-ks.cfg
\# Kickstart Configurator for CentOS 7 by yao zhang
install
url --url="http://172.16.1.201/Centos-7/"
text
lang en_US.UTF-8
keyboard us
zerombr
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"
network --bootproto=static --device=eth0 --gateway=10.0.0.254 --ip=10.0.0.199 --nameserver=223.5.5.5 --netmask=255.255.255.0 --activate
network --bootproto=static --device=eth1 --ip=172.16.1.199 --netmask=255.255.255.0 --activate
network --hostname=oldboy
\#network --bootproto=dhcp --device=eth1 --onboot=yes --noipv6 --hostname=CentOS7
timezone --utc Asia/Shanghai
authconfig --enableshadow --passalgo=sha512
rootpw --iscrypted $6$X20eRtuZhkHznTb4$dK0BJByOSAWSDD8jccLVFz0CscijS9ldMWwpoCw/ZEjYw2BTQYGWlgKsn945fFTjRC658UXjuocwJbAjVI5D6/
clearpart --all --initlabel
part /boot --fstype xfs --size 1024
part swap --size 1024
part / --fstype xfs --size 1 --grow
firstboot --disable
selinux --disabled
firewall --disabled
logging --level=info
reboot%packages
@^minimal
@compat-libraries
@debugging
@development
tree
nmap
sysstat
lrzsz
dos2unix
telnet
wget
vim
bash-completion
%end%post
curl -o /root/optimize.sh http://172.16.1.201/ks_config/optimize.sh &>/dev/null
curl -o /root/update_hstname_ip.sh http://172.16.1.201/ks_config/update_hstname_ip.sh &>/dev/null
/bin/sh /root/optimize.sh
%end优化的脚本
[root@kickstart ks_config]# cat optimize.sh
\#!/usr/bin/bash
rm -f /etc/yum.repos.d/*
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
修改主机名和ip的脚本
[root@kickstart ks_config]# cat update_hstname_ip.sh
\#!/usr/bin/sh
if [ $# -ne 2 ];then
echo "/bin/sh $0 hostname PartIP"
exit 1
fi
hostnamectl set-hostname $1
/bin/bash
sed -i "s#199#$2#g" /etc/sysconfig/network-scripts/ifcfg-eth[01]
systemctl restart network