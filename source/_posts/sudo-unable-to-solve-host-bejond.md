layout: post
title: 'sudo: unable to solve host hostname'
date: 2016-03-10 17:12:44
tags:
- Hosts
- Linux
category:
- Linux
---


1.症状：
当你是使用sudo来执行命令时，总是在命令执行时，首先输出：“sudo:unable to resolve host Lily-desktop”（假设Lily-desktop 是你的当前主机的名字（host-name)）, 但是怎样才能使使用sudo像以前一样不错误提示，正常工作呢。
 
when you’re running commands with sudo at beginning.It outputs “sudo:unable to resolve host Lily-desktop”(here assume current host-name is Lily-desktop),however the commands work as well as before.
 
 
2.产生该症状的原因：
这种症状经常发生在更改了hostname的情形下。在通过 编辑  /etc/hostname 改变了主机名字（hostname）之后， 我们还需要改变 /etc/hosts文件的相应部分的内容
 
This always happens after host-name changed.After change host-name by edit /etc/hostname,we also need to do a change in /etc/hosts file.
 
3.解决方案：
Solve:
Edit /etc/hosts:   // 编辑  /etc/hosts 文件
gksudo gedit /etc/hosts
make it looks like (change boldfaced words to current host-name): //使粗体字对应的词与当前的主机名即hostname 一致
127.0.0.1	localhost 127.0.1.1	Lily-desktop
以上信息来自 http://blog.csdn.net/wzb56_earl/article/details/6289988

http://nixcraft.com/ubuntu-debian/13543-ubuntu-sudo-unable-resolve-host.html


// 对于解决方案可以直接用命令 echo "127.0.0.1 yourhostname" >> /etc/hosts
这样会直接将主机名对应127.0.0.1追加到hosts末尾

// echo "test" > a.txt 会清除a.txt的内容, 然后添加"test"
