# 第17章 Linux系统管理-定时任务

### 1. 定时任务基本概述

**01. 什么是定时任务？**

```

    设定某个日期或时间周期性执行指令。比如设定一个闹铃，叫你每天早上7点钟起床等，这就是一个定时任务。

```

**02. 什么是crond？**

```

    crond是linux系统中用来定期执行命令或脚本的一种服务软件，一般情况下，我们安装完centos操作系统之后，
    
默认便会启动Crond任务调度服务。

    crond服务会定期（默认每分钟检查一次）检查系统中是否有要执行的任务工作，如果有，便会根据其预先设定的定
    
时任务规则自动执行该定时任务工作，这个crond定时任务服务就相当于我们平时早起使用的闹钟一样。

```

**03. 为什么要使用定时任务？**

```

    我们举个例子说明：例如，我们的数据库或者代码程序需要每天晚上0点做一次备份，这样每天夜里都需要执行的周期性工作，如果要让人操作的话，就得每天半夜爬起来，然后登录系统执行任务，执行完接着睡觉。另外，执行任务的过程也可能持续几个小时，这样一来，一个晚上我们就都不用睡觉了，这样显然是不行的。那么有什么办法来解决这个周期性的执行任务需求呢？

    这就是linux系统的定时任务crond，这相当于我们平时生活中的闹钟功能，每天晚上提前设置定时，早晨按时叫醒你。

```

**04. 定时任务主要分为以下两种使用情况。**

```

1.系统级别的定时任务： 临时文件清理、系统信息采集、日志文件切割

2.用户级别的定时任务： 定时向互联网同步时间、定时备份系统配置文件、定时备份数据库的数据

```

### 2. 定时任务管理

**01. 定时任务相关文件和目录**

```

/etc/cron.hourly/      #系统定时任务每个小时运行这个目录里的内容

/etc/cron.daily/       #系统定时任务每天运行这个目录里的内容

/etc/cron.weekly/      #系统定时任务每周运行这个目录里的内容

/etc/cron.monthly/     #系统定时任务每月运行这个目录里的内容

/etc/cron.deny         #定时任务的黑名单

/etc/crontab           #定时任务的配置文件之一

```

**02. 用户定时任务相关**

```

/var/spool/cron/root     #（root是用户名 root用户的定时任务）

/var/log/cron            #定时任务的日志文件，运行过程的一个记录

#定时任务是使用crontab命令来配置的

-e          #编辑当前用户的定时任务，相当于vi /var/spool/cron/root

-l          #显示当前用户的定时任务，相当于cat /var/spool/cron/root

-r          #删除当前的crontab文件

-u          #指定用户执行定时任务，默认是root用户。

#为何使用crontab命令  

1.有语法检查功能

2.方便快捷

```

**03. 定时任务的配置文件说明**

```

SHELL=/bin/bash                     #执行的命令的方式
PATH=/sbin:/bin:/usr/sbin:/usr/bin  #能搜索命令的环境变量的路径
MAILTO=root                         #邮件的接收人

# For details see man 4 crontabs
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

分    时  日   月  周
*     *    *    *   *  root ntpdate ntp1.aliyun.com

crond是按照分钟进行计算的,不支持秒

# *  表示任意的(分、时、日、月、周)时间都执行

# -  表示一个时间范围段, 如5-7点

# ,  表示分隔时段, 如6,0,4表示周六、日、四

# /1 表示每隔n单位时间, 如*/10 每10分钟

```

**04. 了解定时任务crontab的编写规范。**

```

00 02 * * *        #每天的凌晨2点整执行

00 02 1 * *        #每月的1日的凌晨2点整执行

00 02 14 2 *       #每年的2月14日凌晨2点执行

00 02 * * 7        #每周天的凌晨2点整执行

00 02 * 6 5        #每年的6月周五凌晨2点执行

00 02 14 * 7       #每月14日或每周日的凌晨2点都执行

00 02 14 2 7       #每年的2月14日或每年2月的周天的凌晨2点执行  

*/10  02 * * *     #每天凌晨2点，每隔10分钟执行一次

* * * * *          #每分钟都执行

00 00 14 2 *       #每年2月14日的凌晨执行命令 

*/5 * * * *        #每隔5分钟执行一次

00 02 * 1,5,8 *    #每年的1月5月8月凌晨2点执行

00 02 1-8 * *      #每月1号到8号凌晨2点执行

00 21 * * *        #每天晚上21:00执行

45 4 1,10,22 * *   #每月1、10、22日的4:45执行

45 4 1-10 * *      #每月1到10日的4:45执行

3,15 8-11 */2 * *  #每隔两天的上午8点到11点的第3和第15分钟执行

0 23-7/2 * * *     #晚上11点到早上7点之间，每隔一小时执行

15 21 * * 1-5      #周一到周五每天晚上21:15执行

```

