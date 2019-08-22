## Linux系统三剑客（Awk、Sed、Grep）

2019-07-15 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(184) 评论(0)

1. ## grep/egrep

- **grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。**
- **Unix的grep家族包括grep、egrep和fgrep。egrep和fgrep的命令只跟grep有很小不同。egrep是grep的扩展，支持更多的re元字符，fgrep就是fixed grep或fast grep，它们把所有的字母都看作单词，也就是说，正则表达式中的元字符表示回其自身的字面意义，不再特殊。linux使用GNU版本的grep。它功能更强，可以通过-G、-E、-F命令行选项来使用egrep和fgrep的功能。**
- **主要作用：给搜索过滤出来的内容加上颜色和排除功能**

1. ### 工作原理

- **它在一个或多个文件中搜索字符串模板。如果模板包括空格，则必须被引用，模板后的所有字符串被看作文件名。搜索的结果被送到标准输出，不影响原文件内容。**
- **grep可用于shell脚本，因为grep通过返回一个状态值来说明搜索的状态，如果模板搜索成功，则返回0，如果搜索不成功，则返回1，如果搜索的文件不存在，则返回2。我们利用这些返回值就可进行一些自动化的文本处理工作。**

CentOS-6.x需要给grep设置别名，才能让过滤出来的内容加上颜色，7则不用。

cat >>/etc/profile<<EOF

alias grep='grep --color=auto'

alias egrep='egrep --color=auto'

EOF

source /etc/profile

1. ### 常用参数

-V         打印grep的版本号

-E             解释PATTERN作为扩展正则表达式，也就相当于使用egrep。 或操作

-F              解释PATTERN作为固定字符串的列表，由换行符分隔，其中任何一个都要匹配。也就相当于使用fgrep。

-G         将范本样式视为普通的表示法来使用。这是默认值。加不加都是使用grep。

-A num     匹配到搜索到的行以及该行下面的num行

-B num     匹配到搜索到的行以及该行上面的num行

-C num     匹配到搜索到的行以及上下各num行

-v     排除，反转匹配，选择没有被匹配到的内容。

-n         给grep过滤出来的内容加上行号

-o         显示命令每一次匹配到了什么

-i         不区分大小写

-c         --count 计算符合样式的行数

--color=auto 可以将找到的关键词部分加上颜色的显示

1. ### 练习题

- **环境**

**cat>oldboy.txt<<EOF**

**I am oldboy teacher!**

**I teach linux.**



**I like badminton ball ,billiard ball and chinese chess!**

**my blog is http://oldboy.blog.51cto.com**

**our size is http://blog.oldboyedu.com**

**my qq is 49000448**



**not 4900000448.**

**my god ,i am not oldbey,but OLDBOY!**

**EOF**

- **过滤出以m为开头的行**

**grep "^m" oldboy.txt**

- **过滤出以m为结尾的行**

**grep "m$" oldboy.txt**

- **过滤出以.为结尾的行**

**grep "\.$" oldboy.txt**

- **过滤出以m和n为开头的行**

**grep "^[mn]" oldboy.txt**

- **过滤出文件中不是以m或n或o开始的行**

**egrep "^[^mno]" oldboy.txt**

**egrep -v "^[mno]" oldboy.txt**

- **过滤出空行，并显示行号**

**grep -n "^$" oldboy.txt**

- **把文件中每个字母总共出现多少次统计出**

**grep -o "[a-Z]" oldboy.txt |sort |uniq -c|sort -rh**

- **把文件中每个单词总共出现多少次统计出**

**egrep -o "[a-Z]+" oldboy.txt |sort |uniq -c|sort -rh**

- **环境**

**cat>id.txt<<EOF**

**汤 610702199107053598**

**邹 371481199403259478**

**莫 52020319810613433X**

**韩 460106199111137270**

**荣 530124197504135438**

**荣 oldboy**

**荣 babygirl**

**荣 530124197504135438**

**阮 360702197902169951**

**翁 331002198504132813**

**任 6212231987082X5176**

**姜 370602198507189574**

**李A BBBCCC98507189574**

**赵B BBB602198507189574**

**霍 320904198403048179**

**EOF**

- **找出正确的身份证号**

**egrep "[0-9]{17}[0-9X]" id.txt**

1. ## sed

- **sed是一种流编辑器，它是文本处理中非常有用的工具，能够完美的配合正则表达式使用，功能不同凡响。sed主要用来自动编辑一个或多个文件，简化对文件的反复操作，编写转换程序等。**
- **主要功能：增删改查**

1. ### 工作原理

- **工作模式：处理时，把当前处理的行存储在临时缓冲区中，称为『模式空间』（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。**

1. ### 常用参数

