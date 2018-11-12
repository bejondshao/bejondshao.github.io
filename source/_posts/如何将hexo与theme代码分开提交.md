layout: post
title: 如何将hexo与theme代码分开提交
tags:
  - Hexo
  - Theme
category:
  - Others
categories:
  - Others
  - Hexo
date: 2017-11-30 22:29:00
---
###### 说在前面
Hexo博客静态页面是用hexo-deployer-git插件自动提交到bejondshao.github.io库中的master分支的。为了保存文章和自定义内容，我将Hexo框架，文章和自定义内容保存在source分支下。平时编辑提交都在source分支做，执行hexo deploy时hexo-deployer-git自动将静态内容提交到master，不必手动切换分支，很方便。

言归正传，之前有介绍过{% post_link Hueman主题升级 升级Hexo主题 %}。里面手动下载源码，手动自定义，随着自定义内容越来越多，每次升级主题会很麻烦。所以有必要将主题也提交到代码库中。但是将主题提交到Hexo与文章中显然不合适，并且想和[hexo-theme-hueman](https://github.com/ppoffice/hexo-theme-hueman)保持同步会很麻烦。所以有必要将主题单独提交到另一个资源中。

1. 将本地`bejondshao.github.io/themes/hueman/`文件夹备份。
2. 将hueman主题从源码移出
`$ git rm -r themes/hueman`（将themes/hueman文件夹以及内容递归移除资源库）
3. 编辑`bejondshao.github.io/.gitignore`文件，在末尾添加`themes/hueman/`
4. 将更改提交到github的source分支：
```
$ git add . （将之前的操作的所有文件添加为待提交内容）
$ git status （查看是否添加正确）
$ git commit -m "source remove hueman" （提交，引号为提交信息，建议提交信息以branch+message格式提交，message尽量写得清晰。）
$ git push origin source（将内容提交到远端。如果提交与远端有冲突，有个解决方法是加-f参数，这会覆盖远端的内容，谨慎使用，因为覆盖不可恢复。）
```
经过以上四步，hueman已经从bejondshao.github.io中完全移出了。以后在hueman中的修改都不会被bejondshao.github.io的git检测到。
5. 将[hexo-theme-hueman](https://github.com/ppoffice/hexo-theme-hueman) fork到自己的账号中。
6. 克隆资源库到本地。
`$ git clone git@github.com:ppoffice/hexo-theme-hueman.git`
会在本地创建hexo-theme-human文件夹，里面包含主题源码。将hexo-theme-human重命名为hueman。
7. 将第一步备份的文件拷贝，覆盖第六步的文件夹。这样所有自定义修改都还在。将hueman文件夹拷贝到`bejondshao.github.io/themes/`下。
8. 编辑hueman/.git/config如下：
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "upstream"]
	url = git@github.com:ppoffice/hexo-theme-hueman.git
	fetch = +refs/heads/*:refs/remotes/upstream/*
[remote "origin"]
	url = git@github.com:bejondshao/hexo-theme-hueman.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```
9. 编辑hueman/.gitignore，将_config.yml那一行移出，因为我们应该把_config.yml自定义的内容也提交到资源库中。
10. 将内容提交到远端库中。我建议切一个新的分支来存储带自定义的内容。
```
$ git checkout -b bejondshao （切创建一个新分支，并切换到那个分支）
$ git add .
$ git commit -m "bejondshao customization"
$ git push origin bejondshao:bejondshao （将本地分支bejondshao的修改提交到远端，并在远端创建bejondshao这个分支）
```
这样，以后更改主题，在hueman资源库中提交，不会影响hexo主体内容。
11. 以后升级主题，只需要执行：
```
$ git checkout bejondshao （确保在分支bejondshao操作，正常也是在这个分支，因为基本不会用到master分支）
$ git pull --rebase upstream master （将upstream那个资源的内容获取到本地，并以upstream的master分支作为当前分支的base，即实现了应用upstream的更新。）
$ git push origin bejondshao （将新的内容提交到个人账户的资源库中。）
```
12. 做完以上操作，已经完成处理好hexo文章和主题的分离了，执行
```
$ hexo clean
$ hexo g
$ hexo s 或 hexo d
```
查看下结果吧:beer:！