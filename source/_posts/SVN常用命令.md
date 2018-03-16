---
layout: post
title: SVN常用命令
date: 2018-03-16 16:34:21
tags:
- SVN
category:
- Tools
---
@ svn checkout http://url
@ svn add filename/dir
@ svn revert filename, 将修改恢复至原始状态
@ svn blame， 查看文件历史更改
@ svn copy filename, 拷贝文件
@ svn cleanup 清理工作副本。有时可能会出现“工作副本已锁定”错误。若要删除锁定，并递归清理工作副本，请使用 svn update
@ svn delete filename
@ svn diff 
@ svn export path1 path2
@ svn commit -m "asdf"
@ svn import path
svn import -m "asdf" URL
@ svn info
@ svn list
@ svn log path
@ svn merge 以指示 Subversion 将存储库中最新版本的文件合并到您的工作副本中。
@ `svn merge -r 1994:1993 .`  将r1994的所有改变回滚，只是回滚到本地，可以比较差异，需要做额外的commit操作才能提交到服务器。
@ svn mkdir path
若要在您的工作副本中创建新目录，请键入：
svn mkdir PATH
若要在您的项目存储库中创建新目录，请键入：
svn mkdir URL
@ svn resolved 解决冲突后，键入 svn resolved PATH...，通知工作副本该冲突已“解决”。
@ svn revert 撤销更改
使用 Subversion 时，您会发现 svn revert PATH... 等效于 Windows 中的 Ctrl Z。您可以：
* 撤消本地工作副本中的任何本地更改，从而解决冲突状态。
* 撤消工作副本中的条目内容及属性更改。
* 取消任何进度安排操作，如添加文件、删除文件等。

注意，如不提供目标，会导致工作副本中的更改丢失。

@ svn status
@ svn switch 可以使用 svn switch URL [PATH] 更新工作副本，以镜像新的 URL。您还可以将工作副本或部分工作副本移动到新的分支。您可以将此子命令用作分支的快捷方式。
@ svn update [path]
@ 项目的主干通常用作开发主线，而分支通常用作主线的变更。分支是正在进行的开发线。在软件开发生命周期中，如果软件产品的发布版本已到期，经常会用 到分支，使测试者可以使用候选版本，使新的开发可以继续进行，不受测试的约束。分支还用于实验性工作，以及完成代码重写。标记是将一组文件修订版本标记为 整体的方式。虽然分支和标记都是使用 svn copy 子命令创建的，但它们是完全不同的。分支表示多个修订版本而标记只表示单个修订版本。
本站点上您项目的 Subversion 存储库支持对您的源文件进行分支和标记。对于 Subversion 来说，标记和分支属于简单实用的“复制”操作。
@ 若要创建分支或标记项目文件，请键入：
svn copy SRC DST -m "在此处键入您的信息"