# 第16章 Linux系统服务

### 1. Linux启动流程

![img](D:\Notes\md_img\16-1.png)

```

#简单说明CentOS-7系统启动过程：

01） 按下电源

02） 开机BIOS自检    

03） MBR引导系统   

04） GRUB菜单

05） 加载内核          

06） 启动系统进程(使用systemd进行管理)       

07） 读取运行级别

08） 进行系统初始化

09） 启动开机自动启动服务（并行启动）

10） 运行getty文件，进入登录页面

```

**CentOS-6与7系列的启动区别**

| CentOS-6启动进程init | CentOS-7启动进程systemd    |
| :------------------- | :------------------------- |
| **采用串行启动**     | **采用并行启动**           |
| **启动效率比较慢**   | **启动效率比较高**         |
| **容易操作启动异常** | **启动异常问题不容易出现** |

### 2. Linux运行级别

**01.什么是运行级别？运行级别就是操作系统当前正在运行的功能级别。**

| System 运行级别 | Systemd目标名称                        | 作用                   |
| :-------------- | :------------------------------------- | :--------------------- |
| **0**           | **runlevel0.target，poweroff.target**  | **关机**               |
| **1**           | **runlevel1.target，rescue.target**    | **单用户模式**         |
| **2**           | **runlevel2.target，multi.target**     | **多用户模式**         |
| **3**           | **runlevel3.target，multi.target**     | **多用户模式**         |
| **4**           | **runlevel4.target，multi.target**     | **多用户模式**         |
| **5**           | **runlevel5.target，graphical.target** | **图形界面或桌面模式** |
| **6**           | **runlevel6.target，reboot.target**    | **重启**               |

```

#系统运行级别路径

[root@qls ~]#  ll /usr/lib/systemd/system/runlevel*.target 
lrwxrwxrwx. 1 root root 15 2019-05-08 18:28 /usr/lib/systemd/system/runlevel0.target -> poweroff.target
lrwxrwxrwx. 1 root root 13 2019-05-08 18:28 /usr/lib/systemd/system/runlevel1.target -> rescue.target
lrwxrwxrwx. 1 root root 17 2019-05-08 18:28 /usr/lib/systemd/system/runlevel2.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 2019-05-08 18:28 /usr/lib/systemd/system/runlevel3.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 2019-05-08 18:28 /usr/lib/systemd/system/runlevel4.target -> multi-user.target
lrwxrwxrwx. 1 root root 16 2019-05-08 18:28 /usr/lib/systemd/system/runlevel5.target -> graphical.target
lrwxrwxrwx. 1 root root 13 2019-05-08 18:28 /usr/lib/systemd/system/runlevel6.target -> reboot.target

#系统运行级别配置文件

[root@qls ~]# ll /etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 37 2019-05-08 18:32 /etc/systemd/system/default.target -> /lib/systemd/system/multi-user.target

```

**02. 如何调整系统运行级别？**

```

#查看系统中的运行级别

[root@qls ~]# systemctl get-default 
multi-user.target

#设置系统运行级别

[root@qls ~]# systemctl set-default  graphical.target
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/graphical.target.
[root@qls ~]# systemctl get-default 
graphical.target

[root@qls ~]# systemctl set-default  runlevel4.target
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/multi-user.target.
[root@qls ~]# systemctl get-default 
multi-user.target

```

### 3. Linux systemd

**01. systemd的由来和新特性**

```

#历史由来

    从CentOS 7系列开始，redhat正式将systemd用于系统管理，来取代CentOS 5的sysV init和CentOS 6的upstart体系；较之之前，systemd为linux系统提供了启动与管理的一整套解决方案；
    
#systemd的新特性

01）系统启动时服务并行启动；

02）按需激活进程，节约资源；

03）系统快照功能，下次启动时可恢复至某一次快照时状态；

04）基于依赖关系定义服务控制逻辑；

```

**02. 什么是systemd ？**

```

    systemd即为system daemon守护进程，systemd主要解决上文的问题而诞生，systemd的目标是，为系统的启动和管理提供一套完整的解决方案。
    
01）最新系统都采用systemd管理（RedHat7,CentOS7,Ubuntu15等）

02）Centos7支持开机并行启动服务，显著提高开机启动效率。

03）Centos7关机只关闭正在运行的服务，而Centos6全部都关闭一次。

04）Centos7服务的启动与停止不在使用脚本进行管理，也就是/etc/init.d下不在有脚本。

05）Centos7使用systemd解决原有模式缺陷，比如原有service不会关闭程序产生的子进程。

```

