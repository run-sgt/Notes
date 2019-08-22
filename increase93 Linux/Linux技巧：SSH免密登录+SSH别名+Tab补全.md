## Linux技巧：SSH免密登录+SSH别名+Tab补全

2019-07-12 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(72) 评论(0)

## 一、前言 

在企业中，随着服务器的数量越来越多，管理起来已经不是很方便了，同时也为了安全方面着想，使用统一的管理服务器进行管理其他的服务器。管理服务器连接其他的服务器的时候，是需要进行密码验证的，这样的话管理起来更是麻烦，所以我们需要ssh免密的进行登录到被管理服务器上面。





## 二、免密码登录 

首先创建本机的公钥和私钥，使用命令ssh-keygen。 默认生成的公钥名为id*rsa.pub ，私钥名为id*rsa。当然也可以通过参数 -t 来指定名称，如：ssh-keygen -t rsa。

创建本机的公钥和私钥
[root@m01 ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:KUE9hNYti3Sui/ClCHBgdv/8kGVmP1XQOrojVVdi2Kg root@m01
The key's randomart image is:
+---[RSA 2048]----+
| .=.. =o |
|.o . .+ * . o +o.|
|o.. .o.+ + . .o..|
|. . ...o*E = . |
|.. +.S . + o |
|. . o* = |
| . + + .o . o |
| . + . o o |
| . . |
+----[SHA256]-----+
管理端会生成/root/.ssh/目录和目录下的公钥和私钥
[root@m01 ~]# ll -d .ssh/
drwx------ 2 root root 57 2019-03-31 21:34 .ssh/
[root@m01 ~]# ll .ssh/
total 8
-rw------- 1 root root 1679 2019-03-31 21:26 id_rsa
-rw-r--r-- 1 root root 390 2019-03-31 21:26 id_rsa.pub
分发公钥给被管理端
[root@m01 ~]# ssh-copy-id -i ~/.ssh/id_rsa.pub 172.16.1.5
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '172.16.1.5 (172.16.1.5)' can't be established.
ECDSA key fingerprint is SHA256:gTDBWN8RT2VkHkXso8gE3nso7QzEXCMTdPGs6EfK4AM.
ECDSA key fingerprint is MD5:27:bd:4d:cd:88:5a:20:af:73:40:ea:86:48:e1:45:4d.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@172.16.1.5's password:Number of key(s) added: 1Now try logging into the machine, with: "ssh '172.16.1.5'"
and check to make sure that only the key(s) you wanted were added.
再次查看管理端的/root/.ssh/目录
[root@m01 ~]# ll .ssh/
total 12
-rw------- 1 root root 1679 2019-03-31 21:26 id_rsa
-rw-r--r-- 1 root root 390 2019-03-31 21:26 id_rsa.pub
-rw-r--r-- 1 root root 172 2019-03-31 21:29 known_hosts
查看known_hosts文件的内容
[root@m01 ~]# cat .ssh/known_hosts
172.16.1.5 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=查看被管理端的/root/.ssh/目录
[root@lb01 ~]# ll .ssh/
total 4
-rw------- 1 root root 390 2019-03-31 21:34 authorized_keys
查看authorized_keys文件的内容
[root@lb01 ~]# cat .ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDW7s8e6wVrq/bkDz3mFiorcInoQnPOv+gDjQBEpfXNvhtpbj9sDe/M010bZC1BOvbCQ4LJ0eCDpKH3C40c29/GbouDBnnL/JENkw7sz/0NGCjaEP2246gnRAQ4Zj+1G3k2B2rdKax9Hvbs3tcVFOsEZdIVj0icJiLF68+dEbZOjN7Wo8wrvK3it/r/U66qQGu/zikZOh5AWqX9c7NE6ZaNao+myMbjiN9Ki4swPjeG36V4ivr+qhGV9lJoCl891tXnLpRz3M/+zlDvvObTtpTlHAD2R+QULTkELlUIck0jvWgjIspxtKSQfZ/tGkZCb4ixv2HmT0jzGtuCL5W7v0sj root@m01
说明：
id_rsa :              #生成的私钥文件id_rsa.pub ：         #生成的公钥文件know_hosts :          #已知的主机公钥清单authorized_keys         #管理端的公钥信息
如果希望ssh公钥生效需满足至少下面两个条件：　　1) .ssh目录的权限必须是700
　　2) .ssh/authorized_keys文件权限必须是600
批量分发管理端公钥脚本（前提是被管理端的root密码一样）
\#!/usr/bin/bash
\# 创建密钥对
rm -f ~/.ssh/id_rsa*
ssh-keygen -t rsa -f ~/.ssh/id_rsa -N "" &>/dev/null# 批量分发公钥的操作
for ip in 5 6 7 8 9 31 41 51 61 71
do
sshpass -p123456 ssh-copy-id -i ~/.ssh/id_rsa.pub "-o StrictHostKeyChecking=no" 172.16.1.$ip &>/dev/null
if [ $? -eq 0 ];then
echo -e "\033[32mhost 172.16.1.$ip Distribution of the secret key Success!!! \033[0m"
echo
else
echo -e "\033[31mhost 172.16.1.$ip Distribution of the secret key Failed !!! \033[0m"
echo
fi        
done
执行脚本，出现失败的原因是，我没有把对应的虚拟机启动
[root@m01 ~]# sh fenfa.sh
![img](D:\Notes\md_img\071219_0729_LinuxSSH1.png)
再次查看管理端know_hosts的文件内容
[root@m01 ~]# cat .ssh/known_hosts
172.16.1.5 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=
172.16.1.6 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=
172.16.1.7 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=
172.16.1.8 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=
172.16.1.51 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGNbQsrXu55jLxY/7WnzAYCoJHfXaJGP362/kjvZ7o+/H0tlnjSwOM9inXnb4MYpGXTR+yJnwTaRgcUm6h07htM=
172.16.1.61 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNpslEwhfPGHWhCwhbR01DhBmbg7saQ3C/2jnIO46e6nKxkGmx95YFhfQx0R9HazcWMjZ1pfK3dQT5UqvK4ARWs=
现在你已经可以免密的进行登录到被管理主机上面去了
[root@m01 ~]# ssh 172.16.1.5
Last login: Sun Mar 31 21:24:05 2019 from 10.0.0.1
[root@lb01 ~]# logout
Connection to 172.16.1.5 closed.
[root@m01 ~]# ssh 172.16.1.6
Last login: Sun Mar 31 21:24:08 2019 from 10.0.0.1
[root@lb02 ~]# logout
Connection to 172.16.1.6 closed.



