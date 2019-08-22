## 第8章 Linux目录文件及系统启动知识

2019-07-15 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(120) 评论(0)

1. **Linux系统目录结构介绍**

   1. ### Linux与Windows目录结构对比

- **Linux与Windows的目录结构对比见下图。**
- **windows特点:E:\学习视频\高清视频\**
- **Linux目录特点: /etc/hosts /root/data/oldboy.txt**

![img](D:\Notes\md_img\071519_0710_8Linux1.png)

1. ### Linux系统目录结构基本特点

- **Linux下一切从根开始**
- **Linux下面的目录是一个有层次的目录结构**
- **在linux中每个目录可以挂载到不同的设备(磁盘)上**
- **Linux下设备不挂载不能使用，不挂载的设备相当于没门没窗户的监狱(进不去出不来)，挂载相当于给设备创造了一个入口(挂载点，一般为目录)**

1. ## Linux的目录结构详解

   1. ### 目录层次标准FHS

**文件系统层次结构标准 (FHS) 定义了在类Unix系统中的目录结构和目录内容。它由Linux基金会维护，最新版为2015年六月三日发布的3.0版，仅用于Linux的各类发行版中。**

- **官网: http://www.pathname.com/fhs/**

1. ### 根下目录结构详解重要的Linux系统文件介绍

| **目录**          | **说明**                                                     |
| ----------------- | ------------------------------------------------------------ |
| **/lost**         | **found系统修复**                                            |
| **/bin**          | **二进制命令所在的目录。**                                   |
| **/boot**         | **系统引导程序所需的文件目录。安装系统分区的时候一般单独要分一个boot分区，大小可谓128-256M，该分区数据增长神效** |
| **/dev**          | **（Device flies）设备软件目录，比如磁盘、光驱…..**          |
| **/etc**          | **系统配置，启动程序**                                       |
| **/home**         | **普通用户的家，目录默认数据存放目录**                       |
| **/lib**          | **启动系统和运行命令所需的共享库文件和内核模块存放**         |
| **/mnt**          | **临时挂载存储设备的挂载点, u盘直接插入光驱无法使用,要先挂载后使用** |
| **/opt**          | **额外的应用软件包**                                         |
| **/proc**         | **操作系统运行时,进程信息及内核信息(比如cpu、硬盘分区、内存信息等)存放在这里。/proc目录是虚拟文件系统的挂载点,proc并不是真正的文件系统,它的定义可以参见/etc/ fstab，系统运行很多信息都在这个目录下。** |
| **/proc/loadavg** | **<==系统负载(系统繁忙程度)**                                |
| **/proc/memrino** | **<==系统内存信息**                                          |
| **/proc/cpuinfo** | **<==系统cpu信息**                                           |
| **/proc/mounts**  | **<==系统挂载信息。**                                        |
| **/root**         | **Linux超级用户root的家目录:root类似win32管理员administrator** |
| **/sbin**         | **和管理系统相关的命令（超级管理员用）**                     |
| **/tmp**          | **(Temporary files)临时文件目录, 这个目录可以被用作回收站使用** |
| **/usr**          | **用户或系统软件应用程序目录**                               |
| **/var**          | **这个目录的内容是经常变动的。是个用来存放系统日志的目录，系统日志的路径/var/log/messages; /var/lib用来存放一些库文件** |

1. **/etc系统初始化及设置相关重要文件**

- **/etc/sysconfig/network-scripts/ifcfg-eth0** 
  **#网卡配置文件**
- **/etc/resolv.conf #Linux系统DNS配置文件**
- **/etc/sysconfig/network #CentOS-6主机名配置文件**
- **/etc/hostname #CentOS-7主机名配置文件**
- **/etc/syscconfig/i18n #CentOS-6字符集配置文件**
- **/etc/locale.conf #CentOS-7字符集配置文件**
- **/etc/hosts #ip地址与域名快速解析的文件**
- **/etc/fstab #配置开机设备自动挂载的文件**
- **/etc/rc.local #存放开机自启动程序命令的文件**
- **/etc/inittab # centos7设定运行级别等配置的文件**
- **/etc/systemd/system/default.target #centos7设定运行级别等配置的文件**
- **/usr/lib/systemd/system/runlevel\*.target #centos7运行级别**
- **/etc/profile及/etc/bashrc #配置系统的环境变量/别名等的文件**
- **/etc/profile.d #用户登录后执行的脚本所在的目录**
- **/etc/issue和/etc/issue.net #配置在用户登录终端前显示信息的文件**
- **/etc/init.d #软件启动程序所在的目录**
- **/etc/motd #配置用户登录系统之后显示提示内容的文件**
- **/etc/redhat-release #声明RedHat版本号和名称信息的文件**
- **/etc/sysctl.conf #Linux内核参数设置文件**

