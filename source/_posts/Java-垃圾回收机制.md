layout: post
title: Java 垃圾回收机制
date: 2016-03-29 12:11:27
tags:
- Java
- JVM
category:
- Java
---
English Original Link: http://www.journaldev.com/2856/java-jvm-memory-model-and-garbage-collection-monitoring-tuning

**Java Garbage Collection(GC)**
GC就是移除未被引用的对象(垃圾对象), 清理出内存来分配其他新对象. Java垃圾回收是自动的, 这基本不需要程序员管理, 区别于C C++等语言, 需要手动分配内存, 手动释放. 所以这使得Java程序员在内存使用时显得肆无忌惮, 对象回收也无法插手, 所以Java自动回收垃圾机制也成为Java项目的一大诟病. 项目到一定规模势必会有性能问题. 因此发现性能问题, 解决性能问题是一项有用的技能.

GC在后台运行, 查找所有的对象, 找到垃圾对象, 然后删除它们. GC一般包括这三步:

1. 标记: 将垃圾对象标记成可被回收状态.
2. 删除: 就是删除啦.
3. 压缩: 在删除垃圾对象后, 为了更好的性能, 会将存活的对象重新整理到一起, 这样会空出成块的内存空间.

上述GC过程有问题, 有什么问题?

1. 很多刚创建的对象就是未被使用的.
2. 在很多GC周期里的对象很可能被未来其他GC周期使用. 就是说很常用的对象可能在未来还会被使用, 如果删除了岂不可惜.

有什么好方法? Java回收机制利用Yong Gen的Minor GC和Old Gen的Major GC来处理这两个问题. Minor GC只会在Eden满了的时候执行, 所以可以避免问题1. Major GC会在Old Gen满了执行, 所以会避免问题2.

**Java GC 种类**
GC一共有五种, 我们可以通过配置来使用不同的机制
1. Serial GC(-XX:+UseSerialGC): 串行GC使用标记-删除-压缩的模式来进行Minor GC和Major GC. 串行GC一般适用于客户端, 比如个人电脑, 非集群的服务器. 适用于CPU不太强悍的时候.
2. Parallel GC(-XX:+UseParallelGC): 并行GC相对于串行GC, 是用N个线程执行Minor GC, N一般和CPU核心数相同, N可以通过-XX:ParallelGCThreads=n来控制. N并不是越大越好, 不要超过CPU核心数.
3. Parallel Old GC (-XX:+UseParallelOldGC): 这种方式也是并行GC, 区别前者是, 这种方式在Young Gen和Old Gen都会使用并行机制.
4. Concurrent Mark Sweep (CMS) Collector (-XX:+UseConcMarkSweepGC): CMS叫时实标记清理机制(我起的, 高大上吧, 哈哈哈). 就是在Old Gen里, 标记了垃圾对象, 不等Old Gen满, 就开始回收. 这有一个好处是每次回收的对象很少, stop-the-world的时间很短, 低延迟. 这在响应速度要求高的应用上使用, 比如购物网站, 如果网站暂停时间久了, 客户可能就关网页不买了, 去别家去买了, 这哪行. 但是, 这种方式, 想想就明白, 肯定需要强悍的硬件支持, 频繁的GC都会是很大的开销. CMS在Young Gen的方式和Parallel GC一样, 我们可以用-XX:ParallelCMSThreads=n来限制回收进程数.
5. G1 Garbage Collector (-XX:+UseG1GC): 垃圾第一, 也叫G1, 这种模式在java 7以上的版本可用, 从长远来看, 它要替代CMS回收模式. G1回收也是并行的, 时实的, 并且是增量压缩的.
这种方式不考虑Young Gen还是Old Gen, 它将Heap分成几等份区域. 当JVM调用GC时, 它先清理存活对象少的区域, 也就是哪里垃圾多扫哪里. 更多信息[Garbage-First Collector Oracle Documentation](http://docs.oracle.com/javase/7/docs/technotes/guides/vm/G1.html). 