### 3. 定时任务实战

**01. 使用root用户每分钟执行一次时间同步。**

```

#如何时间同步

#时间同步需要ntpdate命令，安装系统时，默认没有此命令，需手动下载安装。

[root@qls ~]# yum install ntpdate -y

#同步时间

[root@qls ~]# ntpdate  ntp.aliyun.com
15 Jun 11:48:25 ntpdate[16494]: adjust time server 203.107.6.88 offset 0.000806 sec

#编写定时任务

[root@qls ~]# crontab  -l
* * * * * ntpdate  ntp.aliyun.com

#查看定时任务日志。

[root@qls ~]# tailf  /var/log/cron
Jun 15 12:10:01 qiudao CROND[7321]: (root) CMD (ntpdate  ntp.aliyun.com)
Jun 15 12:10:01 qiudao CROND[7322]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jun 15 12:11:01 qiudao CROND[7339]: (root) CMD (ntpdate  ntp.aliyun.com)

#修改系统时间，查看时间是否同步更新

[root@qls ~]# date -s  12:00
Sat Jun 15 12:00:00 CST 2019
[root@qls ~]# date
Sat Jun 15 12:00:03 CST 2019
[root@qls ~]# date          #时间并没有同步，因为什么呢？
Sat Jun 15 12:01:53 CST 2019

#查看系统发送的邮件信息

[root@qls ~]# tailf /var/spool/mail/root
X-Cron-Env: <SHELL=/bin/sh>
X-Cron-Env: <HOME=/root>
X-Cron-Env: <PATH=/usr/bin:/bin>
X-Cron-Env: <LOGNAME=root>
X-Cron-Env: <USER=root>
Message-Id: <20190615040401.4F2FE2008209@qiudao.localdomain>
Date: Sat, 15 Jun 2019 12:04:01 +0800 (CST)

/bin/sh: ntpdate: command not found         #报错，说命令找不到。

#重新编写定时任务
[root@qls ~]# crontab  -l
* * * * * /usr/sbin/ntpdate  ntp.aliyun.com

#再次查看时间的时候，时间已经同步了，但是系统还是在发送邮件。

[root@qls ~]# date
Sat Jun 15 12:44:41 CST 2019
You have new mail in /var/spool/mail/root

#把邮件服务停掉

[root@qls ~]# systemctl  stop postfix.service 

#查看邮件内容，发现已经不在接收邮件信息了。
[root@qls ~]# tailf /var/spool/mail/root
X-Cron-Env: <SHELL=/bin/sh>
X-Cron-Env: <HOME=/root>
X-Cron-Env: <PATH=/usr/bin:/bin>
X-Cron-Env: <LOGNAME=root>
X-Cron-Env: <USER=root>
Message-Id: <20190615044607.F26322008209@qiudao.localdomain>
Date: Sat, 15 Jun 2019 12:46:07 +0800 (CST)

15 Jun 12:46:07 ntpdate[7832]: adjust time server 203.107.6.88 offset 0.002100 sec

#邮件服务停止之后，在/var/spool/postfix/maildrop/目录下，会生成很多的小文件，定时任务执行一次，目录就会生成一个文件，日积月累的话，磁盘的inode会被沾满，导致磁盘故障。

[root@qls ~]# ll /var/spool/postfix/maildrop/
total 16
-rwxr--r-- 1 root postdrop 603 2019-06-15 12:47 25191402ED40
-rwxr--r-- 1 root postdrop 602 2019-06-15 12:48 523DE402ED41
-rwxr--r-- 1 root postdrop 603 2019-06-15 12:49 7BAC0402ED43
-rwxr--r-- 1 root postdrop 602 2019-06-15 12:50 A44E9402ED44

#重新编写定时任务

[root@qls ~]# crontab  -l
* * * * * /usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null

#再次查看/var/spool/postfix/maildrop/，发现小文件已经不在生成了。

[root@qls ~]# ll /var/spool/postfix/maildrop/
total 28
-rwxr--r-- 1 root postdrop 600 2019-06-15 12:52 01254402ED46
-rwxr--r-- 1 root postdrop 603 2019-06-15 12:47 25191402ED40
-rwxr--r-- 1 root postdrop 602 2019-06-15 12:53 29C3F402ED47
-rwxr--r-- 1 root postdrop 602 2019-06-15 12:48 523DE402ED41
-rwxr--r-- 1 root postdrop 603 2019-06-15 12:49 7BAC0402ED43
-rwxr--r-- 1 root postdrop 602 2019-06-15 12:50 A44E9402ED44
-rwxr--r-- 1 root postdrop 603 2019-06-15 12:51 CD9CE402ED45

#开启邮件服务，查看系统是否还会发送邮件信息吗

[root@qls ~]# systemctl  start  postfix.service 

[root@qls ~]# tailf /var/spool/mail/root
X-Cron-Env: <SHELL=/bin/sh>
X-Cron-Env: <HOME=/root>
X-Cron-Env: <PATH=/usr/bin:/bin>
X-Cron-Env: <LOGNAME=root>
X-Cron-Env: <USER=root>
Message-Id: <20190615045646.1055B200820D@qiudao.localdomain>
Date: Sat, 15 Jun 2019 12:48:07 +0800 (CST)

15 Jun 12:48:07 ntpdate[7867]: adjust time server 203.107.6.88 offset 0.005083 sec

#系统已经不在会发送邮件信息了。

总结：

1.编写定时任务时，先在命令行上面执行一次，查看是否可以执行成功。

2.在定时任务中，请使用绝对路径书写命令，不然的话，定时任务有的命令找不到执行路径。

3.把定时任务执行的结果定向到空，如果不定向到空的话，邮件服务开启时，系统会一直发送邮件信息，邮件服务关闭时，系统会大量生成小文件。

```

