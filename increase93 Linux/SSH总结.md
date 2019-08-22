## SSH总结

2019-07-15 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(76) 评论(0)

1. ## 远程服务介绍说明

SSH是一个安全协议，在进行数据传输时，会对数据包进行加密处理，加密后在进行数据传输。确保了数据传输安全。那SSH服务主要功能有哪些呢？

1.提供远程连接服务器的服务、2.对传输的数据进行加密

SSH主要用来连接服务器

Telnet主要用来连接网络设备

SSH：     实现数据加密传输 22 默认支持root用户远程连接

telnet：        实现数据明文传输 23 默认不支持root用户远程连接

1. ### 如何实现telnet远程管理

服务端配置

第一里程：下载安装telnet远程管理服务端软件

[root@oldboy ~]# yum install -y telnet-server telnet

第二里程：启动telnet服务

[root@oldboy ~]# systemctl start telnet.socket

第三里程：检查服务是否启动成功

[root@oldboy ~]# netstat -lntup|grep 23

第四里程：创建一个普通用户并给其设置密码

[root@oldboy ~]# useradd www

[root@oldboy ~]# echo "1" |passwd --stdin www

客户端配置

第一里程：下载安装telnet软件

yum install -y telnet

第二里程：实现远程连接

telnet 10.0.0.7 （要输入普通用户信息和密码信息）

1. ## SSH服务软件重要文件组成

[root@oldboy ~]# rpm -ql openssh-clients

/etc/ssh/ssh_config --- ssh客户端配置文件

/usr/bin/scp --- 远程拷贝命令

/usr/bin/sftp --- 实现ftp文件传输功能命令

/usr/bin/slogin --- 远程登陆命令

/usr/bin/ssh --- 远程登陆命令

/usr/bin/ssh-copy-id --- 分发公钥命令？？？

[root@oldboy ~]# rpm -ql openssh-server

/etc/ssh/sshd_config --- 服务端配置文件

[root@oldboy ~]# rpm -ql openssh

/usr/bin/ssh-keygen --- 创建密钥对命令

1. ## SSH服务远程连接原理

1）服务器端开启ssh服务，在端口22监听客户端请求

2）客户端发出远程连接请求

3）如果是第一次请求连接，服务端会响应一个确认信息，并把自己的公钥发送给客户端

4）客户端进行确认并根据服务端发来的公钥对登录的密码进行加密

5）加密后的信息传回到服务端，服务端用自己的私钥解密，若密码正确，则用户登录成功。

![img](D:\Notes\md_img\071519_0706_SSH1.png)

注意：

sshv1版本：密钥对不会定期更换，容易被黑客人员破解

sshv2版本：密钥对会进行定期更换，不容易被黑客人员破解

检查版本：ssh -1 root@10.0.0.51 如果报错说明ssh只支持sshv2版本

1. ## SSH服务远程连接方式

基于密码的方式：交互方式远程连接

基于密钥的方式：免交互方式远程连接

基于密钥方式工作原理：

1）在客户端创建出新的密钥对

2）客户端将公钥传输给服务端（基于密码传输）

3）服务端接受公钥文件并保存

4）当客户端再次请求和服务端建立连接

5）服务端会进行公钥质询过程

6）客户端利用私钥解密公钥，将解密结果发给服务端

7）最终实现客户端和服务端远程连接建立

![img](D:\Notes\md_img\071519_0706_SSH2.png)

1. ## SSH服务基于密钥方式实现远程连接

管理服务器

第一个里程：创建密钥对信息

[root@oldboy ~]# ssh-keygen -t dsa

Generating public/private dsa key pair.

Enter file in which to save the key (/root/.ssh/id_dsa):

Enter passphrase (empty for no passphrase):

Enter same passphrase again:

Your identification has been saved in /root/.ssh/id_dsa.

Your public key has been saved in /root/.ssh/id_dsa.pub.

The key fingerprint is:

SHA256:Uii0oUMScMl72i4c1WRs1ewKYeTKhjtrg6L/9Rvxq8k root@oldboy

The key's randomart image is:

+---[DSA 1024]----+

|=oo.oo...o |

| +oo +B. o |