**03. systemd与sysV init 以及upstart不兼容之处**

```

01）/etc/rc.d/init.d目录中的服务管理脚本不可以用systemctl命令来管理，systemctl命令的参数已经固定

02）/etc/rc.d/init.d目录中的服务管理脚本启动的服务，与systemd管理启动的进程之间无法通信

03）systemd虽然模拟出runlevel，但是与init、upstart机制的运行级别不完全一致

```

**04. systemd启动、运行时的相关配置文件**

```

/usr/lib/systemd/system/目录：每个服务最主要的启动脚本设置，有点类似于init的/etc/init.d下面的文件

/run/systemd/system/目录：系统执行过程中所产生的服务脚本，/usr/lib/systemd/system/高

/etc/systemd/system/目录：管理员根据主机系统的需要所建立的执行脚本。其实这个目录有点像以前/etc/rc.d/rcN.d/之类的功能，执行优先级又比/run/systemd/system/高

```

**05. systemd管理服务相关命令**

```

#systemctl管理服务的启动、停止、重启、重载、状态等常用命令

[root@qls ~]# systemctl start vsftpd        #启动

[root@qls ~]# systemctl stop vsftpd         #停止

[root@qls ~]# systemctl status vsftpd       #查看运行状态

[root@qls ~]# systemctl restart vsftpd      #重启

[root@qls ~]# systemctl reload  vsftpd      #重载

[root@qls ~]# systemctl mask  vsftpd        #禁止服务运行

[root@qls ~]# systemctl unmask  vsftpd      #取消禁止服务运行

#当我们启动一个服务之后，可以用status查看服务的状态信息

状态                          描述

loaded                  #服务单元的配置文件已经被处理

active(running)         #服务持续运行

active(exited)          #服务成功完成一次的配置

active(waiting)         #服务已经运行但在等待某个事件

inactive                #服务没有在运行

enable                  #服务开机自启动

disable                 #服务开机不自启动

static                  #服务开机不启动，但可以被其他服务调用启动

#systemctl设置服务的开机启动、不启动、及查看各运行级别下的启动状态

#开机自启动

[root@qls ~]# systemctl enable vsftpd

#关闭开机自启动

[root@qls ~]# systemctl disable vsftpd

实现一个服务的开机自启动和关闭开机自启动,无非就是创建软连接的一个过程而已

#查看各运行级别下的启动状态

[root@qls ~]# systemctl list-unit-files

```

**06. systemctl管理服务器关机重启**

```

#关机

[root@qls ~]# systemctl  poweroff 

#重启

[root@qls ~]# systemctl  reboot 

```

**07. systemctl的journalctl的日志**

```

[root@qls ~]# journalctl -n 20      #查看最后20行内容

[root@qls ~]# journalctl -f         #动态查看日志

[root@qls ~]# journalctl -p err     #查看日志级别
```

```

[root@qls ~]# journalctl -u sshd    #查看某个服务的日志

```

### 4. Linux单用户模式

**如何使用单用户模式更改系统密码？（加入你忘记了root用户的密码），这里以CentOS-7为例。**

**第一步：重新启动或开启CentOS-7系统，在选择进入系统的Grub菜单界面如下图所示，根据提示按“e”小写字母进入编辑界面，如下图所示：**

![img](D:\Notes\md_img\16-2.png)

**第二步：然后，要按方向键向下键（否则可能看不到想要找的行），找到以字符串“Linux16”开头的行，将光标移动到该行的结尾，然后输入“init=/bin/bash enforcing=0”（前者作用让系统登录后加载bash解释器，后者是且关闭Selinux），如下图所示：**

![img](D:\Notes\md_img\16-3.png)

**第三步：配置完成后，根据界面下面的英文提示按“Ctrl-x”组合键以单用户模式启动Linux如下图所示：**

![img](D:\Notes\md_img\16-4.png)

```

特殊说明：

1）图中的“mount-o rw,remount/”命令是重新挂载根目录为可写状态（rw表示可写，remount是重新挂载），在单用户模式下默认根文件系统是处于只读状态。

2）也可以在增加内核参数时，把以字符串“Linux16”开头的行中间部分的ro参数改为rw，则可以替代繁琐的“mount-o rw,remount /”命令。

```

