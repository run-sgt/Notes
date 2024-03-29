在网络环境下，通俗的讲，将IO分为两步： 

```
1.等； 
2.数据搬迁。 
```

如果要想提高IO效率，需要将等的时间降低。

如果要想提高IO效率，需要将等的时间降低。

```
五种IO模型包括：阻塞IO、非阻塞IO、信号驱动IO、IO多路转接、异步IO。其中，前四个被称为同步IO。
```

在介绍五种IO模型时，我会举生活中钓鱼的例子，加深理解。

### 1.阻塞IO（blocking I/O）

```
A拿着一支鱼竿在河边钓鱼，并且一直在鱼竿前等，在等的时候不做其他的事情，十分专心。只有鱼上钩的时，才结束掉等的动作，把鱼钓上来。

在内核将数据准备好之前，系统调用会一直等待所有的套接字，默认的是阻塞方式。 

其实，我们例子中所说的鱼竿就是这一个文件描述符。这个模型是我们最常见的，程序调用和我们编写的基本程序是一致的。

fd=connect();
write(fd);
read(fd);
close(fd);
1
2
3
4
程序的read必须在write之后执行，当write阻塞住了，read就不能执行下去，一直处于等待状态。
```

![](D:\Notes\md_img\blocking io.png)

### 2.非阻塞IO（noblocking I/O）

```
B也在河边钓鱼，但是B不想将自己的所有时间都花费在钓鱼上，在等鱼上钩这个时间段中，B也在做其他的事情（一会看看书，一会读读报纸，一会又去看其他人的钓鱼等），但B在做这些事情的时候，每隔一个固定的时间检查鱼是否上钩。一旦检查到有鱼上钩，就停下手中的事情，把鱼钓上来。 

其实，B在检查鱼竿是否有鱼，是一个轮询的过程。

每次客户询问内核是否有数据准备好，即文件描述符缓冲区是否就绪。当有数据报准备好时，就进行拷贝数据报的操作。当没有数据报准备好时，也不阻塞程序，内核直接返回未准备就绪的信号，等待用户程序的下一个轮寻。

但是，轮寻对于CPU来说是较大的浪费，一般只有在特定的场景下才使用。
```

![](D:\Notes\md_img\no blocking io.png)

### 3.信号驱动IO（signal blocking I/O）

```
C也在河边钓鱼，但与A、B不同的是，C比较聪明，他给鱼竿上挂一个铃铛，当有鱼上钩的时候，这个铃铛就会被碰响，C就会将鱼钓上来。 

信号驱动IO模型，应用进程告诉内核：当数据报准备好的时候，给我发送一个信号，对SIGIO信号进行捕捉，并且调用我的信号处理函数来获取数据报。
```

![](D:\Notes\md_img\signal blocking io.png)

### 4.IO多路转接（I/O multiplexing）

```
D同样也在河边钓鱼，但是D生活水平比较好，D拿了很多的鱼竿，一次性有很多鱼竿在等，D不断的查看每个鱼竿是否有鱼上钩。增加了效率，减少了等待的时间。 

IO多路转接是多了一个select函数，select函数有一个参数是文件描述符集合，对这些文件描述符进行循环监听，当某个文件描述符就绪时，就对这个文件描述符进行处理。

其中，select只负责等，recvfrom只负责拷贝。 
IO多路转接是属于阻塞IO，但可以对多个文件描述符进行阻塞监听，所以效率较阻塞IO的高。
```

![](D:\Notes\md_img\multiplexing io.png)

### 5.异步IO（asynchronous I/O）

```
E也想钓鱼，但E有事情，于是他雇来了F，让F帮他等待鱼上钩，一旦有鱼上钩，F就打电话给E，E就会将鱼钓上去。 

当应用程序调用aio_read时，内核一方面去取数据报内容返回，另一方面将程序控制权还给应用进程，应用进程继续处理其他事情，是一种非阻塞的状态。

当内核中有数据报就绪时，由内核将数据报拷贝到应用程序中，返回aio_read中定义好的函数处理程序。
```

![](D:\Notes\md_img\asynchronous io.png)

---

很少有Linux系统支持，Windows的IOCP就是该模型。 

![](D:\Notes\md_img\all_five io.png)

可以看出，阻塞程度：阻塞IO>非阻塞IO>多路转接IO>信号驱动IO>异步IO，效率是由低到高的