## 三、别名登录 

使用 vim ~/.ssh/config命令编辑文件，添加如下内容：

Host lb01
HostName 172.16.1.5
Port 22
User root
IdentityFile ~/.ssh/id_rsa.pub
IdentitiesOnly yes
使用ssh <Host>登录目的主机，这里使用sshlb01就相当于执行ssh root@172.16.1.5
注释：HostName         #指定登录的主机名或IP地址Port             #指定登录的端口号User             #登录用户名IdentityFile     #登录的公钥文件IdentitiesOnly     #只接受SSH key 登录
编写配置文件
[root@m01 ~]# cat ~/.ssh/config
Host lb01
HostName 172.16.1.5
Port 22
User root
IdentityFile ~/.ssh/id_rsa.pub
IdentitiesOnly yes
Host lb02
HostName 172.16.1.6
Port 22
User root
IdentityFile ~/.ssh/id_rsa.pub
IdentitiesOnly yes
Host web01
HostName 172.16.1.7
Port 22
User root
IdentityFile ~/.ssh/id_rsa.pub
IdentitiesOnly yes
Host web02
HostName 172.16.1.8
Port 22
User root
IdentityFile ~/.ssh/id_rsa.pub
IdentitiesOnly yes
别名免密登录
[root@m01 ~]# ssh web02
Last login: Sun Mar 31 22:06:50 2019 from 172.16.1.61
[root@web02 ~]# logout
Connection to 172.16.1.8 closed.
[root@m01 ~]# ssh web01
Last login: Sun Mar 31 22:00:19 2019 from 172.16.1.61
[root@web01 ~]# logout
Connection to 172.16.1.7 closed.
[root@m01 ~]# ssh lb01
Last login: Sun Mar 31 22:05:19 2019 from 172.16.1.61
[root@lb01 ~]# logout
Connection to 172.16.1.5 closed.



## 四、tab补全 

linux系统本身就自带tab补全命令，但是他这个补全只是针对命令补全的，而我们这里是需要输入别名的时候，能够补全这个别名。想要使用这个功能的话，需要下载一些软件。

[root@m01 ~]# yum install bash-completion bash-completion-extra -y
查看结果
[root@m01 ~]# ssh web0
web01 web02 web03
[root@m01 ~]# ssh lb0
lb01 lb02
亦或者将下方此行加入 .bashrc 最后一行即可.特别要注意的是，这两种不能混在一起使用，这也是一个不大但也不小的坑。
complete -W "$(echo $(grep '^ssh ' .bash_history | sort -u | sed 's/^ssh //'))" ssh