**第四步：配置完后，执行exec	/sbin/init命令重启系统，注意，此时，无法使用其它重启命令。特殊强调：如果是系统的SELinux处于开启状态，并且前文修改内核选项时，没有增加“enforcing=0”，则此处需要多执行一条“touch	/.autorelabel”命令以便在下次系统引导前重新标记系统中的所有相关文件，因为在SELinux开启时，修改root密码时修改password文件会导致SELinux安全上下文报错，如果前文修改内核选项时，已增加“enforcing=0”，那么在修改密码文件时，Selinux是关闭状态，因此，这里就不需要执行“touch	/.autorelabel”命令了。**

![img](D:\Notes\md_img\16-5.png)

**第五步：重启系统后验证修改后的密码是否能登录，出现如下图所示，表示密码修改并登录成功。**

![img](D:\Notes\md_img\16-6.png)

**如果CentOS-7系统运行级别被设置成重启的级别**

**第一步：重新启动或开启CentOS-7系统，在选择进入系统的Grub菜单界面如下图所示，根据提示按“e”小写字母进入编辑界面，如下图所示：**

![img](https://www.increase93.com/Picture/16-2.png)

**第二步：然后，要按方向键向下键（否则可能看不到想要找的行），找到以字符串“Linux16”开头的行，将光标移动到该行的结尾，如果是物理机，添加：rd.break，如果是虚拟机，添加：rd.break console=tty0,如下图所示,之后按Ctrl+x键进入switch_root。**

![img](D:\Notes\md_img\16-7.png)

**第三步：输入命令mount，发现根为/sysroot/，并且不能写，只有ro权限，重新挂载已经挂载了的根文件系统（以读写权限挂载 ），挂载好后将根改成sysroot（执行命令：chroot /sysroot），然后修改运行级别，exit退出重启，如下图所示：**

![img](D:\Notes\md_img\16-8.png)

**CentOS-6忘记root密码或者调整了运行级别为6**

```

进入单用户级别：

1.重启服务器，一直按“E”键,进入grub引导菜单的内核界面

2.在内核界面选择使用上下箭头移动并选择内核

3.内核选定之后再次按下“E”键进行编辑

4.在最后一行后面加上数字1（是单用户模式的运行级别）或者single然后回车

5.然后根据提示按下b进行重新引导

6.然后设置密码和重启

```

### 5. Linux下救援模式

**01. 场景一：当系统坏了，无法登陆系统，但需要把里面的数据复制出来。**

**第一步：先挂载光盘，然后选择光盘引导为第一位，如下图所示。**

![img](D:\Notes\md_img\16-9.png)

![img](D:\Notes\md_img\16-10.png)

**第二步：进入故障排除模式，然后选择救援模式，如下图所示。**

![img](D:\Notes\md_img\16-11.png)

![img](D:\Notes\md_img\16-12.png)

**第三步：挂载真实系统后，发现数据都还存在，之后就可以挂在磁盘，将数据备份出来。**

![img](D:\Notes\md_img\16-13.png)

```

sh-4.2# mkfs.xfs -f /dev/sdc
sh-4.2# mkdir /data
sh-4.2# mount /dev/sdc /data
sh-4.2# cp -a /opt /data
sh-4.2# umount /dev/sdb

```

**02. 场景二：修复MBR，主要出现在安装双系统的时候，后安装的系统把原来的MBR删除了，需要修复**

**第一步：破坏硬盘的前446字节，模拟MBR引导损坏，会发现重启无法启动系统**

```

[root@qls ~]# dd < /dev/zero >/dev/sda bs=1 count=446

```

**第二步：重启，查看系统状态**

![img](D:\Notes\md_img\16-14.png)

**第三步：进入救援模式，分配一个shell，进行修复系统**

```

#修复grub
bash-4.2# grub2-install /dev/sda

#退出
bash-4.2# exit

#重启进入系统
bash-4.2# reboot

```

**03. 场景三：CentOS-7 误删除grub文件，进行修复**

**第一步：模拟故障**

```

#删除grub2
[root@qls ~]# rm -fr /boot/grub2/

#重启
[root@qls ~]# reboot

```

**第二步：查看系统状态**

![img](D:\Notes\md_img\16-15.png)

**第三步：进入救援模式，分配一个shell，进行修复系统**

```
bash-4.2# grub2-install /dev/sda
bash-4.2# grub2-mkconfig -o /boot/grub2/grub.cfg
bash-4.2# ls /boot/grub2
bash-4.2# exit
sh-4.2# reboot
```