-V     --version    显示版本信息

-h     --help    显示帮助

-n 取消默认输出，与p结合使用

-i 修改文件的内容

-i.bak 先备份，然后在修改文件内容

-r 支持扩展正则表达式符号

; 多条命令分隔符,取不连续的多行

a 在某行后面追加一行内容

i 在某行前面插入一行内容

c 在原位置替换。

s     替换指定字符

g     获得内存缓冲区的内容，并替代当前模板块中文本

p     打印模板块的行

d     删除，删除选择的行

! 非的意思，取反

=     打印当前行号

1. ### 练习题

- **环境**

**cat >sed.txt<<"EOF"**

**101,$oldboy,CEO**

**102,$zhangyao,CTO**

**103,$Alex,COO**

**104,$yy,CFO**

**105,$feixue,CIO**

**EOF**

- **查**
- **取出单行内容**

**sed -n '2p' sed.txt**

- **取连续多行内容**

**sed -n '2,4p' sed.txt**

- **取出不连续多行**

**sed -n '2p;4p' sed.txt**

- **找出文件包含yy的行**

**sed -n '/yy/p' sed.txt**

- **显示从包含oldboy的行到包含yy的行**

**sed -n '/oldboy/,/yy/p' sed.txt**

- **显示包含oldboy的行和包含yy的行**

**sed -n '/oldboy/p;/yy/p' sed.txt**

**sed -nr '/oldboy|yy/p' sed.txt**

- **增**
- **a (append) 追加 i(insert) 插入**
- **在第二行后面追加内容106,$lidao,UFO**

**sed '2a 106,$lidao,UFO' sed.txt**

**sed '3i 106,$lidao,UFO' sed.txt**

- **在第二行后面追加多行内容lidao,UFO 107,$lhw,BYJM**

**sed '2a106,$lidao,UFO\n107,$lhw,BYJM' sed.txt**

**sed '3i106,$lidao,UFO\n107,$lhw,BYJM' sed.txt**

- **把内容追加到结尾106,$lidao,UFO**

**sed '$a106,$lidao,UFO' sed.txt**

- **删**
- **删除单行内容**

**sed '2d' sed.txt**

- **删除连续多行内容**

**sed '2,4d' sed.txt**

- **删除不连续多行内容**

**sed '2d;4d' sed.txt**

- **删除空行**
- **环境**

**cat >sed.log<<"EOF"**

**101,$oldboy,CEO**





**102,$zhangyao,CTO**



**103,$Alex,COO**



**104,$yy,CFO**



**105,$feixue,CIO**



**106,$lidao,UFO**

**EOF**

- **排除空行**

**sed '/^$/d' sed.txt**

- **排除空行，但有的空行里面有空格和tab键**

**sed -r '/^[ \t]\*$/d' sed.log**

- **改**
- **把文件中的数字修改为oldboy**

**sed 's#[0-9]#oldboy#g' sed.txt**

- **把文件中的以103开头的行修改为oldboyedu.com**

**sed 's#^103.\*$#oldboyedu.com#g' sed.txt**

**sed '/^103/c oldboyedu.com' sed.txt**

- **把文件中的$字符替换为%**

**sed 's#\$#%#g' sed.txt**

- **后向引用**
- **取出IP地址ifconfig eth0 的ip**

**ifconfig eth0|sed -n '2p'|sed -r 's#(.\*t )(.\*)( n.\*$)#\2#g'**

**ifconfig eth0|sed -nr '2s#(.\*t )(.\*)( n.\*$)#\2#gp'**

1. ## awk

- **awk其名称得自于它的创始人 Alfred Aho 、Peter Weinberger 和 Brian Kernighan 姓氏的首个字母。实际上 AWK 的确拥有自己的语言： AWK 程序设计语言 ， 三位创建者已将它正式定义为"样式扫描和处理语言"。它允许您创建简短的程序，这些程序读取输入文件、为数据排序、处理数据、对输入执行计算以及生成报表，还有无数其他的功能。**
- **awk 是一种很棒的语言，它适合文本处理和报表生成，其语法较为常见，借鉴了某些语言的一些精华，如 C 语言等。在 linux 系统日常处理工作中，发挥很重要的作用，掌握了 awk将会使你的工作变的高大上。 awk 是三剑客的老大，利剑出鞘，必会不同凡响。**
- **主要作用：取行取列、计算和统计**

1. ### 工作原理

- **工作模式：命令行赋值==>BEGIN模块(读取文件之前)==>读入一行，进入模式判断是否是要找的内容，不是就重复刚才的工作，继续读入下一行直到文件结尾，是就将内容放在END模块里面，继续读入下一行直到文件结尾，最后把找到内容就进如END模块执行并结束。**

