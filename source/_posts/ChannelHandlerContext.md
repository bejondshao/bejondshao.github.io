---
layout: post
title: ChannelHandlerContext
date: 2017-11-22 19:20:32
tags:
- Java
- Netty
category:
- Java
---
io.netty.channel.[ChannelHandlerContext](http://netty.io/4.0/api/io/netty/channel/ChannelHandlerContext.html )

**Interface ChannelHandlerContext**

```
public interface ChannelHandlerContext
extends AttributeMap
```

使得ChannelHandler可以和它所在的ChannelPipeline以及其他handlers交互。一个handler可以在ChannelPipeline中通知下一个ChannelHandler，动态更改它所属的ChannelPipeline。

**通知**
你可以通过调用ChannelHandlerContext的多种方法通知同一个ChannelPipeline中最近的handler。请通过{% post_link ChannelPipeline %}了解事件是如何传递的。

**修改pipeline**
可以通过调用ChannelHandlerContext.pipeline()获取handler所属的ChannelPipeline。你可以在pipeline中动态的增加，移除，替换handlers。

**获取ChannelHandlerContext为了以后使用**
比如在handler方法外触发一个事件，甚至是在另一个线程触发事件。

```
 public class MyHandler extends ChannelDuplexHandler {

     private ChannelHandlerContext ctx;

     public void beforeAdd(ChannelHandlerContext ctx) {
         this.ctx = ctx;
     }

     public void login(String username, password) {
         ctx.write(new LoginMessage(username, password));
     }
     ...
 }
```

**存储有状态的信息**
AttributeMap.attr(AttributeKey)允许你存储，访问handler和context有状态的信息。请通过{% post_link ChannelHandlerContext %}获取多种存储信息的方式。

**一个handler可以属于多个contexts**
不难理解，一个ChannelHandler可以被添加到多个ChannelPipeline中。也就是说一个ChannelHandler实例可以有多个ChannelHandlerContext，因此如果一个ChannelHandler实例被多次添加到一个或多个ChannelPipelines中，那么它就可以被不同的ChannelHandlerContext调用。
下面例子handler将有多个独立的AttributeKeys，其数量和它被添加到pipelines的次数是一样的。不管它是被添加到同一个pipeline多次，还是添加到不同的pipelines。

```
 public class FactorialHandler extends ChannelInboundHandlerAdapter<Integer> {
   
      private final AttributeKey<Integer> counter =
             new AttributeKey<Integer>("counter");

         // This handler will receive a sequence of increasing integers starting
         // from 1.
         @Override
         public void channelRead(ChannelHandlerContext ctx, Object msg) {
         Integer a = ctx.attr(counter).get();

         if (a == null) {
           a = 1;
           attr.set(a);
         }
        else {
         attr.set(new Integer(a.intValue() + 1));
         }
       }
    }

     // 不同的context对象都给了"f1","f2"不同的名字，即使他们都指向同一个handler实例。
     // 因为FactorialHandler在context中存储了它的状态（使用AttributeKey）。
     // 当p1和p2开启时，实际上这个对象被计算了4次。
     FactorialHandler fh = new FactorialHandler();

     ChannelPipeline p1 = Channels.pipeline();
     p1.addLast("f1", fh);
     p1.addLast("f2", fh);

     ChannelPipeline p2 = Channels.pipeline();
     p2.addLast("f3", fh);
     p2.addLast("f4", fh);
```

**更多资源**
可以参考{% post_link ChannelHandler %}和{% post_link ChannelPipeline %}查阅更多关于inbound和outbound操作的信息，它们有什么不同的功能，它们在pipeline中是如何流通的，它们是如何在应用中处理操作的。