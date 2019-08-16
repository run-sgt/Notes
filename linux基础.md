## linux基础

### 运维的职责

```
1.企业的数据安全
2.网站7*24小时运行
3.企业服务好
```

### 服务器

```
服务器尺寸：以高度为单位:U (unit) 1U = 1.75 inch 4.45 cm
服务器分类：机架式 刀片式 塔式
服务器品牌：Dell # 使用最多
		  IBM  # 最贵的
		  HP 华为 航天联志 联想   
云服务器：阿里云 # 使用最多，要学会建工单
		腾讯云 # 游戏公司用的比较多
Dell常用型号 1U          2U 
		1850/1950   2850/2950
		R410/R420	R710
		R430/R610	R720/R730
		R620/R630	R740
		
		型号解析比如R720
        		R 机架式 M 刀片式 T 塔式
				7 CPU路数 1-3单路，4-7双路
				2 代路 从第10代开始
				0 CPU品牌 0代表Inter 5代表AMD
服务器核心部件
		电源 品牌 ATX
        		 SSI 服务器专用
        CPU 单位 路 衡量CPU的个数
        	Intel
        	AMD
        内存 
        	程序 进程 守护进程(持续运行中的程序)
        	写buffer 缓冲 写入
        	读cache 缓存 读取       
        磁盘 机械 固态
        接口 SCSI SATA SAS IDE PCIE M2
        单位 bit Bytes 
        光驱
        	U盘启动
        	ftp http网络安装
        	无人值守批量安装(cobbler,kickstart)
        RAID卡 
        	好处： 1.更高的容量
        		  2.数据更安全(冗余)
        		  3.更高的性能
        	级别： RAID0 RAID1 RAID5 RAID10
        	软RAID 和 硬RAID
        远程管理卡 
```

### linux历史

#### Linux核心概念

**自由软件**：

```
没有商业化软件版权制约，源代码开放，可无约束自用传播
​自由意味着freedom，而免费意味着free，这是2个不同的概念，例如Red Hat Linux自用但不免费，CentOS Linux自由而免费
​自由软件赋予软件使用者四种自由：
	1.不论目的为何，有运行该软件的自由
	2.有研究该软件如何运行，以及按需改写该软件的自由
	3.有重新发布拷贝的自由
	4.有改进软件，以及向公众发布改进的自由
```

**FSF** 	自由软件基金会

**GNU **	FSF的主要项目

**GPL**(`General Public License`)开源许可协议

```
保证任何人有共享和修改自由软件的自由，任何人有权取得、修改和重新发布自由软件的源代码权利，但都必须同时给出具体更改的源代码
```

#### Linux操作系统的组成

```
Linux操作系统 = Linux 内核 + GNU软件及其系统软件 + 必要的应用程序
```

#### Linux几个重要的发行版本

```
1.Red Hat Red Hat Linux 9.0的内核为2.4.20 在版本9.0后，不在遵循GPL协议，成为收费产品，其版本依次有Red Hat 3.x|Red Hat4.x|Red Hat5.x|Red Hat6.x|7.x
2.Fedora ，Red Hat的一个分支，仍遵循GPL协议，可以认为是Red Hat的预发布版
3.CentOS，以Red Hat所发布的源代码重建符合GPL许可协议的Linux系统，即将Red Hat Linux源代码商标及非自用软件部分去除后再编译而成的版本，目前已被Red Hat收购，但是仍然开源。
```

### VMware12中安装CentOS 7.6

#### 新建虚拟机

```
自定义(高级) => Workstation 12.0 => 稍后安装操作系统 => Linux|CentOS 64位 => 虚拟机名称CentOS-7.6-again => 默认 => 2048 => 使用NAT(通过宿主机联网) => 默认 => 默认 => 创建新虚拟磁盘 => 最大磁盘大小40gb|将虚拟磁盘拆分成多个文件 => 默认 => 完成
```

#### 安装CentOS 7.6

```
加载CentOS 7.6镜像，安装CentOS系统
点击编辑虚拟机设置 => 添加内网网卡，点击添加，选择网络适配器，下一步，默认，单击网络适配器2，选择右边LAN区段(L)再点击LAN区段(S)，添加，172.16.1.0/24，然后出来选择一些添加的LAN区段 => CD/VCD(IDE) => 使用ISO映像文件，加载体统映像，确定 => 开启此虚拟机
启动后进入系统安装界面 => 按↑选中Install CentOS 7，然后按Tab键，输入' net.ifnames=0',注意前面的空格，然后回车开始安装 => 等待一大堆绿色的ok出现、消失.....
```

