# 计算机网络

## 概述
应用层(application-layer）的任务是通过应用进程间的交互来完成特定网络应用。应用层协议定义的是应用进程（进程：主机中正在运行的程序）间的通信和交互的规则
 
OSI 要比上面的网络模型多了 表示层 和 会话层，其他层基本一致。表示层主要包括数据压缩和数据加密以及数据描述，数据描述使得应用程序不必担心计算机内部存储格式的问题，而会话层提供了数据交换的定界和同步功能，包括建立检查点和恢复
 
运输层(transport layer) 的主要任务就是负责向两台主机进程之间的通信提供通用的数据传输服务。应用进程利用该服务传送应用层报文。
 
运输层主要使用以下两种协议
传输控制协议 TCP（Transmission Control Protocol）--提供面向连接的，可靠的数据传输服务。
用户数据协议 UDP（User Datagram Protocol）--提供无连接的，尽最大努力的数据传输服务（不保证数据传输的可靠性）
 
网络层(network layer)负责为分组交换网上的不同主机提供通信服务。 在发送数据时，网络层把运输层产生的报文段或用户数据报封装成分组和包进行传送。在 TCP/IP 体系结构中，由于网络层使用 IP 协议，因此分组也叫 IP 数据报 ，简称 数据报。
 
数据链路层(data link layer)通常简称为链路层。两台主机之间的数据传输，总是在一段一段的链路上传送的，这就需要使用专门的链路层的协议。 在两个相邻节点之间传送数据时，数据链路层将网络层交下来的 IP 数据报组装程帧，在两个相邻节点间的链路上传送帧。每一帧包括数据和必要的控制信息（如同步信息，地址信息，差错控制等）。
 
在物理层上所传送的数据单位是比特。 物理层(physical layer)的作用是实现相邻计算机节点之间比特流的透明传送，尽可能屏蔽掉具体传输介质和物理设备的差异。 使其上面的数据链路层不必考虑网络的具体传输介质是什么。“透明传送比特流”表示经实际电路传送后的比特流没有发生变化，对传送的比特流来说，这个电路好像是看不见的。
 
 
## 三次握手
TCP是流stream，不是段segment。它有个缓冲区，区里是比特流，然后根据网络层的情况选取某一部分发出去，syn和ack都表示第x个字节，表示已经发到/接收到什么位置了。
 
之所以三次，是因为TCP是双向传输，服务器和客户端都有缓冲区。
 
第一次：客户端client告诉服务器server我这的字节编号（初始序号ISN）。即client对server发送一个SYN段，指明client想要连接的server端口，以及初始序号ISN(1415531521)。 SYN是报文段1。
 
第二次：然后服务器收到后发送ack，然后服务器把自己的字节编号发给用户，这里是两步，但是发ack和发字节编号可以同时进行，所以就一步。server发回包含server初始序号 ISN（1823083521）的SYN（报文2），同时，将确认序号（ACK）设置为client 的 ISN+1 (1415531522) 对client的SYN报文段进行确认。
 
第三次：最后客户端拿到服务器的字节编号后发送ack。
client必须把确认序号（ACK）设置为server的 ISN+1 （1823083521）来对server的报文段进行确认（报文段3）


如果已经明确知道服务端不用给客户端发消息，两次握手就够了
所以三次握手的目的，是让双方都知道对方的缓冲区里字节编号。
相当于匹配这个关键信息，匹配上了就ok了
 
因为是流stream，所以需要匹配。但是UDP不用，它没有缓冲区，它是一段一段segment的，所以不需要握手
 
个人简单理解：
第一次：client -> server，证明了client的发送端功能正常
第二次： server -> client 证明了server的发送端和接受端功能均正常
第三次： client -> server 证明了client的接收端功能正常
 
第一次，client 将标识位 SYN 置为 1, seq = x (Sequence number)发送给server, 此时本机状态为 SYN-SENT
第二次，server 收到包之后，将状态切换为SYN-RECEIVED，并将标识位 SYN 和 ACK都置为1, seq = y, ack = x + 1, 并发送给客户端。y是数据的长度
第三次，客户端收到包后，将状态切换为ESTABLISHED，并将标识位ACK置为1， ack = y + 1, 并发送给服务端。服务端收到包之后，也将状态切换为ESTABLISHED
 
## 四次挥手
终止一个TCP连接需要4次握手
因为 TCP half-close
 
TCP 连接是 ( 即数据在两个方向上能同时传递 )，因此每个方向必须单独地进行关闭
 
A 发送 FIN 到 B，即 A 关闭了 A ->B 方向上的数据流动（A 是主动关闭）
B 接收了 A 发的 FIN，B还可以给 A 发数据， B属于被动关闭
 
收到一个 FIN 只意味着在这一方向上没有数据流动。一个 TCP 连接在收到一个 FIN后仍能发送数据。
 
FIN 完成标识
 
第一次： client 关闭连接发出 FIN（1415531522） 对server
第二次： server收到这个 FIN，发回一个 ACK（1415531523）， 确认序号（ACK）为收到的序号+1
第三次： TCP的server需要向应用程序传送文件结束符号EOF，服务器程序关闭连接，server 向 client 发送 FIN （1823083522）
第四次： client需要发回一个确认序号ACK （1823083523），确认序号是收到序号+1
 
第四次握手后，主机发送确认信号后并没有立即断开连接，而是等待了 2 个报文传送周期，原因是：如果第四次握手的确认信息丢失，服务器将会重新发送第三次握手的断开连接的信号，而服务器发觉丢包与重新发送的断开连接到达主机的时间正好为 2 个报文传输周期。
 
## 为什么要四次挥手
任何一方都可以在数据传送结束后发出连接释放的通知，待对方确认后进入半关闭状态。当另一方也没有数据再发送的时候，则发出连接释放通知，对方确认后就完全关闭了TCP连接。
举个例子：A 和 B 打电话，通话即将结束后，A 说“我没啥要说的了”，B回答“我知道了”，但是 B 可能还会有要说的话，A 不能要求 B 跟着自己的节奏结束通话，于是 B 可能又巴拉巴拉说了一通，最后 B 说“我说完了”，A 回答“知道了”，这样通话才算结束。
 
 
 
 
## TCP 和 UDP
 
 
 
 
## 从输入URL到页面加载发生了什么
1. DNS解析
2. TCP连接（三次握手）
3. 发送HTTP请求
4. 服务器处理请求并返回HTTP报文
5. 客户端浏览器解析渲染页面
6. 连接结束（四次挥手）
 
页面呈现
• 通过HTML解析器解析HTML文档，构建一个DOM Tree，同时通过CSS解析器解析HTML中存在的CSS，构建Style Rules，两者结合形成一个Attachment。
• 通过Attachment构造出一个呈现树（Render Tree）
• Render Tree构建完毕，进入到布局阶段（layout/reflow），将会为每个阶段分配一个应出现在屏幕上的确切坐标。
最后将全部的节点遍历绘制出来后，一个页面就展现出来了。
 
 
 
 
 



## TCP 粘包问题
每个UDP包中就有了消息头（消息来源地址，端口等信息），这样，对于接收端来说，就容易进行区分处理了。所以UDP不会出现粘包问题。
 
TCP粘包是指 发送方发送的若干包数据到接收方接收时粘成一包，从接收缓冲区看，后一包数据的头，紧接着前一包数据的尾。出现粘包现象的原因是多方面的，它既可能由发送方造成，也可能由接收方造成。
TCP是stream，数据堆积在缓冲区里积压，发送大小不确定，所以不同的数据包可能会粘在一个TCP报文里发出去。这是粘包问题
 
一是人为添加头部，里面放一些控制信息，比如数据长度
二是强制刷新缓冲区，比如一个包是500字节，放进缓冲区后强制发送，不等后续数据放进去
 
基本都是这两种思路，最常用的就是封装一层，然后接收方再拆包，先读头部控制信息，然后读这个长度的数据
 
什么时候需要考虑粘包：
  1. 如果利用TCP每次发送数据，就与对方建立连接，然后双方发送完一段数据后，就关闭连接，这样就不会出现粘包问题（因为只有一种包结构,类似于http协议）
  2. 如果发送数据无结构，如文件传输，这样发送方只管发送，接收方只管接收存储就ok，也不用考虑粘包
  3. 如果双方建立连接，需要在连接后一段时间内发送不同结构数据， 粘包就可能出现
 
粘包出现的原因
 
1 发送端需要等缓冲区满才发送出去，造成粘包
2 接收方不及时接收缓冲区的包，造成多个包接收
