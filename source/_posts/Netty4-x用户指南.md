2---
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
5. 你可以对Channel实现设定参数。这里我们在写TCP/IP服务器，所以我们可以设定tcpNoDelay，keepAlive等参数。具体参数请参考[ChannelOption](http://netty.io/4.1/api/io/netty/channel/ChannelOption.html)和[ChannelConfig](http://netty.io/4.1/api/io/netty/channel/ChannelConfig.html)
6. option()是针对boosGroup接收的channels，也就是NioServerSocketChannel。childOption()是针对父ServerChannel所接收的channels，在这里也是NioServerSocketChannel。
7. 绑定端口，启动服务器。这里绑定8080端口到所有的NICs（network interface cards，网卡）。你可以随意调用bind()，但需要绑定不同的端口。

##### 看下接收的数据
测试服务器最简单的方式是在命令行输入`$ telnet localhost 8080`，然后输入内容。
但没法确定这个服务器好用，我们需要在服务器输出点什么来体现它好用。
我们知道，channelRead()会在接收数据时被调用，所以我们可以在DiscardServerHandler里面加打印内容:
```
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) {
    ByteBuf in = (ByteBuf) msg;
    try {
        while (in.isReadable()) { // (1)
            System.out.print((char) in.readByte());
            System.out.flush();
        }
    } finally {
        ReferenceCountUtil.release(msg); // (2)
    }
}
```
1. 这个循环很低效，可以被替换为:
`System.out.println(in.toString(io.netty.util.CharsetUtil.US_ASCII))`
2. 这里可以替换为:
`in.release()`
如果再`telnet`，就会在服务器console看到打印内容。
Discard示例[源码](https://github.com/netty/netty/tree/4.1/example/src/main/java/io/netty/example/discard)

##### Echo示例
服务器一般要给客户端返回信息，我们来实现[ECHO](http://tools.ietf.org/html/rfc862)协议。其实很简单，在DiscardServerHandler里，将channelRead()改为:
```
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) {
    ctx.write(msg); // (1)
    ctx.flush(); // (2)
}
```
1. [ChannelHandlerContext](http://netty.io/4.1/api/io/netty/channel/ChannelHandlerContext.html)提供多种操作，让你触发多种I/O事件和操作。这里调用write(Object)将接收的信息逐字的输出。注意这里我们没release消息。因为Netty在将消息写出去时会自动释放。
2. ctx.write(Object)并没有将消息写出去。消息在内部缓存了，写是由ctx.flush()做的。你可以简单的用ctx.writeAndFlush(msg)来实现。

如果你再telnet，输入内容，就会发现有消息返回给客户端。
Echo示例[源码](https://github.com/netty/netty/tree/4.1/example/src/main/java/io/netty/example/echo)

##### Time服务器
实现[TIME](http://tools.ietf.org/html/rfc868)协议。这和前面不同的是，这次发送一个保护32位二进制数字的消息，不接受任何请求，并且在发送消息后关闭连接。
由于我们要忽略所接收的消息，并在连接建立时发送信息，这次不用channelRead()方法，而用channelActive()方法。
```
package io.netty.example.time;

public class TimeServerHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelActive(final ChannelHandlerContext ctx) { // (1)
        final ByteBuf time = ctx.alloc().buffer(4); // (2)
        time.writeInt((int) (System.currentTimeMillis() / 1000L + 2208988800L));
        
        final ChannelFuture f = ctx.writeAndFlush(time); // (3)
        f.addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture future) {
                assert f == future;
                ctx.close();
            }
        }); // (4)
    }
    
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```
1. channelAction()韩剧i在连接建立时被调用。这里我们随意写一个32位数据作为当前时间。
2. 要发送消息，需要分配新的缓冲区。由于数据是32位的，所以ByteBuf需要至少4个字节。通过ChannelHandlerContext.alloc()获取当前的[ByteBufAllocator](http://netty.io/4.1/api/io/netty/buffer/ByteBufAllocator.html)，并分配缓冲区。
3. 记录初始化的信息。ByteBuf发送消息时不需要调用`java.nio.ByteBuffer.flip()`。ByteBuf没有这样的方法，因为它有两个指针，一个用于读，一个用于写。在你向ByteBuf写内容时，写指针索引会增加，读指针索引不会变。读指针索引和写指针索引代表消息的开始和结束。
相反，NIO buffer不调用flip()是没法清晰的体现消息的开始和结束的。如果你忘记调用flip()，会很糟糕，很可能消息没有发送，也可能发送错误的消息。
ChannelHandlerContext.write()和writeAndFlush()方法返回[ChannelFuture](http://netty.io/4.1/api/io/netty/channel/ChannelFuture.html)。ChannelFuture代表还没发生的I/O操作。任何已请求的操作可能还没执行，因为Netty中所有的操作都是异步的。比如，下面的代码可能在消息发送前就关闭了连接:
```
Channel ch = ...;
ch.writeAndFlush(message);
ch.close();
```
所以，你需要注意，要在ChannelFuture完成后再调用close()方法。当写操作完成后，它会通知它的监听器。另外注意，close()可能不会直接关闭连接，这个方法返回的是ChannelFuture。
4. 在写请求完成后，我们怎么知道它完成了？在ChannelFuture添加一个[ChannelFutureListener](http://netty.io/4.1/api/io/netty/channel/ChannelFutureListener.html)就行了。这里，我们添加一个匿名ChannelFutureListener用来在操作完成时关闭channel。
你可以简单的用预定义的listener:
`f.addListener(ChannelFutureListener.CLOSE);`

测试time服务器，需要在命令行输入：
`$ rdate -o <port> -p <host>`

##### Time客户端
这节研究如何用Netty创建客户端。和服务端区别是Bootstrap和Channel的实现不同。
```
package io.netty.example.time;

public class TimeClient {
    public static void main(String[] args) throws Exception {
        String host = args[0];
        int port = Integer.parseInt(args[1]);
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        
        try {
            Bootstrap b = new Bootstrap(); // (1)
            b.group(workerGroup); // (2)
            b.channel(NioSocketChannel.class); // (3)
            b.option(ChannelOption.SO_KEEPALIVE, true); // (4)
            b.handler(new ChannelInitializer<SocketChannel>() {
                @Override
                public void initChannel(SocketChannel ch) throws Exception {
                    ch.pipeline().addLast(new TimeClientHandler());
                }
            });
            
            // Start the client.
            ChannelFuture f = b.connect(host, port).sync(); // (5)

            // Wait until the connection is closed.
            f.channel().closeFuture().sync();
        } finally {
            workerGroup.shutdownGracefully();
        }
    }
}
```
1. [Bootstrap](http://netty.io/4.1/api/io/netty/bootstrap/Bootstrap.html)和ServerBootstrap类似，不同的是Bootstrap用于非服务端，比如客户端，无连接channel。
2. 如果你只指定一个EventLoopGroup，它将会担当boss和worker组。这里并没用到boss组。
3. 这里用[NioSocketChannel](http://netty.io/4.1/api/io/netty/channel/socket/nio/NioSocketChannel.html)而不是NioServerSocketChannel。
4. 这里没有childOption()因为客户端SocketChannel没有父Channel。
5. 用connect()而非bind()。

客户端代码和服务端代码没多少差别。ChannelHandler实现要接收32位数据，转换成Date，打印出来，最后关闭连接:
```
package io.netty.example.time;

import java.util.Date;

public class TimeClientHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        ByteBuf m = (ByteBuf) msg; // (1)
        try {
            long currentTimeMillis = (m.readUnsignedInt() - 2208988800L) * 1000L;
            System.out.println(new Date(currentTimeMillis));
            ctx.close();
        } finally {
            m.release();
        }
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```
1. 在TCP/IP中，Netty读取数据，然后存到ByteBuf里。