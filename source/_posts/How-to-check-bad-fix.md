title: How to check bad fix
date: 2015-07-16 15:23:23
tags:
- Backport
- Liferay
category:
- Liferay
---
最近做了一个backport的票，backport就是别人以前修复了这个bug，我只需要将fix移植到客户的系统中。这是一个没有技术含量的票，遇到简单的票，三下五除二就解决了。但是遇到目标版本与master差别大时，也许backport后不好用。结果这次就遇到了，并且引起UI问题。

我先是查看所有commit的修复，发现没有关于UI的修复。找了好久也没有找出原因。后来我想到部分backport，只backport主要修复，但是这也需要对修复很了解才能做到合理取舍，只得放弃。

于是我采用最笨的方法，以文件为单位，单个测试。因为是UI问题，单个功能JSP文件错误就可能导致问题，我便找到可能的文件，将其还原到被修改前的状态，全部复制到backport后的版本上。这就相当于backport，但是唯独不backport这个文件。发现UI问题不见了！那就确定是这个文件引起。然后再找出此次修复的所有关于这个文件的commit,找到diff，每次应用一个commit，当应用到某个commit时，问题出现了，说明此次commit的fix导致了这个问题，然后仔细分析此次修改，最后找到问题。

========================================================================

Recently I did a ticket about backport. Backport is just copy others fix to fix customer's issue. This kind of tickets has little tech. If it's simple, nothing progress. But if it's not simple, that's disgusting. But this time, it's the latter. And it's an UI issue.

At first, I tried to look through the commits and found there's no fix about UI. And then I tried to part backport it. But this needs me know the fix well, so that I can part backport it. So I give this solution up.

At last, I use a stupid way, but it does make sence. I focus on each file. Since this is an UI issue, it relates js, css, jsp files. I found the JSP files which might be the reason. I took out one jsp, and find the original status that didn't apply any fix. And  I copied all the content to the backported file, which means I backported all the fix, but only left this JSP as "unfixed". The result is, the UI issue was gone! That was clear enough. Then I applied the commits of the file one by one, until I can reproduce the issue. Then I located the commit that made bad fix. Then I took care of this fix and found out the cause.