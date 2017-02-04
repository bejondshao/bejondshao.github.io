layout: post
title: 如何获取thread dump和heap dump
date: 2017-02-04 17:36:54
tags:
- Java
- JVM
category:
- Java
---

@ 获取服务器进程号：
```
C:\Users\Bejond>jps -l
3584 sun.tools.jps.Jps
5248
7088 E:\code\server\wildfly-10.0.0.Final_\jboss-modules.jar
5156 org.jetbrains.jps.cmdline.Launcher
5560 org.jetbrains.idea.maven.server.RemoteMavenServer
6716 com.intellij.rt.execution.CommandLineWrapper
```
也可以使用 jps -lv，查看传递给JVM的参数。这里看到进程号是7088

@ 生成thread dump。Thread dump展示所有线程和stack traces。你可以查看在生成thread dump时服务器的工作状态。

Note: 采集thread dump需要每隔10秒至少采集三次。也可以每隔5秒至少采集6次。这能体现服务器在这段时间的变化，比如哪个线程悬挂，可能是死锁，可能是等待其他线程返回。使用jstack命令生成thread dump，并保存到目录里。
```
jstack -l 7088 > e:\threaddump.txt
```

###### psexec软件可以远程采集thread dump。但是需要在远程机器上配置客户端。

@ 使用 IBM Thread and Monitor Dump Analyzer for Java 工具分析，执行
```
java -jar jca457_001.jar
```
{% asset_img 1.png Thread Dump List %}
{% asset_img 2.png Thread Status Analysis %}

@ Heap dump

Heap dump是java堆的一个快照。在发生OutOfMemory问题时，heap dump非常重要的根据。比如堆中最大的对象有泄露内存的嫌疑。
使用jmap命令获取heap dump。
```
jmap -dump:format=b,file=e:\heapdump.hprof 7088
```
@ 使用Eclipse Memory Analyzer分析heap dump。

1. 找最大的对象
2. 找EMA建议的Leak Suspects，找的对象最好涉及到项目的类，而非第三方或JVM类。

在找问题前先浏览server.log和boot.log文件。

使用得手的工具，分析日志和dump会让你离找到问题根源更进一步。

@ heap dump可以在触发OOM时自动生成：

-XX:-HeapDumpOnOutOfMemoryError 参数表示当JVM发生OOM时，自动生成DUMP文件。  

-XX:HeapDumpPath=${目录}参数表示生成DUMP文件的路径，也可以指定文件名称，例如：XX:HeapDumpPath=${目录}/java_heapdump.hprof。如果不指定文件名，默认为：java_<pid>_<date>_<time>_heapDump.hprof。 
