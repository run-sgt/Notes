```
1.查找/tmp目录下，属主不是root，且文件名不以f开头的文件
​touch /tmp/a.txt
chown user /tmp/a.txt
find /tmp/ -not -user root ! -name 'f*'
2.查找/var目录下属主为root，且属组为mail的所有文件
​find /var -user root -group mail -ls
3.查找/var目录下属主不属于root、lp、gdm的所有文件
​find /var/ -type f -not \( -user root -o -user lp -o -user adm \) -ls
4.查找/var目录下最近一周内其内容修改过，同时属主不为root，也不是postfix的文件
​find /var -type f -mtime -7 ! \( -user root -o -user postfix \) -ls
5.查找/etc目录下大于1M且类型为普通文件的所有文件
​find /etc/ -type f -size +1M ls -lh
6.将/etc/中的所有目录(仅目录)复制到/tmp下，目录结构不变
​find /etc -type d | xargs -I {} mkdir -p /tmp/{} 
或 find /etc -type d exec mkdir -p /tmp/{} \;

7.将/etc目录复制到/var/tmp/,/var/tmp/etc的所有目录权限777/var/tmp/etc目录中所有文件权限666
​cp -a /etc /var/tmp/
find /var/tmp/ -type d -exec chmod 777 {} \;
find /var/tmp/ ! -type d -exec chmod 666 {} \;
8.保留/var/log/下最近7天的日志文件,其他全部删除
​find /var/log -mtime +6 -delete
或 find /var/log ! -mtime -7 -delete
9.创建touch file{1..10}10个文件, 保留file9,其他一次全部删除
​find ./ -type f ! -name 'file9' -delete
10.解释如下每条命令含义
​
mkdir /root/dir1
​
touch /root/dir1/file{1..10}
​
find /root/dir1 -type f -name "file5"
​查找文件名为file5
find /root/dir1 ! -name "file5"
​查找文件名不是file5
find /root/dir1 -name "file5" -o -name "file9"
​查找文件名为file5或file9
find /root/dir1 -name "file5" -o -name "file9" -ls
​-ls 有个bug，只查找-o条件最后一个
find /root/dir1 \( -name "file5" -o -name "file9" \) -ls
​
find /root/dir1 \( -name "file5" -o -name "file9" \) -exec rm -rvf {} \;
​
find /root/dir1 ! \( -name "file4" -o -name "file8" \) -exec rm -vf {} \;

```

```
16.创建10个用户，前缀是你的名，后缀为数字序列，比如zengjia1等，并给其10个用户设置统一密码为123456，最后尝试是否可以登录。
[root@qls ~]# seq  10  | awk  '{print "useradd  egon"$1 "  &&  echo '123456' | passwd --stdin  egon"$1}' | bash
17.创建10个用户，前缀是oldboy，后缀为数字序列，比如oldboy1等，并给其10个用户设置随机密码，最后尝试是否可以登录。
[root@qls ~]# seq  10  | awk  '{print "useradd  mac"$1 "  &&  mkpasswd -l 6 -d -s 2 | tee pass"$1".txt | passwd --stdin  mac"$1}' | bash

18.使用命令调换/etc/passwd文件里 root 位置和/bin/bash 位置？ 即将所有的第一列和最后一列位置调换？
sed -r 's#(.*)(:x.*:)(.*)#\3\2\1#g' passwd
或
awk  -F:  '{print $NF":"$2":"$3":"$4":"$5":"$6":"$1}'  passwd
```

