# History of Internet Protocols

## 时间线

### ARPAnet - 阿帕网

现代的因特网起源于20世纪60-70年代美国的军工项目: **ARPA**net, (**ARPA, Advanced Research Projects Agency, 高级研究计划署**, 隶属于美国国防部), 目的是连接美国的四个大学, 这个时候的ARPAnet(阿帕网)只是一个非常小型的网络, 在之后提出扩展方案的时候**最多**也只连接了五十几台机器而已.

人们在ARPAnet上感受到了**前所未有的快乐**: 

+ 1. 远程登录, 科学家可以不用亲自前往计算机站点就能访问包含信息的数据库
+ 2. 文件传输, 科研人员可以通过网络复制和保存或者发送文件, 但是不是使用的FTP(文件传输协议), 因为FTP在1971年在RFC-114中被才被提出, 而ARPAnet在1970年秋季对于文件传输的IMP包进行了升级
+ 3. 电子邮件, 字面意思, 这个时期电子邮件被创造出来

在1972年程序员Ray Tomlinson通过改编一对名为 ``SNDMSG`` 和 ``READMAIL`` 的 ``Tenex`` 操作系统应用程序，为 ARPAnet 开发了一种电子邮件系统. **Tomlinson选择了"@"符号将接收者和接收者主机的名称结合在一起, 这是我们今天仍然使用的约定.**

电子邮件在 ARPAnet 上首次亮相后不久， 一些团队成员就开始使用邮件列表软件应用程序. 很快, ARPAnet 上的某人就可以一步一步将电子邮件发送给一群人. 针对特定主题的邮件列表开始出现. 第一个是``SF-LOVERS``, 该列表将ARPANET团队中的**科幻迷**联系起来(Science Fiction就是科幻嘛...). 但是, ARPA不赞成将网络用于非官方目的, 并要求名单拥有者解散所有未经授权的邮件清单. 用户后来通过指出他们帮助测试了网络的邮件容量, **说服了**ARPA允许这些列表的存在

而这个网络是怎么搭建起来的呢?

### 很久以前

在很久很久以前, 科研人员使用计算机的方式是这样的: 有一个像房间一样大的主机, 然后有多个通过分时连接的终端可以同时连接主机并且操作(**主机上的操作系统是分时操作系统**).

```
    time slice
+---------------+
+---+---+---+---+
| 1 | 2 | 3 | 4 |
+---+---+---+---+
```

但是ARPA决定建立一个能够连接全美国的网络, (艹, 这听上去太伟大了), ARPA根据与美国政府之间已有的研究关系选择了最初的计算机站点. 每个站点都有自己的工程师团队， 负责将站点计算机连接到ARPAnet. 初始ARPAnet结构中的四台主机包括：

+ 加州大学洛杉矶分校的大学计算机，它是在Sigma实验操作系统上运行的 SDS Sigma 7
+ 斯坦福研究所的SDS-90计算机，该计算机在Genie操作系统上运行
+ 加利福尼亚大学卡勒弗里德互动数学中心的OS / MVT操作系统上运行的IBM 360/75
+ 犹他大学的带有Tenex操作系统的DEC PDP-10计算机

> **最终, 经历了种种挫折, 在1969年年底之前, 四台计算机全部接入了ARPAnet.**

### RFC的诞生

计算机是接入了, 但是传输数据的方式是十分混乱的. 为此ARPAnet团队做出的最重要的决定之一就是**创建主机和IMP(Interface Message Processor, 接口消息处理器)遵循的标准化协议系统**, 也就是对于网络协议的开发.

