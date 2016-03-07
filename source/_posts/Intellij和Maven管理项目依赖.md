layout: post
title: Intellij和Maven管理项目依赖
date: 2016-03-07 12:41:44
tags:
- Intellij
- Maven
category:
- Tools
---
在了解maven以前, 我们的项目依赖都是放在WEB-INF/lib/下, 并且跟随项目打包, 这样有一个好处是移植性很好, 并且可以通过版本管理,比如git管理jar包. 产品下载下来就可以使用, 但是弊端在于占用了网络资源. 当lib大于100M的时候, 可以想象其他用户要下载要多花费很多时间. 而开发者下载时更是浪费了资源, 因为很多jar包开发者本来自己就具备了, 不必下载. 另外多个项目共同部署在一个服务器下时, jar包依赖会导致冲突, 这种问题算是莫名其妙的一类, 因为这种问题自己很难找到原因, 在网上搜索获得的答案几率比较小.
总的说来, maven管理项目依赖有利有弊, 但随着maven在开发者社区的普及, 会令项目移植更方便, 更快捷.

这几天在研究maven, 我们知道maven的项目依赖都记录在pom.xml, 这有一个好处是这个文件可以受git管理, 并且要更新更改jar包依赖只需要更改这个文件即可. 这个文件是手动编辑的, 编辑起来也不算费劲. 但是考虑到以前项目依赖都是放在一个目录里, 在Intellij或eclipse添加依赖就可以了, 而maven的依赖要手动写,未免有点不爽.
其实Intellij能自动生成依赖, 并且将依赖添加到module的依赖中. 这正是本文要说的.

在介绍Intellij与maven结合之前, 先介绍下Intellij自身对依赖的处理. 每当我们创建project或module时, 都会创建一个module-name.iml文件, 这个文件是Intellij对module的记录, 包括module类型,所包含的组件,以及依赖包的记录. 每当我们手动添加依赖后, 都会在iml文件中记录, 如图:
{% asset_img intellij-dependency.png Intellij Dependency  %}

但是这个iml是属于Intellij的, 我们一般都是忽视这个文件的, 就是将其添加到.gitignore中. 因为如果这个文件被版本管理, 这个文件就会被其他开发者(或者你的另一个终端)下载, 而这个文件往往会导致冲突, 每次解决与项目无关的文件的冲突, 会不会有种想删了它的冲动, 对, 所以我们不管理这个文件. 所以接下来的操作, 我们不关注iml文件, 所有的项目依赖都由pom.xml管理.

pom.xml这个文件的更改就涉及jar包的更新, 算是重大的更新, 所以每次更改都推荐将其单独提交.

1. 用Intellij创建一个单独的maven module, 叫testmaven.
{% asset_img intellij-create-maven.png Intellij Create a Maven Module %}

2. 创建Hello.java
```
package com.bejond.testmaven;
/**
 * Created by bejond on 16-3-7.
 */
public class Hello {
	public void main(String[] args) {
		System.out.println("Hello!");
	}
}
```

3. 创建TestHello.java
package com.bejond.testmaven;
```
/**
 * Created by bejond on 16-3-7.
 */
public class TestHello {

   @Test
   public void testHello() {
      Hello hello = new Hello();
      hello.main(new String[] {});
   }
}
```
4. 这时会发现@Test注解不存在, 我们需要配置pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.bejond.testmaven</groupId>
    <artifactId>testmaven</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
5. 每当pom.xml更改, Intellij就会检测到, 并且提示你导入更改, 以影响项目的依赖.
{% asset_img before-import.png Add pom.xml Dependency %}

6. 点击"Import Changes", 这时, 打开module settings (F4), 会发现项目依赖添加了Junit(以Maven开头), 说明pom起作用了.
{% asset_img after-import.png After Import Changes %}

7. 然后到TestHello.java引入就可以了. (或者使用快捷键"Alt Enter")
```
import org.junit.Test;
```
==================================
Intellij更强大的在于, 它可以预测我们要导入的包, 也就是说pom.xml依赖可以由Intellij生成!
在第4步, 转到TestHello.java, 因为我们在这个文件要使用新的包. 点击"@Test", 等待三秒, 然后在弹出的小灯泡点击"Add Maven Dependency". (上述过程可以使用快捷键"Alt Enter"), 
{% asset_img add-maven-dependency.png Intellij add Maven Dependency %}

选择要使用的jar. 
{% asset_img add-jar.png Select Jar %}

﻿然后再执行第5,6,7就可以了.

==================================
所以现在我们回顾一下, 当我们利用maven管理包, 并且通过pom.xml记录依赖时, 我们不必手动下载对应的包(当中央资源库没有时, 也需要手动下载并指定), 然后在我们使用包时, 通过Intellij为我们生成pom依赖, 进而就可以正常使用了. 

虽然想要达到的目的和以前一样, 但是新的依赖管理方式让项目依赖更方便, 更智能. 当然Maven还有其他的项目管理功能, 比如编译, 测试, 打包, 安装以供其他项目使用, 很方便.


