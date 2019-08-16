```
1.显示“I'm a student”到屏幕上面
​echo "I'm a student"
2.将“I'm a student”追加到hello.txt文件中
​echo "I'm a student" >hello.txt
3.查看oldboy.txt文件中的内容，并显示行号（请使用cat命令）。
​cat -n hello.txt
4.使用cat命令把oldboy oldgirl student三行内容（每个字符串是一行内容）写入到test.txt文件中
cat >>text.txt<<'GG'
oldboy
oldgirl
student
GG
5.查看系统中/etc/services文件中的内容（使用more和less命令，进行上下页查看，并搜索关键字‘ssh’，之后退出。）
1.more /etc/services
/ssh
2.less /etc/services
/ssh
6.显示/etc/passwd文件的前5行内容。
head -n 5 /etc/passwd
7.显示/etc/passwd文件的最后5行内容。
tail -n 5 /etc/passwd
8.显示/etc/services文件的第11行到第20行的内容
head -n 20 /etc/services|tail -n 10
grep -n '.' /etc/errvices | grep -A 9 '^11:'
9.已知文件123.txt内容如下，请过滤出包含oldboy字符串的命令
test
OLDBOY
online
oldboy
oldboyoldboy
答
cat >123.txt<<EOF
test
OLDBOY
online
oldboy
oldboyoldboy
EOF
grep -n oldboy 123.txt
10.过滤出文件123.txt中不包含test的行，并给其过滤出来的内容加上行号
grep -n [^test] 123.txt
或grep -v test -n 123.txt
11.要求过滤出文件123.txt中包含oldboy的字符串，忽略大小写。
grep -io oldboy 123.txt
12.要求过滤出文件123.txt中包含online字符串的行，并统计共有多少行。
grep -c online 123.txt
13.要求过滤出文件123.txt中包含oldboy的单词。
grep -o oldboy 123.txt
14.调试系统服务时，希望能实时查看系统日志/var/log/messages的更新,如何做？
tail -F /var/log/messages
15.打印配置文件/etc/passwd内容的行号及内容，你有几种方法可以实现？
方法一cat -n /etc/passwd
方法二less -N /etc/passwd

```

```
练习题一
1.如何查看当前所在的目录？
pwd
2.如何查看/etc/目录下的所有文件，请用命令实现。
tree -F /etc/
3.如何查看/dev/目录下文件的详细信息？
ll /etc/
4.我只想查看/root目录的信息，而不是root目录下文件的信息，请问怎么实现？
ll -d /root
5.如何对/etc/目录下文件按照时间排序查看，请用命令实现。
从后到前ll -t /etc
从前到后ll -rt /etc
6.我想测试虚拟机的联网情况，怎样设置一个别名，并且执行别名就能得到虚拟机的联网情况。
alias p_baidu='ping baidu.com'
p_baidu
7.怎样取消你刚才设置的别名？
unalias p_baidu
8.怎样查看你所执行过得命令？
history
9.怎样清除你之前所执行过得命令？
history -c
10.如何快速的执行你最近的一次以ls开头执行的命令？
!ls
11.请说明一下这两个目录的用处/home、/root？
/home所有普通用户的家目录
/root超级管理员的家目录
12.请说明/etc这个目录主要是用来存放什么的？注意，这个目录极其重要。
配置文件目录，用于存放系统的配置文件
13.写出你的虚拟机网卡配置文件。
/etc/sysconfig/network-scripts/
ifcfg-eth0
ifcfg-eth1
14.说明下面这几个文件的作用：/etc/hostname、/etc/resolv.conf、/etc/hosts。
主机名配置文件、本地的DNS配置文件、本地域名解析配置文件
15.说明下列目录的用处?
/var	可变目录
/tmp	文件临时存放目录
/dev	设备目录
/proc	虚拟信息，存放系统实时状态信息目录
/mnt	临时挂载点
/opt	早期第三方软件安装目录
/bin	普通命令的目录
/sbin	超级管理员使用的命令目录
16.如何快速返回上一次所在的目录？
cd -
17.创建一个名为oldboy的目录，然后进入该目录.
mkdir oldboy
cd oldboy
18.在oldboy下创建一个名为student.txt的文件
mkdir -p oldboy/student.txt
19.查看/etc/hosts文件的内容。
cat /etc/hosts
20.复制/etc/hostname目录到/tmp目录下。
cp /etc/hostname /tmp
21.创建一个目录，shanghai。
mkdir shanghai
22.创建一个文件，test.txt
touch test.txt
echo >test.txt
23.进入到shanghai这个目录。
cd shanghai
24.显示你当前所在的位置。
pwd
25.如何快速从当前目录切换到家目录？
cd ~
```