1. ### 常用参数

-V 显示版本号信息

NR ==表示行号

$n 取某一列的内容

$0 取一整行内容

$NF 最后一列

-v 创建或修改awk内部变量

FS 简写F输入域分隔符，默认为一个空格

OFS 输出域分隔符，默认为一个空格

！ 非

i=i+1 == i++ 计算次数，总数

i=i+某一列 计算总和，累加

1. ### 练习题

- **环境**

**cat >>reg.txt<<EOF**

**Zhang Dandan 41117397 :250:100:175**

**Zhang Xiaoyu 390320151 :155:90:201**

**Meng Feixue 80042789 :250:60:50**

**Wu Waiwai 70271111 :250:80:75**

**Liu Bingbing 41117483 :250:100:175**

**Wang Xiaoai 3515064655 :50:95:135**

**Zi Gege 1986787350 :250:168:200**

**Li Youjiu 918391635 :175:75:300**

**Lao Nanhai 918391635 :250:100:175**

**EOF**

- **取出第三列以4开头的行**

**awk '$3~/^4/{print $0}' reg.txt**

**awk '$3~/^4/' reg.txt**

- **取出第三列不是以4开头的行**

**awk '$3!~/^4/{print $0}' reg.txt**

**awk '$3!~/^4/' reg.txt**

**第一列是姓氏**

**第二列是名字**

**第一第二列合起来就是姓名**

**第三列是对应的ID号码**

**最后三列是三次捐款数量**

- **姓氏是Zhang的人，显示他的第二次捐款金额及她的名字**

**awk -F "[ :]+" '/Zhang/{print $1,$2,$5}' reg.txt**

**awk -F "[ :]+" '/Zhang/{print $1,$2,$(NF-1)}' reg.txt**

- **显示第三列所有ID号码最后一位数字是1或5的人的全名**

**awk -F "[ ]+" '$3~/[15]$/{print $1,$2}' reg.txt**

**awk -F "[ ]+" '$3~/[1|5]$/{print $1,$2}' reg.txt**

- **显示Xiaoyu的捐款.每个值时都有以$开头.如$520$200$135**

**awk '/Xiaoyu/{print $4}' reg.txt |sed 's#:#$#g'**

**awk '/Xiaoyu/{print $4}' reg.txt |tr ':' '$'**

**awk -F '[ :]+' -vOFS='$' '/Xiaoyu/{print "$"$4,$5,$6}' reg.txt**

**awk -F '[ :]+' '/Xiaoyu/{print "$"$4"$"$5"$"$6}' reg.txt**

- **显示所有人的全名，以姓,名的格式显示，如Meng,Feixue**

**awk '{print $1","$2}' reg.txt**

**awk -vOFS=',' '{print $1,$2}' reg.txt**

- **取出IP地址ifconfig eth0**

**ifconfig eth0|awk 'NR==2{print $2}'**

- **用awk分别计算200和60的加减乘除的值。**

**awk 'BEGIN{print 200+60}'**

**awk 'BEGIN{print 200-60}'**

**awk 'BEGIN{print 200/60}'**

**awk 'BEGIN{print 200\*60}'**

**awk 'BEGIN{print 200%60}'**

- **统计/etc/services文件中的空行的数量**

**awk '/^$/{i=i+1}END{print i}' /etc/services**

**grep '^$' /etc/services |wc -l**

**sed -n '/^$/p' /etc/services |wc -l**

- **环境 seq 10 >num.txt 求这个文件中所有数字的和。**

**awk '{i=i+$1}END{print i}' num.txt**

**第一列是IP地址 第七列是访问的路径，第十列是服务端响应给客户端的字节数**

- **统计access.log的服务端响应给客户端的总字节数**

**awk '{i=i+$10}END{print i}' access.log**

- **统计access.log的每个访问ip出现的次数最多的十个**

**awk '{i[$1]++}END{for(ip in i)print ip,i[ip]}' access.log |sort -rnk2|head |column -t**

- **统计secure日志中，谁在破解你的密码（每个破解你密码的ip的出现次数的前十个）**

**awk '/Failed password/{h[$(NF-3)]++}'END'{for(bl in h)print bl,h[bl]}' secure-20161219 |sort -rnk2 |head|column -t**

- **统计access.log中被请求最多的路径(取出前十个)**

**awk '{i[$7]++}END{for(ip in i)print ip,i[ip]}' access.log |sort -rnk2|head |column -t**

- **统计access.log中每个ip地址的访问次数和每个ip地址服务端响应的总字节数**

**awk '{h[$1]++;i[$1]+=$10}END{for (pol in h)print pol,h[pol],i[pol]}' access.log|sort -rnk2|head|column -t**