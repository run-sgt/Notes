## 第二章 Linux操作系统及历史介绍

2019-07-11 分类：[运维基础](https://www.increase93.com/?cat=5) 阅读(89) 评论(0)

1. ## Linux简介

   1. ### 什么是操作系统？

如果被问到什么是操作系统，可能很多的初学者都会一脸茫然。虽然我们都知道平时一直在用的Windows7/8/10,其实就是操作系统，却无法准确给出操作系统的定义或者向提问者解释清楚什么是操作系统。

操作系统，英文名称Operating System，简称OS，是计算机系统中必不可少的基础系统软件，它是应用程序运行以及用户操作必备的基础环境支撑，是计算机系统的核心。

简单理解操作系统就是一个人与计算机硬件之间的中介。打个比喻，没有操作系统的机器就像是没有用的砖头一样，而有操作系统的机器就是可以玩的砖头。操作系统就是处于用户与计算机系统硬件之间用于传递信息的系统程序软件。例如：操作系统会在接收到用户输入的信息后，将其传给计算机系统硬件核心进行处理，然后再把计算机系统硬件的处理结果返回给使用者。

操作系统的作用是管理和控制计算机系统中的硬件和软件资源，例如，它负责直接管理计算机系统的各种硬件资源，比如对CPU、内存、磁盘等的管理，同时对系统资源供需的优先次序进行管理。操作系统还可以控制设备的输入、输出以及操作网络与管理文件系统等事务。同时，它也负责对计算机系统中各类软件资源的管理。例如各类应用软件的安装、运行环境设置等。下图给出了操作系统与计算机硬件、软件之间的关系示意图。

![img](D:\Notes\md_img\071119_0355_Linux1.png)

**图-操作系统与计算机软硬件关系示意图**

下图是简单理解操作系统作用的示意图。人机交互的窗口（你如何操作电脑的）。

![img](D:\Notes\md_img\071119_0355_Linux2.png)

**图-操作系统作用示意图**

目前PC（Intel x86系列）计算机上比较常见的操作系统有Windows、Linux、DOS、Unix等。Mac OS是苹果电脑Macintosh机器的专用操作系统，从本质上将，Mac OS 也是UNIX的一个变体。

请思考一下世界上什么操作系统使用率最高？

1. ### 什么是Linux？

1.如何快速了解什么是Linux？

类似windows，Linux也是一个操作系统软件，但不同的是，Windows：收费，不开源，主要用于日常办公、游戏、娱乐多一些；而linux是一套开放源代码程序的、并可以自由传播的类Unix操作系统软件，多用户、多任务并且支持多线程和多CPU的操作系统。总结Linux就是一个操作系统,主要用于企业环境。

Linux系统主要被应用于服务器端、嵌入式开发和个人PC桌面3大领域，其中服务器端领域是重中之重。

我们熟知的大型、超大型互联网企业（百度，Sina，淘宝等）都在使用Linux系统作为其服务器端的程序运行平台，全球及国内排名前1000的网站中，90%的企业使用的主流系统都是Linux系统。

从上面的内容可以看出，linux操作系统之所以如此流行，是因为它具有如下一些优秀特点：

是开放源代码的程序文件，可自由修改

Unix系统兼容，具备几乎所有Unix的优秀特性

可自由传播，无任何商业化版权之约

适合Intel等x86 CPU系列架构的计算机

2.学完后可以掌握哪些技术？

​    学习完 Linux 云计算运维课程后，学生可以掌握到:

具备中小企业服务器的基础运维能力、自动化运维，例如:电商、游戏、金融、物流等

具备中小企业数据库运维、掌管公司核心命脉系统，例如:银行、金融、电商

具备中小企业公有云运维的能力，例如:公司使用的是阿里云、腾讯云等

具备中小企业企业集群架构维护，例如:上百台、甚至上千台规模的架构维护与实施

具备中小企业企业代码发布能力，例如:如何快速对数百台服务器进行项目迭代

具备企业私有云平台的构建及运维，例如:构建企业私有云平台

能够解决运维过程中出现的各种问题，例如:网站访问慢、数据恢复、业务扩展、DDOS攻击等、

肯定有人说: 为什么都是胜任中小企业的运维能力，难道不能胜任中大型公司的运维能力吗？

3.课程学完能达到什么程度，或者说拿到多少钱？

此处建议打开拉钩、BOSS直聘、以及往期学长的Offer

1. ## Linux起源

   1. ### Unix历史

我觉得既然是历史，那就让他成为历史吧，因为我根本记不住历史。(因为我不是导游，不靠记历史赚钱)。虽然历史不重要，但是还是需要了解Linux在发展过程中的一些重要人物。

说到linux的起源，就不得不提到linux之前的Unix系统。Unix系统于1969年在AT&T（美国电话电报公司是一家美国电信公司，成立于1877年，曾长期垄断美国长途和本地电话市场。）的贝尔实验室诞生，20世纪70年代，它逐步盛行，这期间，又产生了一个比较重要的分支，就是大约1977年诞生的BSD（Berkeley Software Distribution）系统。从BSD系统开始，各大厂商及商业公司开始了根据自身公司的硬件架构，并以BSD系统为基础进行Unix系统的研发，从而产生了各种版本的Unix系统，例如：SUN公司的Solaris，IBM公司的AIX，HP公司的HP UNIX等。

下图给出了Unix系统诞生、发展的时间及版本分支介绍，供读者参考。

![img](D:\Notes\md_img\071119_0355_Linux3.png)

**图-Unix诞生及版本分支发展简略图解**

在上图中可以看到，本章的"主人公"Linux系统，诞生于1991年左右，因此，可以说Linux是从Unix发展而来的。

1. ### Unix的五大优秀特性

细心的同学应该注意到前面曾讲过"类Unix操作系统兼容产品"或"类Unix操作系统软件"吧？可为什么都要开发类Unix系统呢？那是因为Unix是一个非常棒的操作系统，它很像一个非常聪明伶俐但不太听话的孩子，而开发者们在开发系统时，一方面想要继承它的"聪明伶俐"，另一方面又想改善它不听话的一面，故而会有如此考虑。那么Unix有哪些"聪明伶俐"的特点呢？

- 技术成熟，可靠性高

使用Unix系统时，即使连续运行若干年也无需重启，它依然可以工作的非常好。毫不夸张的说，只要计算机硬件不坏，Unix就很难会出现问题。

- 极强的可伸缩性

Unix支持的CPU处理器体系架构非常多，包括Intel/AMD及HP-PA、MIPS、PowerPC、UltraSPARC、ALPHA等RISC芯片，以及SMP、MPP等技术。

提示：可能是由于早期各大厂商都基于Unix进行适合自己的硬件开发，因此，Unix支持的CPU架构才更多。

- 强大的网络功能

Internet互联网最重要的协议TCP/IP就是在Unix上开发和发展起来的。此外，Unix还支持非常多的常用的网络通信协议，如NFS、DCE、IPX/SPX、SLIP、PPP等。

- 强大的数据库支持能力

Oracle、DB2、Sybase、Informix等大型数据库，都把Unix作为其主要的数据库开发和运行平台，一直到目前为止，依然如此。

- 强大的开发功能

正是Unix促使了C语言的诞生，并相互促进与发展，成为当时工程师的首选操作系统和开发环境。互联网早期有重大意义的软件新技术的出现几乎都在Unix上，例如：TCP/IP、WWW、Java、XML等。

1. ### Unix操作系统的革命

70年代中后期，由于各厂商及商业公司开发的Unix及内置软件都是针对自己公司特定硬件的，因此在其他公司的硬件上基本上无法直接运行，而且当时没有人对开发基于x86架构的CPU的系统感兴趣。另外，70年代末，Unix又面临了突如其来的被AT&T回收版权的重大问题，特别是要求禁止对学生群体提供Unix系统源代码，这样的问题一度引起了当时Unix业界的恐慌，也因此产生了商业纠纷。

由于Unix面临版权回收问题，以及代码不开源等的问题，这直接或间接的导致了新的类Unix系统的诞生以及自由软件运动的建立和发展。

1984年，Richard Stallman（斯托曼）发起了开发自由软件的运动，并成立了自由软件基金会(Free Software Foundation，FSF)和GNU项目。当时发起这个自由软件运动和创建GNU项目的目的其实很简单，就是想开发一个类似Unix系统、并且是自由软件的完整操作系统，也就是要解决70年代末Unix版权问题以及软件源代码面临闭源的问题，这个系统叫做GNU操作系统。

需要补充说明的是：这个GNU系统后来没有流行起来。现在的GNU系统通常是使用Linux系统的内核，以及使用了GNU项目贡献的一些组件加上其它相关程序组成，这样的组合被称为GNU/Linux操作系统。

也是在80年代初期，同样是由于之前的Unix系统版权和源代码限制等问题，使得当时大学里教学Unix系统的束缚很大。因此，当时的一个大学的教授，名字为Andrew Tanenbaum（谭宁邦），于1984年（大概）开始着手编写新的用于教学的Unix系统，目标是开发新的Unix系统，尽可能的和原有的Unix系统兼容，并且可以运行于x86 PC平台上面的系统，这个系统的名字为Minix。

不过，由于谭宁邦开发的这个Minix系统的目的只是用于教学，因此，Minix系统的功能无法满足商用的需求，但是Minix的产生对于Linux的诞生又是至关重要的一个部分。

1. ### Linux诞生

linux系统的诞生开始于芬兰赫尔辛基大学的一位计算机系的学生，名字为Linus Torvalds。在大学期间，他接触到了学校的Unix系统，但是当时的Unix系统仅为一台主机，且对应了多个终端，使用时存在操作等待时间很长等一些问题，无法满足年轻的Linus Torvalds的需求。因此他就萌生了自己开发一个Unix的想法，于是不久，他就找到了Minix操作系统，和我们现在一样，他把Minix系统安装到了他的I386个人计算机上。此后，Linus Torvalds又开始陆续阅读了Minix系统的源代码，从Minix系统中学到了很多重要的系统核心程序设计理念和设计思想，从而逐步开始了linux系统雏形的设计和开发。

Linux的标志和吉祥物为一只名字叫作Tux的企鹅——Torvalds'Unix，下图所示。

![img](D:\Notes\md_img\071119_0355_Linux4.png)

**图-linux标志**

1. ### linux的发展历程

1984年，Andrew S. Tanenbaum（谭宁邦）开发了用于教学的Unix系统，命名为Minix。

1989年，Andrew S. Tanenbaum将MINIX系统运行于x86的PC计算机平台。

1990年，芬兰赫尔辛基大学学生Linus Torvalds首次接触Minix系统。

1991年，Linus Torvalds开始在MINIX上编写各种驱动程序等操作系统内核组件。

1991年底，Linus Torvalds 公开了Linux内核源码0.02版（[http://www.kernel.org）。
](http://www.kernel.org/)

[注意，这里公开的Linux内核源码并不是我们现在使用的Linux系统的全部，而仅仅是Linux内核kernel部分的代码。
](http://www.kernel.org/)

[1993年，Linux 1.0版发行，Linux转向GPL版权协议。
1994年，Linux的第一个商业发行版Slackware问世。
1995年，Red hat软件公司成立，同年发布了Red hat Linux 2.0。
1996年，美国国家标准技术局的计算机系统实验室确认Linux 版本1.2.13 （由Open Linux公司打包）符合POSIX标准。
1997年，Red hat Linux 5.0发布，它支持Intel、alpha和sparc平台和大多数的应用软件。极其简单易用的RPM模块化的安装、配置和卸载工具，使程序的安装可在15分钟内完成。软件升级也方便了。
1999年，Linux的简体中文发行版问世。
2003年4月，Red hat Linux 9.0发布。重点放在改善桌面应用方面，包括改进安装过程、更好的字体浏览、更好的打印服务等。2003年，Red hat Linux 市场份额超过80%。
2004年4月，Red hat 公司正式停止对Red hat Linux 9.0版本的支持，标志着Red hat Linux 的正式完结。原本的桌面版Red hat Linux 发行包则与来自民间的Fedora计划合并，成为Fedora Core发行版本。Red hat 公司不再开发桌面版的linux发行包，而将全部力量集中在服务器版的开发上，也就是Red Hat Enterprise Linux版。
2005年10月，RHEL4（Red Hat Enterprise Linux 4 ）发布
2007年3月，主流版本RHEL5(Red Hat Enterprise Linux 5 )发布，CentOS系统开始在中国互联网公司流行。
2010年4月，RHEL6（Red Hat Enterprise Linux 6.0 ）BETA测试版发布。
2014年6月，Red Hat Enterprise Linux 7.0发布，RHEL7.0版本和RHEL6.0以前的版本有了较大的变化，例如：内核版本升级到3.10以上，系统的启动和基本管理变化较大，文件系统也有传统的ext改为xfs，同时在大数据、云计算、docker方面做了很大优化。
Linux发展历程中相关人物总结我们一定要向前辈们致以深深地敬意，没有他们，就没有今天的Linux这样优秀系统存在了（下图所示）。
![img](D:\Notes\md_img\071119_0355_Linux5.png)![img](D:\Notes\md_img\071119_0355_Linux6.jpg)![img](D:\Notes\md_img\071119_0355_Linux7.jpg)![img](D:\Notes\md_img\071119_0355_Linux8.jpg)unix诞（蛋）生于 1969年的贝尔实验室Andrew S. Tanenbaum（谭宁邦）
Minix开发者，用于教学
1984Richard Stallman（斯托曼）
自由软件（FSF）与GNU项目发起人
GPL（通用公共许可）协议
1984Linus Torvalds（托瓦兹）
Linux之父
linux内核
1991**图-Linux系统诞生发展过程中关键代表人物**Linux核心概念及特点自由软件简单地理解，自由软件的核心就是没有商业化软件版权制约，源代码开放，可无约束自由传播。
注意：自由软件强调的是权利问题，而非是否免费的问题。大家一定要理解这个概念，自由软件中的自由是"言论自由"中的"自由"，而不是"免费啤酒"中的"免费"。
自由意味着freedom，而免费意味着free，这是完全不同的概念。例如：Red Hat Linux自由但不免费，CentOS Linux是自由且免费的。
自由软件关乎使用者运行、复制、发布、研究、修改和改进该软件的自由。更精确的说，自由软件赋予软件使用者四种自由：
不论目的为何，有运行该软件的自由。
有研究该软件如何运行，以及按需改写该软件的自由。当然，取得该软件源代码为达成此目的之前提。
有重新发布和拷贝的自由。
有改进该软件，以及向公众发布改进的自由，这样整个社群都可受惠。同样，取得该软件的源码为达成此目的之前提。
自由软件基金会FSFFSF（Free Software Foundation）的中文意思是自由软件基金会，是Richard Stallman于1984年发起和创办的。FSF的主要项目是GNU项目。GNU项目本身产生的主要软件包括：Emacs编辑软件、gcc编译软件、bash命令解释程序和编程语言、内核hurd(开发中)，以及gawk 等。
GNUGNU的全称为GNU is not unix，意思是"GNU不是UNIX"，GNU计划，又称革奴计划，是由Richard Stallman在1984年公开发起的，是FSF的主要项目。前面已经提到过，这个项目的目标是建立一套完全自由的和可移植的类Unix操作系统。
但是GNU自己的内核Hurd仍在开发中，离实用还有一定的距离。现在的GNU系统通常是使用Linux系统的内核、加上GNU项目贡献的一些组件，以及其他相关程序组成的，这样的组合被称为GNU/Linux操作系统。
到1991年Linux内核发布的时候，GNU项目已经完成了除系统内核之外的各种必备软件的开发。在Linus Torvalds和其他开发人员的努力下， GNU项目的部分组件又运行到了Linux内核之上，例如：GNU项目里的Emacs、gcc、bash、gawk等，至今都是Linux系统中很重要的基础软件。
![img](D:\Notes\md_img\071119_0355_Linux9.png)图-GNU项目logo
GPLGPL全称为General Public License，中文名为通用公共许可，是一个最著名的开源许可协议，开源社区最著名的Linux内核就是在GPL许可下发布的。GPL许可是由自由软件基金会（Free Software foundation）创建的。
1984年，Richard Stallman发起开发自由软件的运动后不久，在其他人的协作下，他创立了通用公共许可证（GPL），这对推动自由软件的发展起了至关重要的作用，那么，这个GPL到底是什么意思呢？
简单的理解，GPL许可的核心，是保证任何人有共享和修改自由软件的自由，任何人有权取得、修改和重新发布自由软件的源代码权利，但都必须同时给出具体更改的源代码。
虽然整个Linux内核是基于GNU通用公共许可的，但是Linux内核并不是GNU计划的一部分，这一点请读者不要混淆。
5.LGPL
LGPL（Lesser General Public License）相对于GPL较为宽松，允许不公开全部源代码，为基于linux平台开发商业软件提欧了更广阔的空间。对于该知识点，有兴趣的同学可到网上查询相关信息。
Linux系统组成（如下图表所示）Linux内核GNU组件（gcc,bash）其他必要应用程序开发者Linus Torvalds项目发起人Richard Stallman(斯托曼)BSD Unix和X Windows以及成千上万的程序员表-Linux系统各组成部分的贡献人员
下图为Linux系统的核心组成原理示意图。
![img](D:\Notes\md_img\071119_0355_Linux10.png)**图-Linux操作系统组成原理示意图**Linux为什么受欢迎？Linux以高效和灵活著称。linux运行与PC上，可以实现几乎全部的Unix特性，同时具有多任务、多用户的能力，支持多线程、多CPU。linux是在GNU通用公共许可（GPL）权限下免费获得的，是一个符合POSIX标准的操作系统。
Linux操作系统软件包不仅包括完整的Linux操作系统，而且还包括了文本编辑器、高级语言编译器，以及X-Windows图形用户界面等应用软件，使用Linux也可以像使用Windows7、Windows10一样，通过窗口、图标和菜单对系统进行操作，当然，这是Linux个人桌面领域的应用，在服务器端领域绝大多数场景下都还是使用命令行、文本模式操作Linux的。
Linux系统之所以受到广大计算机爱好者的喜爱，主要原因有两个：
一是，Linux属于自由软件，用户不用支付任何费用就可以获得系统和系统的源代码，并且可以根据自己的需要对源代码进行必要的修改，无偿使用，无约束地自由传播。
二是，Linux具有Unix的全部优秀特性，任何使用Unix操作系统或想要学习Unix操作系统的人，都可以通过学习Linux来了解Unix，同样可以获得Unix中的几乎所有优秀功能，并且Linux系统更开放，社区开发和全世界的使用者也更活跃。
Linux更多特点介绍还记得前面我们对操作系统的小结吗？除了那些特点意外，其实Linux还具有如下一些特点：
可以说Linux是Unix在PC上的克隆版，仿Unix内核构建，几乎与Unix指令集完全兼容。
是一个完善的支持多用户、多任务、多进程、多CPU的系统
具有很高的系统稳定性、安全性和可靠。
有完善的网络服务，支持HTTP、FTP、SMTP、POP、SAMBA、SNMP、DNS、DHCP、SSH、TELNET等。
是基于GNU许可，自由开放的系统
有大量的第三方免费应用程序。
得到了众多业界厂商支持，如IBM、Oracle、Intel、HP、MOTO、Google等。
有完善的大型数据库平台，包括Oracle、DB/2、Sybase、MySQL、PostgreSQL等。
有完善的图形用户界面，包括GNOME、KDE等
有完善的开发平台，包括C/C++、Java、Perl等，支持各类图形界面API，如GTK+、QT等。
Linux的应用领域与Windows操作系统软件一样，Linux也是一个操作系统软件。但与Windows不同的是，Linux是一套开放源代码程序的，并可以自由传播的类UNIX操作系统软件，随着信息技术的更新变化，Linux应用领域已趋于广泛。1.IT服务器端
如今的IT服务器领域是Linux、UNIX、Windows三分天下，Linux系统可谓是后起之秀，尤其是近几年，服务器端Linux操作系统不断地扩大着市场份额，每年增长势头迅猛，并对Windows及UNIX服务器市场的地位构成严重的威胁。
Linux作为企业级服务器的应用十分广泛，利用Linux系统可以为企业构架Web服务器、数据库服务器、负载均衡服务器、邮件服务器、DNS服务器、代理服务器(透明网关)、路由器等，不但使企业降低了运营成本，同时还获得了Linux系统带来的高稳定性和高可靠性。
随着Linux在服务器领域的广泛应用，从近几年的发展来看，该系统已经渗透到了电信、金融、政府、教育、银行、石油等各个行业，同时各大硬件厂商也相继支持Linux操作系统。这一切都在表明，Linux在服务器市场的前景是光明的。同时，大型、超大型互联网企业(百度、新浪、淘宝等)都在使用Linux系统作为其服务器端的程序运行平台，全球及国内排名前十的网站使用的几乎都是Linux系统，Linux已经逐步渗透到各个领域的企业里。
2.嵌入式开发平台
由于Linux系统开放源代码，功能强大、可靠、稳定性强、灵活，而且具有极大的伸缩性，再加上它广泛支持大量的微处理器体系结构、硬件设备、图形支持和通信协议，因此，在嵌入式应用的领域里，从因特网设备(路由器、交换机、防火墙、负载均衡器等)到专用的控制系统(自动售货机、手机、PDA、各种家用电器等)，Linux操作系统都有很广阔的应用市场。特别是经过这几年的发展，它已经成功地跻身于主流嵌入式开发平台。例如，在智能手机领域，Android Linux已经在智能手机开发平台牢牢地占据了一席之地。
3.个人PC桌面
所谓个人桌面系统，其实就是我们在办公室使用的个人计算机系统，
例如： Windows XP、Windows 7、MAC等。Linux系统在这方面的支持也已经非常好了，完全可以满足日常的办公及家用需求，例如：浏览器上网浏览(例如：Firefox浏览器)；办公室软件(OpenOffice，兼容微软Office软件)处理数据；收发电子邮件(例如：ThunderBird 软件)；实时通信(例如：QQ 等)；文字编辑(例如：vi、vim、emac)；多媒体应用。
虽然Linux个人桌面系统的支持已经很广泛了，但是在当前的桌面市场份额还远远无法与Windows系统竞争，这其中的障碍可能不在于Linux桌面系统产品本身，而在于用户的使用观念、操作习惯和应用技能，以及曾经在Windows上开发的软件的移植问题。
Linux发行版本操作系统的内核的定义：操作系统的内核是一个管理和控制程序，负责管理计算机的所有物理资源，其中包括。文件系统、内存管理、设备管理和进程管理。
](http://www.kernel.org/)

[Linux内核（kernel）版本主要有4个系列，分别为Linux kernel 2.2、Linux kernel 2.4、Linux kernel 2.6，Linux kernel3.x ，更多更新的内核版本请浏览](http://www.kernel.org/)[https://www.kernel.org/。
](https://www.kernel.org/)

[Linux的发行商包括Slackware、Redhat、Debian、Fedora、TurboLinux、Mandrake、SUSE、CentOS、Ubuntu、红旗、麒麟等。
](https://www.kernel.org/)

[下面来看看其中几个重要的发行版本。
1.Red Hat：Red Hat Linux 9.0的内核为2.4.20。在版本9.0后，Red Hat不再遵循GPL协议，成为收费产品（但仍开源），发展的新版本依次为Red Hat 3.x、Red Hat 4.x、Red Hat 5.x、Red Hat 6.x、Red Hat 7.x。Red Hat Enterprise 6.x
2.Fedora：为Red Hat的一个分支，仍遵循GPL协议，可以认为是Red Hat预发布版。（游戏公测）
3.CentOS (Community Enterprise Operating System)：与redhat几乎一模一样。Red Hat的另一个重要分支，以Red Hat 所发布的源代码重建符合GPL许可协议的Linux系统，即将Red Hat Linux源代码的商标LOGO以及非自由软件部分去除后再编译而成的版本，目前CentOS已被Red Hat公司收购，但仍开源免费。
CentOS Linux是国内互联网公司使用最多的Linux系统版本，也是本的"主人公"，本书后面所有的内容讲解都是基于CentOS这个操作系统的，绝大部分内容几乎无需任何修改同样适合其它操作系统版本。
提示：有关Linux操作系统，记住Redhat、CentOS、Ubuntu、Fedora、SUSE、Debian等即可。Redhat与CentOS的区别和联系，有时会被面试官问到，需要重点了解。
选择适合的Linux系统学习在了解linux版本及应用领域之后，接下来就要定位我们到底该往哪个方向发展了。如果你想 做一个网站的后端运维工程师，那就走服务器领域的路线；如果你想进入嵌入式领域，就要学习嵌入式领域的技能；如果你对桌面linux系统感兴趣，那么可以深入桌面系统领域。选择 的领域不同，要学习和掌握的技能自然就会有差别，因此，这个选择就很重要了。当你选择来这里学习的时候，就已经说明你所选择的IT服务器linux系统领域。没错，我们的课程内容就是基于Linux服务器应用领域的。
Linux发行版本及应用场景（如下表所示）
**Linux发行版版本选择**Linux桌面系统Ubuntu（开发人员开发平台）服务器端linux系统首选Redhat（最火，收服务费）或CentOS（免费,但任何问题自行解决）这两者当中选CentOS如果对安全要求很高Debian或FreeBSD使用数据库高级服务或电子邮件网络用户SUSE（德国多）想新技术，新功能
是redhat和CentOS的测试版或预发布版Fedora
Fedora=稳定之后=>RedHat=去logo去除收费=>CentOS中文红旗linux，麒麟linux选择CentOS Linux的版本
本章讲解的Linux运维技术主要是基于CentOS x86_64 Linux的，绝大部分知识几乎无需任何修改同样也适用于Red Hat Linux等同源或类似Linux系统版本。
windows:
    个人版 windows98、windows2000、windowsXP、windows7、windows8、windows10
    企业版    windowsserver2003、windowsserver2008、windowsserver2012、windowserver2016
linux:
    linux5
linux6 linux6.4、linux6.7、linux6.8、linux6.9
linux7 linux7.1、linux7.2、linux7.3、linux7.4、linux7.5、linux7.6
企业: linux7系统(7.3、7.4)、linux6系统（6.7、6.8、6.9）
教学: linux7.6
下面是CentOS系列版本所对应的内核版本：
CentOS 5系列===>linux 2.4
CentOS 6系列===>linux 2.6
CentOS 7系列===>linux 3.10
面试技巧：大家被面试官问及使用的是什么操作系统时，一定要一次性说出来（系统版本、内核版本、32位还是64位），例如：我的工作中使用的是CentOS 7.5 x86_64位Linux系统，内核版本为3.10.0-862，这样才是一个合格的Linux运维人员的表现。
Linux相关红帽认证介绍![img](D:\Notes\md_img\071119_0355_Linux11.png)1.红帽认证管理员(RHCSA,全称为Red Hat Certified System Administrator)属于红帽Linux的初级入门认证，比较适合Linux兴趣爱好者，需要考生对Linux系统有一定的了解并能够熟练的使用Linux命令来完成以下任务：
能够管理文件、目录、文档以及命令行环境。
能够使用分区、LVM逻辑卷来管理本地存储。
能够安装、更新、维护、配置系统与核心服务。
能够熟练创建、修改、删除用户与用户组，并要会使用LDAP进行集中目录身份认证。
能够熟练配置防火墙以及SELinux来保障系统安全。
2.红帽认证工程师(RHCE,全称为Red Hat Certified Engineer)属于红帽Linux的中级水平认证，考生必需已获得RHCSA认证，难度相对RHCSA更大，适合有基础的Linux运维管理员，主要考察对下列服务的管理与配置能力：
能够熟练配置防火墙规则链与SElinux安全上下文
能够配置静态路由、数据包筛选以及网络地址转换。
能够配置iSCSI互联网小型计算机系统接口服务。
能够编写Shell脚本来批量创建用户、自动完成系统的维护任务。
能够配置Http/https网络服务。
能够配置Ftp文件传输服务
能够配置NFS网络文件系统服务。
能够配置SMB服务器信息服务。
能够配置SMTP简单邮件传输服务。
能够配置Ssh远程管理服务。
能够配置NTP网络时间服务。
3.红帽认证架构师(RHCA,全称为Red Hat Certified Architect)属于红帽Linux的最高级别认证，公认的Linux操作系统顶级认证，目前中国大陆仅通过不到300人（2015年最新数据），考生需要在获得RHCSA与RHCE认证后再完成5门课程的考试才能获得红帽RHCA认证，因此难度最大、备考时间较长、费用也最高（考试费约在1.8-2.1万左右），需要考察学生对红帽卫星服务、红帽系统集群、红帽虚拟化、系统性能调优以及红帽云系统的安装搭建与维护能力。
常⻅问题: ⽼师我到底要不要考⼀个红帽证书，学完咱们这个Linux云计算课程能不能考?](https://www.kernel.org/)



未经允许不得转载！博主QQ：1176494252：[技术笔记分享](https://www.increase93.com/) » [第二章 Linux操作系统及历史介绍](https://www.increase93.com/?p=131)