**02. 每分钟把系统时间追加到一个文件中，以2019-06-15_13:54:10格式**

```

#在命令行上面执行时间，按照要求打印出来

[root@qls ~]# date +%F_%T
2019-06-15_13:54:10

#追加到一个文件中

[root@qls ~]# date +%F_%T >>/root/time.txt

[root@qls ~]# cat time.txt
2019-06-15_13:56:33

#编写定时任务
[root@qls ~]# crontab  -l
* * * * * /usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null
* * * * * /usr/bin/date +%F_%T >> /root/time.txt 

#查看日志
[root@qls ~]# tailf /var/log/cron
Jun 15 14:00:01 qiudao CROND[8337]: (root) CMD (/usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null)
Jun 15 14:00:01 qiudao CROND[8338]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jun 15 14:00:01 qiudao CROND[8339]: (root) CMD (/usr/bin/date +)
Jun 15 14:01:01 qiudao CROND[8362]: (root) CMD (run-parts /etc/cron.hourly)
Jun 15 14:01:01 qiudao CROND[8363]: (root) CMD (/usr/bin/date +)

#查看被追加的文件，发现定时任务并没有执行成功，这是因为什么呢？

[root@qls ~]# cat time.txt 
2019-06-15_13:56:33

#修改定时任务

[root@qls ~]# crontab  -l
* * * * * /usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null
* * * * * /usr/bin/date +\%F_\%T >> /root/time.txt 

#再次查看日志和执行结果

[root@qls ~]# tailf  /var/log/cron
Jun 15 14:10:22 qiudao crontab[8517]: (root) LIST (root)
Jun 15 14:11:01 qiudao crond[6110]: (root) RELOAD (/var/spool/cron/root)
Jun 15 14:11:01 qiudao CROND[8520]: (root) CMD (/usr/bin/date +%F_%T >> /root/time.txt )

[root@qls ~]# tailf time.txt 
2019-06-15_13:56:33
2019-06-15_14:11:01
2019-06-15_14:12:01
2019-06-15_14:13:01

总结：

1.定时任务中，有些特殊符号会被误解，需转义。

```

**03. 每天凌晨3点做一次备份？将/etc/目录进行打包,备份到/backup下面，且只能保留最近三天的数据。**

```

思路：

1) 将备份所涉及到的命令写入到一个脚本文件中.

2) 每天备份文件名要求格式: hostname-2019-06-15_etc.tar.gz

3) 存放备份内容的目录要求只保留三天的数据

4) 在执行计划任务时，不要输出任务信息

```

