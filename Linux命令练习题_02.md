```
1.用户基础权限为9位，每三位为一组，每组代表着谁的权限？
2.权限中的rwx-，每个字符所代表什么意思？对应的数字是什么？
3.-rwxr-xr-x，写出对应数字权限
4.-rwxr--r--，写出对应数字权限
5.-r-xr-x--x，写出对应数字权限
6.-rw-r-xr-x，写出对应数字权限
7.-r--r--r--，写出对应数字权限
8.-r-xr-----，写出对应数字权限
9.---x-w-r--，写出对应数字权限
10.-rwxr--rw-，写出对应数字权限
11.-rw-r--r--，写出对应数字权限
12.---xr--rwx，写出对应数字权限
13.777，写出对应字母权限
14.545，写出对应字母权限
15.744，写出对应字母权限
16.600，写出对应字母权限
17.641，写出对应字母权限
18.711，写出对应字母权限
19.700，写出对应字母权限
20.555，写出对应字母权限
21.733，写出对应字母权限
22.713，写出对应字母权限
23.建一个目录/test,查看这个目录的默认权限是?
24.进入/test目录中,建一个文件abc,查看其默认的权限为？
25.创建一个文件test.txt,并其将权限改为600.
26.将test.txt文件的权限改为755.
27.将test.txt文件的权限改为000.
28.修改test.txt文件的权限为644.
29.给test.txt文件的属主加上x权限。
30.给test.txt文件的其他用户加上x权限。
31.去除test.txt文件的所有执行权限。
32.给/test目录及目录下的所有文件或目录的权限统一改为744。
33.给/test目录及目录下的所有文件或目录加上x权限（属主、属组、其他用户都执行权限）。
34.使用stat命令查看test.txt文件的权限，并使用awk将权限取出来，比如：755
35.使用stat命令查看test.txt文件的权限，并使用sed将权限取出来，比如：755

```

