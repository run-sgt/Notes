# 第14章 Linux系统管理-磁盘管理

[第14章 Linux系统管理-磁盘管理](https://www.increase93.com/base-14.html#header-n0)[1. 磁盘的基本概念](https://www.increase93.com/base-14.html#header-n3)[2. 磁盘的基本结构](https://www.increase93.com/base-14.html#header-n15)[3. 磁盘的预备知识](https://www.increase93.com/base-14.html#header-n35)[4. 磁盘基本分区Fdisk](https://www.increase93.com/base-14.html#header-n96)[5. 磁盘基本分区Gdisk](https://www.increase93.com/base-14.html#header-n108)[6. 磁盘挂载方式Mount](https://www.increase93.com/base-14.html#header-n120)[7. 虚拟内存Swap介绍](https://www.increase93.com/base-14.html#header-n203)[8. 磁盘阵列RAID概述](https://www.increase93.com/base-14.html#header-n213)[9. 磁盘阵列RAID实战](https://www.increase93.com/base-14.html#header-n228)[10. 逻辑卷LVM概述](https://www.increase93.com/base-14.html#header-n308)[11. 逻辑卷LVM实践](https://www.increase93.com/base-14.html#header-n317)[12. 磁盘常见故障](https://www.increase93.com/base-14.html#header-n345)

### 1. 磁盘的基本概念

**01. 什么是磁盘**



```

```









```

    绝大多数人对硬盘都不陌生，
    一块小小的硬盘里，就可以存储海量的照片、音乐和电影等，尤其是你们喜爱的各类**动作片。
    但如此小的空间，是如何存储那么多信息的呢？

```



![img](https://www.increase93.com/Picture/14-1.gif)



```

```









```

    每个硬盘中心都是一摞高速运转的圆盘，圆盘由一层带磁的微型金属颗粒组成，每个颗粒也被称为一比特，都有自己的磁化程度，用于储存0和1。

```



![img](https://www.increase93.com/Picture/14-2.gif)



```

```









```

    当记录数据时，硬盘的磁头开始通电，形成强磁场，数据在磁场的作用下转变成电流，使颗粒磁化，从而将信息记录在圆盘上。
    
```



![img](https://www.increase93.com/Picture/14-3.gif)



```

```









```
    
    由海量颗粒组成的信息，就是我们存在硬盘里的数据。
    
```



![img](https://www.increase93.com/Picture/14-4.png)

**什么是磁盘、软盘、硬盘？**



```

```









```

1.什么是磁盘？

磁盘是指利用磁记录技术存储数据的存储器，是所有硬式存储的统称，如最早出现的软盘，现在的硬盘，都是磁盘中的一部分。

2.什么是软盘？

磁盘开始指的是1.44MB的3.5英寸磁盘，这是很早时候的电脑储存盘，也叫软盘。

软盘在如今已经很少被使用了，使用软盘需要安装软盘驱动。软盘容量小，容易损坏。随着数据的大量增长，软盘已经不满足日益所需的数据存储需求，所以，硬盘便被研发了出来。

3.什么是硬盘？

硬盘就是一种最为常见的外存储器，它好比是数据的外部仓库一样。电脑除了要有“工作间”，还要有专门存储东西的仓库。是计算机中的最大的存储装置了，我们会将磁盘的储存片装到硬质金属盒子里，这样就可以得到更好的保护，而且在使用寿命上也比软盘优越得多，只有低格的时候才会对硬盘有很大的伤害。

4.磁盘与软盘硬盘的关系？

磁盘是一种统称，即是一开始的软盘，又是现如今的硬盘，但是在现在，说起磁盘，都指的是硬盘。

```



### 2. 磁盘的基本结构

**01. 什么是盘片？**



```

```









```
    
    盘片是硬盘中承载数据存储的介质。硬盘盘片是以坚固耐用的材料为盘基，将磁粉附着在铝合金(新材料也有用玻璃)圆盘片的表面上，表面被加工的相当平滑。这些磁粉被划分成称为磁道的若干个同心圆，在每个同心圆的磁道上就好像有无数的任意排列的小磁铁，它们分别代表着0和1的状态。当这些小磁铁受到来自磁头的磁力影响时，其排列的方向会随之改变。
    利用磁头的磁力控制指定的一些小磁铁方向，使每个小磁铁都可以用来储存信息。硬盘是由多个盘片叠加在一起，互相之间由垫圈隔开。
    硬盘中一般会有多个盘片，每个盘片包含两个面，每个盘面都对应地有一个读/写磁头（Head，简写为H）。受到硬盘整体体积和生产成本的限制，盘片数量都受到限制，一般都在5片以内。盘片的编号自下向上从0开始，如最下边的盘片有0面和1面，再上一个盘片就编号为2面和3面。

```



![img](https://www.increase93.com/Picture/panpian.png)

**02. 什么是磁道？**



```

```









```
    
    当磁盘旋转时，磁头若保持在一个位置上，则每个磁头都会在磁盘表面划出一个圆形轨迹，这些圆形轨迹就叫做磁道。
    每个盘片的盘面在出厂的时候被划分出了多个同心圆环，数据就存储在这样的同心圆环上面，每个盘面可以划分多个磁道，但是肉眼不可见。通常一块盘面有成千上万个磁道。
    磁盘上的磁道是一组记录密度不同的同心圆。磁表面存储器是在不同形状(如盘状、带状等)的载体上。
    
```



![img](https://www.increase93.com/Picture/cidao.png)

**03. 什么是扇区？**



```

```









```
    
    在硬盘出厂时会对磁盘进行一次低级格式化，其实就是再将每个磁道划分为若干个弧段，每个弧段就是一个扇区（Sector）。若干个扇区就组成整个盘片，硬盘的读写以扇区为基本单位。这种以簇为最小分配单位的机制，使硬盘对数据的管理变得相对容易。现在每个扇区可存储512字节数据，已经成了业界的约定。
    低级格式化就是将空白的磁盘划分出柱面和磁道,再将磁道划分为若干个扇区,每个扇区又划分出标识部分ID、间隔区GAP和数据区DATA等。
    硬盘低级格式化的功用：硬盘低级格式化是对硬盘最彻底的初始化方式，经过低格后的硬盘，原来保存的数据将会全部丢失，所以一般来说低格硬盘是非常不可取的，只有非常必要的时候才能低格硬盘。而这个所谓的必要时候有两种，一是硬盘出厂前，硬盘厂会对硬盘进行一次低级格式化；另一个是当硬盘出现某种类型的坏道时，使用低级格式化能起到一定的缓解或者屏蔽作用。
    
```



![img](https://www.increase93.com/Picture/shanqu1.png)

**04. 什么是柱面？**



```

```









```
    
    柱面实际上就是我们抽象出来的一个逻辑概念，简单来说就是处于同一个垂直区域的磁道称为柱面，即各盘面上面相同位置的集合，这样数据如果存储到相同半径磁道上的同一扇区，这样可以实现并行读取，主要是减少磁头寻道时间。
    
```



![img](https://www.increase93.com/Picture/zhumian1.png)

**05. 什么是磁头？**



```

```









```
    
    磁头是硬盘中最昂贵的部件，也是硬盘技术中最重要和最关键的一环。主要作用是读取磁盘磁道上面的金属块，负责读或写入数据。
    
```



![img](https://www.increase93.com/Picture/citou.png)

**磁盘整体结构图**

![img](https://www.increase93.com/Picture/14-5.png)

![img](https://www.increase93.com/Picture/14-9.png)

![img](https://www.increase93.com/Picture/14-7.jpg)

### 3. 磁盘的预备知识

**我们需要了解下磁盘的接口类型，磁盘涉及的相关术语，磁盘在Linux下的命令方式**

**01. 磁盘的接口类型**

**IDE，SCSI （已经被淘汰）**

![img](https://www.increase93.com/Picture/14-8.jpg)

**SATA III 与 SAS （企业使用较多）**

![img](https://www.increase93.com/Picture/14-11.jpg)

**SSD（支持SATA、PCI-E）**

[M2接口百度百科](https://baike.baidu.com/item/m.2接口)

[你了解M.2固态硬盘吗？](http://baijiahao.baidu.com/s?id=1604321789851430125&wfr=spider&for=pc)

[装机到底要不要买M.2接口的SSD？](http://www.sohu.com/a/253988990_610530)

[SATA、MSATA、PCIE和M2都是什么？](http://www.sohu.com/a/229021919_463982)

[固态硬盘SATA和MSATA有什么区别？](https://zhidao.baidu.com/question/754765227221408324.html)

**机械和固态的对比**

| 机械（HDD）      | 固态（SSD）        |
| :--------------- | :----------------- |
| **容量大价格低** | **容量小价格高**   |
| **抗击打能力弱** | **抗击打能力高**   |
| **读写速度慢**   | **读写速度很快**   |
| **数据恢复易**   | **数据恢复难**     |
| **使用时间无限** | **使用时间1-2w次** |

**02. 磁盘的基本术语**



```

```









```

尺寸：2.5英寸、3.5英寸

容量：B、KB、MB、GB、TB、PB、EB

转速：5.4k、7.2k、10k、15k，单位表示为RPM，RPM是Revolutions per minute的缩写，是转/每分钟。

IOPS：每秒能够发生IO的次数

```



**03. 磁盘在系统上的命名方式**

| 设备名称     | 分区信息      | 设备类型                       |
| :----------- | :------------ | :----------------------------- |
| **/dev/sda** | **/dev/sda1** | **第一块物理磁盘第一分区**     |
| **/dev/sdb** | **/dev/sdb2** | **第二块磁盘第二个分区**       |
| **/dev/vdd** | **/dev/vdd4** | **第四块虚拟磁盘的第四个分区** |



```

```









```

PS1：系统中分区由数字编号表示，1-4表示给主分区使用和扩展分区，逻辑分区从5开始，为什么分区还有限制？不应该是随意分配？因为MBR分区表只能分配4个主分区？why？

```



[MBR为什么只能划分4个主分区？](https://baike.baidu.com/item/主引导记录/7612638)



```

```









```

PS2：前面我们已经提到过MBR分区表只能分配4个主分区，但现在还有一种新型的分区表GPT，GPT支持分配128个主分区。注意MBR与GPT之间不能互转，会导致数据丢失。

```



[MBR与GPT之间又有什么区别？](http://www.360doc.com/content/18/0614/22/6140124_762487520.shtml)

**MBR位置:0磁头0磁道1扇区。**

![img](https://www.increase93.com/Picture/14-12.png)



```

```









```

#如何查看磁盘的大小及文件内容的大小
[root@qls ~]# du -sh /etc/
34M /etc/

#查看磁盘使用情况
[root@qls ~]# df -h    
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G  5.8G   32G  16% /
devtmpfs        909M     0  909M   0% /dev
tmpfs           919M     0  919M   0% /dev/shm
tmpfs           919M  508K  919M   1% /run
tmpfs           919M     0  919M   0% /sys/fs/cgroup
tmpfs           184M     0  184M   0% /run/user/0

#查看设备详情,以及分区的情况
[root@qls ~]# lsblk 
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vda    253:0    0  40G  0 disk 
└─vda1 253:1    0  40G  0 part /

```



**在linux中，当我们给系统添加一块新硬盘时，我们是无法使用的，因为他还没有分区和格式化，只有当我们将新硬盘分区并格式化之后，挂载在某个目录下，才能供我们正常使用，接下来我们要学习两种种硬盘分区工具，Fdisk，Gdisk；分别是针对于MBR分区格式，GPT分区格式进行分区。还有一种高级分区工具Parted（可以针对于多种分区表进行分区，使用起来也是比较复杂的，不建议使用）。**

### 4. 磁盘基本分区Fdisk

**适用于磁盘小于2TB的磁盘，分区类型MBR，主分区4或主分区3+扩展分区（逻辑分区+…），分区后需要保存后才能生效**

**01. 先准备磁盘**



```

```









```
由于没有真实的服务器,只能添加虚拟的磁盘，VMware中添加硬盘，添加一块20G的硬盘。
```



**02.分区fdisk--->分低于2TB的磁盘**



```

```









```

[root@qls ~]# ll /dev/sd*
brw-rw---- 1 root disk 8,  0 7月   5 19:21 /dev/sda
brw-rw---- 1 root disk 8,  1 7月   5 19:21 /dev/sda1
brw-rw---- 1 root disk 8,  2 7月   5 19:21 /dev/sda2
brw-rw---- 1 root disk 8, 16 7月   5 19:21 /dev/sdb
brw-rw---- 1 root disk 8, 32 7月   5 19:21 /dev/sdc
[root@qls ~]# lsblk 
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   39G  0 part 
  ├─centos-root 253:0    0   37G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0   20G  0 disk 
sdc               8:32   0    4T  0 disk 

[root@qls ~]# fdisk  /dev/sdb
Command (m for help): m         #输入m列出常用的命令
Command action
   a   toggle a bootable flag                   #切换分区启动标记
   b   edit bsd disklabel                       #编辑sdb磁盘标签
   c   toggle the dos compatibility flag        #切换dos兼容模式
   d   delete a partition                       #删除分区
   l   list known partition types               #显示分区类型
   m   print this menu                          #显示帮助菜单
   n   add a new partition                      #新建分区
   o   create a new empty DOS partition table   #创建新的空白分区表
   p   print the partition table                #显示分区表的信息
   q   quit without saving changes              #不保存退出
   s   create a new empty Sun disklabel         #创建新的Sun磁盘标签
   t   change a partitions system id            #修改分区ID,可以通过l查看id
   u   change display/entry units               #修改容量单位,磁柱或扇区
   v   verify the partition table               #检验分区表
   w   write table to disk and exit             #保存退出
   x   extra functionality (experts only)       #拓展功能

#分配第1个分区
[root@qls ~]# fdisk /dev/sdb 
Command (m for help): n    
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p           #选择分配主分区
Partition number (1-4, default 1):      #主分区编号        
First sector (2048-41943039, default 2048):     #开始的扇区 回车即可
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-41943039, default 41943039): +1G #设置分区的大小
Partition 1 of type Linux and of size 1 GiB is set

Command (m for help): p     #查看当前磁盘的分区情况

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x44d0ef75

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux


#分配第2个分区
Command (m for help): n  
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): 
Using default response p
Partition number (2-4, default 2): 
First sector (2099200-41943039, default 2099200): 
Using default value 2099200
Last sector, +sectors or +size{K,M,G} (2099200-41943039, default 41943039): +2G
Partition 2 of type Linux and of size 2 GiB is set

Command (m for help): p

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x44d0ef75

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb2         2099200     6293503     2097152   83  Linux
    


#分配6个分区     (超出了4个主分区,最佳 3p+1e+3l   2p+1e+4l)
Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb2         2099200     6293503     2097152   83  Linux
/dev/sdb3         6293504    10487807     2097152   83  Linux
/dev/sdb4        10487808    41943039    15727616    5  Extended
/dev/sdb5        10489856    20975615     5242880   83  Linux
/dev/sdb6        20977664    31463423     5242880   83  Linux
/dev/sdb7        31465472    41943039     5238784   83  Linux


#检查分区情况
[root@qls ~]# lsblk 
sdb               8:16   0   20G  0 disk 
    |-sdb1            8:17   0    1G  0 part 
    |-sdb2            8:18   0    2G  0 part 
    |-sdb3            8:19   0    2G  0 part 
    |-sdb4            8:20   0    1K  0 part 
    |-sdb5            8:21   0    5G  0 part 
    |-sdb6            8:22   0    5G  0 part 
    `-sdb7            8:23   0    5G  0 part 

```



**03. 格式化->创建文件系统(装修)**



```

```









```

[root@qls ~]#  mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

```



**04. 挂载**



```

```









```

[root@qls /]# mount /dev/sdb1 /soft/        #将/soft作为/dev/sdb1分区的一个入口目录
[root@qls /]# touch /soft/1111          #添加111文件,实际上是在/dev/sdb1分区上添加的数据
[root@qls /]# ls /soft/
1111
[root@qls /]# umount /soft/                 #卸载/soft挂载信息
[root@qls /]# ls /soft/                     #soft又回到了原来的目录属性
nginx  nginx-1.16.0

---------注意: 原来写入到/dev/sdb1分区的数据不会被删除,当重新挂载一个新的目录,文件还会回来.
[root@qls /]# mkdir /data
[root@qls /]# mount /dev/sdb1 /data/
[root@qls /]# ls /data/
1111

```



**05. 实现永久挂载,写入配置文件/etc/fstab 以实现开机时,自动挂载**



```

```









```

[root@qls ~]# cat /etc/fstab
#设备名称   挂载的位置   文件系统类型  挂载的参数   是否备份    是否检查
/dev/sdb1   /data       xfs         defaults    0           0

#请先卸载挂载的信息,然后再mount -a
[root@qls ~]# mount -a

```



### 5. 磁盘基本分区Gdisk

**分区表为GPT，主分区\*128，分区后需要保存后才能生效**

**01. 准备环境**



```

```









```

VMware中添加硬盘，添加一块2T的硬盘。

#安装软件包
[root@qls ~]# yum install gdisk -y

```



**02. 创建分区**



```

```









```

[root@qls ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   50G  0 disk 
├─sda1   8:1    0  200M  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0 47.8G  0 part /
sdb      8:16   0   20G  0 disk 
├─sdb1   8:17   0    1G  0 part 
└─sdb2   8:18   0    2G  0 part 
sdc      8:32   0    2T  0 disk 
sr0     11:0    1  4.3G  0 rom  

[root@qls ~]# gdisk /dev/sdc
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help): m
b   back up GPT data to a file                          #将GPT数据备份到文件中
c   change a partition's name                           #更改分区的名称
d   delete a partition                                  #删除分区
i   show detailed information on a partition            #显示分区的详细信息
l   list known partition types                          #列出已知的分区类型
n   add a new partition                                 #添加一个新的分区
o   create a new empty GUID partition table (GPT)       #创建一个新的空GUID分区表(GPT)
p   print the partition table                           #打印分区表
q   quit without saving changes                         #没有保存更改就退出
r   recovery and transformation options (experts only)  #恢复和转换选项(仅限专家使用)
s   sort partitions                                     #年代分类分区
t   change a partition's type code                      #不要更改分区的类型代码
v   verify disk                                         #验证磁盘
w   write table to disk and exit                        #将表写入磁盘并退出
x   extra functionality (experts only)                  #额外功能(仅限专家使用)
?   print this menu                                     #打印菜单

Command (? for help): n     #创建新的分区
Partition number (1-128, default 1):    #分区编号
First sector (34-4294967262, default = 2048) or {+-}size{KMGTP}:    #扇区开始位置
Last sector (2048-4294967262, default = 4294967262) or {+-}size{KMGTP}: #设置分区大小
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): L     #查看文件系统，默认是Linux
0700 Microsoft basic data  0c01 Microsoft reserved    2700 Windows RE          
3000 ONIE boot             3001 ONIE config           4100 PowerPC PReP boot   
4200 Windows LDM data      4201 Windows LDM metadata  7501 IBM GPFS            
7f00 ChromeOS kernel       7f01 ChromeOS root         7f02 ChromeOS reserved   
8200 Linux swap            8300 Linux filesystem      8301 Linux reserved      
8302 Linux /home           8400 Intel Rapid Start     8e00 Linux LVM           
a500 FreeBSD disklabel     a501 FreeBSD boot          a502 FreeBSD swap        
a503 FreeBSD UFS           a504 FreeBSD ZFS           a505 FreeBSD Vinum/RAID  
a580 Midnight BSD data     a581 Midnight BSD boot     a582 Midnight BSD swap   
a583 Midnight BSD UFS      a584 Midnight BSD ZFS      a585 Midnight BSD Vinum  
a800 Apple UFS             a901 NetBSD swap           a902 NetBSD FFS          
a903 NetBSD LFS            a904 NetBSD concatenated   a905 NetBSD encrypted    
a906 NetBSD RAID           ab00 Apple boot            af00 Apple HFS/HFS+      
af01 Apple RAID            af02 Apple RAID offline    af03 Apple label         
af04 AppleTV recovery      af05 Apple Core Storage    be00 Solaris boot        
bf00 Solaris root          bf01 Solaris /usr & Mac Z  bf02 Solaris swap        
bf03 Solaris backup        bf04 Solaris /var          bf05 Solaris /home       
bf06 Solaris alternate se  bf07 Solaris Reserved 1    bf08 Solaris Reserved 2  
bf09 Solaris Reserved 3    bf0a Solaris Reserved 4    bf0b Solaris Reserved 5  
c001 HP-UX data            c002 HP-UX service         ea00 Freedesktop $BOOT   
eb00 Haiku BFS             ed00 Sony system partitio  ed01 Lenovo system partit
Press the <Enter> key to see more codes:    #翻页
ef00 EFI System            ef01 MBR partition scheme  ef02 BIOS boot partition 
fb00 VMWare VMFS           fb01 VMWare reserved       fc00 VMWare kcore crash p
fd00 Linux RAID            
Hex code or GUID (L to show codes, Enter = 8300):   #选择默认
Changed type of partition to 'Linux filesystem'

Command (? for help): p     #打印分区表信息
Disk /dev/sdc: 4294967296 sectors, 2.0 TiB
Logical sector size: 512 bytes
Disk identifier (GUID): 2DD1054A-9F8C-427A-803B-4F4E6F7F38B4
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 4294967262
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048      4294967262   2.0 TiB     8300  Linux filesystem

Command (? for help): w     #保存退出

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y    #确定
OK; writing new GUID partition table (GPT) to /dev/sdc.
The operation has completed successfully.

[root@qls ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   50G  0 disk 
├─sda1   8:1    0  200M  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0 47.8G  0 part /
sdb      8:16   0   20G  0 disk 
├─sdb1   8:17   0    1G  0 part 
└─sdb2   8:18   0    2G  0 part 
sdc      8:32   0    2T  0 disk 
└─sdc1   8:33   0    2T  0 part 
sr0     11:0    1  4.3G  0 rom  

```



**03. 格式化->创建文件系统(装修)**



```

```









```

[root@qls ~]# mkfs.xfs /dev/sdc1
meta-data=/dev/sdc1              isize=512    agcount=4, agsize=134217663 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=536870651, imaxpct=5
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=262143, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

```



**04. 挂载**



```

```









```

[root@qls ~]# mkdir /gbt
[root@qls ~]# mount /dev/sdc1 /gbt
[root@qls ~]# cd /gbt
[root@qls /gbt]# ll
total 0
[root@qls /gbt]# dd if=/dev/zero of=test.txt bs=10M count=100
100+0 records in
100+0 records out
1048576000 bytes (1.0 GB) copied, 1.62123 s, 647 MB/s
[root@qls /gbt]# ll
total 1024000
-rw-r--r-- 1 root root 1048576000 Jul  9 23:48 test.txt
[root@qls /gbt]# cd
[root@qls ~]# 
[root@qls ~]# umount /gbt
[root@qls ~]# ll /gbt
total 0

```



**05. 实现永久挂载,写入配置文件/etc/fstab 以实现开机时,自动挂载**



```

```









```

[root@qls ~]# cat /etc/fstab
#设备名称   挂载的位置   文件系统类型  挂载的参数   是否备份    是否检查
/dev/sdc1   /gbt        xfs         defaults    0           0

#请先卸载挂载的信息,然后再mount -a
[root@qls ~]# mount -a

```



### 6. 磁盘挂载方式Mount



```

```









```

前面我们已经提到过，如果需要使用磁盘的空间，需要准备一个空的目录作为挂载点，与该设备进行关联。
    
mount主要是为文件系统指定一个访问入口。
    
PS：类似我的商场没有门，那么就无法进入购买商品，此时通过mount命令可以创建一个入口。给超市安装一个门。

```



**01. 通过mount进行挂载，但重启将会失效；我们称为临时生效。**



```

```









```

#选项：
    -t  #指定文件系统挂载分区
    -a  #挂载/etc/fstab中的配置文件
    -o  #指定挂载参数
    
#示例：挂载/dev/sdb1至db1目录
[root@qls ~]# mkdir /db1
[root@qls ~]# mount -t xfs /dev/sdb1 /db1

```



**02. 挂载的磁盘，如果不想使用可以用umount命令进行卸载。**



```

```









```

#选项：
    -l  #强制卸载

#示例：
#1.卸载目录方式
[root@qls ~]# umount /db1

#2.卸载设备方式
[root@qls ~]# umount /dev/sdb1

#3.umount不能卸载的情况
[root@qls db1]# umount /db1  
umount: /db1: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1)

#PS: 如上情况解决办法有两种, 1.切换至其他目录 2.使用'-l'选项强制卸载

```



**03. 如果需要实现永久挂载则需要将挂载信息写入/etc/fstab配置文件中实现**



```

```









```

#建议挂载时使用UUID

#1.怎样获取磁盘分区的UUID，使用blkid命令获取
[root@qls ~]# blkid|grep sdc1
/dev/sdc1: UUID="c7c940b3-277b-49fa-960b-eef5ed6e9b1b"
    
#2.手动测试是否能正常的挂载
[root@qls ~]# mount UUID="c7c940b3-277b-49fa-960b-eef5ed6e9b1b" /datac

#3.将信息写入到/etc/fstab文件中,保证下次启动系统自动挂载
UUID="c7c940b3-277b-49fa-960b-eef5ed6e9b1b" /datac xfs defaults 0 0 

#4.卸载/datac挂载信息, 然后使用mount -a检查/etc/fstab文件是否ok
[root@qls ~]# mount -a

PS: 如果不检查写错了直接重启,  会导致系统无法启动. 

```



**04. /etc/fstab配置文件编写格式**

| 要挂载的设备  | 挂载点（入口） | 文件系统类型 | 挂载参数     | 是否备份 | 是否检查 |
| :------------ | :------------- | :----------- | :----------- | :------- | :------- |
| **/dev/sdb1** | **/data**      | **xfs**      | **defaults** | **0**    | **0**    |

**第一列：device:这里用来指定你要挂载的文件系统的设备名称或块信息，除了指定设备文件外，也可以使用UUID、LABEL来指定分区。**

**第二列：dir:指定挂载点的路径;**

**第三列：type:指定文件系统的类型,如ext3,ext4,xfs等;**

**第四列：options:指定挂载的参数,默认为defaults;**

| 参数            | 含义                                                         |
| :-------------- | :----------------------------------------------------------- |
| **async/sync**  | **是否同步方式运行，默认async（异步）。**                    |
| **user/nouser** | **是否允许普通用户使用mount命令挂载，默认nouser。**          |
| **exec/noexec** | **是否允许可执行文件执行，默认exec。**                       |
| **suid/nosuid** | **是否允许存在suid属性的文件，默认suid。**                   |
| **auto/noauto** | **执行mount -a时，此文件系统是否被主动挂载，默认auto。**     |
| **rw/ro**       | **是否只读或者读写模式进行挂载。默认rw。**                   |
| **defaults**    | **具有rw，suid，exec，auto，nouser，async等默认参数的设定。** |

**第五列：dump:表示该挂载后的文件系统能否被dump备份命令作用;**

| 选项  | 含义                             |
| :---- | :------------------------------- |
| **0** | **代表不做备份。**               |
| **1** | **代表要每天进行备份操作。**     |
| **2** | **代表不定日期的进行备份操作。** |

**第六列：pass:这里用来指定如何使用fsck来检查硬盘。**

| 选项  | 含义                                                         |
| :---- | :----------------------------------------------------------- |
| **0** | **代表不检查**                                               |
| **1** | **检验，挂载点为 / 的（即根分区）时候，必须在这里填写1，其他的都不能填写1。** |
| **2** | **检验 (当1级别检验完成之后进行2级别检验)**                  |

**05. 磁盘练习题**



```

```









```

1.准备一个200GB的磁盘空间
2.挂载到/data目录下   
3.往目录里存放一个 "docs|txt"  
4.实现开机挂载(请测试)
5.随意修改/etc/fstab重启系统, 进入修复.
6.准备一个200GB的磁盘空间
7.挂载到/data_read目录下   
8.无法往/data_read目录中写入文件

```



### 7. 虚拟内存Swap介绍



```

```









```

Swap分区在系统的物理内存不足时，将硬盘空间中的一部分空间释放出来，以供当前运行的程序使用。

PS：当物理内存不足时会随机kill掉占用内存的进程，从而产生oom，临时使用swap可以解决。

```



**01. 创建分区，并格式化为swap分区**



```

```









```

[root@qls ~]# fdisk  /dev/sdb       #分1个G大小
[root@qls ~]# mkswap /dev/sdb1      #格式化为swap

```



**02. 查看当前swap分区大小，然后进行扩展和缩小**



```

```









```

[root@qls ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1980        1475          80          10         424         242
Swap:          2047           4        2043

#1.扩展swap分区大小
[root@qls ~]# swapon /dev/sdb1
[root@qls ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1980        1475          80          10         424         242
Swap:          3047           4        2043

#2.缩小swap分区大小
[root@qls ~]# swapoff /dev/sdb1
[root@qls ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1980        1475          80          10         424         242
Swap:          2047           4        2043
[root@qls ~]# swapoff -a #代表关闭所有的swap

```



**03. 检查当前swap分区有哪些设备**



```

```









```

[root@qls ~]# swapon -s
文件名             类型      大小   已用  权限
/dev/dm-1   partition   2097148   4616    -2
/dev/sdb1   partition   1048572   0       -2

```



**04. 如果磁盘没有过多的分区可用，也可以通过文件增加SWAP空间，本质上还是磁盘**



```

```









```

[root@qls ~]# dd if=/dev/zero of=/opt/swap_file bs=1M count=500
[root@qls ~]# chmod 0600 /opt/swap_file
[root@qls ~]# mkswap -f /opt/swap_file 
[root@qls ~]# swapon /opt/swap_file
[root@qls ~]# free -m

PS: 如果希望swap开机自动挂载，将swap信息追加至/etc/fstab即可。

```



### 8. 磁盘阵列RAID概述

**01. 什么是RAID磁盘阵列**



```

```









```

    RAID简称磁盘阵列，那么什么是阵列，例如：古代打仗时为什么要对士兵进行排兵布阵，其目的在于提高士兵整体的作战能力，而不是某个士兵的战斗力。
    那么回到磁盘中，我们也可以将多块磁盘组合进行排列，提高磁盘的整体的读写能力和冗余能力，通常我们将其称为磁盘阵列。

```



**02. 为什么要使用RAID磁盘阵列**



```

```









```

1.在RAID中，可以让很多磁盘同时传输数据，因为多块磁盘在逻辑上感觉是一个磁盘，所以使用RAID可以达到单个磁盘的几倍、几十倍甚至上百倍的速率。
2.硬盘其实非常的脆弱，它经常会坏掉。所以有了RAID这个东西。它的目的是将好几个硬盘合并在一起，就算硬盘坏了一个，剩下还有好几个硬盘是正常的，这样服务器还能正常提供服务。保证磁盘高可用。
3.可以获得更高的容量。
PS：RAID可以预防数据丢失，但并不能百分百保证数据不丢，所以在使用RAID的同时还是要备份重要的数据。

```



**03. 实现磁盘阵列RAID的几种模式**



```

```





```

一、RAID0条带卷，最少两块硬盘（单块也可以做）。读写性能好，但没有容错机制。坏一块硬盘数据全丢。

二、RAID1镜像源，只能两块硬盘，写入性能慢，读取性能一般、有容错机制，但磁盘有50%浪费。

三、RAID5校验卷，至少三块硬盘，只允许坏一块盘，有效空间1/3，读写速度稍快。坏掉一块硬盘后，读会慢。
```





```

四、RAID10，先做RAID1，在做RAID0，至少四块硬盘，读写很快，有容错机制，但磁盘有50%浪费。

五、RAID总结：

冗余从好到坏：Raid1==>Raid10==>Raid5==>Raid0
性能从好到坏：Raid0==>Raid10==>Raid5==>Raid1
成本从低到高：Raid0==>Raid5==>Raid1==>Raid10

企业中的服务器如何选择Raid级别：根据数据的存储和访问的需求，去匹配对应的Raid级别。这句话适用于互联网的所有公司。

```



![img](https://www.increase93.com/Picture/raid0-5.png)

![img](https://www.increase93.com/Picture/raid10.png)

![img](https://www.increase93.com/Picture/raid-1.png)

![img](https://www.increase93.com/Picture/raid-2.png)

**04. 磁盘阵列RAID创建的两种方式**

```
1、软件RAID
通过系统功能或ID软件实现RAID，没有独立硬件和接口，需要占用一定的系统资源(CPU、硬盘接口速度），并且受操作系统稳定性影响；

2、硬件RAID（视频）
通过独立的ID硬件卡实现，有些主板集成ID硬件，有些需要购买独立的ID硬件卡，硬件RAID不需要占用其他硬件资源，稳定性和速度都比软件RAID要强

PS：实际生产环境中RAID通常是使用硬RAID方式。如果使用的是公用云，那么厂商会将该问题解决。
```

**05. RAID硬盘失效处理--热备和热插拔**

```
热备：HotSpare

定义：当冗余的RAID组中某个硬盘失效时，在不干扰当前RAID系统的正常使用的情况下，用RAID系统中另外一个正常的备用硬盘自动顶替失效硬盘，及时保证RAID系统的冗余性

全局式：备用硬盘为系统中所有的冗余RAID组共享

专用式：备用硬盘为系统中某一组冗余RAID组专用

热插拔：HotSwap

定义：在不影响系统正常运转的情况下，用正常的硬盘物理替换RAID系统中失效硬盘

关键在于热插拔时电子器件的保护机制
```

### 9. 磁盘阵列RAID实战

**01. 由于使用Linux操作系统模拟的软RAID，所以需要在虚拟机上添加多块硬盘。**

```
直接在虚拟机中添加即可。
```

**02. 同时我们还需要知道创建软RAID使用什么命令。mdadm，如果没有，直接yum进行安装即可**

```
[root@qls ~]# yum install mdadm -y
```

**03. mdadm磁盘阵列命令选项**

```
PS: 
1、我们做硬件RAID，是在装系统前还是之后？  
	前
2、raid5需要3块硬盘,那么使用4块硬盘，可以做raid5吗？
	可以  n-1/n

Linux内核中有一个md模块在底层管理RAID设备，它会在应用层给我们提供一个管理软RAID的应用工具mdadm

mdadm命令常见选项解释：

-A		#激活磁盘阵列
-C		#建立一个新阵列
-D		#打印阵列设备的信息
-G		#改变阵列大小或形态
-S		#停止阵列
-r		#移除设备
-l		#设定磁盘阵列的级别
-n		#指定阵列磁盘的数量
-x		#指定阵列中备用盘的数量
-f		#将设备状态定为故障
-a		#添加设备到阵列
-v	 	#显示详细信息软RAID：通过操作系统实现
```

**04. 实验环境：**

| Raid种类   | 磁盘                          | 热备盘  |
| :--------- | :---------------------------- | :------ |
| **Raid0**  | **sdb、sdc**                  |         |
| **Raid1**  | **sdd、sde**                  | **sdf** |
| **Raid5**  | **sdg、sdh、sdi**             | **sdj** |
| **Raid10** | **分区：sdk1,sdk2,sdk3.sdk4** |         |

```
[root@qls ~]# lsblk 
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sdb               8:16   0   20G  0 disk 
sdc               8:32   0   20G  0 disk 
sdd               8:48   0   20G  0 disk 
sde               8:64   0   20G  0 disk 
sdf               8:80   0   20G  0 disk 
sdg               8:96   0   20G  0 disk 
sdh               8:112  0   20G  0 disk 
sdi      		  8:128  0   20G  0 disk 
sdj      		  8:144  0   20G  0 disk 
sdk      		  8:160  0   20G  0 disk 
```

**RAID0**

**创建RAID0实验环境：**

| Raid种类  | 磁盘         | 热备盘 |
| :-------- | :----------- | :----- |
| **Raid0** | **sdb、sdc** |        |

```
1、创建Raid0
[root@qls ~]# mdadm  -C -v /dev/md0 -l 0 -n 2 /dev/sdb /dev/sdc

2、查看阵列信息
[root@qls ~]# mdadm -Ds		#找到丢失的信息。
[root@qls ~]# mdadm -D /dev/md0

3、生成配置文件
[root@qls ~]# mdadm -Ds > /etc/mdadm.conf

4、对创建的RAID0进行文件系统创建并挂载
[root@qls ~]# mkfs.xfs /dev/md0p1
[root@qls ~]# mkdir /raid0
[root@qls ~]# mount /dev/md0p1 /raid0/
[root@qls ~]#  df -Th /raid0/

5、开机自动挂载
[root@qls ~]#
```

**RAID1**

**创建RAID0实验环境：**

| Raid种类  | 磁盘         | 热备盘  |
| :-------- | :----------- | :------ |
| **Raid1** | **sdd、sde** | **sdf** |

```
1）创建RAID1，并添加1个热备盘
2）模拟磁盘故障，看备用盘是否会自动顶替故障盘
3）从raid1中移出故障盘

1、创建RAID1
[root@qls ~]# mdadm -C -v /dev/md1 -l 1 -n 2 -x 1 /dev/sd[d,e,f]
[root@qls ~]# mdadm  -Dsv > /etc/mdadm.conf

2、查看RAID阵列信息
[root@qls ~]# mdadm  -D /dev/md1

3、在RAID设备上创建文件系统
[root@qls ~]# mkfs.xfs /dev/md1
[root@qls ~]# mkdir /raid1
[root@qls ~]# mount /dev/md1  /raid1/
[root@qls ~]# cp /etc/passwd /raid1/

4、下面模拟RAID1中数据盘/dev/sde出现故障，观察/dev/sdf备用盘能否自动顶替故障盘
[root@qls ~]# mdadm  /dev/md1 -f  /dev/sde

5、查看一下阵列状态信息
[root@qls ~]# mdadm  -D /dev/md1
Number   Major   Minor   RaidDevice State
0       8       96        0   active sync   /dev/sdg
2       8      128        1   spare rebuilding   /dev/sdi 	#热备盘已经在同步数据
1       8      112        -    faulty   /dev/sdh  

6、查看数据是否丢失
[root@qls ~]# ls /raid1/    #数据正常，没有丢失

7、移除损坏的设备：
[root@qls ~]# mdadm  -r /dev/md1 /dev/sde

8、检查是否还存在热备盘
[root@qls ~]# mdadm  -D /dev/md1
Number   Major   Minor   RaidDevice State
 0       8       96        0      active sync   /dev/sdd
 2       8      128        1      active sync   /dev/sdf 

9、重新在添加一块新热备盘
[root@qls ~]# mdadm  -a /dev/md1  /dev/sde
```

**RAID5**

**创建RAID5实验环境：**

| Raid种类  | 磁盘              | 热备盘  |
| :-------- | :---------------- | :------ |
| **Raid5** | **sdg、sdh、sdi** | **sdj** |

```
1）使用三块盘创建RAID5, 使用-x添加1个热备盘
2）模拟损坏一块磁盘,然后备用盘自动顶上,只能顶一次
3）在模拟损坏,检测数据是否丢失,如果没有再次模拟损坏.


1、创建RAID5阵列,添加一块备用盘
[root@qls ~]# mdadm -C -v /dev/md5 -l 5 -n 3 -x 1 /dev/sd{g,h,i,j}

2、格式化RAID5,并挂载使用
[root@qls ~]# mkfs.xfs /dev/md5
[root@qls ~]# mkdir /raid5
[root@qls ~]# mount /dev/md5 /raid5/

3、检查RAID详情
[root@qls ~]# mdadm -D /dev/md5

4、模拟一块磁盘损坏
[root@qls ~]# mdadm -f /dev/md5 /dev/sdj

5 检查备用盘是否顶上
[root@qls ~]# mdadm -D /dev/md5 

6、在模拟一块磁盘损坏
[root@qls ~]# mdadm -f /dev/md5 /dev/sdh

......持续此步骤进行验证
```

**RAID10**

**创建RAID10实验环境：**

| Raid种类   | 磁盘                          | 热备盘 |
| :--------- | :---------------------------- | :----- |
| **Raid10** | **分区：sdk1,sdk2,sdk3.sdk4** |        |

```
1、分4个主分区，每个分区1G大小
[root@qls ~]# fdisk  /dev/sdk 

2、简单测试是否能成功创建RAID10
[root@qls ~]# mdadm -C -v /dev/md10 -l 10 -n 4 /dev/sdk[1-4]

3、查看RAID详情
[root@qls ~]# mdadm  -D /dev/md10

4、格式化并挂载
[root@qls ~]# mkfs.xfs  /dev/md10
[root@qls ~]# mkdir  /raid10
[root@qls ~]# mount /dev/md10 /raid10
[root@qls ~]# cp /etc/services  /raid10

5、模拟一块磁盘损坏
[root@qls ~]# mdadm  -f /dev/md10 /dev/sdk2
```

**总结**

```
PS：重启后RAID将全部失效。如果希望永久生效，请将RAID信息写入/etc/mdadm.conf配置文件，同时还需要将挂载信息写入/etc/fstab文件中。
如果想实现的同学们可自行百度。当然你肯定有疑惑为什么不讲，因为RAID生产根本不用，所以我们只是看看效果，如果你不想练习也anyway？但是RAID的概念面试的时候是必问，你的明白我意思？
```

### 10. 逻辑卷LVM概述

**01. 什么是LVM？**

```
	LVM是Logical Volume Manager （逻辑卷管理）的简写，它是Linux环境下对磁盘分区进行管理的一种机制。又译为逻辑卷宗管理器、逻辑扇区管理器、逻辑磁盘管理器，是Linux核心所提供的逻辑卷管理（Logical volume management）功能。它在硬盘的分区之上，又创建一个逻辑层，以方便系统管理硬盘分区系统。
  	最先由IBM开发，在AIX系统上实现，OS/2 操作系统与 HP-UX也支持这个功能。在1998年，Heinz Mauelshagen 根据在 HP-UX 上的逻辑卷管理器，写作出第一个 Linux 版本的逻辑卷管理器。
```

**02. LVM解决了什么问题**

```
	当我们刚开始安装Linux操作系统是遇到的一个常见的难以决定的问题就是如何正确地评估各分区大小，以分配合适的硬盘空间。
	普通的磁盘分区好之后就无法改变其大小，而遇到出现某个分区空间耗尽时，解决的方法可以是符号链接，或者调整分区大小的工具，但这只是暂时解决方法，没有从根本上解决问题。
	随着Linux的逻辑卷管理功能的出现，这些问题都迎刃而解，用户可以在无需停机的请款下可以方便的调整各个分区大小。
```

**03. LVM架构图**

![img](https://www.increase93.com/Picture/14-14.png)

**04. LVM基本概念**

```
物理卷(PV):（physical volume）,把常规的磁盘设备通过pvcreate命令对其进行初始化，形成了物理卷。其实就是硬盘或分区。（面粉）

卷组(VG):（volume group）,把多个物理卷组成一个逻辑的整体，这样卷组的大小就是多个硬盘之和。或者理解就是由一个或多个PV组成的整体。（面团）

逻辑卷(LV)：（logical volume），从卷组中划分需要的空间大小出来。用户仅需对其格式化然后即可挂载使用。从VG中切割出的空间用于创建文件系统。（切成馒头）

基本单元(PE)：(physical extend),分配的逻辑大小的最小单元，默认为4MB的基本块。（假设分配100MB逻辑空间，则需要创建25个PE）
```

### 11. 逻辑卷LVM实践

**01. 逻辑卷实践**

```
1）创建物理卷，将普通磁盘转换为物理卷。
2）创建卷组，将物理卷加入到卷组中。
3）在卷组中划分逻辑卷，然后挂载使用。
```

**1. 准备物理磁盘，如果是虚拟机需要关闭添加磁盘，以便更好的实验**

```
[root@qls ~]# lsblk 
sdb               8:16   0   20G  0 disk  
sdc               8:32   0   20G  0 disk
```

**2. 将磁盘转换为物理卷**

```
#下载安装软件包
[root@qls ~]# yum install -y lvm2

[root@qls ~]# pvcreate /dev/sdb
```

**3. 将/dev/sdb物理卷加入卷组(资源池)-->名字lvm_test**

```
[root@qls ~]# vgcreate lvm_test /dev/sdb

#检查卷组
[root@qls ~]# vgs
```

**4. 创建逻辑卷，分配名称，以及大小，指定卷组**

```
选项：
	-L	#逻辑卷大小
	-n	#逻辑卷名字
	
[root@qls ~]# lvcreate -L 100M -n lv1 lvm_test	##将lvm_test分配給lv1
```

**5. 格式化逻辑卷**

```
[root@qls ~]# mkfs.xfs /dev/lvm_test/lv1
```

**6. 挂载使用**

```
[root@qls ~]# mkdir /lv1

[root@qls ~]# mount /dev/lvm_test/lv1 /lv1/

[root@qls ~]# cp /etc/services  /lv1/
```

**02. 卷组管理，如何扩展卷组大小，如何删除卷组**

**1. 如何扩大VG卷组**

```
#1.新硬盘加入pv
[root@qls ~]# pvcreate /dev/sdc

#2.先检查当前的vg卷组大小
[root@oldboyedu ~]# vgs
  VG               #PV #LV #SN Attr   VSize    VFree
  lvm_test             1   1   0 wz--n- 1020.00m 920.00m

#3.使用vgextend扩展卷组
[root@qls ~]# vgextend lvm_test /dev/sdc
Volume group "lvm_test" successfully extended

#4.再次检查，发现卷组已经扩大
[root@oldboyedu ~]# vgs
VG               #PV #LV #SN Attr   VSize   VFree
lvm_test             2   1   0 wz--n-   1.99g 1.89g
  
```

**2. 假设想移除/dev/sdb磁盘，建议先将sdb磁盘数据先迁移到sdc磁盘，然后在移除。(同一卷组的磁盘才可以进行在线迁移)，(注意卷组的名称---)**

```
#1.检查当前逻辑卷VG中PV使用情况
[root@qls ~]# pvs
PV      VG   Fmt  Attr PSize PFree
/dev/sdb vg1 lvm2 a -- 2.00g 1.76g 
/dev/sdc vg1 lvm2 a -- 2.00g 2.00g

#2.pvmove在线数据迁移，将sdb的数据迁移至sdc 
[root@qls ~]# pvmove /dev/sdb
 /dev/sdb: Moved: 100.00%

#3.检查是否将sdb数据迁移至sdc 
[root@qls ~]# pvs
PV      VG   Fmt  Attr PSize PFree 
/dev/sdb vg1 lvm2 a -- 2.00g 2.00g 
/dev/sdc vg1 lvm2 a -- 2.00g 1.76g

#4.从卷组中移除sdb磁盘
[root@qls ~]# vgreduce lvm_test /dev/sdb
 Removed "/dev/sdb" from volume group "lvm_test"
  
```

**3. 逻辑卷管理，动态的调整逻辑卷大小**

**01. 逻辑卷的扩展，是取决于vg卷中是否还有剩余的容量，注意扩展逻辑卷不能超过卷组VG的总大小**

```
[root@qls ~]# vgs
VG               #PV #LV #SN Attr   VSize    VFree
datavg             1   1   0 wz--n- 1020.00m 920.00m

#1.扩展lv逻辑卷，增加800M分配给逻辑卷 {注意: 800M +800M 不一样}
[root@qls ~]# lvextend -L +800M /dev/lvm_test/lv1

#或者可以选择分配磁盘池中多少百分比给逻辑卷
[root@qls ~]# lvextend -l +50%FREE /dev/lvm_test/lv1

#2.扩展逻辑卷后需要更新fs文件系统
[root@qls ~]# xfs_growfs /dev/lvm_testvg/lv1    #xfs文件格式扩容
```

**02. 缩小逻辑卷，不要去操作，因为线上在使用的磁盘真的敢操作吗？**

**03. 删除逻辑卷，如果不想使用逻辑卷可以选择删除。**

```
[root@qls ~]# umount /dev/lvm_test/lv1

[root@qls ~]# lvremove /dev/lvm_test/lv1
Do you really want to remove active logical volume lvm_test/lv1? [y/n]: y
  Logical volume "lv1" successfully removed
```

> 注意：LVM如果有一个磁盘损坏,整个lvm都坏了， lvm只有动态扩展作用，底层用RAID + 上层LVM = 既有冗余又有动态扩展

### 12. 磁盘常见故障

**01. 磁盘block满故障**

```
#环境准备，先创建一个2G大小的分区

[root@qls ~]# lsblk  /dev/sdb1
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sdb1   8:17   0   2G  0 part /data

[root@qls ~]# df -h |grep sdb1
/dev/sdb1       2.0G   33M  2.0G   2% /data

#准备一个大文件，发现分区已经报错“No space left on device”

[root@qls ~]# dd if=/dev/zero of=/data/test.txt bs=100M count=180 
dd: error writing ‘/data/test.txt’: No space left on device
21+0 records in
20+0 records out
2101149696 bytes (2.1 GB) copied, 22.0136 s, 95.4 MB/s

#再次在该分区下面添加新文件，已经提示报错。

[root@qls ~]# cp Desktop.zip  /data/
cp: error writing ‘/data/Desktop.zip’: No space left on device
cp: failed to extend ‘/data/Desktop.zip’: No space left on device

#解决思路：先找到大小太大的目录。

[root@qls ~]# du -sh /* |grep G

#再依次向下查找，直到找到大文件为止。

[root@qls ~]# du -sh /data/* |grep  G
2.0G	/data/test.txt

#找到文件之后，首先要确认好之后再删除，不要直接的去删除

[root@qls ~]# rm -f /data/test.txt 
[root@qls ~]# df -h |grep sdb1
/dev/sdb1       2.0G   35M  2.0G   2% /data
```

> 注意：如果该文件存在硬链接，一定要把硬链接文件删除。

![img](https://www.increase93.com/Picture/yinglianjie.png)

**02. 磁盘inode满故障**

```
#环境准备，创建一个30M的分区。

[root@qls ~]# df -h |grep sdb1
dev/sdb1        27M  1.6M   26M   6% /test
[root@qls ~]# lsblk |grep sdb1
└─sdb1   8:17   0   30M  0 part /test

#批量创建大量的小文件

[root@qls ~]# touch /test/data/file/{1..15360}.txt

#再去到该分区中创建新的文件就会报错。

[root@qls ~]# touch /test/test.txt
touch: cannot touch ‘/test/test.txt’: No space left on device

#解决思路：查看磁盘分区使用情况，发现磁盘空间是正常的，再次查看inode，发现inode满了。

[root@qls ~]# df -h |grep sdb1
/dev/sdb1        27M  9.7M   17M  37% /test
[root@qls ~]# df -i |grep sdb1
/dev/sdb1         15424 15424        0  100% /test

#怎样找出大量的小文件呢？首先要找出大目录（小文件过多，会导致目录变大。）

[root@qls ~]# find /test  -type d -size +300k |xargs ls -lhd

#找到大量小文件之后，首先要确认好之后再删除，不要直接的去删除

[root@qls ~]# find /test/data/file/ -type f -delete

#检查结果

[root@qls ~]# df -i |grep sdb1
/dev/sdb1         15424     5    15419    1% /test
```

 