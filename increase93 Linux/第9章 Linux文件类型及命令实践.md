## 第9章 Linux文件类型及命令实践

2019-07-15 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(90) 评论(0)

1. **Linux文件属性概述**

-rw-------. 1 root root 4434 May 30 13:58 ks.cfg

-rw-------. ①:文件类型与权限

1 ②:硬链接次数

root ③:所属用户

root ④:所属组

4434 ⑤:文件大小

May30 13:58 ⑥⑦⑧:最新修改的时间与日期

ks.cfg ⑨:文件或目录名称

![img](D:\Notes\md_img\071519_0710_9Linux1.png)

高清图片地址:

https://dwz.cn/7IrfgpR1 #区分大小写

1. ## Linux文件类型及文件扩展名

   1. ### 文件类型与扩展名介绍

**Linux下扩展名只是方便用户记忆，对文件类型不影响**

**.txt #文本文件**

**.log #日志文件**

**.conf .cfg .configure .xml #配置文件**

**.sh .bash #shell脚本**

**.py #python脚本**

1. ### Linux中的文件类型

**ls -l 文件名 //看第一个字符**

**格式 说明**

**-         普通文件(文本, 二进制, 压缩, 图片, 日志等)**

**d         目录文件**

**b         设备文件(块设备)存储设备硬盘 /dev/sda1, /dev/sda2**

**c             设备文件(字符设备)打印机，终端 /dev/tty1, /dev/zero**

**s             套接字文件, 进程间通信(socket)**

**p         管道文件**

**l             链接文件**

1. ### 文件及目录查找命令

   1. #### file 显示文件的类型

**file [文件] #查看普通文件类型**

**[root@oldboy ~]# file /bin/yum**

**/bin/yum: Python script, ASCII text executable**

1. #### which 显示命令的全路径

**which [命令]**

1. #### whereis 显示命令及其相关文件全路径

**whereis [命令] #查看命令及其相关内容**

**[root@oldboyedu ~]# whereis ls**

**ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz**



**注意：type -a 也是直接可以的。**

1. #### locate：快速定位文件路径(不推荐)

**快速定位文件路径**

**yum install mlocate -y #建立表格数据库，方便查找**

1. #### updatedb(不推荐)

**更新mlocate数据库**

1. ### find 查找目录下的文件

**作用：查找目录下文件**

**find /oldboy –maxdepth 1 –type f –name "\*.txt" -size +1M**

**-maxdepth #最大查找多少层，一定放在最前面，1是该目录下一层**

**-type #类型 f d l**

**-iname #名字 (忽略大小写)**

**-name #名字** 

**-size +1M(大于1M）-100k(小于100k ) #根据大小查找文件** 

**-mtime +7(7天以前) -7(7天以内) #根据时间查找文件**

**示例：**

**find /root/ -type f -iname "\*.txt" |xargs cp -t /tmp/**

**cp $(find /root/ -type f -iname "\*.txt") /tmp/**

**find /root/ -type f -iname "\*.txt" -exec cp {} /tmp/ \;**

**find /root/ -type f -iname "\*.txt" | xargs -i cp {} /tmp/**

1. ### xargs 将标准输入转换成命令行参数

**seq 10 >ett.txt**

**xargs -n5 <ett.txt**

**-n 分组**

**主要配合find命令使用**

1. ## 压缩包管理与时间命令

   1. ### tar 打包压缩命令

      1. #### 创建压缩包

**[root@oldboy ~]# tar zcvf /tmp/etc.tar.gz /etc/**

**把/etc/打包压缩 压缩包放在/tmp/etc.tar.gz**

**tar 参数 zcvf**

**z 表示通过gzip软件进行压缩**

**c 表示创建压缩包**

**v 表示显示创建压缩包和解压的过程**

**f 表示指定压缩包（f一定要放在这几个参数的最后）**

1. #### 查看压缩包

**t 查看压缩包内容**

**tar tf /tmp/etc.tar.gz**

1. #### 解压(默认是当前目录)

**x 表示解压**

**解压时进入到tmp 下 cd/tmp**

**tar xf /tmp/etc.tar.gz**

1. #### 解压到指定位置

**/tmp/etc.tar.gz 解压到 /opt**

**tar xf /tmp/etc.tar.gz -C /opt/**



**排除 --exclude**

1. #### tar命令打包压缩提示: Removing leading `/' from member names背后原因分析

**在tar命令使用绝对路径打包压缩的时候，提示下面内容:**

**tar: Removing leading `/' from member names**

**删除 开头的 / 文件的名字中**

**tar打包压缩的时候 会删除开头的/**

**绝对路径自动换成相对路径**

**压缩包里面存放的是绝对路径 解压的时候覆盖源文件**

**为了避免这个悲剧：**

​     **tar命令打包压缩的时候 默认 绝对路径---->相对路径（安全）**

**tar: Removing leading `/' from member names**

1. ### zip 打包压缩命令

   1. #### 压缩文件

**zip test.zip test.log**

1. #### 压缩目录

**zip -r test.zip test**

**-q #静默模式**

1. #### 解压

**unzip test.zip**

1. #### 解压到指定目录

**unzip -d /opt scripts.zip**

1. #### 解压的其他参数

**-l 只看一下zip压缩包中包含哪些文件，不进行解压缩**

**-v 查看显示的文件列表还包含压缩比率**

**-t 检查zip文件是否损坏**

1. ### date 显示与设置系统时间

date 显示或设置系统时间

显示日期以2018-09-30 显示

[root@oldboyedu /etc]# date +%F

2018-09-30

[root@oldboyedu /etc]# date +%Y-%m-%d

2018-09-30

显示日期，不显示世纪

[root@oldboyedu /etc]# date +%y-%m-%d

18-09-30

显示时间以10:30:12 显示

[root@oldboyedu /etc]# date +%T

10:30:12

[root@oldboyedu /etc]# date +%H:%M:%S

10:31:23

1. #### date 以指定格式显示日期

**date +格式**

**#%Y year**

**#%m month 月**

**#%d day 日期**

**#%H hour 小时**

**#%M minute 分钟**

**#%S sec 秒**

**#+%F === %Y-%m-%d 2018-09-30**

**#+%T === %H:%M:%S**

**#+%w 周几（0周日)**

**date 参数**

**-d         根据你的描述显示日期**

**-s     修改日期**

**[root@oldboyedu /etc]# date +%F**

**2018-09-30**

**[root@oldboyedu /etc]# date -d "-1 day"**

**Sat Sep 29 10:45:09 CST 2018**

**[root@oldboyedu /etc]# date -d "1 day" +%F**

**2018-10-01**

**[root@oldboyedu /etc]# date -s "20180930 10:54:00"**

**Sun Sep 30 10:54:00 CST 2018**

1. #### 如何让linux自动同步网络时间 :ntpdate

**yum install ntpdate -y**

**常用的网络时间服务器**

**ntp1.aliyun.com**

**[root@oldboyedu /etc]# ntpdate ntp1.aliyun.com**

**30 Sep 11:03:36 ntpdate[2989]: step time server 120.25.115.20 offset 346.461947 sec**

**[root@oldboyedu /etc]# date**

**Sun Sep 30 11:03:52 CST 2018**

**设置定时任务，每5分钟同步一次系统时间**

1. ### 本章知识点总结

- **Linux文件属性概述**
- **Linux文件类型及文件扩展名**
- **文件及目录查找命令**
- **tar打包压缩命令**
- **zip/unzip打包压缩与解压**
- **date显示与设置系统时间**