```
1.创建目录/web01，所有者是user01，所属组是user02；创建目录/web02，所有者是user02，所属组是user01。
2.复制/etc/fstab文件到/opt下，设置文件所有者为tomcat读写权限，所属组为apps组有读写权限，其他人无权限
3.将以下权限翻译成数字，将数字权限用字母表示
rw-r-xr--	
rw-r--r--	
rwx--x--x	
rw-------	
rwxr--r--	
rw-rw-r--	
rwxrwxrwx	
751			
771			
632			
551			
622			
746			
644			
755	
4.假设公司研发部的用户david和peter属于组A，财务部的用户life和laowang属于组B，根据要求创建用户及组。
5.接第4题，建立目录file1，该目录里面的文件只能由研发部人员读取、增加、删除、修改以及执行，其他用户不能对该目录进行任何操作
6.接第4题，建立目录file2，该目录里面的文件只能由财务部人员读取、增加、删除、修改以及执行，其他用户不能对该目录进行任何操作
7.接第4题，建立目录file3，该目录里面的文件研发部人员可以读取、增加、删除、修改以及执行，其他部门只能做查看操作
8.接第4题，建立目录file4，该目录里面的文件只有研发部的经理David拥有所有操作权限，研发部的其他人只有查看权限，其他部门不能进行任何操作
9.新建用户组，shengchan,demo,要求如下:
	01、tom是shengchan组的附加成员
	02、leo是demo组的附加成员
	03、新建用户admin不属于以上任何一个组
10.新建目录要求如下：
	01、/pub/目录为公共存储目录，所有用户可以读写执行
	02、/sc目录是生产部的目录，生产部的成员读写执行，并且生产部人员建立的文件自动归属到
		shengchan组中
	03、admin用户可以在/sc目录中可以读
11.新建目录/www01，/www02，/www03
12.接第11题，更改/www01目录的权限，使其他用户对它没有任何权限；
13.接第11题，更改/www02目录的权限，使所属组对它拥有读写执行权限；
14.接第11题，更改/www03目录的权限，任何用户都可以读写，但是在/www03目录中创建的任何文件都属于grp1组
15.新建用户zhangsan，lisi，wangergou，三个用户都属于同一个用户组f4，密码都为oldboy
16.接15题，上述用户和组都能在/data/code目录，访问，创建，删除文件，其他用户无法访问该目录
17.接15题，/data/code目录下创建的所有文件自动归属于f4组所有
18.接15题，现在新增了一批用户，属于默认组，需要开放其他用户在code目录的读权限
19.接15题，新增的所有其他用户在code目录下创建的文件自动归属f4组
20.有两个用户组，分别为python组、linux组，python组的人可以修改读取python组的文件，但不能让linux组的人读取；linux组的人可以修改读取linux组的文件，但不能让python组的人读取。
21.在当前目录中新建文件text，假设该文件的权限为614。现要求设置该文件属主(u)增加执行权限，属组(g)增加写权限，其他用户(o)删除读权限，应该如何操作，另外修改后的权限用字母应该如何表示
22.在当前目录中创建目录aaa，并把该目录的权限设置为只有文件主有读、写和执行权限
23.设某文件myfile的权限为-rw-r--r--，若要增加所有人可执行的权限，应该怎么做
24.登录到普通用户user01，删除/opt这个目录，提示权限不足，如果要删除/opt这个目录，不使用root用户删除的话，你该怎么解决？
25.创建目录/test，要求所有用户对这个目录有所有权限，现在只想让每个用户在这个目录只能对属于自己的文件进行操作，怎么实现？
26.创建一个文件/opt/file.txt,这个文件很重要，现要求所有人只能查看和追加内容进去，不允许有其他的操作，怎么实现？
27.取消上题的设置，现要求所有人只能查看此文件，不允许有其他的操作，怎么实现？
28.请问在当前系统中新创建的目录和文件，权限各是多少，为什么？
29.如果我想要在系统中新创建的目录权限为555，文件为444，怎么实现？
30.umask怎么计算目录及文件权限的？
31.输入last，统计当前连接本机的每个远程终端IP的连接总次数
32.输入lscpu，取出当前系统的cpu核心数
33.输入df -h，取出当前系统根分区剩余可用磁盘空间
34.显示/proc/meminfo文件中以s开头的行(忽略大小写)
35.扩展题：在你修改了test.txt文件之后，如何知道在你下次要修改这个文件时，这个文件有没有被别人修改过？

```