一开始的开发和设计是非常混乱的, 直到Steve Crocker撰写了[RFC-3](https://www.rfc-editor.org/rfc/rfc3.txt), 创建了著名的RFC(Request for Comments), 才让整体的工作变得有序起来:

> The Network Working Group seems to consist of Steve Carr of Utah, Jeff Rulifson and Bill Duvall at SRI, and Steve Crocker and Gerard Deloche at UCLA.  Membership is not closed.

> Documentation of the NWG's effort is through notes such as this.  Notes may be produced at any site by anybody and included in this series. (我们网络工作小组的文档应该像这个文档一样, 任何人都可以在任何站点编写文档, 但是一定要包括在本系列文档内)

顺便在RFC-3中还解释了一下1和2都去哪里了:

> Two notes (1 & 2) have been written so far.  These are both titled HOST Software and are by Steve Crocker and Bill Duvall, separately.

### NCP - 世界上最早的网络协议

很快, 在步入正轨之后, **NWP(Network Working Group, 网络工作小组)** 就整出了一个叫做 **NCP(Network Control Program, 网络控制程序)** 的东西.

NCP是一种**Host-to-Host**的协议, 它建立了使用**数字主机地址进行网络通信**的惯例, 并且是DNS(Domain Name System)的前驱. 

不仅如此, 它还**创建了一种革命性的数据传输方式**: Packet Switching(分组交换), 发送方可以把数据包切割成若干个小的数据包, 然后发送, 而接收端可以将小的数据包重新组合成为原先的数据包.

我们可以从[RFC-33](https://www.rfc-editor.org/rfc/rfc33.txt): New HOST-HOST Protocol中看到对于NCP的详细描述:

```
                                 SRI
                                _____
                               /     \
                              |  XDS  |
                              |  940  |
                               \_____/
                                  |
                            +----------+
                            |    IMP   |
                            +----------+
                             /   |    \
                            /    |     \
                           /     |      \  +----+    _____
                          /      |       \ | I  |   /     \
       ______     +----+ /       |        \| M  |--|  DEC  |
      /      \    | I  |/        |         | P  |  | PDP-10|
     |   IBM  |---| M  |         |         +----+   \_____/
     | 360/75 |   | P  |\        |
      \______/    +----+ \       |                    UTAH
                          \      |
        UCSB               \     |
                          +----------+
                          |    IMP   |
                          +----------+
                              |
                           ___|___
                          /       \
                         |   XDS   |
                         |(sigma)-7|
                          \_______/

                            UCLA

   Figure 1 Initial network configuration
```

该图像表示出了当时四台主机的连接情况.

```
   |<------------ 24bits ----------->|
   |                                 |
   +---------------------------------+
   |                                 |
   |        Leader (32 bits)         |
   |               __________________|
   |              | 100 ---    ----0 |<----16 bits of marking
   +--------------+------------------+
   |                                 |
   |                                 |
   |   Text of messages (96 bits)    |
   |                                 |
   +------------------------+--------+
   | 100-----          ----0|
   +-------^----------------+
           |
           |______16 bits of padding added
                  by the interface

   Figure 2  A typical message from a 24-bit machine

```

该图像表示了在NCP(也就是RFC-33中描述的'新协议')中用于传输数据的格式

```
          24                    8          8
   +----------------------+-----------+----------+
   |  User Number         |           |          |
   +----------------------+-----------+----------+
                                |          |___AEN
                                |
                                |___HOST number
   Figure 3 A typical socket
```

该图像展示了套接字(socket)的定义.

```
              |<--- connection --->|
   +---------+                      +---------+
   |         |        link          |         |
   | process |--(|--------------|)--| process |
   |         |   ^              ^   |         |
   +---------+   |              |   +---------+
                 |              |
             send socket    receive socket

   Figure 4 The relationship between sockets and processes
```

该图像展示了套接字和进程之间的关系.

### 因特网

看到这里你应该已经对NWG中的四五个青年肃然起敬了吧. 但是事情还远远不止于此.

1973年，罗伯特·卡恩（Robert Kahn）发起了一项实验， 这项技术被他称为INTERNET, 也就是因特网: **将两个或多个独立的网络合并为一个更大的网络**. 他开始研究将 ARPAnet 与ARPA分组无线网络集成的方法.

是的, 不仅要合并网络, 还要合并无线和有线网络.

这里的分组无线网络指的是**ALOHA**(夏威夷语: 你好), 是世界上最早的无线计算机网络协议

> 1971 ALOHAnet developed by Norman Abrahamson, University of Hawaii, heart of Ethernet and connected to the ARPAnet in 1972.

有趣的是, Robert的研究成果被它命名为Alto ALOHA, 这和之前的无线网络几乎是同名的, 但是最后它被大家叫做了: **Ethernet(以太网)**

### TCP/IP

在NCP之后, NWG开始设计 TCP/IP 套件, 这期间的讨论大多以IEN的形式出现, 比如以下资料就出自 IEN-3 :

```
    FIELD                                                           BITS
    Version                                                            4
    Flags                                                       variable
    Data Identifier                                                   16
    Acknowledgement Identifier                                        16
    Type of Service                                                    4
    Destination Address                                         variable
    Fragment Information
      Message Identifier                                              16
      Fragment Sequence Number                                        16
      Last Fragment Flag                                               1
    Data Length                                                       16
    Data                                                        variable
    Checksum                                                          16
```

```
    FIELD                                                           BITS
    Version                                                            4
    Flags
      Data Identifier                                                  1
      Acknowledgement Identifier                                       1
    Data Identifier                                                   16
    Acknowledgement Identifier                                        16
    Type of Service                                                    4
    Destination Address                                         variable
    Fragment Information
      Message Identifier                                              16
      Fragment Sequence Number                                        16
      Last Fragment Flag                                               1
    Data Length                                                       16
    Data                                                        variable
    Checksum                                                          16
```

大家就可以看出和之前的协议设计大有不同, 增加了很多的冗余字段, 至于这其中的原因可以参考我的另一篇博客, 其中有协议设计历史的演变: [ShiinaOrez的博客](https://shiinaorez.github.io/2019/10/02/tlv-ttlv/)