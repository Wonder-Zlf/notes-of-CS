# TCP四次挥手



## 1、TCP三次握手和四次挥手

![img](http://dl.iteye.com/upload/attachment/0077/6058/5d4e8c89-fc42-3862-bdb8-399bc982f410.png)

几个标志位：

SYN: synchronous建立联机

ACK: acknowledgement 确认

PSH: PUSH传送

FIN: Finish结束

RST: Reset重置

URG: urgent紧急

Sequence number 顺序号码

ACK number 确认号码



## 2、TCP过程中客户端和服务器端状态迁移

**客户端TCP状态迁移：**
CLOSED->SYN_SENT->ESTABLISHED->FIN_WAIT_1->FIN_WAIT_2->TIME_WAIT->CLOSED
**服务器TCP状态迁移：**
CLOSED->LISTEN->SYN收到->ESTABLISHED->CLOSE_WAIT->LAST_ACK->CLOSED

**各个状态的意义如下：**
LISTEN - 侦听来自远方TCP端口的连接请求；
SYN-SENT -在发送连接请求后等待匹配的连接请求；
SYN-RECEIVED - 在收到和发送一个连接请求后等待对连接请求的确认；
ESTABLISHED- 代表一个打开的连接，数据可以传送给用户；
FIN-WAIT-1 - 等待远程TCP的连接中断请求，或先前的连接中断请求的确认；
FIN-WAIT-2 - 从远程TCP等待连接中断请求；
CLOSE-WAIT - 等待从本地用户发来的连接中断请求；
CLOSING -等待远程TCP对连接中断的确认；
LAST-ACK - 等待原来发向远程TCP的连接中断请求的确认；
TIME-WAIT -等待足够的时间以确保远程TCP接收到连接中断请求的确认；
CLOSED - 没有任何连接状态；