```
1.输入时间命令"date"将当前系统时间输出到/data/1.txt
2.输入时间命令"date"将当前系统时间追加到/data/1.txt
3.在当前系统能ping通百度的情况下，使用" ping -c3 baidu.com "将返回的信息输出到/data/1.txt
4.使用“ls /ta”将错误的信息输出到/data/1.txt
5.将/data/1.txt的文件内容，标准输出到/data/2.txt
6.使用"seq 10 50"将以0结尾的行标准输出到3.txt
7.把/etc/fstab文件内容重定向到/tmp目录下文件名为fstab.out
8.把字符"hello world"追加到/tmp/fstab.out文件尾部
9.输入df -h，取出当前系统根分区已用磁盘空间的百分比，并将取出来的数值输出到/data/1.txt
10.使用命令ping测试10.0.0.100是否通畅，把输出的结果不管是正确的还是错误的都追加到/data/1.txt
11.使用重定向从文件/etc/passwd中读取数据。
12.用 << 举个命令的使用例子。
13.复制/etc/passwd文件到当前目录下，把文件中的：替换成#，不能使用sed和vim命令。
14.执行测试虚拟机是否能上网的命令，把错误的结果保存到/data/1.txt
15.清空/data/1.txt文件中的内容，不能使用vim命令。
16.在当前系统能ping通百度的情况下，使用命令curl cip.cc 查看当前网络出口地址，取出关键字"数据二"所在的行，以空格为分隔符，取出第三列的内容
17.改变文件所有者的命令是？
18.新建一个1.txt文件，文件内容如下
1123
1122
112233
321
3306
8080
80
23
21
8081
8082
8085
19.接18题，显示1.txt第3行到第10行的内容（三种方法）
20.接18题，显示1.txt第3行和第10行的内容（两种方法）
21.新建用户oldboy，oldgirl，属同一用户组edu
22.修改文件1.txt的所有者为oldboy，属组为edu
23.除所有者以外，属组和其他用户均没有任何权限（要求普通用户进行验证）
24.复制/etc/fstab文件到/var/tmp下，设置文件所有者为wangcai读写权限，所属组为sysadmins组有读写权限，其他人无权限
25.创建下面的用户、组和组成员关系，用户natasha，使用admins作为附属组，用户harry，也使用admins作为附属组，用户sh，不可交互登录系统，natasha，harry，sh密码都是centos
26.新建一个用户rose,uid为555,gid为500,注释信息为:linux,家目录在/rose
27.执行命令echo "I am qls,myqq is 1176494252">/oldboy/oldboy.txt，
现在需要从文件中过滤出“qls”和“1176494252”字符串，请给出命令？（最少三种方法）
28.接上题，如果需要从文件中过滤出“qls,1176494252”字符串，请再给出命令？
29.如何查看/etc/services文件的有多少行？（三种方法）
30.我想在/data/oldboyedu目录下面创建 一个oldboy.txt文件，出现了如下报错。
[root@qiudao ~]# touch /data/oldboyedu/oldboy.txt
touch: cannot touch ‘/data/oldboyedu/oldboy.txt’: No such file or directory
问：为何出现这样的错误？该怎么解决？
31.请输入你知道的20个命令及作用
32.翻译题
01).command not found  					   
02).No such file or directory  			   
03).Permission denied  					   
04).overwrite				
05).File exists  							
06).Is a directory  						
07).Not a directory  	                   	
08).Warning: Changing a readonly file      
09).Found a swap file by the name ".1.swp" 
10).unrecognized option '--oldboy'  
11).Operation not permitted
12).invalid option

```

```
1.查找/etc/目录下，所有.conf后缀的文件 
2.查找/目录下最后创建时间是3天前，后缀是*.log的文件
3.查找/目录下文件名包含txt的文件
4.查找/目录下属主是oldboy并且属组是oldboy的文件
5.查找/目录下属主是oldboy但是属组不是oldboy的文件
6.查找/目录下属主是oldboy或者属主是oldgirl的文件
7.查找/tmp目录下属主既不是oldboy，也不是oldgirl的文件
8.查找/var/log目录下7天以前的文件
9.查找/tmp目录下15天以前的文件删除
10.查找/home目录下，类型是目录的，并且属主是oldboy的目录
11.查找/var/log下大于100kb且以log结尾的所有文件
12.查找tmp目录下所属组group1，所属主user1的目录
13.同时查找根目录下名为1.txt，2.txt的文件和名字带a的目录
14.查找/tmp目录下所有文件并删除
15.查找/etc目录下至少有一类用户没有写权限的文件

```

