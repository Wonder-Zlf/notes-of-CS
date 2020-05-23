# Java的SOCKET模型

## 1、TCP和UDP

学习socket前必须掌握TCP/IP协议。IP是建立在OSI网络模型中的网络层的协议族，主要功能实现为数据包选择路由。而TCP和UDP都是在网络层之上的传输层，用于实现端到端的数据传输。

TCP（Transmission Control Protocol）传输控制协议，是一种面向连接的、可靠的、基于字节流的通信协议。TCP通过三次握手建立连接，每个顺序的数据包由ACK消息确认收到。

UDP（User Datagram Protocol）用户数据协议，是一种无连接的协议，也就是不会通过类似三次握手建立连接。每一个数据包都是一个独立的信息，都包含了发送端ip和目的地ip。它在网络上发出和传输，但是不一定保证接收端能接到，数据包间的顺序也不一定正确。



## 2、OSI模型的协议

| OSI模型的层级 | 功能                                   | TCP/IP协议簇                            |
| ------------- | -------------------------------------- | --------------------------------------- |
| 应用层        | 文件传输、电子邮件、文件服务、虚拟中断 | TFTP, HTTP, SNMP,FTP, SMTP, DNS, Telnet |
| 表示层        | 数据格式化，代码转换，数据加密         | 没有协议                                |
| 会话层        | 解除或建立与别的节点的联系             | 没有协议                                |
| 传输层        | 提供端对端的接口                       | TCP, UDP                                |
| 网络层        | 为数据包选择路由                       | IP, ICMP, RIP, OSPF, BGP, IGMP          |
| 数据链路层    | 传输有地址的帧以及错误检查功能         | SLIP,CSLIP,PPP,ARP,RARP,MTU             |
| 物理层        | 以二进制数据形式在物理媒体上传输数据   | ISO2110,IEEE802,IEEE802.2               |



## 3、socket模型

socket通常被称为”套接字“，用于描述IP地址和端口，是一个通信链的句柄。应用程序通过”套接字“向网络发出请求或者应答网络请求。

简单理解，socket是将TCP/IP协议包装好了，提供直接可用的编程接口API。在网络编程中，要在客户端和服务器端都建立socket的实例，对双方而言socket是一个平等对传端口。

Java.net.Socket是Java自带的套接字，包含多个可重载的构造器和足够的函数方法。

https://docs.oracle.com/javase/9/docs/api/java/net/Socket.html



## 4、socket的主要方法

java.net.Socket继承于java.lang.Object，有八个构造器，其方法并不多，下面介绍使用最频繁的三个方法：

1）Accept方法用于产生”阻塞”，直到接受到一个连接，并且返回一个客户端的Socket对象实例。”阻塞”是一个术语，它使程序运行暂时”停留”在这个地方，直到一个会话产生，然后程序继续；通常”阻塞”是由循环产生的。

2）getInputStream方法获得网络连接输入，同时返回一个InputStream对象实例。

3）getOutputStream方法连接的另一端将得到输入，同时返回一个OutputStream对象实例。



## 5、TCP编程模型

5.1 服务器端

1）创建serverSocket对象，绑定监听端口

2）通过accept方法监听客户端请求

3）建立连接后，通过输入流读取客户端发送的请求消息

4）通过输出流向客户端发送响应消息

5）关闭相应资源



5.2 客户端

1）创建socket对象，指明需要连接的服务器的地址和端口号

2）连接建立后，通过输出流向服务器发送请求消息

3）通过输入流获取服务器响应的消息

4）关闭相应资源



5.3 多线程实现服务器与客户端之间通信

1）服务器创建ServerSocket, 循环调用accept()等待客户端连接

2）客户端创建一个socket并请求和服务器连接

3）服务器端接收客户端请求，创建socket与该客户建立专线连接

4）建立连接的两个socket在一个单独的线程上对话

5）服务器端继续等待新的连接



## 6、Socket编码实现与参考

https://blog.csdn.net/a78270528/article/details/80318571?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1