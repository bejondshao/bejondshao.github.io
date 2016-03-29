layout: post
title: Java 内存管理机制
date: 2016-03-29 12:05:42
tags:
- Java
- JVM
category:
- Java
---
English Original Link: http://www.journaldev.com/2856/java-jvm-memory-model-and-garbage-collection-monitoring-tuning

 JVM Heap被分为两部分, Young Generation, Old Generation. Perm并不属于Heap
{% asset_img Java-Memory-Model.png Java Memory Model %}
**Young Generation (Young Gen)**
所有的new出来的对象都放在Young Gen, 当Young Gen满了, 就会执行Garbage Collection (GC), 此时的GC称为Minor GC. Young Gen被分成三部分: Eden Memory和两个Survivor Memory.
* 大多数新创建的对象都分配在Eden memory.
* Eden满了怎么办? JVM就会执行Minor GC. 被引用的对象都会存活下来, 它们将被移到Survivor Memory里, 图中的S0或S1.
* Eden满了就会执行Minor GC, 满了就执行. Minor GC执行时总会检查Survivor对象, 并且将其中一个Survivor区的对象移到另一个Survivor区里, 方便统计存活的对象大小. 也就是说, 总有一个Survivor区是空的.
* 在S0, S1轮回几次的存活对象都是比较坚挺的, 所以干脆就移到Old Memory了.
通常来讲, 对象移动到Old Memory是有标准的, 设定一个时间值来确定是否移动.
* 在Minor GC时, Old Gen可以被忽略. Old Gen对Young Gen的引用被认为是实际的GC Root。

**Old Generation (Tenured Gen)**
        Old Gen也叫Tenured Generation. 上面说了, Old Gen都保存比较坚挺的对象, 这些对象是时常被使用的, 起码也是使用频率很高的. 但是Old Memory满了怎么办? 会执行GC, 叫Major GC. Major GC只会清理Old Gen, 耗时比较长.

**世界静止(Stop the world event)**
        所有的GC都会让世界静止, 垃圾回收的时候, 所有线程的工作都暂停, 请求和响应都暂停. Minor GC时数据量比较小, 所以Minor GC很快, 基本感觉不到. 但是Major GC由于要检查所有活着的对象, 花费时间比较长. 应该让Major GC的时间尽可能短(这是很难滴). 所以, 如果服务器短时间频繁做Major GC, 会导致time out问题.
        GC的耗时取决于使用什么策略进行GC, 所以JVM调优会比较影响性能.

**Permanent Generation (Perm Gen)**
        Perm Gen保存应用的元数据, 包括该应用的类, 方法的定义和实现, 也包括java的类库和API. 注意, Perm Gen不属于Heap. Perm Gen的对象在full GC的时候会被回收. (Full GC又是什么鬼?)

**Full GC**
        Full GC区别与Major GC, Major GC只会清理Old Gen. 而Full GC会清理整个jvm 内存的内容, 包括Young Gen, Old Gen, Perm.[触发Full GC的情况](http://bejondshao.github.io/2016/03/29/%E8%A7%A6%E5%8F%91Full-GC%E7%9A%84%E6%83%85%E5%86%B5/)

**Method Area**
        Method area是存储在Perm的一些类结构, 常量, 静态变量, 方法实现等.

**Memory Pool**
        Memory Pool是由JVM内存管理创建的一个内存池, 用于存储固定不变的对象. String Pool就是一个栗子. Memory Pool可能在Heap, 也可能在Perm, 取决于JVM内存管理的实现方式.

**Runtime Constant Pool**
        运行期常量池存储常量, 静态变量程序块和静态方法. 运行期常量池属于Method Area

**Java Stack Memory**
        栈内存用来执行一个线程(Thread), 程序抛异常显示的内容就是从栈获取的. 包括方法特定的值, 比如临时变量, 对其他对象的引用(当然了, 对象存储在Heap里). 栈就像一个试管一样, 先进来的变量存在栈底, 因此遵循先进后出(Last In First Out, LIFO)的顺序. 与Heap的区别详见[Difference between Stack and Heap Memory](http://www.journaldev.com/4098/java-heap-memory-vs-stack-memory-difference) .

**Java Heap Memory Switches**
        Java有很多变量可以设置,对jvm, heap的设置

|VM 设置|描述|
|---|---|
|-XMs|JVM启动时heap的初始大小|
|-XMx|heap的最大值|
|-Xmn|Young Gen的大小, 剩下的就是Old Gen的大小|
|-XX:PermGen|Perm Gen初始大小|
|-XX:MaxPermGen|Perm Gen最大值|
|-XX:SurvivorRatio|Eden与单个Survivor的比率, 如果-XX:SurvivorRation=8(默认值), 那么就是Eden:S0=8:1, 所以Eden占Young Gen 8/10, S0和S1分别占Young Gen 1/10|
|-XX:NewRatio|Old与Young的比率, 默认值2|
更多配置 [JVM Options Official Page](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html) 和 [redcreen的专栏--JVM系列三:JVM参数设置、分析](http://www.cnblogs.com/redcreen/archive/2011/05/04/2037057.html).