```
1.1 Linux关机重启的命令有哪些 ？（至少4个）
1.2 你知道哪些bash的快捷键呢？请把他写出来，并写出他的作用？（至少6个）
1.3 Linux的发行版本都有哪些？（至少6个）
1.4 如果我在当前在/目录下，之后执行了以下操作，请告诉我，最后我所在的目录位置？
cd /etc/sysconfig/
cd ..
cd ..
cd -
cd ~
1.5 显示/etc/services文件的第11行到第20行的内容？
1.6 已知文件123.txt内容如下，请过滤出不包含online字符串的命令
test
OLDBOY
online
oldboy
oldboyoldboy
1.7 已知执行ip a s eth0结果如下：要求取出IP地址（两种方法）
[root@qiudao ~]# ip a s eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group 
    link/ether 00:0c:29:77:fe:83 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.90/24 brd 10.0.0.255 scope global eth0 	#最前面为4个空格。
       valid_lft forever preferred_lft forever
inet6 fe80::20c:29ff:fe77:fe83/64 scope link
1.8 已知/data/test.txt文件内容如下：要求取出不包含bejing的行，（两种方法）
shanghai
bejing
nanjing
1.9 调试系统服务时，希望能实时查看系统日志/var/log/messages的更新,如何做？
1.10 统计/etc/passwd文件一共有多少行？（两种方法）
1.11 查找ifconfig命令的绝对路径在哪里？（两种方法）
1.12 统计文件/etc/services的大小？（两种方法）
1.13 执行下面的命令echo "Im qiuzengjia, is QQ 1176494252" >file.txt，要求取出该文件中的姓名和QQ号。（注意逗号前面没有空格）。（两种方法）
1.14 统计/var/log下的所有文件（包含目录）个数（包括隐藏文件）。
1.15 以“:”为分隔符，取出/etc/passwd第一行的第七列也就是最后一列的内容？（两种方法）
1.16 请用一条命令完成创建目录/oldboy/test，即创建/oldboy目录及/oldboy/test。
1.17 把/oldboy目录及其子目录下所有以扩展名 .txt结尾的文件中，文件包含oldboy的字符串全部替换为oldgirl （两种方法）
1.18 把/etc/passwd文件中的第5到第15行的内容保存到/oldboy/test.txt中
1.19 说出下面几个特殊符号的含义  *  >  >>   &>  #  ..  .  $
1.20 把/data/test.txt文件中的beijing替换为shanghai,给出命令(请使用sed命令).
1.21 找出/var/log目录中以.log结尾的文件，并显示这些文件的属性信息。
1.22 过滤出/etc/services 文件包含3306或1521两数字所在的行的内容。（两种方法）
1.23 将/data目录下的修改时间是7天以前,并且大于100k的文件复制到/tmp目录下。（两种方法）
1.24 将/data目录中大于50k且小于100k 的文件，把文件中的oldboy替换为oldgirl。（两种方法）
1.25 写出redhat中，配置网卡及dns的配置文件是什么？有什么区别？
1.26 vim中把所有oldboy替换为oldgirl，给出命令
1.27 查找/app/logs/下7天以前的文件并删除
1.28 /data目录下（一级目录）查找包含“hello-beijing”或“hello-nanjing”的所有文件及行号
1.29 请输入你知道的20个命令及作用（不做直接扣十分）
1.30 寻找名称为mytest.log的文件
1.31 用命令行更改config.txt文件,把里面的“name”更改为“address”
1.32 查找目录/var/log下大于1M的文件移动到/tmp/log下。（两种方法）
1.33 如下常见报错，说明一下如下报错信息？
01).command not found  					   
02).No such file or directory  			   
03).Permission denied
04).File exists
1.34 把/etc/fstab文件内容重定向到/tmp目录下文件名为fstab.out
1.35 把/etc/passwd文件中的：替换成#，不能使用sed和vim命令及管道符号。
1.36 显示test.txt第3行和第10行的内容（两种方法）
1.37 将以下权限翻译成数字，将数字权限用字母表示
rw-r--r--
rw-rw-r--
622			
746
1.38 假设公司研发部的用户david和peter属于组A，财务部的用户life和laowang属于组B，根据要求创建用户及组。
1.39 创建目录/test，要求所有用户对这个目录有所有权限，现在只想让每个用户在这个目录只能对属于自己的文件进行操作，怎么实现？
1.40 用户基础权限为9位，每三位为一组，每组代表着谁的权限？
1.41 创建一个文件test.txt,并其将权限改为600.
1.42 创建一个用户基本组，shanghai02，指定GID为1099.
1.43 创建一个用户linux01，指定该用户的基本组为linux，附加组为shanghai02。
1.44 怎样临时关闭和永久关闭firewalld 这个服务？
1.45 创建test01用户，给test01用户使用非交互式设置密码为123456
1.46 使用root用户给test01用户设置一个18位的随机密码，并进行登录。
1.47 普通模式下的S是什么作用？
1.48 删除用户user2，不保留其家目录。
1.49 给/test目录及目录下的所有文件或目录加上x权限（属主、属组、其他用户都执行权限）。
1.50 将/test目录的属主修改为qiudao，属组改为linux，需创建用户和用户组。
```

