---
layout: post
title: Channel
date: 2017-11-22 19:16:23
tags:
- Java
- Netty
category:
- Java
---
io.netty.channel.[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)
**Interface Channel**

```
public interface Channel
extends AttributeMap, java.lang.Comparable<Channel>
```

Channel是一个网络socket的连接，一个组件，可以处理一些I/O操作，比如读，写，连接，绑定。

Channel会给一个user提供：

- 当前channel的状态（open? connected?）
- channel的配置参数（ChannelConfig，接收数据的尺寸）
- channel所支持的I/O操作
- 和当前chennel绑定的ChannelPipeline，用于处理所有的I/O事件和请求

**所有I/O操作都是异步的**
Netty中所有I/O操作都是异步的。也就是说所有I/O调用都会直接的返回，不能保证I/O请求在返回的时候已经完成，返回的结果有个ChannelFuture实例，这个ChannelFuture实例提醒你所请求的操作是否成功，失败或者取消。

**Channels都是阶级分明的**
一个Channel可以有一个parent，表示它是如何创建的。比如一个ServerSocketChannel创建一个SocketChannel，socketChannel.parent()就会返回ServerSocketChannel。
等级结构的意义取决于传输实现。比如你可以写一个新的Channel实现，创建sub-channels共享一个socket连接，比如BEEP和SSH。

**向下转换，来访问特定的操作**
一些传输暴露额外传输相关的操作。将Channel向下转换成子类型来调用相关操作。比如旧的I/O数据报传输，多播加入、离开操作是由DatagramChannel提供的。

**释放资源**
调用close()或close(ChannelPromise)是重要的，当Channel工作完成时，释放所有资源，保证所有资源都恰当的被释放。

