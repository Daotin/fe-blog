## IP && UDP && TCP

IP 中包括源地址IP，目标地址IP。

UDP协议包括要发送到的应用程序的端口号。

**所以 IP 通过 IP 地址信息把数据包发送给指定的电脑，而 UDP 通过端口号把数据包分发给正确的程序。**

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190912172107.png)



在使用 UDP 发送数据时，有各种因素会导致数据包出错，虽然 UDP 可以校验数据是否正确，但是对于错误的数据包，UDP 并不提供重发机制，只是丢弃当前的包，而且 UDP 在发送之后也无法知道是否能达到目的地。



虽说 UDP 不能保证数据可靠性，但是传输速度却非常快，，所以 UDP 会应用在一些关注速度、但不那么严格要求数据完整性的领域，如在线视频、互动游戏等。



所以就引入**TCP协议**：



对于数据包丢失的情况，TCP 提供重传机制；

TCP 引入了数据包排序机制，用来保证把乱序的数据包组合成一个完整的文件。





一个完整的TCP连接的生命周期：

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190912172151.png)



## 思考题

*TCP与Http的关系？*



HTTP协议和TCP协议都是TCP/IP协议簇的子集。



HTTP协议属于应用层，TCP协议属于传输层，HTTP协议位于TCP协议的上层。



请求方要发送的数据包，在应用层加上HTTP头以后会交给传输层的TCP协议处理，应答方接收到的数据包，在传输层拆掉TCP头以后交给应用层的HTTP协议处理。建立 TCP 连接后会顺序收发数据，请求方和应答方都必须依据 HTTP 规范构建和解析HTTP报文。

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190912172217.png)