```
进入CentOS图形化安装界面
1.语言选择，默认英文(可以选择简体中文，但是不建议) => Continue
2.Installation Summary界面
	#1.Date & Time 设置 => 选择上海 => Done
	#2.SoftWare Selection 设置  => 选择最小安装Minimal Install然后右边勾选除Smart Card Support以外的所有插件 => Done
	#3.Installation Destination 系统分区设置 => 最下面勾选'I will configure partitioning' => Done => 弹出对话框New CentOS 7 Installation，选择Standard Partition，点击'+' => /boot设置500,Add mount point => 继续点'+',swap设置2048,Add mount point => 继续点'+', / 不设置，默认剩余空间都给它，Add mount point => Done => Accept Changes
	#4.Kdump => 不勾选Enable kdump => Done
	#5.Network & Host Name 网卡设置界面
		这里我们需要设置2个网卡，一个连接外网一个连接内网
		##1.设置Host Name 主机名，主机名在实际工作中是很重要的标识，所以需要根据服务器实际作用进行对应命名，以作辨别。
		##2.设置2个网卡
			Ethernet(eth0) => Configure => General 勾选第一项'Automatically connect to this network when it is available'(系统已启动就自动开启网卡服务) =>IPv4 Settings Method选择Manual，Addresses中点Add，添加Address(IP)、Netmask(子网掩码)、Gateway(网关)、DNS server(DNS服务器)，我的配置是：10.0.0.100|24|10.0.0.2|223.5.5.5 => Save
            Ethernet(eth1) => Configure => General跟eth0一样，IPv4 Settings中，Address填内网IP 172.16.1.101，Netmask 24，网关和DNS不需要 => Save => Begin Installation开始安装系统，安装期间需要设置一下密码，点击左边钥匙图标，Root Password，设置密码，密码在实际工作中应该尽可能复杂，这个很重要，当然我们现在是测试，所以设置123就可以了，点Done第一次会提示密码太简单，再点一次就OK了。然后等待系统安装完成...
```

系统安装完成，点击Reboot重启，就进入系统了，使用系统管理员账户root，密码123进行登录

#### 安装CentOS过程中一些需要注意的点

```
0.虚拟机的网络类型
	NAT Network Address Translation，通过宿主机上网和交换数据
    	虚拟机网卡连接到宿主机的VMnet8上，此时系统的VMware NAT Server服务就充当了路由器，负责将虚拟机发送到VMnet8的包进行地址转换然后发送到实际的网络上，再将实际网络上返回的包进行地址转换后通过VMnet8发送给虚拟机
    Bridged 桥接模式，虚拟机被分配与物理主机相同网段的独立IP，所有网络功能和网络中的真实机器完全一样，此模式下的创建的虚拟机就好像一台真正的计算机，他会直接连接到实际的网络上，逻辑上联网与宿主机没有联系。
    Host-Only 仅主机，此模式下虚拟机网卡会连接到宿主机的VMnet1上，但是宿主机系统并不会为其提供任何路由服务，因此只能与宿主机进行通信，不能连接到实际的互联网中。
1.网卡命名规则
	安装系统最一开始需要对网卡设置命名规则，这样以后添加网卡的时候就会从ech0开始
	net.ifnames=0
	(后续查看网卡信息命令目录 cd /etc/sysconfig/network-scripts/)
2.分区规则
	在进行分区时候一般分三个区/boot|swap|/
	/boot 引导分区500-1024MB
	swap 虚拟内存分区物理内存的1-2倍，峰值16G
	/ 主分区，剩余多少给多少
```

#### VMware虚拟机 快照与克隆

```
开机状态
挂起状态
关机状态

都可以做快照，克隆只能克隆关机转态的快照

克隆	
链接克隆   占有磁盘空间小，基于宿主机
完整克隆	占用磁盘空间大，完全独立
```

#### 远程连接工具

```
Xshell
secureCRT
putty
iterm2
```

### linux系统命令基础

#### 个性化

```
vi /etc/bashrc
G
o
PS1="\[\e[37;40m\][\[\e[32;1m\]\u\[\e[37;40m\]@\h \[\e[36;40m\]\w\[\e[0m\]]\[\e[32;1m\]\\$ \[\e[0m\]"
esc键
:wq
source   /etc/bashrc
```

#### bash shell

```
把用户输入的命令翻译给内核，把执行的结果输出到屏幕上面
执行方式
	1.命令 执行
	2.shell scripts 脚本执行
```

#### bash特性

```
1.tab命令补全
	选项补全
	参数补全	
2.历史命令(下文有详细说明)
	history  查看历史命令的记录
```

#### 命令行结构

```
[root@sgt ~]# 
root	当前登录的用户名
@		分隔符
sgt		主机名
~		位置表示当前路径，本身表示当前用户的家目录
#		超级管理员的命令行提示符
$		普通用户的命令行提示符
[oldboy@sgt ~]$
```

#### 命令的语法结构