```

#脚本内容如下：

[root@qls ~]# cat /scripts/backup.sh
#!/bin/bash
mkdir -p /backup
cd /
tar czf backup/$(hostname)_$(date +%F-%H-%M)_etc.tar.gz etc/
find  backup/ -type f -name "*.tar.gz" -mtime +3|xargs rm -f

#测试脚本

[root@qls ~]# sh /scripts/backup.sh
[root@qls ~]# ll /backup/
total 9988
-rw-r--r-- 1 root root 10223879 2019-06-15 14:23 qiudao_2019-06-15-14-23_etc.tar.gz

#编写定时任务

[root@qls ~]# crontab  -l
* * * * * /usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null
* * * * * /usr/bin/date +\%F_\%T >> /root/time.txt 
* * * * * /bin/bash  /scripts/backup.sh &>/dev/null

#查看执行日志

[root@qls ~]# tailf /var/log/cron
Jun 15 14:30:01 qiudao CROND[8784]: (root) CMD (/usr/sbin/ntpdate  ntp.aliyun.com  &>/dev/null)
Jun 15 14:30:01 qiudao CROND[8785]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jun 15 14:30:01 qiudao CROND[8786]: (root) CMD (/usr/bin/date +%F_%T >> /root/time.txt )
Jun 15 14:30:01 qiudao CROND[8787]: (root) CMD (/bin/bash  /scripts/backup.sh &>/dev/null)

#查看结果

[root@qls ~]# ll /backup/
total 29964
-rw-r--r-- 1 root root 10223879 2019-06-15 14:23 qiudao_2019-06-15-14-23_etc.tar.gz
-rw-r--r-- 1 root root 10223879 2019-06-15 14:26 qiudao_2019-06-15-14-26_etc.tar.gz
-rw-r--r-- 1 root root 10223879 2019-06-15 14:27 qiudao_2019-06-15-14-27_etc.tar.gz

#防止定时任务出现命令找不到和特殊字符不识别错误，修改一下脚本

[root@qls ~]# cat /scripts/backup.sh 
#!/bin/bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
mkdir -p /backup
cd /
tar czf backup/$(hostname)_$(date +\%F-\%H-\%M)_etc.tar.gz etc/
find  backup/ -type f -name "*.tar.gz" -mtime +3|xargs rm -f

```

**04. 怎样备份定时任务**

```

给定时任务的文件做备份   ---> /var/spool/cron/{hostname}

```

**05. 怎样拒绝一个用户使用定时任务**

```

#使用root将需要拒绝的用户加入/etc/cron.deny

[root@qls ~]# echo "oldboy" >> /etc/cron.deny

#2.登陆该普通用户，测试是否能编写定时任务
[oldboy@qiudao ~]$ crontab -e
You (oldboy) are not allowed to use this program (crontab)
See crontab(1) for more information


```

### 4. 定时任务课堂练习题

```

1.每分钟打印你的名字到oldboy.txt中。

2.每周六的早上8点到12点，执行/scripts/test.sh脚本。

3.每个月的1号，执行/scripts/oldboy.sh脚本

4.每年的10月份1号到7号，执行/scripts/holiday.sh脚本

5.每天的9点和11点，执行/scripts/test.sh脚本

```

### 5. 定时给小姐姐发情书

**先配置邮箱发邮件**

```

#有的服务器没有mail，需要手动下载安装
[root@qls ~]# yum install -y mailx

#编辑邮件配置文件
[root@qls ~]# vim /etc/mail.rc
#发件人
set from=1176494252@qq.com
#邮件服务器
set smtp=smtp.qq.com
#发件人用户名
set smtp-auth-user=1176494252@qq.com
#发件人密码（QQ邮箱不可以使用密码，只能使用授权码）
set smtp-auth-password=xxx
#登录方式
set smtp-auth=login
#邮件服务器协议及端口
set smtp=smtps://smtp.qq.com:465
#忽略证书
set ssl-verify=ignore
#指定证书位置
set nss-config-dir=/etc/pki/nssdb/


#或者指定别的证书位置，创建证书目录
[root@qls ~]# mkdir -p /root/.certs
#进入证书目录
[root@qls ~]# cd /root/.certs
#获取证书
[root@qls ~]# echo -n | openssl s_client -connect smtp.qq.com:465 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ~/.certs/qq.crt
[root@qls ~]# certutil -A -n "GeoTrust SSL CA" -t "C,,"  -d  ~/.certs  -i  ~/.certs/qq.crt
[root@qls ~]# certutil -A -n "GeoTrust Global CA" -t "C,,"  -d  ~/.certs  -i  ~/.certs/qq.crt
[root@qls ~]# certutil -L -d /root/.certs
[root@qls ~]# certutil -A -n "GeoTrust SSL CA - G3" -t "Pu,Pu,Pu" -d ~/.certs -i ~/.certs/qq.crt

```

**1. 登录QQ邮箱，进行配置**

![img](D:\Notes\md_img\mail-1.png)

