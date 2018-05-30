layout: post
title: Intellij debug模式启动很慢调整
tags:
  - Java
  - Intellij
categories:
  - Tools
date: 2018-05-30 20:02:00
---
{% asset_img intellij.png Intellij Idea %}

1. 移除不需要的断点，在debug窗口，点击在Stop按钮下有个`View breakpoints`按钮，可以移除断点。也可以编辑当前project的`.idea/workspace.xml`文件，找到`method_breakpoints`节点，移除不需要的节点。可以先`mute breakpoints`启动，速度提升很多。

2. 检查debug窗口，settings，"Show values inline"(在代码后面动态显示变量内容)，"Show method return values"（显示方法返回的值），酌情关闭。开启这两个选项会有性能消耗。

3. Settings -> Debugger -> Data Views -> Java，"Enable alternative view for Collections classes"（对集合有额外的展示界面）和"Enable 'toString()' object view"（展示object的toString()方法）酌情关闭。这两个也会有性能消耗。

4. 最小化`Memory`小窗。这个`Memory`小窗是用来监测运行时变量所占内存的，一般不必开启，默认是最小化的。

5. 获取hostname，然后在/etc/hosts里添加

```
127.0.0.1 your_host_name.local
::1 your_host_name.local
```


##### 参考

[[1] Intellij Support: Java: slow performance or hangups when starting debugger and stepping](https://intellij-support.jetbrains.com/hc/en-us/articles/206544799-Java-slow-performance-or-hangups-when-starting-debugger-and-stepping)

[[2] Stackoverflow: IntelliJ IDEA debugger is too slow to start on macOS](https://stackoverflow.com/questions/44680463/intellij-idea-debugger-is-too-slow-to-start-on-macos)