```
ls [OPTION]... [FILE]...

ls  命令
    ls				命令
    ls	-l			命令+选项  短横杠   --all
    ls  -l	/root	命令+选项+参数

[root@qls ~]# ls-l
-bash: ls-l: command not found  命令找不到

1.命令和选项和参数之间至少要有一个空格。
2.选项前面有短横杠和长横杠
```

#### 常用快捷键

```
ctrl+c 	终止当前的操作
ctrl+d 	退出当前登录的用户
ctrl+l 	清屏，置顶
ctrl+a 	将当前光标所在位置移动到命令行行首
ctrl+e 	将当前光标所在位置移动到命令行行尾
ctrl+u 	删除当前光标所在位置向前的所有字符
ctrl+k 	删除当前光标所在位置向后的所有字符
ctrl+y 	粘贴剪切板上的所有内容
ctrl+w 	删除当前光标所在位置向前的一组字符串
ctrl+方向键 向左或向右移动一组字符串
ctrl+r	搜索历史命令
ctrl+z	把当前的任务放到后台运行
(查看ping命令进程 ps -ef|grep ping)
ctrl+r	锁屏
ctrl+q	解锁
esc+.	使用上一条命令的最后参数
!! 		执行上一条命令
！ls		执行最近的一次以ls开头的命令
```

#### 历史记录命令

```
history  查看历史命令的记录
history -c	 清空所有的历史命令
history -w	 把历史命令放在文件中  .bash_history
	使用 cat .bash_history 进行查看
history -d   指定删除id
```

#### 别名

将一段命令起个名字，这样直接输入这个名字就代表输入了这行命令

```
定义别名
alias p_baidu='ping baidu.com'
使用别名
p_baidu
取消别名
unalias p_baidu
```

**查看系统自带别名**

```
[root@sgt ~]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias p_baidu='ping baidu.com'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

#### 查看命令帮助信息

```
man 查看所有命令信息
help 查看内置命令的帮助信息
-h
--help
info # 显示命令帮助信息
```

##### 几个查询命令帮助信息的网站

```
https://man.linuxde.net/
http://linux.51yip.com/
```

#### 关机与重启命令

##### 关机

```
shutdown -h
	shutdown -h 0 0分钟后关机，立刻关机
	shutdown -h 5 5分钟后关机
	shutdown -h 14:00 在14:00执行关机
	shutdown -c 取消关机命令
poweroff 关机
halt 关机，只关闭系统不关闭电源
	halt -p 关闭电源
init 0 切换运行级别关机
```

##### 重启

```
shutdown -r
    shutdown -r 0 立刻重启
    shutdown -r 2 2分钟后重启
    shutdown -r 15:00 在15:00执行重启
reboot 重启
init 6 切换运行级别重启
```

##### 注销

```
logout 		注销 不能退出非登录式shell
exit 		退出当前用户
ctrl + d 	快捷键注销
```

#### 显示IP地址命令

```
ip a
    ip a		 显示所有网卡的ip地址
    ip a s eth0  显示eth0网卡的ip地址(ip a show eth0的简写)

ifconfig
	需要先安装net-tools：yum install -y net-tools
    ifconfig 		查看所有
    ifconfig eth0	查看eth0网卡ip地址
    
hostname 本身显示主机名的命令
	hostname -i 查看所有网卡mac地址 内网、外网ip地址
		fe80::de5e:fc76:8350:f42f%eth0 fe80::4e14:fc9e:4e93:1be5%eth1 10.0.0.100 172.16.1.100
	hostname -I 只查看所有网卡内网、外网ip地址
		10.0.0.100 172.16.1.100
```

### 文件管理

#### CentOS Linux系统文件目录结构

```
/bin -> usr/bin 			普通命令的目录(带软链接)
/boot						引导目录
/dev						设备目录
  |__ /dev/cdrom sr0 			光驱，光盘 *
  |__ /dev/random				产生随机数 *
  |__ /dev/zero					源源不断产生数据
  |__ /dev/pts/					虚拟终端
  |__ /dev/sd*					磁盘分区
  |__ /dev/stderr				错误输出2
  |__ /dev/stdin				标准输入0
  |__ /dev/stdout				标准输入1
  |__ /dev/null					黑洞 *
/etc						配置文件目录
  |__ /etc/sysconfig/network-scripts/ifcfg-eth0		网卡配置文件 *
  |__ /etc/hostname									主机名配置文件
  |__ /etc/resolv.conf								本地的DNS配置文件
  |__ /etc/fstab									挂载设备目录
  |__ /etc/sysconfig/selinux						安全策略
  |__ /etc/motd										登录系统前显示的自定义内容
  |__ /etc/hosts									本地域名解析文件