![img](D:\Notes\md_img\mail-2.png)

**2. 测试邮件是否可以发送**

```

[root@qls ~]# echo 'test' |mail -s 'hello'  1176494252@qq.com
[root@qls ~]# Error in certificate: Peer's certificate issuer is not recognized.    #忽略

```

![img](D:\Notes\md_img\mail-3.png)

**3. 在网上找几篇情书...**

```

#我都是网上搜的，可能很low，见谅

[root@qls ~]# cat>>qingshu1.txt<<EOF
我很笨，我不知道要怎么表达我心中对你的感觉。我知道牵手容易，但是牵一辈子，是很难的。可是我就是想和你牵手一起走过每个人生的路口，我不敢说我可以给你一切，但是只要是我可以给的，我都愿意而且无怨无悔。喜欢你，喜欢到都恨自己，恨自己不能当着你的面说喜欢。我不知道你会接受或者拒绝，在写这封信的时候也不去想那么多了，只是想把现在心里所有的话都写出来，让你知道，在你身边，一直有一个人，因为你的笑靥而开心，因为你的皱眉而伤神。你微笑，我可以开心好几天。就像向日葵一样，看到太阳就会露出微笑，感觉到一切的美好。你是我心中的小太阳。我，真的，想牵起你的手。
EOF

[root@qls ~]# cat>>qingshu2.txt<<EOF
喜欢你的人很多，不缺我一个，但我爱的人很少，只有你一个！
EOF

[root@qls ~]# cat>>qingshu3.txt<<EOF
一直想说，无论走到哪里，最想去的是你的身边。
EOF

[root@qls ~]# cat>>qingshu4.txt<<EOF
愿我们彼此相爱，一直到时间的尽头
EOF

[root@qls ~]# cat>>qingshu5.txt<<EOF
我相信我们可以一起，等青丝变白发
EOF

[root@qls ~]# cat>>qingshu6.txt<<EOF
无论岁月如何变迁，我对你的爱曾从不改变
EOF

```

**4. 发送测试及定时发送**

```

#测试是可以发送成功的
[root@qls ~]# cat qingshu1.txt   |mail -s '致我最爱的小姐姐'  1176494252@qq.com

#接下来就可以写脚本，放入定时任务啦，先创建一个情书的目录
[root@qls ~]# mkdir qingshu_dir

#将所有情书，移动到该目录下
[root@zls ~]# mv qingshu* qingshu_dir/

#编写脚本
[root@qls ~]# vim send_mail.sh
#!/bin/bash

qs_name=`ls -1 /root/qingshu_dir/|head -1`

cat /root/qingshu_dir/${qs_name} |mail -s '致我最爱的小姐姐' 1176494252@qq.com

if [ $? -eq 0 ];then
        rm -f /root/qingshu_dir/$qs_name
fi
```

```

#编辑crontab
[root@qls ~]# crontab -e
* * * * * /bin/bash /root/send_mail.sh &>/dev/null

```

### 6. 定时任务编写思路

```

1.手动执行命令，然后保留执行成功的结果。

2.编写脚本
    脚本需要统一路径/scripts
    脚本内容复制执行成功的命令(减少每个环节出错几率)
    
3.执行脚本
    使用bash命令执行, 防止脚本没有增加执行权限(/usr/bin/bash   /bin/bash)
    执行脚本成功后，复制该执行的命令，以便写入cron
    
4.编写定时任务
    加上必要的注释信息, 人、时间、任务
    设定定时任务执行的周期
    粘贴执行脚本的命令(不要手敲)
    
5.调试定时任务
    增加任务频率测试
    检查环境变量问题（最好在脚本中重新定义环境变量PATH）
    检查crond服务日志

```

### 7. 总结：9句箴言

```

1.定时任务规则之前加注释

2.使用脚本执行定时任务（只有一条简单命令的可以直接使用命令执行）

3.运行脚本一定要用绝对路径执行，统一脚本位置。

4.定时任务中date命令的百分号需转义才能使用。

5.命令或脚本结果(正确及错误)定向到空(>/dev/null 2>&1)或追加到文件中 >>/tmp/oldboy.txt 2>&1

6.避免不必要的程序及命令输出,如打包命令，tar  -v的显示过程的选项。

7.打包压缩使用相对路径（切到目标目录的上一级打包目标）

8.定时任务脚本中的程序文件 ,尽量用绝对路径,用户的定时任务中的文件默认存放在当前用户的家目录

9.系统与命令位置有关的环境变量问题,建议脚本中重新定义环境变量PATH。

```