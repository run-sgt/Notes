# 第12章 Linux系统管理-打包与压缩

[第12章 Linux系统管理-打包与压缩](https://www.increase93.com/base-12.html#header-n0)[1. 压缩包概述](https://www.increase93.com/base-12.html#header-n3)[2. Zip打包压缩](https://www.increase93.com/base-12.html#header-n30)[3. Gzip打包压缩](https://www.increase93.com/base-12.html#header-n33)[4. Tar打包压缩](https://www.increase93.com/base-12.html#header-n36)[5. Linux打包与压缩习题](https://www.increase93.com/base-12.html#header-n44)[6. 显示时间命令](https://www.increase93.com/base-12.html#header-n46)[7. 如何让系统自动同步时间](https://www.increase93.com/base-12.html#header-n50)

### 1. 压缩包概述

**1. 什么是压缩包**



```

```









```

    就是将多个文件和目录合并为一个特殊的文件

```



**2. 为什么使用压缩包**



```

```









```

1.减少占用的体积

2.加快网络的传输
    
```



**3. Windows的压缩和Linux的有什么不同**



```

```









```

windows:    zip  rar(linux不支持)

linux:      zip tar.gz tar.bz2  .gz

如果希望windows的软件能被linux解压,或者linux的软件包被windows能识别,选择zip.

PS: 压缩包的后缀不重要,但一定要携带.

```



**4. Linux下常见的压缩包类型**

| 格式         | 压缩工具                                                     |
| :----------- | :----------------------------------------------------------- |
| **.zip**     | **zip压缩工具**                                              |
| **.gz**      | **gzip压缩工具，只能压缩文件，会删除源文件（通常配合tar使用）** |
| **.bz2**     | **bzip2压缩工具，只能压缩文件，会删除源文件（通常配合tar使用）** |
| **.tar.gz**  | **先使用tar命令归档打包，然后使用gzip压缩**                  |
| **.tar.bz2** | **先使用tar命令归档打包，然后使用bzip压缩**                  |

### 2. Zip打包压缩



```

```









```

使用zip命令可以对文件进行压缩打包，解压则需要使用unzip命令

```





```

```









```

#默认情况下，没有zip和unzip工具，需要进行安装

[root@qls ~]# yum install zip unzip -y
    
#压缩文件为zip包(并不会删除源文件)
                               
[root@qls ~]# zip   filename.zip    filename
                                
#压缩目录为zip包(压缩时需要添加-r参数,递归的方式)
    
[root@qls ~]# zip  -r  dir.zip    dir/
    
#解压zip文件包(不要覆盖掉原来的文件,尽可能找一个空的目录解压,然后再mv移动过去)

[root@qls ~]# unzip filename.zip

#测试linux的zip是否能与windows的zip互通    ---->可以

#zip命令常用选项

-q      #不显示指令执行过程；

-r      #递归处理，将指定目录下的所有文件和子目录一并处理；

-T      #检查压缩包是否完整；

#unzip命令的选项

-l      #显示压缩文件内所包含的文件；

-t      #检查压缩文件是否正确

-q      #执行时不显示任何信息；

-d      #指定文件解压缩后所要存储的目录；

```



### 3. Gzip打包压缩



```

```









```

gzip是在Linux系统中经常使用的一个对文件进行压缩和解压缩的命令，既方便又好用。

```





```

```









```

#如果系统中没有该命令，请进行下载安装

[root@qls ~]# yum install gzip -y

#对文件进行压缩

[root@qls ~]# gzip file     

#查看gz压缩后的文件

[root@qls ~]# zcat file.gz  

#解压gzip的压缩包

[root@qls ~]# gzip -d file.gz   

#对目录进行压缩

[root@qls ~]# gzip -r dir/

#解压目录

[root@qls ~]# gzip -rd dir/

#选项

-d      #解开压缩文件；

-r      #递归处理，将指定目录下的所有文件及子目录一并处理；

```



### 4. Tar打包压缩



```

```









```

tar是linux下最常用的压缩与解压缩，支持文件和目录的压缩归档

```





```

```









```

#语法

tar [OPTION...] [FILE]...

#选项

z           #使用gzip压缩归档后的文件（.tar.gz）

j           #使用bzip2压缩归档后的文件（.tar.bz2）

J           #使用xz压缩归档后的文件（.tar.xz）

c           #表示创建压缩包

v           #表示显示创建压缩包和解压的过程

f           #表示指定压缩包（f一定要放在这几个参数的最后）

t           #查看压缩包内容

x           #解压

C           #指定解压目录位置

X           #排除多个文件（写入需要排除的文件名称）

P           #使用绝对路径打包

h           #打包软连接

--exclude   #排除不需要打包的文件

#常用打包与压缩组合

czf         #打包tar.gz格式

cjf         #打包tar.bz2格式

cJf         #打包tar.xz格式

xf          #自动选择解压模式

tf          #查看压缩包内容

```





```

```









```

#如何打包,将所有的文件,和目录打成一个包，tar命令进行归档,gzip进行压缩，tar.gz

[root@qls /]# tar czf tt.tar.gz var/ dir1/ test.log     

#扩展了解项:

#打包为bz2格式的压缩包

[root@qls /]# tar cjf tt.tar.bz2 var/ dir1/ test.log   
    
#打包为xz格式的压缩包

[root@qls ~]# tar cJf tt.tar.xz var/ dir1/ test.log   

#查看压缩包的内容

[root@qls ~]# tar tf tt.tar.gz                          
    
#解压软件包，解压tar.gz tar.bz2 tar.xz

[root@qls ~]# tar xf tt.tar.gz                          

#解压默认是解压到当前目录下

[root@qls ~]# tar xf tt.tar.gz -C /tmp/             

#打包时的路径选择: 相对路径  绝对路径

[root@qls ~]# tar czf sys.tar.gz /etc/ /var/
tar: Removing leading `/' from member names
tar: Removing leading `/' from hard link targets

#问题: 为什么有一个删根的操作:

        防止在解压的过程中,直接覆盖原有的目录以及文件
        
#推荐的打包方式:

#将etc目录 var目录 统一打包存放到/root/目录下的 etc.tar.gz压缩包中

[root@qls ~]#  cd /

[root@qls /]# tar czf root/etc.tar.gz   etc/ var/

#如何打包时排除文件: (备份不需要使用排除,)

#1.排除单个文件

[root@qls /]# tar czf etc.tar.gz --exclude=etc/services etc/

#2.排除多个文件

[root@qls /]# tar czf etc.tar.gz --exclude=etc/services --exclude=etc/rc.local etc/

[root@qls /]# tar czf etc.tar.gz --exclude=etc/{passwd,shadow,gshadow} etc/ 

#3.将需要排除的文件写入文件中

[root@qls /]# cat paichu.list       #必须写相对路径
etc/services
etc/rc.local
etc/rc.d/rc.local

#通过-X指定需要排除的文件列表, 最后进行打包压缩

[root@qls /]# tar czfX etc.tar.gz paichu.list etc/

#如何打包时保留软连接文件

#打包链接文件,打包链接文件的真实文件

[root@qls ~]# cd /

[root@qls /]# tar czfh local.tar.gz  etc/rc.local

```



**Find与Tar打包的结合方式**



```

```









```

#打包/tmp下所有文件

[root@qls ~]# cd /

[root@qls /]# find tmp/ -type f | xargs tar czf tmp.tar.gz

#打包/tmp下所有文件

[root@qls /]# tar czf tmp.tar.gz $(find /tmp/ -type f)

#统计文件的大小

[root@qls /]# du -sh tmp.tar.gz
    
#经统计结果对比: 压缩前的文件或目录假设占用了6MB,压缩后可能就占用1MB.

```



**Find与Du打包的结合方式**



```

```









```

#扩展: 统计/下面的目录的大小,只统计1级

[root@qls ~]# find / -maxdepth 1 -type d |sed -r 's#(.*)# du -sh \1 #g'|bash

[root@qls ~]# ls -d /* |xargs du -sh

[root@qls ~]# du -sh /*

```



### 5. Linux打包与压缩习题



```

```









```

1.列出linux常用打包工具并写相应的压缩、解压缩参数

gzip: 只能压缩单个文件
    1.gzip  filename            #打包|压缩
    2.gzip -d filename.gz       #解压
    3.zcat  filename.gz         #查看压缩包中的内容

zip: 支持压缩多个目录和文件--->希望能与windows进行互通
    1.zip   -r   filename.zip    filename
      命令  递归    选项             参数
      搬家  多个    箱子             物品

    2. unzip  filename.zip     #解包
      
tar: 能够支持单个文件和多个文件,并也能与windows进行互通. 因为很多开源的项目提供的压缩包就是tar.gz
    tar czf  filname.tar.gz   filename directory    #打包
    tar xf   filname.tar.gz                         #解压
    tar xf   filname.tar.gz -C /tmp                 #解压到指定解压的目标位置
    tar tf   filename.tar.gz                        #查看压缩包内容
    -----------------------------------
    tar cjf  filename.tar.bz2                       #打包格式为bz2
    tar cJf  filename.tar.xz                        #打包格式为xz

2.如何用gzip命令对文件进行压缩、解压缩，给出实践步骤。

    1.gzip  filename            #打包|压缩
    2.gzip -d filename.gz       #解压

3.如何用zip命令对文件以及目录进行压缩、解压缩，给出实践步骤。
    1.zip   -r   filename.zip    filename
      命令  递归    选项             参数
      搬家  多个    箱子             物品

    2. unzip  filename.zip     #解包

4.创建一个自己名字的文件至/opt目录，给出实践步骤。

[root@qls ~]# touch /opt/ttt
        
5.打包opt/整个目录，并命名为opt_2019_08_19.tar.gz，给出实践步骤。

#写死

[root@qls ~]# tar czf opt_2019-08-19.tar.gz /opt/

[root@qls ~]# date +%F
2019-08-19

[root@qls ~]# tar czf opt_$(date +%F).tar.gz /opt/

[root@qls ~]# ls
opt_2019-08-19.tar.gz 

[root@qls ~]# date -s "2019/08/16"

[root@qls ~]# tar czf opt_$(date +%F).tar.gz /opt/
opt_2019-08-16.tar.gz

6.查看打包好的opt_2019_08_19.tar.gz里的文件，给出实践步骤。

[root@qls ~]# tar tf opt_2019-08-19.tar.gz 

7.将打包好的opt_2019_08_19.tar.gz内容指定解压至/tmp目录，并给出实践步骤。

[root@qls ~]# tar xf opt_2019-08-19.tar.gz  -C /tmp/

8.打包以当前主机名+ip的命名方式的压缩包: 比如: www.oldboyedu.com_2019-08-19_10.0.0.99.tar.gz ,压缩/opt目录即可

    1.提取主机名              hostname
    2.提取IP地址             ifconfig ens32|awk '/inet / {print $2}'
    3.提取当前时间            date +%F
    
[root@qls ~]# tar czf $(hostname)_$(ifconfig eth0|awk '/inet / {print $2}')_$(date +%F).tar.gz   /opt 

```



### 6. 显示时间命令



```

```









```

很多shell脚本里面需要打印不同格式的时间或日期，以及要根据时间和日期执行操作。

date命令是显示或设置系统时间与日期。

```





```

```









```

#选项

-d      #根据你的描述显示日期

-s      #修改日期

%H      #小时，24小时制（00~23）

%M      #分钟（00~59）

%s      #从1970年1月1日00:00:00到目前经历的秒数

%S      #显示秒（00~59）

%T      #显示时间，24小时制（hh:mm:ss）

%d      #一个月的第几天（01~31）

%j      #一年的第几天（001~366）

%m      #月份（01~12）

%w      #一个星期的第几天（0代表星期天）

%W      #一年的第几个星期（00~53，星期一为第一天）

%y      #年的最后两个数字（1999则是99）

%Y      #年，实际

%F      #显示日期（%Y-%m-%d）

```





```

```









```

#date命令示例：

[root@qls ~]# date
Sat May 18 21:33:24 CST 2019

[root@qls ~]# date +%F
2019-05-18

[root@qls ~]# date +%T
21:33:33

[root@qls ~]# date +%y-%m-%d
19-05-18

[root@qls ~]# date +%w
6

[root@qls ~]# date +%s
1558186507

[root@qls ~]# date +%d
18

[root@qls ~]# date +%W
19

[root@qls ~]# date +%j
138

[root@qls ~]# date -d "-1 day" +%F
2019-05-17

[root@qls ~]# date -d "+1 day" +%F
2019-05-19

[root@qls ~]# date -d "1 d" +%F
2019-05-18

[root@qls ~]# date -d "+1 year" +%F
2020-05-18

[root@qls ~]# date -d "11 year" +%F
2030-05-18

[root@qls ~]# date -d "+1 year" +%F
2020-05-18

[root@qls ~]# date -d "1 year" +%F
2020-05-18

[root@qls ~]# date -s 20201231
Thu Dec 31 00:00:00 CST 2020

[root@qls ~]# date +%F
2020-12-31

[root@qls ~]# date -s 11
Sat May 18 11:00:00 CST 2019

[root@qls ~]# date +%T
11:00:15

[root@qls ~]# date -s 11:30:30
Sat May 18 11:30:30 CST 2019

[root@qls ~]# date +%T
11:30:31

[root@qls ~]# date -s "20201231 12:00:00"
Thu Dec 31 12:00:00 CST 2020

[root@qls ~]# date +%F
2020-12-31

[root@qls ~]# date +%T
12:00:12

```



### 7. 如何让系统自动同步时间



```

```









```

ntpdate命令是用来设置本地日期和时间。

PS1：必须有 root 权限才能在主机上运行这个命令。

PS2：如果NTP服务器守护程序在主机上运行，命令将拒绝ntpdate设置日期。

```





```

```





```

#系统中默认没有该命令，需要自行下载安装

[root@qls ~]# yum install ntpdate -y 

#常用可进行同步时间的NTP服务器

ntp.aliyun.com

ntp1.aliyun.com......ntp7.aliyun.com

#同步时间

[root@qls ~]# ntpdate ntp.aliyun.com
18 May 21:49:06 ntpdate[36642]: adjust time server 203.107.6.88 offset -0.003359 sec

[root@qls ~]# date +%F_%T
2019-08-19_21:49:27
```





```

#让系统自动同步时间，只需添加一条定时任务即可。

```



 