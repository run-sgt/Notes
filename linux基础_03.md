#### 打包压缩

```
压缩包工具
.zip			zip压缩工具
.bz2			bzip2压缩工具，只能压缩文件，会删除源文件
.gz				gzip压缩工具，只能压缩文件，会删除源文件
tar.gz			gzip压缩，使用tar打包
tar.bz2			使用bzip2压缩，使用tar打包
```

安装`yum install -y zip unzip`

**使用zip**

```
zip	压缩
zip ser.zip services			将services目录及以下文件打包成ser.zip文件
zip -r etc.zip /etc				递归压缩，将etc目录及其所有文件打包成etc.zip
zip -rq etc.zip /etc			-q代表静默输出，压缩过程不会显示，递归压缩，
								将etc目录及其所有文件打包成etc.zip
zip -T etc.zip					检查压缩包是否完整

unzip 			解压zip格式压缩包
	-d			指定解压目录
	-l			查看压缩包内容
	-t			检查压缩包里的内容是否完整
	-q			静默输出
```

```
du -sh /etc			查看etc目录及其下所有文件的大小
```

**使用gzip**

```
gzip test			只能压缩test文件，不能压缩目录，源test文件删除
gzip -r sgt			压缩sgt目录下的所有文件，sgt目录不能被压缩，只是把sgt下所有文件一个一个变成gz压缩包了
gzip -d test.gz		-d是解压
```

```
zcat test.gz	查看压缩包文件
```

**使用bzip2**

```
只能压缩文件，不能压缩目录，压缩完源文件会被删除
bzip2 test			压缩
bzip2 -d test.bz2	解压
bzcat test.bz2		查看
```

##### tar

```
打包工具
选项
	z			使用gzip格式压缩
	c			创建压缩包
	f			指定压缩包名
	组合：zcf
	-t			查看压缩包里的内容
	-h			打包软链接
	--exclude	排除
	--exclude-from=xxx	指定排除列表文件
	X			指定排除列表文件
	
tar zcvf test.tar.gz test				对test文件进行打包，选项v代表显示打包过程
tar zcf etc.tar.gz /etc

tar xf etc.tar.gz						x:解压,f指定解压文件
tar xf etc.tar.gz -C	/opt/			C指定解压路径
tar zcf root/etc.tar.gz etc/			解除压缩时删根操作	
tar zcPf etc1.tar.gz /etc				-P使用绝对路径打包
tar jcf etc.bz2 /etc					使用bz2方式打包
tar Jcf etc.xz /etc						使用xz方式打包
tar czf etc.tar.gz --exclude=/etc/hosts	/etc 排除单个文件打包
tar czf etc.tar.gz --exclude=/etc/{hosts,hostname} /etc	排除单个文件打包
只要使用tar打包的都能使用其解压
```

##### tar与find组合使用

```
find /var/log/ -name '*.log' | xargs tar log.tar.gz

错误打包方式，不可取
find /var/log -name 'l=*log' -exec tar czf log1.tar.gz {} \;

tar czf log1.tar.gz $(find /var/log -name 'l=*log')
```

##### find跟du组合

```
find /etc -type d -maxdepth 1 | sed -r 's#(.*)# du -sh \1#g' |bash
ls -d /* | xargs du -sh
```

##### 练习

```
1.列出linux常用打包工具并写相应的压缩、解压缩参数

2.如何用gzip命令对文件进行压缩、解压缩，给出实践步骤。
gzip test
gzip -d test.gz
3.如何用zip命令对文件以及目录进行压缩、解压缩，给出实践步骤。
zip -r xxx.zip /tmp/xxx
unzip -qd /tmp/ xxx.zip
4.创建一个自己名字的文件至/opt目录，给出实践步骤。
touch /tmp/a.txt     
5.打包opt/整个目录，并命名为opt_2019_08_19.tar.gz，给出实践步骤。
tar zcvf opt_2019_08_19.tar.gz opt/
tar zcvf opt_$(date +%Y_%m_%d).tar.gz /opt
6.查看打包好的opt_2019_08_19.tar.gz里的文件，给出实践步骤。
tar -tf opt_2019_08_19.tar.gz
7.将打包好的opt_2019_08_19.tar.gz内容指定解压至/tmp目录，并给出实践步骤。
tar xf opt_2019_08_19.tar.gz -C /tmp
8.打包以当前主机名+ip的命名方式的压缩包: 比如: www.oldboyedu.com_2019-08-19_10.0.0.99.tar.gz ,压缩/opt目录即可
tar zcvf opt_$(hostname)_$(date +%Y_%m_%d)_$(ifconfig eth0 | awk 'NR==2{print $2}').tar.gz /opt
```

#### rpm包

```
noarch 适用于不同的CPU
el7 使用与centOS7
```

```
挂载镜像
选项
	rpm -qi xxx					显示软件包的详细信息
	rpm -qa xxx					查看软件包是否安装
	rpm -qc xxx					查询软件包的配置文件
	rpm -qf $(which ifconfig)	搜索命令属于哪个软件包，前提是本地要有这个命令
	rpm -qf /usr/sbin/ifconfig	同上
mount  /dev/cdrom   /mnt
rpm -e tree		-e代表删除，删除tree包
rpm	-ivh 安装包路径.***rpm				i(install)安装 v过程 h进度
rpm安装不会安装依赖，需要自行手动安装依赖，
所以一般用yum安装，他会自动安装需要的依赖
yum install -y ***			-y免交互
yum本地安装
yum localinstall -y 文件路径
yum provides ping	查看命令所属软件包
yum search ping		搜寻软件包
yum check-update	查询可更新软件包
yum update tree		更新软件包
yum list			查看可安装的所有软件包
yum list installed	查看系统中已经安装的包
clean				清空缓存
	all				清空所有
	packages		清空所有软件包
yum info tree		查看软件包的详细信息
yum history			查看yum的历史事务
yum remove tree		删除tree
yum reinstall tree	如果配置文件被删除的时候，可以重新安装

head /etc/yum.conf
cachedir = ***		缓存生成路径
keepcache=1			开启缓存
```

##### 源码编译

```
make
make install

操作步骤
下载源码包
wget 下载地址
解压
tar xf ***.gz
执行
./configure --prefix=/app/nginx-1.16 --with-http_ssl_module
yum install gcc* -y

yum install -y pcre pcre-devel
yum install openssl openssl-devel -y
echo $?			返回值为0的话说明成功
make
make install
ln -s /app/nginx-1.16 /app/nginx
/app/nginx/sbing/nginx
ss -lntp|grep 80
关闭防火墙
systemctl status firewalld
systemctl stop firewalld
systemctl disable firewalld
```

