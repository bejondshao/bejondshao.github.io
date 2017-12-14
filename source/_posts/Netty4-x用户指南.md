---
layout: post
title: Netty4.x用户指南
date: 2017-11-28 15:27:24
tags:
- Java
- Netty
category:
- Java
---
[原文](https://github.com/netty/netty/wiki/User-guide-for-4.x)
[完整翻译可参考](https://doc.yonyoucloud.com/doc/netty-4-user-guide/Architectural%20Overview/Architectural%20Overview.html)
#### 序言
---------------------
##### 难题
如今我们通常使用应用或类库与不同的系统交互。比如，我们通常使用HTTP客户端类库来获取web服务器的信息，通过web service调用远程服务。但是普通的协议实现通常无法完成诸如大文件，邮件信息，即时通讯比如金融信息，多玩家网络游戏等功能。这些需求都对信息处理有很高的性能要求。你可能想实现一个HTTP服务器，比如基于AJAX的聊天系统，影音媒体库，或网盘。你甚至想实现一个符合你特殊需求的协议。还有个不可避免的情况，当你需要处理一个有专利权的协议，使得它能和一个旧系统进行交互。问题是我们多久能实现这个协议，并且要保证稳定性和高性能。

--------------
##### 解决方案
[Netty](http://netty.io/)就是一个致力于提供异步消息驱动的网络应用框架，用来快速开发可维护可扩展的高效的服务器与客户端协议的工具。

Netty是一个能简单快速的开发网络应用协议的NIO客户端服务器框架。它使网络编程比如TCP和UDP变得简单化，流程化。

“快速且简单”并不意味着不容易维护或性能损失。Netty借鉴于以往的FTP，SMTP,HTTP，多种二进制和基于文本的协议等的实现经验，在设计时就格外小心。所以，Netty具有易用，高性能，高可靠，稳定，灵活的特点。

有人会发现其他网络应用框架也声称有这些优点，你可能会想知道Netty与其他框架有什么不同。答案就是Netty被设计的哲学。Netty在被设计的第一天起就致力于在实现和API文档给用户最好的体验。这并不那么直观，但这种哲学会让你意识到，在你读这篇指南和使用Netty时，它让你的生活更简单。

-----------------------
##### 动手试试
这章意在构建简单的Netty例子来让你快速开始。读完这章，你能基于Netty快速的写出一个客户端和服务端。

如果你想彻底的学习Netty，你应该从第二章：[Architectural Overview](http://netty.io/3.5/guide/#architecture)学习。

{% asset_img architecture.png Architecture %}

##### 写一个Discard服务器
[DISCARD](http://tools.ietf.org/html/rfc863)协议接收消息然后忽略。

```
package io.netty.example.discard;

import io.netty.buffer.ByteBuf;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;

/**
 * Handles a server-side channel.
 */
public class DiscardServerHandler extends ChannelInboundHandlerAdapter { // (1)

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) { // (2)
        // 直接忽略收到的信息。
        ((ByteBuf) msg).release(); // (3)
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) { // (4)
        // 异常时关闭连接。
        cause.printStackTrace();
        ctx.close();
    }
}
```
1. DiscardServerHandler继承[ChannelInboundHandlerAdapter](http://netty.io/4.1/api/io/netty/channel/ChannelInboundHandlerAdapter.html)。ChannelInboundHandlerAdapter实现了[ChannelInboundHandler](http://netty.io/4.1/api/io/netty/channel/ChannelInboundHandler.html)。ChannelInboundHandler提供了多种事件处理方法的定义。有了ChannelInboundHandlerAdapter，只需要继承它就可以了，而不用实现ChannelInboundHandler。
2. 在这重写channelRead()事件处理方法。当接收客户端的消息时，就会调用channelRead()。本例中，消息类型是[ByteBuf](http://netty.io/4.1/api/io/netty/buffer/ByteBuf.html)
3. ByteBuf是一个引用计数对象，需要显式调用release()释放。需要注意，通常由handler释放传给handler的引用计数的对象。通常channelRead()像这样实现：
```
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) {
    try {
        // 对msg的操作
    } finally {
        ReferenceCountUtil.release(msg);
    }
}
```
4. exceptionCaught()方法在Netty产生I/O异常时被调用。通常产生异常要记录并关闭通道。当然你也可能在关闭前发送响应code和message。

现在创建一个Server和main()方法来启动服务器。
```
package io.netty.example.discard;

import io.netty.bootstrap.ServerBootstrap;

import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelOption;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;

/**
 * Discards any incoming data.
 */
public class DiscardServer {

    private int port;

    public DiscardServer(int port) {
        this.port = port;
    }

    public void run() throws Exception {
        EventLoopGroup bossGroup = new NioEventLoopGroup(); // (1)
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap(); // (2)
            b.group(bossGroup, workerGroup)
             .channel(NioServerSocketChannel.class) // (3)
             .childHandler(new ChannelInitializer<SocketChannel>() { // (4)
                 @Override
                 public void initChannel(SocketChannel ch) throws Exception {
                     ch.pipeline().addLast(new DiscardServerHandler());
                 }
             })
             .option(ChannelOption.SO_BACKLOG, 128)          // (5)
             .childOption(ChannelOption.SO_KEEPALIVE, true); // (6)

            // 绑定开启接收接入的连接。
            ChannelFuture f = b.bind(port).sync(); // (7)

            // 等待直到服务器socket关闭。
            // 这个例子里不会发生，但是你可以平滑的关闭服务器。
            f.channel().closeFuture().sync();
        } finally {
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws Exception {
        int port;
        if (args.length > 0) {
            port = Integer.parseInt(args[0]);
        } else {
            port = 8080;
        }
        new DiscardServer(port).run();
    }
}
```
1. [NioEventLoopGroup](http://netty.io/4.1/api/io/netty/channel/nio/NioEventLoopGroup.html)是一个多线程循环事件处理器。Netty提供多种[EventLoopGroup](http://netty.io/4.1/api/io/netty/channel/EventLoopGroup.html)实现适应不同传输协议。在服务端，通常有两个NioEventLoopGroup，第一个为boss，接收接入的连接。第二个为worker，处理boss注册的连接。至于有多少线程可以处理Channels，取决于EventLoopGroup的实现，一般可以在构造器指定。如果不指定thread数，默认是用于Netty的处理器数的两倍。
2. [ServerBootstrap](http://netty.io/4.1/api/io/netty/bootstrap/ServerBootstrap.html)是创建Server的工具类。可以用{% post_link Channel Channel %}直接创建Server。但直接用Channel构建比较繁琐，还是ServerBootstrap简单。
3. 用[NioServerSocketChannel](http://netty.io/4.1/api/io/netty/channel/socket/nio/NioServerSocketChannel.html)初始化Channel，接收接入的连接。
4. 这里的handler会赋一个新的被接收的Channel。[ChannelInitializer](http://netty.io/4.1/api/io/netty/channel/ChannelInitializer.html)是一个特殊的handler，用来替用户配置一个新的Channel。通常你会配置新Channel的{% post_link ChannelPipeline ChannelPipeline %}，添加些handlers比如DiscardServerHandler，来实现你的网络应用。