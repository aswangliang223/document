# Netty 执行流程

![](http://q8sats5bw.bkt.clouddn.com/img/netty 执行流程.jpg)

# Reactor 模型

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200425114523211.png)

Netty-Server 采用了多线程模型。不过线程池是由 EventLoopGroup 充当。EventLoopGroup
中的每一个 EventLoop 都绑定着一个线程，用于处理该 Channel 与当前 Server 间的操作。一
个 Channel 只能与一个 EventLoop 绑定，但一个 EventLoop 可以绑定多个 Channel。即 Channel
与 EventLoop 间的关系是 n:1。  

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200425114658026.png)

Netty-Client 采用的是线程池模型。因为其只需要与 Server 连接一次即可，无需区分连
接请求与 IO 请求。  