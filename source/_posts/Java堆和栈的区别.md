layout: post
title: Java堆和栈的区别
date: 2016-03-31 13:37:02
tags:
- Java
- JVM
category:
- Java
---
**Java Heap Memory(堆)**
堆是用来存放java程序运行时的对象和jre的类的. 我们创建的对象总是存在堆里. GC就是清理堆中没有被引用的对象. 堆里的对象是全局可见可引用的.
**Java Stack Memory(栈)**
栈用于线程运行时存放变量的, 包括临时变量, 指向存放在堆里的对象的引用等. 栈就像一个试管一样, 遵循后进先出(Last In First Out). 当程序调用一个方法时, 就会在栈里取一块区域, 用来分配临时变量和参数. 当方法执行完之后, 这块区域就释放了. 所以当有方法1调用方法2时, 会先申请一块区域存放方法1的内容, 然后再在栈里申请一块区域存放方法2的内容(Last In), 而方法2肯定会在方法1前结束, 因此先释放(First Out), 然后再释放方法1的区域. 栈相对于堆的存取速度快很多, 同时栈比堆小很多.

举个例子
```java
package com.bejond.testheapandstack;
public class Test{
    public static void main(String[] args) {
        int a = 1;
        Object object = new Object();
        Test test = new Test();
        test.foo(test);
      }
  
    private void foo(Object object) {
        String string = object.toString();
    }
}
```
下图显示栈和堆的数据分配情况.
{% asset_img heap_stack.png Heap and Stack %}

咱们一步一步来.
* Line 4, main()方法开始, 就会在栈申请一个空间.
* int a 是基本类型数据, 放在栈里. 
* object是临时变量, 放在栈里, 创建的Object放在堆里, test也同object.
* Line 9, 调用方法foo, 所以在栈申请一块空间给foo()
* foo()的参数obj, 是一个对象的引用, 存在栈里. 由于传的是object, 所以obj指向object所引用的对象. 
* string存在栈里, 它指向object.toString()返回的字符串, 字符串存在String Pool里.
* 当然, fool()方法调用toString(), toString()也会在栈里申请空间.
* 当toString()执行完成后, toString()的空间就释放了.
* 当foo()执行完成后, foo()的空间也释放了.
* 接着执行main(), Line 10, main()执行完成, 也会被释放.

**堆和栈的区别**
1. 堆用来存储java程序所有创建的对象, 而栈针对某个线程存储临时变量和参数.
2. 当我们new一个对象时, 它总是存储在堆里. 栈存放一个引用, 也就是个名字, 指向堆中的对象. 
3. 堆中的对象是全局可见的, 而栈中的内容只是当前线程可见.
4. 当方法执行完, 临时变量就被销毁, 栈遵从FILO原则释放内容, 而堆里的对象仍然存在, 成为垃圾对象, 然后会随着GC被清理掉. (所以我们在方法中应尽可能少的创建对象)
5. 我们可以使用-Xms和-Xmx设定启动时堆大小和堆的最大值, 用-Xss设定栈的大小.
6. 当堆满了, 并且GC后仍然没法分配内存给新对象时, 会抛`java.lang.OutOfMemoryError: Java Heap Space`错误. 而栈满了会抛`java.lang.StackOverFlowError`错误.
7. 栈相对于堆来讲很小, 由于使用FILO方式管理栈, 所以栈的存取速度比堆快很多.