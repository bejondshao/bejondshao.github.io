---
layout: post
title: ChannelHandler
date: 2017-11-22 19:18:02
tags:
- Java
- Netty
category:
- Java
---
io.netty.channel.[ChannelHandler](http://netty.io/4.0/api/io/netty/channel/ChannelHandler.html)

**Interface ChannelHandler**

```
public interface ChannelHandler
```

处理I/O事件，或者拦截I/O操作，并传到它的ChannelPipeline中的下一个handler。

**Sub-types**
ChannelHandler本身并没提供很多方法。要处理ChannelInboundInvoker或者ChannelOutboundInvoker操作，你需要实现ChannelHandler的子接口。最常用的是：

    @ ChannelInboundHandlerAdapter，处理并拦截inbound操作。
    @ ChannelOutboundHandlerAdapter，处理并拦截outbound操作。



**Context object**
ChannelHandler由一个ChannelHandlerContext对象提供。通过context对象，ChannelHandler可以和ChannelPipeline沟通。ChannelHandler可以通过context上行，下行传递事件，动态更改pipeline，或者将信息（使用AttributeKeys）存储到handler中。

**State management**
ChannelHandler一般需要存储状态信息。最简单的方式是使用成员变量。

```
public interface Message {
     // your methods here
 }

 public class DataServerHandler extends SimpleChannelInboundHandler<Message> {

     private boolean loggedIn;

      @Override
     public void channelRead0(ChannelHandlerContext ctx, Message message) {
         Channel ch = e.getChannel();
         if (message instanceof LoginMessage) {
             authenticate((LoginMessage) message);
             loggedIn = true;
         } else (message instanceof GetDataMessage) {
             if (loggedIn) {
                 ch.write(fetchSecret((GetDataMessage) message));
             } else {
                 fail();
             }
         }
     }
     ...
 }
```
由于handler实例有一个状态变量，和每个connection相关联，你必须对每个新的channel创建新的handler实例，防止未授权的客户端获取机密信息：
```
 // Create a new handler instance per channel.
 // See ChannelInitializer.initChannel(Channel).
 public class DataServerInitializer extends ChannelInitializer<Channel> {
      @Override
     public void initChannel(Channel channel) {
         channel.pipeline().addLast("handler", new DataServerHandler());
     }
 }
```

**使用AttributeKey**
建议使用成员变量存储handler的数据，有时候你可能不想创建很多的handler实例，这时你可以使用ChannelHandlerContext提供的AttributeKeys

```
public interface Message {
     // your methods here
 }

  @Sharable
 public class DataServerHandler extends SimpleChannelInboundHandler<Message> {
     private final AttributeKey<Boolean> auth =
           AttributeKey.valueOf("auth");

      @Override
     public void channelRead(ChannelHandlerContext ctx, Message message) {
         Attribute<Boolean> attr = ctx.attr(auth);
         Channel ch = ctx.channel();
         if (message instanceof LoginMessage) {
             authenticate((LoginMessage) o);
             attr.set(true);
         } else (message instanceof GetDataMessage) {
             if (Boolean.TRUE.equals(attr.get())) {
                 ch.write(fetchSecret((GetDataMessage) o));
             } else {
                 fail();
             }
         }
     }
     ...
 }
```

既然heandler的state已经和ChannelHandlerContext绑定，就可以给不同的pipelines添加相同的handler实例：

```
 public class DataServerInitializer extends ChannelInitializer<Channel> {

     private static final DataServerHandler SHARED = new DataServerHandler();

      @Override
     public void initChannel(Channel channel) {
         channel.pipeline().addLast("handler", SHARED);
     }
 }
```

**@Sharable注解**
如果一个ChannelHandler声明为Sharable，就可以创建一个handler实例，把它加到不同的ChannelPipelines。如果不加这个注解，在向pipeline加handler时，需要创建新的handler实例，因为它可能有不能共享的数据，比如成员变量。

**更多资料**
请参考{% post_link ChannelPipeline %}有关inbound和outbound操作的信息，它们的功能有什么区别，它们在pipeline是如何运作的，如何处理操作。