1. ### 网卡配置文件详解

- **修改网卡配置文件之后，需要重启网卡才能生效**

**systemctl restart network**

**[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0**

**UUID=30126a53-c912-4552-a7fe-af1925bd4d2f #网卡uuid克隆虚拟机需要删除。**

**HWADDR=00:0c:29:fe:f9:8a #mac地址，网卡唯一标识，克隆虚拟机要删除。**

**NM_CONTROLLED=no #要不要被网络管理器控制，选no**

**USERCTL=no #USER ConTrol普通用户是否可以管理网卡，no**

**PEERDNS=yes #要不要覆盖/etc/resolv.conf文件（DNS客户端文件）**

**IPV6INIT=no #是否支持ipV6**

**DEVICE=eth0 #网卡设备的名字，eth0是第一块。**

**ONBOOT=yes #开机时，是否和系统一起启动**

**TYPE=Ethernet #以太网**

**BOOTPROTO=none #启动协议.dhcp（动态主机配置协议）.static/none（静态ip）**

**IPADDR=10.0.0.200 #IP地址**

**NETMASK=255.255.255.0 #子网掩码(也可以是24)**

**GATEWAY=10.0.0.2 #网关**

**DNS1=223.5.5.5 #第一个DNS**

**DNS2=223.6.6.6 #第二个DNS**

1. #### 配置DNS的配置文件

- **下面两个配置文件都可以配置DNS**
- /etc/sysconfig/network-scripts/ifcfg-eth0

**配置方法：DNS1=xxx.xxx.xxx.xxx DNS2=xxx.xxx.xxx.xxx**

**此配置文件配置好DNS后是需要重启网卡配置文件才能生效。**

- **/etc/resolv.conf**

**配置方法：nameserver xxx.xxx.xxx.xxx nameserver xxx.xxx.xxx.xxx**

**此配置配置完成后是实时生效的**

- **这两个配置文件哪个优先级高呢。**

**网卡配置文件中的DNS优先于/etc/resolv.conf**

1. ### 修改主机名

   1. #### CentOS-6系统修改主机名

- **命令修改 #修改之后需要重新登录终端生效（临时生效，重启系统之后失效）**

**hostname 新的主机名**

- **配置文件修改 #修改之后需要重启系统生效（永久生效）**

**修改配置文件/etc/sysconfig/network**

1. #### CentOS-7系统修改主机名

- **命令修改 #重新登录终端生效（永久生效）**

**hostnamctl set-hostname 新的主机名**

- **配置文件修改 #修改之后需要重启系统生效（永久生效）**

**修改配置文件/etc/hostname**

1. ### 修改字符集（中文乱码）

- **如何查看系统的字符集**

**echo $LANG**

- **命令修改 #临时生效，重启系统之后失效**

**export LANG=zh_CN.UTF-8**

**localectl set-locale LANG=en_US.utf8 #需重新加载配置文件生效**

- **配置文件修改**

**CentOS-6修改配置文件/etc/syscconfig/i18n**

**CentOS-7修改配置文件/etc/locale.conf**

- **生效（永久生效）**

**source 配置文件**

1. ### 运行级别

   1. #### CentOS-6运行级别

- **如何查看**

**runlevel**

- **如何修改**

**init 级别 / 或者修改运行级别配置文件/etc/inittab**

- **运行级别及对应含义**

**0 关机状态（不要把运行级别永久设置为0）**

**1 单用户模式 root密码忘记故障**

**2 多用户模式（不能使用NFS软件）**

**3 完全的多用户模式文本模式命令行模式**

**4 没有使用**

**5 桌面模式 X11**

**6 重启（不要把运行级别永久设置为0）**

1. #### CentOS-7运行级别

- **如何查看**

**systemctl get-default**

- **如何修改**

**systemctl set-default 运行级别**

- **运行级别**

[root@oldboy ~]# ll /usr/lib/systemd/system/runlevel*.target

lrwxrwxrwx. 1 root root 15 Jul 23 17:05 /usr/lib/systemd/system/runlevel0.target -> poweroff.target

lrwxrwxrwx. 1 root root 13 Jul 23 17:05 /usr/lib/systemd/system/runlevel1.target -> rescue.target

lrwxrwxrwx. 1 root root 17 Jul 23 17:05 /usr/lib/systemd/system/runlevel2.target -> multi-user.target

lrwxrwxrwx. 1 root root 17 Jul 23 17:05 /usr/lib/systemd/system/runlevel3.target -> multi-user.target

lrwxrwxrwx. 1 root root 17 Jul 23 17:05 /usr/lib/systemd/system/runlevel4.target -> multi-user.target

lrwxrwxrwx. 1 root root 16 Jul 23 17:05 /usr/lib/systemd/system/runlevel5.target -> graphical.target

lrwxrwxrwx. 1 root root 13 Jul 23 17:05 /usr/lib/systemd/system/runlevel6.target -> reboot.target

1. ### 别名与环境变量

   1. #### 别名

- **查看系统中已经定义的别名**

**alias #alias 命令**

- **命令设置（临时生效，重启系统之后失效）**

**alias rm='**
**echo rm command bny'**

- **配置文件修改**

**vim /etc/profile|**
**/etc/bashrc (别名所有用户可用)#国法**

**vim ~/.bashrc|**
**~/.bash_profile(别名当前用户可用)#家规**

- **生效（永久生效）**

**source 配置文件名**

- **取消别名**

**unalias 有别名的命令**

1. #### 环境变量

- **如何查看系统已经存在的环境变量**

**env**

- **命令设置（临时生效，重启系统之后失效）**

**export TEST=123 #环境变量统一大写(普通变量随意)，名称不要与系统中已经定义好的变量冲突**

- **查看**

**echo $TEST**

- **配置文件定义**

**vim /etc/profile(所有用户可用)#国法**

**vim ~/.bash_profile(当前用户可用)#家规**

- **生效（永久生效）**

**source 配置文件**

- **取消环境变量**

**unset TEST**

- **对系统的一个小优化**

cat >>/etc/profile.d/color.sh<<"EOF"

alias ll='ls -l --color=auto --time-style=long-iso'

PS1='\[\e[32;1m\][\u@\h \W]\$ \[\e[0m\]'

EOF

source /etc/profile

1. ### /usr目录的重要知识介绍

- **/usr/local/：编译安装软件默认的位置路径**
- **/usr/src：存放源码文件的目录**

1. #### 常见的安装软件方法

- yum

**yum install -y tree xxxx          #安装软件**

**yum grouplist     #查看已安装的软件包**

**yum groupinstall         #安装指定的软件包**

**yum provides          #查询命令属于哪个软件包**

**yum remove sl -y #删除一个软件包**

- rpm

**-ivh rpm –ivh          #软件包**

**-e rpm –e          #卸载软件包**

**-qa rpm –qa          #查询所有软件包**

**-ql rpm –ql           #查看软件包里的文件列表**

**-qf rpm –qf          #查询命令或文件属于哪个软件包**

**-qc rpm -qc      #查询软件的主配置文件**

- 编译安装三部曲

**./conflgure #编译参数选项**

**make #开始编译**

**make install #开始安装**

1. ### /var目录下的路径知识

- **/var/log #记录系统及软件运行信息文件所在的目录**
- **/var/log/messages #系统级别日志文件**
- **/var/log/secure #用户登录信息日志文件**
- **/var/log/dmesg #记录硬件信息加载情况的日志文件**

1. ### /proc目录下的重要路径知识

- **/proc/meminfo #系统内存信息**
- **/proc/cpuinfo #关于处理器的信息，如类型，厂家，型号，性能等**
- **/proc/loadavg #系统负载信息，uptime的结果**
- **/proc/mounts #已加载的文件系统的列表**

1. ### /dev目录下的路径知识

- **/dev/sd\* #磁盘设备**
- **/dev/sr0或者/dev/cdrom #光驱设备文件**
- **/dev/zero #写0设备，作用：1.清空磁盘数据 2.产生空设备文件**
- **/dev/null #黑洞设备，作用：销毁操作系统输出信息使用**

1. ## Linux 系统启动流程说明（重点）

![img](D:\Notes\md_img\071519_0710_8Linux2.png)

- **高清原图地址:**

[**https://dwz.cn/YjANKTT0**](https://dwz.cn/YjANKTT0) **#区分大小写**

1. ### CentOS-6系启动流程

- **BIOS开机自检**

**检查硬件是否健康。如cpu风扇是否正常，内存是否正常，时钟是否正常，这个过程是读取ROM上的指令执行的。**

**系统想要启动必须先加载BIOS，按下电源键时，给微控制器下达一条复位指令，各寄存器复位，最后下达一条跳转指令，跳转到BIOS的ROM，使得硬件去读取主板上的BIOS程序，在这之前都是由硬件来完成，之后硬件就会把控制权交给BIOS。**

**随后BIOS程序加载CMOS（可读写的RAM芯片，保存BIOS设置硬件参数的数据）的信息，借CMOS取得主机的各项硬件配置。取得硬件配置的信息之后，BIOS进行加电自检（Power-on self Test，POST）过程,检测计算机各种硬件信息，如果发现硬件错误则会报错（发出声音警告）。之后BIOS对硬件进行初始化。BIOS将自己复制到物理内存中继续执行，开始按顺序搜寻可引导存储设备，决定存储设备的顺序（即定义第一个可引导的磁盘，当然是在有两个磁盘的前提），接下来就会读取磁盘的内容，但是要读取磁盘文件必须要有文件系统，这对BIOS挂载文件系统来说是不可能，因此需要一个不依赖文件系统的方法使得BIOS读取磁盘内容，这种方法就是引入MBR。最后BIOS通过INT13硬件中断功能读取第一个可引导的存储设备的MBR（0磁道0扇区）中的boot loader。将MBR加载到物理内存中执行。MBR载入内存后，BIOS将控制权转交给MBR（准确的说应该是MBR中的boot loader），然后MBR接管任务开始执行。**

- **MBR引导**

**载入了第一个可引导的存储设备的MBR后，MBR中的boot loader就要读取所在磁盘的操作系统核心文件（即后面所说的内核）了。**

**但是不同操作系统的文件系统格式不同，还有一个磁盘可以安装多个操作系统，如何让boot loader做到引导的就是用户想要的操作系统，这么多不同的功能单靠一个446字节的boot loader是远远不够的。必须有一个相对应的程序来处理各自对应的操作系统核心文件，这个程序就是操作系统的loader（注意不是MBR中的boot loader），这样一来boot loader只需要将控制权交给对应操作系统的loader，让它负责去启动操作系统就行了。**

**一个硬盘的每个分区的第一个扇区叫做boot sector，这个扇区存放的就是操作系统的loader，所以常说一个分区只能安装一个操作系统。MBR的boot loader有三个功能：提供选单，读取内核文件，转交给其它loader。**

**提供选单就是给用户提供一张选项单，让用户选择进入哪个操作系统；读取内核文件的意思是，系统会有一个默认启动的操作系统，这个操作系统的loader在所在分区的boot sector有一份，除此之外，也会将这个默认启动的操作系统的loader复制一份到MBR的boot loader中，这样一来MBR就会直接读取boot loader中的loader了，然后就是启动默认的操作系统；转交给其它的loader，当用户选择其它操作系统启动的时候，boot loader会将控制权转交给对应的loader，让它负责操作系统的启动。**

- **GRUB菜单**

**grub是boot loader中的一种，就grub来说，为了打破在MBR中只有446Bytes用于存放boot loader这一限制，所以这一步的实现是这样的：grub是通过分成三个阶段来实现加载内核这一功能的，这三个阶段分别是：stage1, stage1.5以及stage2。**

**stage1：存放于MBR的前446Bytes，用于加载stage1.5阶段，目的是为了识别并驱动stage2(或者/boot)所在分区的文件系统。**

**stage1.5：存放于MBR之后的扇区，加载stage2所在分区的文件系统驱动，让stage1中的boot loader能识别stage2所在分区的文件系统。**

**stage2：存放于磁盘分区之上，具体存放于/boot/grub目录之下，主要用于加载内核文件(vmlinuz-VERSION-RELEASE)以及ramdisk这个临时根文件系统(initrd-VERSION-RELEASE.img或initramfs-VERSION-RELEASE.img)。**

**概述：假如要启动的是硬盘设备，首先硬件平台主板BIOS必须能够识别硬盘，然后BIOS才能加载硬盘中的boot loader，而boot loader自身加载后就能够直接识别当前主机上的硬盘设备了；不过，能够识别硬盘设备不代表能够识别硬盘设备中的文件系统，因为文件系统是额外附加的一层软件组织的文件结构，所以要对接一种文件系统，就必须要有对应的能够识别和理解这种文件系统的驱动，这种驱动就称为文件系统驱动。而stage1.5就是向grub提供文件系统驱动的，这样stage1就能访问stage2及内核所在的分区(/boot)了。**

- **加载内核**

**内核（Kerenl）在得到系统控制权之后，首先要进行自身初始化，而初始化的主要作用是：**

- **探测可识别到的所有硬件设备；**
- **加载硬件驱动程序，即加载真正的根文件系统所在设备的驱动程序（有可能会借助于ramdisk加载驱动）；**
- **以只读方式挂载根文件系统（如果有借助于ramdisk这个临时文件系统（虚根），则在这一步之后会执行根切换；否则不执行根切换）；**
- **运行用户空间的第一个应用程序：/sbin/init。**

**到这里内核空间的启动流程就结束了，而接下来是用户空间完成后续的系统启动流程。**

**注意：ramdisk和内核是由boot loader一同加载到内存当中的，ramdisk是用于实现系统初始化的、基于内存的磁盘设备，即加载至内存（的某一段空间）后把内存当磁盘使用，并在内存中作为临时根文件系统提供给内核使用，帮助内核挂载真正的根文件系统。而之所以能够帮助内核挂载根文件系统是因为在ramdisk这个临时文件系统的/lib/modules目录下有真正的根文件系统所在设备的驱动程序；除此之外，这个临时文件系统也遵循FHS，例如有这些固定目录结构：/bin, /sbin, /lib, /lib64, /etc, /mnt, /media, ...**

**因为Linux内核有一个特性就是通过使用缓冲/缓存来达到加速对磁盘上文件的访问的目的，而ramdisk是加载到内存并模拟成磁盘来使用的，所以Linux就会为内存中的"磁盘"再使用一层缓冲/缓存，但是ramdisk本来就是内存，它只不过被当成硬盘来使用罢了，这就造成双缓冲/缓存了，而且不会起到提速效果，甚至影响了访问性能；CentOS 5系列以及之前版本的ramdisk文件为initrd-VERSION-RELEASE.img，就会出现上述所说到的问题；而为了解决一问题，CentOS 6/7系列版本就将其改为initramfs-VERSION-RELEASE.img，使用文件系统的方式就可以避免双缓冲/缓存了，可以说这是一种提速机制。**

- **启动init进程**

**grub中默认指定init=/sbin/init程序，可以在grub.conf中kernel行自定义执行程序init=/bin/bash,此时可以绕过下面步骤直接进入bash界面。**

**内核源代码文件中显示996行左右，规定了init启动的顺序，/sbin/init->/etc/init->/bin/init->/bin/sh。**

- **读取/etc/inittab文件**

**inittab文件里面定义了系统默认运行级别，这一步做了一些工作如下：**

**初始运行级别(RUN LEVEL)；**

**系统初始化脚本；**

**对应运行级别的脚本目录；**

**定义UPS电源终端/恢复脚本；**

**在虚拟控制台生成getty,以生成终端；**

**在运行级别5初始化X。**

- **执行/etc/rc.d/rc.sysinit程序**

**系统初始化一些脚本，主要完成以下工作。**

**设置主机名；**

**设置欢迎信息；**

**激活udev和selinux可以在grub.conf中,kernel行添加selinux=0以关闭selinux；**

**挂载/etc/fstab文件中定义的文件系统；**

**检测根文件系统，并以读写方式重新挂载根文件系统；**

**设置系统时钟；**

**激活swap设备；**

**根据/etc/sysctl.conf文件设置内核参数；**

**激活lvm及software raid设备；**

**加载额外设备的驱动程序；**

**清理操作。**

**/etc/rc\*.d/文件（各种服务）**

**里面定义的是各种服务的启动脚本，可以ls查看，S开头代表开机启动的服务，K开头的是关机要执行的任务。#代表数字，一个数字代表一个运行级别，共7个运行级别。**

**/etc/rc.d/rc.local文件**

**这里面可以自定义开机启动的命令。**

- **执行/bin/login**

**执行/bin/login程序，等待用户登录。**

- **简单说明CentOS-6系统启动过程：**

**1）按下开关按钮         #给服务器供电**

**2） BIOS自检操作         #检查硬件是否存在异常（显示logo画面）**

**3） MBR引导系统         #硬盘启动系统光驱启动系统 U盘启动系统网络方式启动系统**

**4） GRUB菜单选择         #选择系统的内核信息可以配置进入系统的方式（单用户模式）**

**5）内核加载 #加载Kerenl内核**

**6）启动系统进程         #系统的第一个进程开始启动init**

**7）读取/etc/inittab     #读取系统运行级别文件**

**8）执行/etc/rc.d/rc.sysinit脚本    #进行系统初始化操作（加载网络信息加载主机名称信息）**

**9）执行/etc/rc.d/rc脚本 #不同系统运行级别，会有不同服务程序需要开机自启动，**

**#管理开机启动的命令chkconfig**

**10）启动mingetty进程     #显示系统登录界面**

1. ### CentOS-7系启动流程

**CentOS7和CentOS6启动流程差不多，只不过到init程序时候，改为了systemd，因此详细解释一下systemd后的启动流程。**

- **uefi或BIOS初始化，开始post开机自检；**
- **加载MBR到内存；**
- **GRUB阶段；**
- **加载内核和inintamfs模块；**
- **内核开始初始化，使用systemd来代替centos6以前的init程序；**

1. **执行initrd.target，包括挂载/etc/fstab文件中的系统，此时挂载后，就可以切换到根目录了；**
2. **从initramfs根文件系统切换到磁盘根目录；**
3. **systemd执行默认target配置。**

**CentOS7系表面是有"运行级别"这个概念，实际上是为了兼容以前的系统，每个所谓的"运行级别"都有对应的软连接指向，默认的启动级别是/etc/systemd/system/default.target，根据它的指向可以找到系统要进入哪个模式。**

**CentOS7系的7个启动模式为：**

- **0 ==> runlevel0.target, poweroff.target**
- **1 ==> runlevel1.target, rescue.target**
- **2 ==> runlevel2.target, multi-user.target**
- **3 ==> runlevel3.target, multi-user.target**
- **4 ==> runlevel4.target, multi-user.target**
- **5 ==> runlevel5.target, graphical.target**
- **6 ==> runlevel6.target, reboot.target**

- **简单说明CentOS-7系统启动过程：**

**1）按下开关按钮         #给服务器供电**

**2） BIOS自检操作         #检查硬件是否存在异常（显示logo画面）**

**3） MBR引导系统         #选择系统的内核信息可以配置进入系统的方式（单用户模式）**

**5）内核加载 # 加载Kerenl内核**

**6）启动系统进程         #系统的第一个进程开始启动systemd**

**7）根据target模式选择启动级别 #/etc/systemd/system/default.target**

**8）进行系统初始化操作（加载网络信息加载主机名称信息）     # /usr/lib/systemd/system/sysinit.target**

**9）启动开机自动启动服务 # /etc/systemd/system /usr/lib/systemd/system/**

**#管理开机启动的命令systemctl**

**10）启动mingetty进程      #显示系统登录界面**

1. ### CentOS-6系与7系的启动区别

**系统启动和服务器守护进程管理器，它不同于CentOS5系的Sysv init，CentOS6系的Upstart（Ubuntu制作出来），systemd是由Redhat的一个员工首先提出来的，它在内核启动后，服务什么的全都被systemd接管，kernel只是用来管理硬件资源，相当于内核被架空了，因此linus很不满意Redhat这种做法。**

![img](D:\Notes\md_img\071519_0710_8Linux3.png)

- **高清原图地址:**

https://dwz.cn/YKPRx3SA #区分大小写

| **centos-6启动进程init** | **centos-7启动进程systemd** |
| ------------------------ | --------------------------- |
| **采用串行启动**         | **采用并行启动**            |
| **启动效率比较慢**       | **启动效率比较高**          |
| **容易操作启动异常**     | **启动异常问题不容易出现**  |

1. ## 本章知识点总结

- **目录结构特点**
- **目录层次标准FHS**
- **根目录下的重要目录和文件**
- **网卡配置文件每行含义**
- **配置DNS**
- **修改主机名**
- **修改字符集**
- **运行级别**
- **别名与环境变量**
- **常见的安装软件方法**
- **启动过程**