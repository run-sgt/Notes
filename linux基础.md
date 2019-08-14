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

...



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
1.网卡命名规则
	安装系统最一开始需要对网卡设置命名规则，这样以后添加网卡的时候就会从ech0开始
	net.ifnames=0
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
2.历史命令
	history  查看历史命令的记录
		-c	 清空所有的历史命令
        -w	 把历史命令放在文件中  .bash_history
        -d   指定删除id
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