/home						所有普通用户的家目录 /home/username
/lib -> usr/lib				库文件 32位
/lib64 -> usr/lib64			库文件 64位
/media						挂载点，可移动设备
/mnt						临时挂载点
/opt						早期第三方软件安装目录(现在已经很少使用)
/proc						虚拟信息，存放系统实时状态信息
/root						超级管理员的家目录
/run						程序运行产生的pid文件存放点
/sbin -> usr/sbin			超级管理员使用的命令目录(带软链接)
/srv						物理设备的信息(平时用不到)
/sys						物理设备
/tmp						文件的临时存放点
/usr						安装软件目录 *
  |__ /usr/local				默认常用目录
/var						可变目录
  |__ /var/run				pid文件
  |__ /var/log				日志
  |__ /var/log/messages		系统日志
  |__ /var/log/secure		用户登录日志
```

#### 路径的定位

```
linux中一个点和两个点的含义
	. 		表示当前目录
	.. 		表示当前目录的上一级目录
绝对路径与相对路径
	以根为开头的路径--->绝对路径，否则--->相对路径
		tips:以~开头的也是绝对路径
		示例：
			[root@sgt ~]# cd /etc/sysconfig/					绝对
			[root@sgt /etc/sysconfig]# cd network-scripts/  	相对
```

#### 文件管理基础命令

##### cd 切换目录

```
cd 						切换到当前用户的家目录下
cd ~					切换到当前用户的家目录下
cd /					切换到根目录下
cd /etc/sysconfig/ 		切换到指定绝对路径下
cd .					保持当前目录
cd ..					切换到当前目录的上一级目录
cd -					切换到上一次的目录
```

##### pwd 显示当前路径

##### tree 文件树

Linux中以.开头的文件都是隐藏文件

需要安装 

```
yum install -y tree
```

```
tree -L 1 / 			查看深度，层级，后面跟的是正整数
tree -d	/var/log		只显示目录
tree -f /var/log		只显示文件(以绝对路径显示)
tree -F /var/log		给目录加个标识符，用来区分目录和文件
tree -a /var/log		显示隐藏文件
```

##### ls 文件列表

```
ls /var/log				显示目录列表
ls -l /var/log 			以长格式显示目录列表，可简写成ll /var/log
ll -d /var/log			只显示目录本身信息
ll -a /var/log			显示所有文件，包括隐藏文件
ll -h /var/log  		以人类可读的形式显示
ll -t /var/log  		按从后到前时间排序显示
ll -rt /var/log			按从前到后时间排序显示
ls -F /var/log			给显示的目录加个标识符'/'
ls -i /var/log			显示inode号
ll -S /var/log			根据文件大小排序
ll -1 /var/log			显示列表
```

##### mkdir 创建目录

```
在Linux中一切皆文件，所有说目录和文件统称为文件
mkdir test				创建单级目录，文件存在会报错
mkdir -p test			创建目录，文件存在不操作，文件不存在创建
mkdir -v test			创建目录，显示过程
mkdir -p test/data		创建级联目录，文件存在不创建，不存在创建
mkdir -pv test/data		创建级联目录并显示过程
-p选项可以让文件创建过程中，如果文件存在则不操作，存在就创建，所以在创建目录时候最好加上该选项
mkdir -m 700 data		创建目录，给该目录赋予权限
mkdir oldboy_{mac,jason,tank}	一次创建多个目录
mkdir oldboy_{01..05}			创建序列，一次创建多个目录，目录名以01-05结尾
mkdir oldboy_{a..e}				创建序列，一次创建多个目录，目录名以a-e结尾

mkdir 'test '			创建的目录是可以带空格的，这样使用普通的ll查看文件就会出现2个相同的test文件
						其实这2个文件是不同的，只是有一个带空格，看不出来，此时使用ll -F看出区别
```

##### touch 创建文件

Linux中文件或目录是分大小写的

创建文件，如果有同名文件或目录，则不能创建，但会修改已存在同名的创建时间

```
touch hello.txt					创建文件
touch hello{shj,lzp,fxx}.txt	创建多文件
touch hello{01..05}.txt			创建文件序列，一次性创建有规律的文件
```

##### cp 复制

```
cp -r oldboy /opt/			递归复制
cp -p oldboy /opt/			保持属性
cp /etc/rc.local /opt/		复制绝对路径下rc.local到opt目录，他会将rc.local非软链接文件复制过去
cp -d oldboy /opt/			复制软链接
cp -a						复制，等效-pdr
cp -t 						将原目标与目标位置交换
cp命令系统系统自带别名cp -i	 自带交互式，所以复制目标目录如果文件已存在会出现询问覆盖
cp oldboy.log /opt/			将当前文件下的oldboy.log复制到opt目录下，
							如果oldboy.log在opt目录下存在，会先询问是否覆盖
\cp oldboy.log /opt/		让别名失效，这个如果oldboy.log在opt目录下存在，会直接覆盖