| o.o*o... |

| .+ooo. . |

| .=+ ..S. |

| oo. ..o |

| ooo . . . |

|o =o.. o o . |

|+oo+. Eo. |

+----[SHA256]-----+

第二个里程：利用公钥分发命令分发公钥信息

[root@oldboy ~]# ssh-copy-id -i ~/.ssh/id_dsa.pub root@10.0.0.51

/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_dsa.pub"

The authenticity of host '10.0.0.51 (10.0.0.51)' can't be established.

ECDSA key fingerprint is SHA256:xMPEOIBogi7ssMeXBe3FKFKTw/yYDpfIE5OKNSrBdMc.

ECDSA key fingerprint is MD5:7f:24:0f:ae:9e:0f:fa:13:83:f9:b0:12:1f:f4:0e:e9.

Are you sure you want to continue connecting (yes/no)? yes

/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

root@10.0.0.51's password:

Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'root@10.0.0.51'"

and check to make sure that only the key(s) you wanted were added.

第三个里程：进行远程连接测试

ssh root@10.0.0.51

ssh root@10.0.0.51 "ifconfig"

问题一：

执行：ssh oldboy@10.0.0.51 不能连接的

说明：基于密钥连接，会识别用户身份信息

密钥分发的实际过程：执行ssh-copy-id命令都干了什么

\01. 将公钥文件传输到被管理服务器相应用户的家目录中

scp -rp ~/.ssh/id_dsa.pub root@10.0.0.51:~

​    输入密码

\02. 被管理服务在家目录下面创建一个.ssh目录

mkdir ~/.ssh/

\03. 将公钥文件移动到.ssh目录中，并进行修改名称和授权

​    mv ~/id_dsa.pub ~/.ssh/authorized_keys

​    chmod 600 ~/.ssh/authorized_keys

为什么要进行重命名：

cat /etc/ssh/sshd_config

47 AuthorizedKeysFile .ssh/authorized_keys

思考:如何实现自动批量分发公钥？

批量分发公钥的操作

\#!/bin/bash

\# 创建密钥对

rm -f ~/.ssh/id_dsa*

ssh-keygen -t dsa -f ~/.ssh/id_dsa -N "" &>/dev/null

\# 批量分发公钥的操作

for ip in 5 6 7 8 9 31 41 51 61 71

do

sshpass -p1 ssh-copy-id -i ~/.ssh/id_dsa.pub "-o StrictHostKeyChecking=no" 172.16.1.$ip &>/dev/null

if [ $? -eq 0 ];then

echo -e "\033[32mhost 172.16.1.$ip Distribution of the secret key Success!!! \033[0m"

echo

else

echo -e "\033[31mhost 172.16.1.$ip Distribution of the secret key Failed !!! \033[0m"

echo

fi

done

1. ## SSH服务配置文件编写

vim /etc/ssh/sshd_config（不要经常改动）

SSH作为远程连接服务，通常我们需要考虑到该服务的安全，所以需要对该服务进行安全方面的配置。

1.更改远程连接登陆的端口

2.禁止ROOT管理员直接登录

3.密码认证方式改为密钥认证

4.重要服务不使用公网IP地址

5.使用防火墙限制来源IP地址

PermitRootLogin no            #→root超级用户黑客都知道，建议禁止它远程登陆能力。

PasswordAuthentication #→禁止使用密码直接远程登录

UseDNS no                 #→禁止ssh进行dns反向解析，影响ssh连接效率参数

GSSAPIAuthentication no     #→禁止GSS认证，减少连接时产生的延迟

\#→http://oldboy.blog.51cto.com/2561410/1300964

思考：如果服务端端口号修改了，可以正常分发公钥？

1. ## sftp命令功能使用方法

sftp -oPort=52113 oldboy@10.0.0.7 --- 进行sftp远程连接

sftp> --- 进入到ftp文件传输模式（ftp相关命令）

bye --- 离开ftp模式

cd path --- 切换远程主机的目录

get --- 从远程服务器上将数据下载到本地

lcd /tmp --- 进行本地切换目录信息

lpwd     --- 查看本地所在路径信息

put --- 进行数据上传操作