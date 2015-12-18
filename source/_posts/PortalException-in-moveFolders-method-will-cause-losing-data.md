layout: post
title: PortalException in moveFolders method will cause losing data
date: 2015-12-18 19:31:53
categories: 
- Liferay
tags:
- liferay
- document & library
---
Sample Issue

Customer has a customer portlet to move folders from one site to another site. They use Liferfay's DLAppServiceUtil.moveFolder API but encounter an issue.

They have a folder which contains two sub folders. The first sub-folder contain normal files and the second sub-folder contain a file without extension and title contains a special character. After perform the move action, move is failed and first sub-folder and its files are deleted.

The steps and sample portlets can be found in LPS-60884

 

------------------------------------------------------------------------

At first we discuss about the issue, it probably connect with transaction. After looking into the code

DLAppServiceImpl.java#L3405-L3436

In moveFolders(), there's a for loop for file, folder or shortcut. For folder, there's another recursion (递归). They are ok, but when the file or shortcut throws exception, DLAppServiceImpl.java#L3145-L3149

The copyFileEntry() will throw PortalException to its parent method, moveFolders(). While moveFolders() catches the exception, it will run "toRepository.deleteFolder(newFolder.getFolderId());", which will delete the folder and file that already copied. But what's worse, moveFolders() will throw exception to its parent method, moveFolders(), it will lead loop deletion, until the whole folder is deleted. This is why causing losing data.

So I add bejondshao:LPS-60884, to check folder before delete it.

Then I find that there are two same files in different repositories, the files are the same location as bad file. Because it calls copyFileEntry() then delete whole folder. This is not good. Because if the folder contains large or thouands of files, copyFileEntry() will take too much disk space. So I use moveFileEntry() instead. By the way, moveFileEntry() + deleteFolder() doing the same logic with copyFileEntry() + deleteFolder().

====================================

Since the issue can't be reproduced in trunk and clean bundle, so we use cqu-folder-move-portlet-6.2.10.1 (working).war to reproduce it. Besides, we use cqu-folder-move-portlet-6.2.10.1 (fix).war to test the fix.

 