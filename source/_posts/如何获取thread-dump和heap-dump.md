layout: post
title: 如何获取thread dump和heap dump
date: 2017-02-04 17:36:54
tags:
- Java
- JVM
category:
- Java
---
@ 远程到主机上
```language
$ ssh bejond@192.168.1.95
bejond@192.168.1.95's password:
```
输入登录密码，即可远程。

@ 获取服务器进程号：
Note: 执行jps（下面jstack, jmap等命令相同）时需要用运行java应用的用户来执行。这些用户往往被创建为nologin。比如创建wildfly用户来运行java程序。
```language
[bejond@centostest ~]$ sudo -u wildfly jps -l
[sudo] password for bejond:
```
上面命令意思为bejond用户以wildfly用户的身份执行jps来查看wildfly用户所运行的程序。
输入bejond用户的登录密码即可。输入密码后输入结果：
> 14273 /opt/wildfly/jboss-modules.jar
  14602 sun.tools.jps.Jps

这里看到进程号是14273。也可以使用`$ sudo -u wildfly jps -lv`，查看传递给JVM的参数。

> 14273 /opt/wildfly/jboss-modules.jar -D[Standalone] -Xms1024m -Xmx1024m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true -XX:-HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/wildfly/ -Dcom.sun.xml.bind.v2.bytecode.ClassTailor.noOptimize=true -Dorg.jboss.boot.log.file=/opt/wildfly/standalone/log/server.log -Dlogging.configuration=file:/opt/wildfly/standalone/configuration/logging.properties
14584 sun.tools.jps.Jps -Dapplication.home=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64 -Xms8m

Note: 如果jps提示command not found。在centos下安装的java默认没有devel包，执行
`$ sudo yum install java-1.8.0-openjdk-devel.x86_64`即可。

@ 生成thread dump。Thread dump展示所有线程和stack traces。你可以查看在生成thread dump时服务器的工作状态。

Note: 采集thread dump需要每隔10秒至少采集三次。也可以每隔5秒至少采集6次。这能体现服务器在这段时间的变化，比如哪个线程悬挂，可能是死锁，可能是等待其他线程返回。使用jstack命令生成thread dump，并保存到目录里。
`$ sudo -u wildfly jstack -l 14273 > /tmp/threaddump.txt`
Note: 保存文件位置要保存到/tmp中，因为/tmp文件夹允许任何用户进行读写，保存到其他文件夹会有可能保存失败。
Note: Windows系统下有个psexec软件，可以远程采集thread dump。但是需要在远程机器上配置客户端。

@ 分析thread dump。
1. 将threaddump拷贝到本机（我使用Gigolo连接远程）。
2. 使用 [IBM Thread and Monitor Dump Analyzer for Java](https://www.ibm.com/developerworks/community/groups/service/html/communityview?communityUuid=2245aa39-fa5c-4475-b891-14c205f7333c) 工具分析，下载到本机，执行
`$ java -jar jca_xxx.jar`
{% asset_img 1.png Thread Dump List %}
{% asset_img 2.png Thread Status Analysis %}
如何分析thread dump这里暂不介绍了。

@ 获取Heap dump

Heap dump是java堆的一个快照。在发生OutOfMemory问题时，heap dump是非常重要的根据。比如堆中最大的对象有泄露内存的嫌疑。
使用jmap命令获取heap dump。远程到主机，执行
`$ sudo -u wildfly jmap -dump:format=b,file=/tmp/heapdump.hprof 14273`
@ 使用Eclipse Memory Analyzer分析heap dump。

1. 找最大的对象
2. 找EMA建议的Leak Suspects，找的对象最好涉及到项目的类，而非第三方或JVM类。

在找问题前先浏览server.log和boot.log文件。

使用得手的工具，分析日志和dump会让你离找到问题根源更进一步。

@ heap dump可以设定jvm参数，在触发OOM时自动生成：

-XX:-HeapDumpOnOutOfMemoryError 参数表示当JVM发生OOM时，自动生成DUMP文件。

-XX:HeapDumpPath=${目录}参数表示生成DUMP文件的路径，也可以指定文件名称，例如：XX:HeapDumpPath=/tmp/java_heapdump.hprof。如果不指定文件名，默认为：java_<pid>_<date>_<time>_heapDump.hprof。