```

##### mv 移动 默认级联

```
mv a.txt /opt/				移动，存在会询问
mv test/ /opt/				移动多级目录不存在级联，直接可以移动
mv -f a.txt /opt/			强制移动，覆盖
mv -t						将原目标与目标位置交换
mv a.txt b.tt				重命名
```

##### rm 删除

```
rm a.txt					带提示删除
rm -f a.txt					直接删除
rm -rf test/				递归删除test目录及其下所有文件
rm -rf test/*				删除test下所有文件
rm -rf oldboy*				匹配删除
rm -rf ./*					删除当前目录下所有
rm -rf ./ *

设置rm命令不能用
alias rm='ehco rm command avalid'
```

##### echo 打印

```
echo hello world		不加引号，出现空格不会看作整体，会解析变量，支持通配符
echo -e 				支持一些特殊符号的功能
echo -e "hello\nworld"
echo 'hello world'		单引号，强引用，真正的所见即所得
echo "$PS1"				双引号，弱引用，支持变量，会解析变量
echo `hostname`			反引号，先执行反引号里面的命令，把结果交给外面命令，和$()作用一样
echo $(hostname)
echo hello world >a.txt	将hello world 写入a.txt文件中(存在，清空覆盖),a.txt没有会新建
echo hello world >>a.txt	将hello world 写入a.txt文件中(存在，追加)，a.txt没有会新建
```

##### cat 查看文件

```
cat /root/a.txt		查看文件内容
cat -n a.txt		显示行号
cat -A a.txt		给每一行的结尾加标识符$
cat >a.txt<<EOF		创建a.txt文件，然后用EOF为写入结束符，然后写入信息，EOF结束，表示写入结束
	hello
	bye
	EOF
cat >>a.txt<<'EOF'	给EOF加引号会解决一个坑，就是写入的信息包含特殊符号时候，比如$add-info2,这样就						会正常写入，不然会没有。
	add-info1
	$add-info2
	EOF
注意:a.txt前面的>>代表追加，如果是>则是清空覆盖	
```

##### more 分页显示文件内容

```
more a.txt
more -5 a.txt			一页显示5行
more +7 a.txt			从第7行开始显示
空格|f				  下一页
b 			  		   上一页
回车					  下一行
/						搜索
=						显示当前光标所在行
q						退出
查看文件后自动退出
```

##### less 分页显示文件内容

```
less a.txt
less -N a.txt	显示带行号
/						搜索，会高亮显示，n向下查找，N向上查找
=						显示当前光标所在行，会显示第几行到第几行、字节大小
其他和more差不多
```

##### head 显示文件头部信息

```
head a.txt			默认显示前10行内容
head -n 6			取消默认输出，指定显示前6行
head -6				取消默认输出，指定显示前6行
head -c	5			显示头部5个字符
```

##### tail 显示文件尾部信息

```
tail a.txt				默认显示最后10行内容
tail -n 6 a.txt			取消默认输出，指定显示最后6行
tail -6	a.txt			取消默认输出，指定显示最后6行
tail -c	5 a.txt			显示尾部5个字符(包含换行符)
tail -f a.txt			[*****]实时显示文件尾部信息，当a.txt不存在就会报错退出
tail -F a.txt			[*****]实时显示文件尾部信息，当a.txt不存在就会报错但是不报错
						会一直尝试查看，不会退出
tailf a.txt				实时查看文件内容更新，在磁盘不更新时，不会读取磁盘，减少磁盘的读写频率
```

##### grep 文本处理工具 过滤

```
grep root passwd	过滤passwd文件中包含root信息的所有行
grep -i				忽略大小写
grep -v				排除
grep -n 			给过滤出来的内容，加上所在文件中的行号
grep -c				统计过滤出的内容有几行
grep -w				精确匹配，只过滤要过滤出来的字符，而不是包含该字符的字符串
					比如hello world，grep -w he a.txt 不会匹配出hello world这行
grep -o 			只显示过滤出来的内容
grep '^root' passwd	过滤passwd文件中以root开头的字符所有行
grep 'root$' passwd	过滤passwd文件中以root结尾的字符所有行
grep -n '^$' passwd	过滤空行,并显示该空行的行号
grep '\$' aaa.txt	将特殊符号$转义成普通字符串
grep -A 2 'halt' passwd		匹配过滤显示出来的内容在向下匹配2行
grep -B 3 'halt' passwd		匹配过滤显示出来的内容在向上匹配3行
grep -C 2 'halt' passwd		匹配过滤显示出来的内容在向上向下各匹配2行

正则
	^ 		以什么开头
    $ 		以什么结尾
    ^$ 		空行
    . 		匹配任意一个字符
    * 		前面一个字符出现0此或0此以上
    .* 		所有
    [] 		整体，包含[]里面的字符
```

##### locate 根据数据库查找（**）

安装 

```
yum install mlocate -y
```

```
每次查找之前需要更新数据库
updatedb
然后再进行查找
locate hostname
支持正则
locate -r hostname$		查找以hostname结尾的
结果：
	/etc/hostname
    /etc/selinux/targeted/active/modules/100/hostname
    /usr/bin/hostname
    /usr/bin/nmtui-hostname
    /usr/lib64/gettext/hostname
```

##### which 查找(***使用最多)

```
which hostname
结果：(注意与locate的区别)
	/usr/bin/hostname
```

##### whereis（不常用）

```
whereis hostname
结果：
	hostname: /usr/bin/hostname /etc/hostname /usr/share/man/man1/hostname.1.gz /usr/share/man/man5/hostname.5.gz
```

##### type

```
type hostname
	查找结果 hostname is /usr/bin/hostname
type cd
	查找结果 cd is a shell builtin
type -a cd
	查找结果 
	cd is a shell builtin
	cd is /usr/bin/cd
```

##### find 查找(很强大，使用频率高，以后细说)

```
find / -type f -name 'hostname'
	查找结果
	/proc/sys/kernel/hostname
    /etc/hostname
    /tmp/hostname
    /usr/bin/hostname
    /usr/lib64/gettext/hostname
    
find / -type f -name '*hostname'    
```

##### 文件下载

```
curl 文件传输
	curl -o 文件重命名 下载url
```

```
查看yum官方源
ll /etc/yum.repos.d/
更换国内源(比如ali源)
阿里源官网 https://opsx.alibaba.com => 镜像 => centos 帮助 =>找到对应版本链接下载
删除官方源 rm -rf /etc/yum.repos.d/*
添加阿里源 (选项-o 指定下载路径)
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
查看仓库 yum repolist
安装wget工具 yum  install -y  wget
安装扩展源 (选项-O 指定下载路径)
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

下载
sz 文件路径
```

##### 文件上传

```
安装
yum install -y  lrzsz
rz建议上传4g以下文件，上传的话会出问题，如果上传4g以上文件需使用xftp工具
上传 => rz 回车，弹出选择上传文件对话框
	   文件重复，会自动重命名
```

##### 字符处理

```
sort 排序
sort -t ':' a.txt 			-t指定':'为分隔符 默认第一列进行排序，不指定默认以空格为分隔符		
sort -t ':' -k2 a.txt 		-k指定'2'为第2列进行排序，不指定默认第一列
sort -t ':' -nk2 a.txt 		-nk2指定'2'为第2列进行排序，同时使用n按照数字进行排序
sort -t ':' -rnk2 a.txt		-rnk2指定'2'为第2列进行排序，同时按照数字进行排序,使用r倒序
```

```
uniq 去重(此功能需要重复的行必须相邻，否则无法去重，所以需要先排序，让重复行相邻再去重)
sort a.txt | uniq					排序后去重
sort a.txt | uniq -c 				统计出现次数
sort a.txt | uniq -c| sort -rn		再次以数字倒序排序
```

```
cut 取列
cut -d ':' -f7 a.txt				-d指定':'分隔符，-f7 取第7列
cut -d ':' -f7 a.txt | sort | uniq -c	使用管道，对取出第7列，进行排序，去重
cut -d ':' -f7 a.txt | sort | uniq -c |sort -n		管道可以连续使用，再以数字排序
cut -d ':' -f7 a.txt | sort | uniq -c |sort -rn		再以数字倒序

echo 'hello world !' | cut -c 1-5		-c 取指定字符
```

```
tr 替换 无法单独对文件操作，需要用<a.txt,当然通过管道可以直接使用
tr 'a' 'b' <a.txt			将a替换成b
tr -d 'a'					删除所有'a'

```

```
wc 统计 (统计列数会以特殊字符进行分隔统计)
wc a.txt					统计全部： 行数 列数 字节数
wc -l a.txt					-l 统计行数
wc -w a.txt					-w 统计列数
wc -c a.txt					-c 统计字节数
```

##### sed 擅长替换，可以增删改查(支持正则，在//中)

```
查
sed -n '11p' services		查看services文件第11行
sed -n '11,20p' services	查看services文件第11到20行，用逗号','，连续行
sed -n '11p;20p' services	查看services文件11行和第20行，用分号';',不连续多行
过滤
sed -n '/root/p' passwd		过滤出root，使用/过滤的字符/
sed -rn '/root|halt/p' passwd	r:让'|’生效，支持扩展正则，过滤多个字符
删
sed '5d' a.txt				删除第5行，d代表删除
sed '5,10d' a.txt		    删除第5-10行，d代表删除
sed '5;8d' a.txt		   	删除第5行和8行，d代表删除
sed '/oldboy/d'	a.txt		删除匹配到到字符串所在行
改
sed 's#aaa#bbb#g' a.txt		s代表替换，g代表全局
							将文件内的aaa替换成bbb，这里的#可以是@&等字符，通常使用#标识
sed -i 's#aaa#bbb#g' a.txt		不加-i只是显示替换的结果，实际文件不生效，加上就会真正的修改替换了
先备份后修改
sed -i.bak 's#aaa#bbb#g' a.txt	替换时候顺便在当前文件夹备份该文件(a.txt.bak)
替换
sed '1,3s#aaa#bbb#g' a.txt		按第1行和第3行进行替换？
sed 's#[0-9]#sb#g' a.txt		将所有单个数字替换成sb
sed 's#[0-9]{1,}#sb#g' a.txt	连续数字替换成sb
关闭selinux
getenforce
sed 's#^SELINUX=.*#SELINUX=disabled#g' /etc/sysconfig/selinux
追加
sed '2ahello' a.txt			2a,2代表第2行 a代表追加，即在第二行后追加hello
sed '$ahello' a.txt			$a,追加到最后面
插入
sed '3iworld' a.txt			3i,3代表第3行，i代表插入，在第三行前面插入world
sed '1iworld' a.txt			1i,插入到最前面
```

##### awk

```
取行
awk 'NR==1' a.txt				NR代表行，取出第一行
awk 'NR==1,NR==3' a.txt			取出第1-3行
awk 'NR==1;NR==3' a.txt			取出第1行和3行
awk 'NR>=2 && NR<=3' a.txt		&& 与关系，取出大于等于2且小于等于3的行
awk 'NR<2 || NR>3' a.txt		|| 或关系，取出小于2或大于3的行
过滤
awk '/root/' a.txt				过滤包含root的字符的行
awk '/root|halt/' a.txt			过滤包含root或halt的字符的行
awk '!/\/sbin\/nologin/' a.txt	\代表转义，！代表取反
取列
awk -F; '{print $7}' a.txt			-F指定分隔符，默认指定空白字符，';'为分隔符，$7取第7列
awk -F '[:]' '{print $7}' a.txt		$7列数，取第7列，可用多个分隔符
awk -F '[:]' '{print $NF}' a.txt	$NF最后一列
ip a s eth0 | awk 'NR==3' | awk -F '[ /]*' {print $3}  空格前面什么都没有也认为一列
ip a s eth0 | awk -F '[ /]+' 'NR==3{print $3}'
if config eth0 | awk 'NR==2{print $2}'
if config eth0 | sed -nr '2s#.*t (.*) n.*#\1#gp'
awk '{print $0,NR}' a.txt	添加行号在后
awk '{print NR,$0}' a.txt	添加行号在前
awk -F: '{print NF,$NF}' passwd		一个NF代表总列数
awk -F: '{print NF,$0}' passwd		$0表示整行内容

```

##### 文件属性

```
-rw-r--r--. 1 root root    25 Aug 16 10:01 file.txt
-文件类型 - 普通文件，d 目录，l 软链接，b 块设备，c 字符设备，s 套接字文件，p 管道文件
		使用 find / -type s -ls 进行查找	
		file命令查看文件类型 file /var/123.txt
 rw-r--r--权限
 			1硬连接数量
 			  root属主
 			       root属组
 			       		   25文件大小
                              Aug 16 10:01文件修改时间
                              			   file.txt文件名(不属于文件属性)
```

##### 链接文件

```
用户数据：数据块，数据真实存放位置，block
元数据：文件属性信息，指向/标识文件数据位置(inode号)
```

**软链接**

```
.相当于windows系统中的快捷方式，与原文件不同，有独立的inode号，可以跨区创建，对文件、目录都能创建，删除.原文件，软链接文件会失效(红底白字闪烁状)
.一般作为企业版本升级、代码上线、目录过深方便使用
.创建方法：ln -s /etc/file.txt f.txt	-s代表创建软链接
```

**硬链接(很少使用)**

```
.给文件加一个入口,inode号相同，
.硬连接只能对文件创建，不能对目录，且无法跨区
.只有当硬连接为0的时候，代表这个文件才能算删除掉
.创建方法：ln file.txt f.link
.目录在创建时候会自带2个硬连接，随着目录里面文件增加，会随着增加(./..)
```

##### 命令执行过程

命令缓存 使用`hash`查看

```
hash -d name 删除指定的命令缓存
hash -r 清空命令缓存表
```

**命令执行流程：执行一个ping命令的整个流程步骤**

```
1) 检查执行的命令是否使用的是绝对路径执行的
2) 检查ping命令是否存在alias别名
3) 检查ping命令是内部命令还是外部命令
4) 如果是内部命令Bash直接执行，如果是外部命令，首先检查Hash缓存，存在则直接调取
5) 如果该命令不存在Hash缓存，则通过PATH路径进行逐行查找该命令所在位置
6) 如果PATH路径没有查找到该命令所在的路径，则返回错误码，command not found
```

#### vim编辑器

安装

```
yum install -y vim
```

##### 命令模式|编辑模式

```
HJKL 			左下上右 移动光标
shift ^ 		移动到行首
shift $ 		移动到行尾
G 				移动到文件行尾
gg 				移动到文件行首
ngg 			n=数字，跳到第n行行首
dd 				删除当前光标所在行，自动跳到下一行首
dG 				删除当前光标所在行及其向下的所有内容
ndd 			删除当前光标所在行向下n行，包含当前行，自动跳下一行首
p 				当前行的下一行粘贴，粘贴多次，np
P 				当前行的上一行粘贴，粘贴多次，np
yy 				复制当前行
nyy 			复制当前行向下n行，包含当前行
r 				单个替换当前光标所在位置进行替换
R 				多个替换
u 				撤销
ctrl + r 		取消撤销
de/dw 			删除当前光标向后的一组字符串
D 				删除光标当前所在行向后的所有内容
x/delete 		从当前光标所在位置向后删除一个字符
X 				当前光标所在位置从后往前删除一个字符
ctrl + f 		向下翻页
ctrl + b 		向上翻页

进入编辑模式相关快捷键
i 				进入编辑模式，且在当前光标所在字符前插入字符
I				进入编辑模式，且在当前光标所在行首插入字符
a				进入编辑模式，且在当前光标所在字符后插入字符
A 				进入编辑模式，且在当前光标所在行尾插入字符
o 				进入编辑模式，且在光标所在行的下面新建一行进行编辑
O 				进入编辑模式，且在光标所在行的上面新建一行进行编辑
C				删除当前光标所在位置向后的当前行内容，并进入编辑模式
ce/cw			删除当前光标所在行的位置一组字符串，并进入编辑模式
s				删除当前所在单个字符，并进入编辑模式
S				删除当前所在行，并进入编辑模式

Esc键		   退出编辑模式
```

##### 末行模式

```
:				进入末行模式
:w				保存
:q				退出
:wq				保存退出
:x				保存退出
ZZ				保存退出
/				搜索(n向下查找搜索内容，N向上查找搜索内容)
:n				跳转到第n行
:set nu			显示行号
删除
:nd				删除第n行
:n,md			删除第n到m行，m肯定要大于n
保存
:w /tmp/1.txt	把编辑的内容保存到/tmp/1.txt
:r /tmp/a.txt	从a.txt读入其内容到当前打开文件放在最前面
:10r /tmp/a.txt	从a.txt读入其内容到当前打开文件放在第10行后
替换
:%s#aaa#bbb#g	全局替换，将aaa替换成bbb
:1,4s#aaa#bbb#g	行替换，替换第1行到第4行，将aaa替换成bbb

批量添加(可视块模式)
    光标移动到要添加内容的第一行或者末行
    ctrl + v		进入可视块模式
    使用光标选择要添加的位置
    shift + i 进入编辑模式
    输入要添加的内容，只输入一行
    按Esc键退出
    批量添加成功
批量删除(可视块模式)
    光标移动到要添加内容的第一行或者末行
    ctrl + v		进入可视块模式
    使用光标选择要删除的位置
    按d/x删除

批量删除(可视行模式)
	光标移动到要删除掉的行上面
	按shift + v 进入可视行模式
	选择要删除的行
	按d/x删除
批量复制(可视行模式)
	光标移动到要复制的行上面，第一行或最后一行
	shift + v 进入可视行模式
	选中要复制的行
	按yy
	按p粘贴+.-
```

##### vim工作模式

```
会生成一个临时文件，保存之后会自动删除该临时文件
非正常状态退出时候，会出现vim故障，此时临时文件不会自动删除，再次进入该文件编辑时候会提示警告
解决
vim -r .filename.swp
保存退出
手动删除临时文件
```

##### vim变量

```
:set nu				显示行号
:set nonu			取消显示行号
:set list			给每一行的结尾加个制表符
:set nolist			取消给每一行的结尾加个制表符
:set ic				让搜索时候忽略大小写
:set noic			取消让搜索时候忽略大小写
:noh				取消高亮或者搜一个不存在的，也会取消高亮

vim配置文件(默认不存在，可创建)
/etc/vimrc
cat .vimrc
```

##### diff 文件比较

```
vimdiff a.txt b.txt
```

##### vim打开多个文件

```
vim o a.txt b.txt	水平打开多个文件
vim O a.txt b.txt	垂直打开多个文件
使用 ctrl + ww 进行切换
```

