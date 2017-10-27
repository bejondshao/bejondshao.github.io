---
layout: post
title: Hexo使用经验
date: 2017-10-27 19:38:52
tags:
- Hexo
category:
- Others
---
**首先，除非手痒，不要乱升级Hexo。**如果你实在没忍住，升级了，出问题了，嘻嘻，关了这个网页吧，哥帮不了你（笑哭）。
本文仅做使用Hexo时遇到的问题汇总，如果对你有帮助，那就有吧。

@ Hexo官方安装方式
`$ npm install hexo-cli -g`
注意是是普通用户，如果安装时遇到权限问题，请想尽办法解决。不要用root安装，会出各种奇怪的问题。
@ 安装后，写好文章，在执行 hexo deploy 后,出现 error deployer not found:git 的错误。
`$ npm install hexo-deployer-git --save`
@ 如果hexo deploy后，输入网站域名，显示404了，但是通过url访问页面还能访问到。
```
$ hexo clean
$ hexo g
$ hexo d
```
再访问试试
@ 在不同系统下，比如OS X和Linux，package-lock.json是不一样的。我建议把package-lock.json加入.gitignore中。
在package-lock.json所在目录执行一下命令：
```
$ cp package-lock.json ~/package-lock.json
$ git checkout package-lock.json
$ git reset package-lock.json
$ git rm --cached package-lock.json
```
编辑 .gitignore，在末尾填入package-lock.json
```
$ cp ~/package-lock.json .
$ rm -f ~/package-lock.json
$ git add .
$ git commit -m "source the text you want to add"
$ git push origin -f source
```
@ 不同系统下，比如OS X和Linux，package.json不同，一定要保证两个环境都是用相同版本的Hexo和插件。我建议都升到最新版。