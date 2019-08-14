# Nginx静态服务配置---详解root和alias指令

[![96](md_img/fb52e0e23e6b.jpg)](https://www.jianshu.com/u/5qrPPM) 

### 静态文件

Nginx以其高性能著称，常用与做前端反向代理服务器。同时nginx也是一个高性能的静态文件服务器。通常都会把应用的静态文件使用nginx处理。

配置nginx的静态文件有两个指令，一个 root 和一个 alias。对于这两个指令，是否需要在路径的后面加上斜杠，经常容易让人犯晕，本文通过尝试不同的匹配规则，归纳了一个比较通用的配置方式。

### 基本配置

与[简明 Nginx Location Url 配置笔记](https://www.jianshu.com/p/e154c2ef002f)一文关于location url配置的实验一样，本文也使用vagrant虚拟机里的nginx。其基本配置如下：

/etc/nginx/sites-enabled/pro.conf

```
server {
        listen 80 default_server;


        server_name localhost;

        access_log /var/log/nginx/pro/access.log;
        error_log /var/log/nginx/pro/error.log;

        error_page 404 /404.html;

        root /vagrant/pro;
        index index.html index.htm;

}
```

项目的目录如下：

```
☁  pro  tree
.
├── 403.html
├── 404.html
├── index.html
├── static
│   ├── flask
│   │   └── m.png
│   └── stc.jpg
└── upload
    └── up.png

3 directories, 6 files
```

分别有两个静态文件夹，一个是static，另外一个是upload。

### 初识root

root 是指定项目的根目录，适用与server和location。可以指定多个，如果locaiton没有指定，会往其外层的server或http中寻找继承。

访问`http://192.168.33.10/static/stc.jpg` 会发现图片已经返回。我们还尚未配置 location，为啥会正确的找到文件？学习`root`或者`alias`指令的时候，最好的办法是给文件拓展名加上一个字符，使得该文件在硬盘中不存在，那么就能从`nginx`的`error.log`中看到nginx寻找文件的方式。

访问 `http://192.168.33.10/static/stc.jpgx`，然后查看 `/var/log/nginx/pro/error.log`文件，可以看到如下的错误信息：

```
2016/09/28 07:41:48 [error] 4416#0: *70 open() "/vagrant/pro/static/stc.jpgx" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /static/stc.jpgx HTTP/1.1", host: "192.168.33.10"
```

即`/vagrant/pro/static/stc.jpgx` 文件不存在。的确我们没有这个文件。如果文件名正确，就能访问，原因是由于在server中指定了`root /vagrant/pro`，此时的nginx就在该目录下寻找文件，而url上的地址，正好和文件的路径一致

```
 http://192.168.33.10  /static/stc.jpg 
 /vagrant/pro          /static/stc.jpg
```

由此可以猜想，nginx中root指令的地址，其实是替换了**匹配后**的url中的host。

### root指令

为了验证上面的猜想，需要多写几个location做实验。添加一个location配置如下：

```
location ^~ /static {
    root /vagrant/pro/static;
}
```

再次访问`http://192.168.33.10/static/stc.jpg`，发现并不能显示图片了，查看error.log 返回如下：

```
2016/09/28 07:48:57 [error] 5978#0: *71 open() "/vagrant/pro/static/static/stc.jpg" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /static/stc.jpg HTTP/1.1", host: "192.168.33.10"
```

nginx把地址识别成`/vargrant/pro/static/static/stc.jpg`多了一个static，套用上面的规则，其组合为`192.168.33.10` == `/vagrant/pro/static` ，url是`/static/stc.jpg`。置换可以得到`/vagrant/pro/static` + `/static/stc.jpg`。与错误的error一致。解决方案就是把root中的static去掉，马上就能访问图片了。

既然是那么把文件夹`static`命名为`stc`，其结果又会怎样？

```
location ^~ /static {
    root /vagrant/pro;
}
```

访问 `http://192.168.33.10/static/stc.jpg` 得到错误：

```
2016/09/28 07:54:46 [error] 5992#0: *73 open() "/vagrant/pro/static/stc.jpg" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /static/stc.jpg HTTP/1.1", host: "192.168.33.10"
```

计算路径`/vagrant/pro` + `/static/stc.jpg`， 找不到`/vagrant/pro/static/stc.jpg`文件，符合之前所说的规则，尝试修改location：

```
location ^~ /stc {
    root /vagrant/pro;
}
```

因为url变了，访问`http://192.168.33.10/stc/stc.jpg`，才能够找到图片。现在把stc文件夹变回static。

### root 与 斜杠

很多人会疑惑，路径最后的斜杠`/`是否要加呢？location中的static后面的斜杠，和匹配后的url有关，不再赘述。root中的路径的斜杠`/`可以再通过实验确定。把location配置如下：

```
location ^~ /static/ {
   root /vagrant/pro/;
}
```

访问`http://192.168.33.10/static/stc.jpg` 一切正常，访问`http://192.168.33.10/static/stc.jpg`，error为找不到"/vagrant/pro/static/stc.jpgs"文件。

如果按照root替换host的规则，那么替换过程为

`/vagrant/pro/` + `/static/stc.jpg` == `/vagrant/pro//static/stc.jpg`。在*nix系统中， 多个斜杠和一个斜杠是等价的，也就是 `/vagrant/pro//static/stc.jpg`与`/vagrant/pro/static/stc.jpg`一样。

这样一来，root路径后面的斜杠，加与不加效果都一样。既然如此，肯定有人会想到这么配置：

```
location ^~ static/ {
    root /vagrant/pro;
}
```

如果安装之前上面的即算法，那么应该是 `/vagrant/pro` + `static/stc.jpg`，相加的应该是`/vagrant/prostatic/stc.jpg`，按理说应该是错误，可是实际上却能访问图片。咄咄怪事？

如果对前文nginx location的url匹配规则了解的话，应该看出来了其实 `^~ static/`并不能匹配。修改 location

```
location ^~ static/ {
    rewrite ^ http://google.com;
   # root /vagrant/pro;
}
```

访问`http://192.168.33.10/static/stc.jpg`依然可以得到图片，没有跳转google，说明并没有匹配`^~ static/`。

其实原理也很简单，还记得我们第一次实验，当时尚未配置location，也同样可以返回图片。没错，尽管`^~ static/`没有匹配，而外层的server定义了root为`/vagrant/pro`，因此搜索图片正常返回，再注释外层的root，再一次访问。此时会得到一个404，查看error如下：

```
2016/09/28 08:18:15 [error] 6227#0: *82 open() "/usr/share/nginx/html/static/stc.jpg" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /static/stc.jpg HTTP/1.1", host: "192.168.33.10"
```

`/usr/share/nginx/html/static/stc.jpg`，说明即使没有指定root，nginx默认也有一个root，`/usr/share/nginx/html`。当然，这个配置和 `^~ static/`没有关系。

如果`~ static/stc.jpgs?` 那么就能命中，此时访问图片，依然能够正确的解析，因此，并不存在 `/vagrant/pro` + `static/stc.jpg`这种情况。理解这里的关键是 root替换host，并加上匹配后的url，匹配后的url当然包括前面的斜杠，匹配部分的url则不会。

对于 `~ static/stc.jpgs?`模式，访问url`http://192.168.33.10/static/stc.jpg`

- 匹配后的url为 /static/stc.jpg
- 匹配部分的url为 static/stc.jpg

掌握这个很重要，直接关系到后面`alias`指令与斜杠的关系。

对于root指令，我们可以归纳。

1. 对于匹配后的url地址，将匹配的location中的root路径替换访问url的host即得到文件的真实地址。（多个斜杠其实等价于一个斜杠）
2. 如果不匹配location，则寻找更外层的root做替换。
3. root指令最后的斜杠可加可不加。

### alias指令

对于root，操作上很简单，只要把root地址替换host后就是文件在硬盘路径（真实地址）。对于alise，它并不是替换匹配后的url地址，而是替换匹配部分的url。alias指令也可以有多个。

添加一个location，和root的方式几乎一样：

```
location ^~ /upload {
   alias /vagrant/pro;
}
```

访问`http://192.168.33.10/upload/up.png`并没有图片，查看error得到：

```
2016/09/28 08:36:18 [error] 6312#0: *90 open() "/vagrant/pro/up.png" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /upload/up.png HTTP/1.1", host: "192.168.33.10"
```

可见 alias的模式并不是`/vagrant/pro` + `/upload/up.png`，而是 `/vagrant/pro` + `/up.png`。

alias这个词在计算机里很常用，字面意思是“别名”，顾名思议就是换一个名字啦。实际替换规则就是把匹配的url地址，换成alias中的路径即可。例如上述的例子替换过程可以模拟如下：

| 过程           | 模式或url                                                    |
| -------------- | ------------------------------------------------------------ |
| url模式        | ^~ /upload                                                   |
| alias路径      | /vagrant/pro                                                 |
| 访问地址       | [http://192.168.33.10/upload/up.png](https://link.jianshu.com/?t=http://192.168.33.10/upload/up.png) |
| 匹配部分的地址 | /upload + /up.png                                            |
| 替换           | /upload == /vagrant/pro                                      |
| 结果           | /vagrant/pro + /up.png                                       |

为了修改图片的访问，修改locaton如下：

```
location ^~ /upload {
    alias /vagrant/pro/upload;
}
```

此时访问`http://192.168.33.10/upload/up.png`就能得到正确的图片啦，仿造上面的计算过程为：

| 过程           | 模式或url                                                    |
| -------------- | ------------------------------------------------------------ |
| url模式        | ^~ /upload                                                   |
| alias路径      | /vagrant/pro/upload                                          |
| 访问地址       | [http://192.168.33.10/upload/up.png](https://link.jianshu.com/?t=http://192.168.33.10/upload/up.png) |
| 匹配部分的地址 | /upload + /up.png                                            |
| 替换           | /upload == /vagrant/pro/upload                               |
| 结果           | /vagrant/pro/upload + /up.png                                |

从结果可以看出，正确的找到了文件路径，如果alias指令路径加上斜杠，那么计算处理的文件路径为：

```
/upload == /vagrant/pro/upload
/vagrant/pro/upload/ + /up.png
```

多个斜杠是合法的。等价于一个斜杠的情况。

下面修改locaiton如下：

```
location ^~ /upload/ {
   alias /vagrant/pro/upload;
}
```

此时匹配时的url则变成 `/upload/ + up.jpg`, 那么置换的结果为 `/vagrant/pro/upload` + `up.png`，而`/vagrant/pro/uploadup.png`的路径是非法的，从error中也能看到置换的错误：

```
2016/09/28 08:52:44 [error] 6452#0: *92 open() "/vagrant/pro/uploadup.png" failed (2: No such file or directory), client: 192.168.33.1, server: localhost, request: "GET /upload/up.png HTTP/1.1", host: "192.168.33.10"
```

解决办法也很简单，把`/vagrant/pro/upload` 改成 `/vagrant/pro/upload/`即可。由此可见，alias最后的斜杠并不像root指令那样可有可无，是否需要，取决于配合loacation的url匹配模式。

前文root模式中，考虑了没有根的斜杠（`~ static/stc.jpgs?`）这种情况，alias情况下会很难捕捉错误。如果locaion配置如下：

```
location ^~ upload/ {
      alias /vagrant/pro/upload/;
}
```

替换置换的文件路径应该为 `/vagrant/pro/upload/up.png`，可是实际测试中，这样配置alias，会一直导致一个301的重定向，如果alias目录没有打开autoindex，则会抛出一个403错误。具体情况尚未知晓，不知道是不是nginx的bug。为了避免这种情况，使用alias的时候，尽量不要配置location为 `^~ upload/`的模式，并且不从根指定url，还是显得不伦不类。

alise作为别名，比起root的一大好处就是不一定要url上的路径和文件路径一样，因为alise并不是替换host，而是替换匹配部分的host。修改配置如下：

```
location ^~ /upload/ {
    alias /vagrant/pro/static/;
}
```

访问 `http://192.168.33.10/upload/stc.jpg`或者 `http://192.168.33.10/upload/flask/m.png`都能正确的访问到static目录下的文件，尽管url上是upload。

替换规则也很简单，`/upload/` == `/vagrant/pro/static/` 得到 `/vagrant/pro/static/` + `stc.jpg` 或 `/vagrant/pro/static/` + `flask/m.png`。

### 总结

nginx的静态文件配置中，root和alias指令都能实现。为了避免混淆，尽量不要写没有根路径的url模式，即避免 `static/`这样的开头，**根路径**的**斜杠**需要保留，没有根路径其实也很奇怪。

root和alias的区别主要在于替换的部分，root模式中，会把root配置的路径替换匹配后的url中的host。alias则把他指定的路径，替换url中匹配的部分。指令中的斜杠对于root指令没有影响，对于alise则按照替换规则匹配即可。

root 指令

```
location /dir/ 
root root_path ->  http://host/dir/file.txt  -> root_path/dir/file.txt
```

alias 指令

```
location /dir
alias alias_path ->  http://host /dir /file.txt  -> alias_path/file.txt

location /dir/ 
alias alias_path/ ->  http://host /dir/ file.txt  -> alias_path/file.txt
```

了解了root和alise之后，通常最佳实际是配置一个项目的根root，其他的文件夹则使用alias，毕竟alias更加灵活