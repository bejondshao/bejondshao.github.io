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

翻译到这里，我在官网没找到Architectural Overview 4.x相关章节，于是谷歌一下，我找到了[这个](https://doc.yonyoucloud.com/doc/netty-4-user-guide/Architectural%20Overview/Architectural%20Overview.html) 。幸好我没全翻译:joy:，不过在翻译前我已经读完这篇指南了，以后看官方文档前先看看有没有中文翻译，就酱:confused:，全文完。
