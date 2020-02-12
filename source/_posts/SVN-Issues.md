layout: draft
title: SVN Issues
author: Bejond Shao
categories:
  - Tools
comments: true
tags: []
date: 2020-02-12 17:08:00
---
> 
14:14	Commit failed with error
			0 files committed, 42 files failed to commit: xxx 移植xxx相关的部分业务
			svn: E200009: Commit failed (details follow):
			svn: E200009: Cannot commit '/Users/abc/xxx/Lastname.java' because it was moved to '/Users/abc/xxx/bizmodel/Lastname.java' which is not part of the commit; both sides of the move must be committed together

The reason is I'm moving the file to another folder while I also editing the file. So It means it can't be 