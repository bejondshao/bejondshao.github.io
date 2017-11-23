---
layout: post
title: ChannelPipeline
date: 2017-11-22 19:19:28
tags:
- Java
- Netty
category:
- Java
---
io.netty.channel.[ChannelPipeline](http://netty.io/4.0/api/io/netty/channel/ChannelPipeline.html)

**Interface ChannelPipeline**

```
public interface ChannelPipeline 
extends java.lang.Iterable<java.util.Map.Entry<java.lang.String,ChannelHandler>>
```
ChannelPipeline是个ChannelHandler的集合，用来处理拦截Channel中的inbound事件和outbound操作。ChannelPipeline实现了一个高级的[拦截过滤](http://www.oracle.com/technetwork/java/interceptingfilter-142169.html  )模式，给用户全面的控制一个事件是如何被处理的，以及多个ChannelHandlers在一个管道里是如何交互的。

**创建一个pipeline**
每个channel都有自己的pipeline，在创建channel时就自动创建pipeline。

**事件在pipeline中是怎么流通的**
I/O事件由ChannelInboundHandler或ChannelOutboundHandler处理的，并调用{% post_link ChannelHandlerContext %}的事件传播方法（比如ChannelHandlerContext.fireChannelRead(Object), ChannelHandlerContext.write(Object)）传到最近的handler。
{% asset_img event-flow.png Event Flow %}
一个inbound事件由inbound handlers（图中左边自下而上的方式）处理。一个inbound handler通常处理由图表底部的I/O线程生成的inbound数据。inbound数据通常由远程端点读取，通常是一个实际的输入操作，例如SocketChannel.read(ByteBuffer)。如果inbound事件继续向上超过了顶部界限，就会被忽略掉，或者日志记录下来。
一个outbound事件由outbound handler（图中右边自上而下的方式） 处理。一个outbound handler通常生成或转换outbound信息，比如写请求。如果outbound事件继续向下超过了底部的界限，就会被与Channel相关的I/O线程处理。I/O线程通常执行实际的操作，比如SocketChannel.write(ByteBuffer)。
举个例子，我们创建如下的pipeline:
```
 ChannelPipeline p = ...;
 p.addLast("1", new InboundHandlerA());
 p.addLast("2", new InboundHandlerB());
 p.addLast("3", new OutboundHandlerA());
 p.addLast("4", new OutboundHandlerB());
 p.addLast("5", new InboundOutboundHandlerX());
```
上面的例子中，Inbound意味着是inbound handler，outbound意味着是outbound handler。当事件为inbound时，执行流程是1,2,3,4,5。当事件是outbound时，执行流程是5,4,3,2,1。在此基础上，ChannelPipeline自动跳过不相干的handlers来简化过程：
- 3和4没有实现ChannelInboundHandler，因此inbound事件只会执行1,2,5。
- 1和2没有实现ChannelOutboundHandler，因此outbound事件只会执行5,4,3。
- 如果5实现了ChannelInboundHandler和ChannelOutboundHandler，那么inbound和outbound事件都会包括5。

**将一个事件传递到下一个handler**
一个handler需要调用ChannelHandlerContext中的事件传递方法来将事件传给下一个handler。这些方法包括：
- Inbound事件传递方法：
    - ChannelHandlerContext.fireChannelRegistered()
    - ChannelHandlerContext.fireChannelActive()
    - ChannelHandlerContext.fireChannelRead(Object)
    - ChannelHandlerContext.fireChannelReadComplete()
    - ChannelHandlerContext.fireExceptionCaught(Throwable)
    - ChannelHandlerContext.fireUserEventTriggered(Object)
    - ChannelHandlerContext.fireChannelWritabilityChanged()
    - ChannelHandlerContext.fireChannelInactive()
    - ChannelHandlerContext.fireChannelUnregistered()
- Outbound事件传递方法：
    - ChannelHandlerContext.bind(SocketAddress, ChannelPromise)
    - ChannelHandlerContext.connect(SocketAddress, SocketAddress, ChannelPromise)
    - ChannelHandlerContext.write(Object, ChannelPromise)
    - ChannelHandlerContext.flush()
    - ChannelHandlerContext.read()
    - ChannelHandlerContext.disconnect(ChannelPromise)
    - ChannelHandlerContext.close(ChannelPromise)
    - ChannelHandlerContext.deregister(ChannelPromise)

例如：
```
 public class MyInboundHandler extends ChannelInboundHandlerAdapter {
      @Override
     public void channelActive(ChannelHandlerContext ctx) {
         System.out.println("Connected!");
         ctx.fireChannelActive();
     }
 }

 public class MyOutboundHandler extends ChannelOutboundHandlerAdapter {
      @Override
     public void close(ChannelHandlerContext ctx, ChannelPromise promise) {
         System.out.println("Closing ..");
         ctx.close(promise);
     }
 }
```

 **创建一个pipeline**
用户在一个pipeline中可以有一个或多个ChannelHandlers来接收I/O事件（比如读）以及请求I/O操作（比如写和关闭）。例如一般的channel pipeline中通常有一下几个handlers：
 1. ProtocolDecoder - 将二进制数据（比如ByteBuf）转换成Java对象。
 2. ProtocolEncoder - 将Java对象转换为二进制数据。
 3. 业务逻辑handler - 实际的业务相关的处理。

举个例子：
```
 static final EventExecutorGroup group = new DefaultEventExecutorGroup(16);
 ...

 ChannelPipeline pipeline = ch.pipeline();

 pipeline.addLast("decoder", new MyProtocolDecoder());
 pipeline.addLast("encoder", new MyProtocolEncoder());

 // 告知pipeline在另一个线程执行MyBusinessLogicHandler事件处理方法而不是一个I/O线程。防止线程
 // 被耗时间的任务阻塞。如果业务逻辑是完全异步的或很快完成，就不需要指定group。
 pipeline.addLast(group, "handler", new MyBusinessLogicHandler());
```

**线程安全**
一个ChannelHandler可以在任何时候被添加或移除，因为ChannelPipeline是线程安全的。比如你可以在敏感信息需要加密时添加加密handler，在转换完成后移除这个handler。