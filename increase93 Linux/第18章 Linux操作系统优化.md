# 第18章 Linux操作系统优化

### 1. 更改Yum源和添加epel源

```
默认国外的yum源(软件仓库)比较慢，所以换成国内的。

#1、备份
[root@qls ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

#2、下载新的CentOS-Base.repo 到/etc/yum.repos.d/
[root@qls ~]# curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

#3.添加epel源
[root@qls ~]# curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

```

### 2. 关闭SELinux

```
    SELinux（Security-Enhanced Linux）是美国国家安全局（NSA）对于强制访问控制的实现，这个功能让系统管理员又爱又恨，这里我们还是把它给关闭了吧，至于安全问题，后面通过其他手段来解决，这也是大多数生产环境的做法，如果非要开启也是可以的。

#临时关闭
[root@qls ~]# setenforce  0

#永久关闭
[root@qls ~]# sed 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config

#检查结果
[root@qls ~]# grep "disabled" /etc/selinux/config

```

### 3. 关闭防火墙（Firewalld）

```
    关闭防火墙的目的是为了让初学者学习更方便，将来在学了Firewalld技术后可再统一开启。 在企业环境中，一般只有配置外网IP的linux服务器才需要开启防火墙，但即使是有外网IP，对于高并发高流量的业务服务器仍是不能开的，因为会有较大性能损失，导致网站访问很慢，这种情况下只能在前端加更好的硬件防火墙了。

#临时关闭
[root@qls ~]# systemctl  stop firewalld

#永久关闭
[root@qls ~]# systemctl  disable  firewalld
```

### 4. 关闭NetworkManager

```
#临时关闭
[root@qls ~]# systemctl  stop  NetworkManager

#永久关闭
[root@qls ~]# systemctl  disable  NetworkManager
```

### 5. 同步系统时间

```
#给定时任务加上注释
[root@qls ~]# echo '#Timing synchronization time' >>/var/spool/cron/root

#定时任务
[root@qls ~]# echo '*/5 * * * * /usr/sbin/ntpdate ntp1.aliyun.com &>/dev/null' >>/var/spool/cron/root

#检查结果
[root@qls ~]# crontab -l
```

### 6. 加大文件描述

```
#加大文件描述符
[root@qls ~]# echo '*               -       nofile          65535 ' >>/etc/security/limits.conf 

#检查结果
[root@qls ~]# tail -1 /etc/security/limits.conf
```

### 7. 别名及环境变量优化

```
#设置
[root@qls ~]# cat>>/etc/profile.d/color.sh<<"EOF"
alias ll='ls -l --color=auto --time-style=long-iso'
PS1="\[\e[37;40m\][\[\e[32;1m\]\u\[\e[37;40m\]@\h \[\e[36;40m\]\w\[\e[0m\]]\[\e[32;1m\]\\$ \[\e[0m\]"
export HISTTIMEFORMAT='%F-%T '
EOF

#生效
[root@qls ~]# source  /etc/profile
```

### 8. 内核优化

```
#设置
[root@qls ~]# cat >>/etc/sysctl.conf<<EOF
net.ipv4.tcp_fin_timeout = 2
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_keepalive_time = 600
net.ipv4.ip_local_port_range = 4000    65000
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_max_tw_buckets = 36000
net.ipv4.route.gc_timeout = 100
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_synack_retries = 1
net.core.somaxconn = 16384
net.core.netdev_max_backlog = 16384
net.ipv4.tcp_max_orphans = 16384
EOF

#生效
[root@qls ~]# sysctl  -p

```

### 9. 配置SSH远程管理服务

```
#禁止DNS进行反向解析
[root@qls ~]# sed -i 's/#UseDNS yes/UseDNS no/g'  /etc/ssh/sshd_config

#检查结果
[root@qls ~]# grep 'UseDNS no'  /etc/ssh/sshd_config 

#禁止GSS认证，减少连接时产生的延迟
[root@qls ~]# sed -i 's/GSSAPIAuthentication yes/GSSAPIAuthentication no/g'  /etc/ssh/sshd_config

#检查结果
[root@qls ~]# grep 'GSSAPIAuthentication no'  /etc/ssh/sshd_config

#生效
[root@qls ~]# systemctl    restart   sshd
```

### 10. 修改主机名和IP脚本

```
#脚本如下

[root@qls ~]# cat>/root/hostname_ip.sh<<"EOF"
#!/usr/bin/sh
source /etc/init.d/functions
if [ $# -ne 2 ];then
  echo "/bin/sh $0 新的主机名  新的IP地址主机位"
  exit 1
fi
hostnamectl  set-hostname   $1
if [ $? -eq 0 ];then
    action "hostname update Successful！" /bin/true
else
    action "hostname update Failed！" /bin/false
fi
sed -ri  "/^IPA/s#(.*\.).*#\1$2#g"  /etc/sysconfig/network-scripts/ifcfg-eth[01]
if [ $? -eq 0 ];then
    action "IP update Successful！" /bin/true
else
    action "IP update Failed！" /bin/false
fi
systemctl  restart  network
bash
EOF
```

### 11. 安装常用软件

```
[root@qls ~]# yum -y install tree nmap sysstat lrzsz dos2unix telnet bash-completion bash-completion-extras vim nc lsof  net-tools rsync ntpdate
```

### 12. Linux基础优化及安全小结

```
1）不用root登录管理系统，而以普通用户登录通过sudo授权管理。

2）更改默认的远程连接SSH服务端口，禁止root用户远程连接，甚至要更改SSH服务只监听内网IP。

3）定时自动更新服务器的时间，使其和互联网时间同步。

4）配置yum更新源，从国内更新源下载安装软件包。

5）关闭SELinux及iptables（在工作场景中，如果有外部IP一般要打开iptables，高并发高流量的服务器可能无法开启）。

6）调整文件描述符的数量，进程及文件的打开都会消耗文件描述符数量。

7）定时自动清理邮件临时目录垃圾文件，防止磁盘的inodes数被小文件占满（注意Centos6和Centos5要清除的目录不同）。

8）Linux内核参数优化/etc/sysctl.conf，执行sysctl -p生效。

9）更改系统字符集为“zh_CN.UTF-8”，使其支持中文，防止出现乱码问题。

10）锁定关键系统文件如/etc/passwd、/etc/shadow、/etc/group、/etc/gshadow、/etc/inittab，处理以上内容后把chattr、lsattr改名为luffy，转移走，这样就安全多了。

11）清空/etc/issue、/etc/issue.net，去除系统及内核版本登录前的屏幕显示。

12）清除多余的系统虚拟用户账号。

13）为grub引导菜单加密码。

14）禁止主机被ping。

15）打补丁并升级有已知漏洞的软件。
```

