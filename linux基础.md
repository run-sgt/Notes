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
rm -rf test/				递归删除
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
echo hello world >a.txt	将hello world 写入a.txt文件中(存在，清空覆盖),a.txt没有会新建
echo hello world >>a.txt	将hello world 写入a.txt文件中(存在，追加)，a.txt没有会新建
```

##### cat 查看文件

```
cat /root/a.txt		查看文件内容
cat -n a.txt		显示行号
cat -A a.txt		给每一行的结尾加标识符
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
grep -o 			只显示过滤出来的内容
grep ^root passwd	过滤passwd文件中以root开头的字符所有行
grep root$ passwd	过滤passwd文件中以root结尾的字符所有行
grep -n '^$' passwd	过滤空行,并显示该空行的行号
grep -E 'root|halt' passwd	过滤含root或者halt的
egrep 'root|halt' passwd	过滤含root或者halt的
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

