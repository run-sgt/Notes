## Yum命令使用示例

2019-07-11 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(42) 评论(0)

**Yum: 即Yellowdog Update Modifier,是一种基于rpm的包管理工具**

**yum命令使用示例**

1. ### 显示yum仓库 

   

2. 



显示所有仓库
[root@qiudao ~]# yum repolist all
显示可用的仓库
[root@qiudao ~]# yum repolist enabled
显示不可用的仓库
[root@qiudao ~]# yum repolist disabled



1. 

2. 

3. 

   

   ### 显示应用程序包 

   

4. 



显示所有的程序包
[root@qiudao ~]# yum list
或
[root@qiudao ~]# yum list all
\#支持正则的查询
[root@qiudao ~]# yum list tre?
![img](D:\Notes\md_img\071119_0455_Yum1.png)
显示可安装的程序包
\#显示所有可安装的程序包
[root@qiudao ~]# yum list available
[root@qiudao ~]# yum list available php
显示可更新或已安装的程序包
\#更新
[root@qiudao ~]# yum list updates
\#已安装的程序包
[root@qiudao ~]# yum list installed
显示仓库中最近增加的程序包
[root@qiudao ~]# yum list recent



1. 

2. 

3. 

   

   ### 安装程序包 

   

4. 



\#安装 tree 程序
[root@qiudao ~]# yum install tree



1. 

2. 

3. 

   

   ### 升级程序包 

   

4. 



[root@qiudao ~]# yum update tree



1. 

2. 

3. 

   

   ### 卸载程序包 

   

4. 



[root@qiudao ~]# yum remove tree
或
[root@qiudao ~]# yum erase tree



1. 

2. 

3. 

   

   ### 查看程序包信息 

   

4. 



[root@qiudao ~]# yum info tree



1. 

2. 

3. 

   

   ### 查看文件的来源 

   

4. 



\#查看某一文件来自于那个程序包
[root@qiudao ~]# yum provides ping



1. 

2. 

3. 

   

   ### 清理本地缓存 

   

4. 



[root@qiudao ~]# yum clean all 
\#清楚插件缓存
[root@qiudao ~]# yum clean plugins



1. 

2. 

3. 

   

   ### 构建缓存 

   

4. 



[root@qiudao ~]# yum makecache



1. 

2. 

3. 

   

   ### 如何使用yum缓存已经安装过了的软件包 

   

4. 



\#缓存rpm包方式一、修改yum全局配置文件
[root@qiudao ~]# vim /etc/yum.conf
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=1                  #启动缓存
\#缓存rpm包方式二，只下载不安装
[root@qiudao ~]# yum install httpd -y --downloadonly --downloaddir=/tmp



1. 

2. 

3. 

   

   ### 搜索 

   

4. 



[root@qiudao ~]# yum search php



1. 

2. 

3. 

   

   ### 查看指定包所依赖的capabilities 

   

4. 



[root@qiudao ~]# yum deplist php



1. 

2. 

3. 

   

   ### 查看yum事务历史 

   

4. 



[root@qiudao ~]# yum history



1. 

2. 

3. 

   

   ### 包组相关的命令 

   

4. 



[root@qiudao ~]# yum groupinstall # 安装包组

[root@qiudao ~]# yum groupupdate #更新包组

[root@qiudao ~]# yum grouplist #显示包组

[root@qiudao ~]# yum groupremove #移除包组

[root@qiudao ~]# yum groupinfo #查看包组信息

这些命令和上面的命令用法类似，只是针对于包组而言