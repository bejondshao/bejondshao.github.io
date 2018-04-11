layout: draft
title: Java 8 新特性
author: Bejond Shao
categories:
  - Java
tags:
  - Java 8
date: 2018-04-09 14:27:00
---
##### Lambda表达式和函数式接口
Lambda表达式（也叫闭包），最简单的形式可以用逗号分隔的参数列表， -> 符号和功能语句块来表示。 语法如下：
```
(parameters) -> expression
或
(parameters) -> {statements;}

```
例如：
```
	Arrays.asList("a", "b", "c").forEach((String e) -> System.out.println(e));
```
其中参数类型可省，编译器根据上下文自动推测。 功能块复杂可以用大括号括起来，像方法体一样：
```
	Arrays.asList("a", "b", "c").forEach(e -> {
		e += ",";
		System.out.println(e);
	});
```
Lambda表达式有返回值， 编译器会根据上下文推断返回值的类型。 如果Lambda的语句块只有一行， 不需要return关键字。 下面两个写法是等价的：
```
	Arrays.asList("a", "b", "d").sort((e1, e2) -> e1.compareTo(e2));
```
```
	Arrays.asList("a", "b", "d").sort((e1, e2) -> {
    	int result = e1.compareTo(e2);
    	return result;
	});

```

总结下Lambda表达式的几个特性：

    可选类型声明： 不需要声明参数类型， 编译器可以统一识别参数值。
    可选的参数圆括号： 一个参数无需定义圆括号， 但多个参数需要定义圆括号。
    可选的大括号： 如果主体包含了一个语句， 就不需要使用大括号。
    可选的返回关键字： 如果主体只有一个表达式返回值则编译器会自动返回值， 大括号需要指定明表达式返回了一个数值。

Lambda表达式只能引用标记了**final**的外层局部变量， 这就是说不能在Lambda内部修改定义在域外的局部变量。 
Lambda表达式主要用来定义行内执行的方法类型接口， 例如， 一个简单方法接口。
Lambda表达式免去了使用匿名方法的麻烦， 并且给予Java简单但是强大的函数化的编程能力。
注意， Lambda表达式当中不允许声明一个与局部变量同名的参数或者局部变量。

##### 默认方法
Java 8引入**default**关键字修饰接口的方法， 该方法可以有方法体。 实现接口的类可以覆盖默认方法。
```
private interface Defaulable {
    // Interfaces now allow default methods, the implementer may or
    // may not implement (override) them.
    default String notRequired() {
        return "Default implementation";
    }
    default void defaultMethod() {}
}

private static class DefaultableImpl implements Defaulable {
}

private static class OverridableImpl implements Defaulable {
    @Override
    public String notRequired() {
        return "Overridden implementation";
    }
}
```

##### 函数式接口
函数式接口是**只有一个抽象方法**的接口， 函数式接口可以隐性的转换成Lambda表达式。 
Java 8提供@FunctionalInterface限定接口为函数式接口。 需要注意， default方法不算抽象方法， 带有@Override注解的继承自夫接口的方法也不算当前接口的方法。