```
练习题二
1.查看oldboy.txt文件中的内容，并显示行号（请使用cat命令）。
cat -n oldboy.txt
2.使用cat命令把oldboy oldgirl student三行内容（每个字符串是一行内容）写入到test.txt文件中
cat >>test.txt<<EOF
oldboy
oldgirl
student
EOF
3.查看系统中/etc/services文件中的内容（使用more和less命令，进行上下页查看，并搜索关键字‘ssh’，之后退出。）
more /etc/services
/ssh
less /etc/services
/ssh
4.显示/etc/passwd文件的前5行内容。
head -n 5 /etc/passwd
5.显示/etc/passwd文件的最后5行内容。
tail -n 5 /etc/passwd
6.显示/etc/services文件的第11行到第20行的内容
head -20 /etc/services|tail -10
grep -n '.' /etc/errvices | grep -A 9 '^11:'
7.已知文件123.txt内容如下，请过滤出包含oldboy字符串的命令
test
OLDBOY
online
oldboy
oldboyoldboy

grep oldboy 123.txt
8.过滤出文件123.txt中不包含test的行，并给其过滤出来的内容加上行号
grep -v test -n 123.txt
9.要求过滤出文件123.txt中包含oldboy的字符串，忽略大小写。
grep -i oldboy 123.txt
10.要求过滤出文件123.txt中包含online字符串的行，并统计共有多少行。
grep -c online 123.txt
11.要求过滤出文件123.txt中包含oldboy的单词。
grep oldboy 123.txt
12.调试系统服务时，希望能实时查看系统日志/var/log/messages的更新,如何做？
tail -F /var/log/messages
13.打印配置文件/etc/passwd内容的行号及内容，你有几种方法可以实现？
cat -n /etc/passwd
less -N /etc/passwd
14.复制/etc/hosts文件到/root目录下面
cp /etc/hosts /root
15.把/root/hosts移动到/tmp目录下
mv /root/hosts /tmp
16.删除/tmp/hosts文件
rm /tmp/hosts
17.复制/etc/services文件拷贝到当前目录。
cp /etc/services ./
18.查询services文件中包含ssh字符有多少行
grep -c ssh /etc/services
16行
19.在当前目录创建一个文件oldboy.txt,并将其移动到/opt目录下面
touch oldboy.txt
mv oldboy.txt /opt
20.在/tmp目录下创建oldboy.txt文件，将其移动到/opt目录，提示文件已经存在，是否覆盖，如果不想看到这个提示，怎么执行命令？
touch /tmp/oldboy.txt
mv -f /tmp/oldboy.txt /opt
21.删除/opt/下的所有文件
rm -rf /opt/*
22.已知，/oldboy已存在，如果给该目录名称修改为/oldgirl,请问怎么实现？
mv /oldboy /oldgirl
23.rm命令可以删除目录和文件，其主要差别就是是否使用了什么选项。
-r
24.怎样强制删除一个文件，例如，删除oldboy.txt文件。
rm -f oldboy.txt
25.什么命令可以移动目录或文件，还可以为文件或目录重命名。
mv
26.如何删除一个非空目录/opt？
rm -rf /opt
27.用"rm -i"删除文件时，系统会提示什么来让你确认
提示是否要删除，y/n
28.用户编写一个文本文件a.txt，想将该文件名称改为txt.a，请问怎么实现？
mv a.txt txt.a
29.你知道有哪些命令可以查看文件内容？请一一列举出来。
cat
more
less
head
tail
grep
30.若希望在查看文件内容过程中可以用光标上下移动来查看文件内容，应使用什么命令？
cat
31.打印/etc/passwd 文件中的第 2-5 行
head -5 /etc/passwd|tail -4
32.查看ip地址的命令有哪些？
ip a
ifconfig
hostname -i
hostname -I
33.只显示/etc/passwd文件的第五行
head -5 /etc/passwd
34.统计/etc/passwd文件一共有多少行
grep -c '.*' /etc/passwd
35.使用cat命令查看文本文件的内容时要对所有的输出行进行显示行号，怎么实现？
-n
36.使用mv命令，下列说法错误的是？
A.mv命令可以重命名文件
B.mv命令可以移动文件
C.mv命令可以复制文件
D.mv命令可以删除文件
c
37.删除文件的命令为？
rm
38.改变bash的提示符实际上就是改变变量？
写一个文件，文件内容如下,下面39-45题请用该文件作答？
cat >>test.txt<<EOF
server {
	lisTEN 80;
	server_nAme www.oldboy.com;
	root /code/dOcs
	index INDEX.html;
}
EOF
39.过滤www.oldboy.com这段关键字
grep www.oldboy.com test.txt
40.同时过滤出root和index的行，不区分大小写
grep -E 'root|index' -i test.txt
41.过滤index，区分大小写
grep index test.txt
42.过滤出带"O"的行，不区分大小写
grep -i [O] test.txt
43.过滤出不带";"的行
grep -v ';' test.txt
44.过滤出以s开头的行
grep ^s test.txt
45.统计该文件的行数
grep -c '.*' test.txt
46.如果某一天你误操作了"rm -rf *"，会发生哪些情况
会删除当前目录下的所有文件
47.rm是个危险的命令，要求用命令rm删除文件时提示“rm command no bny”，怎么实现？
alias rm='ehco rm command avalid'
48.设置rm命令不能用之后，有要删除/oldboy这个目录，怎么实现？
\rm -rf /oldboy
49.已知123.txt文件内容如下：
egon
frank
jason 
要求过滤出jason这一行的内容？你有几种方法？
3种
grep jason 123.txt
tail -1 123.txt
more +3 123.txt
50.接上题，要求不显示egon这行内容，怎么实现？你有几种方法？
3种
grep -v egon 123.txt
tail -2 123.txt
more +2 123.txt
```

