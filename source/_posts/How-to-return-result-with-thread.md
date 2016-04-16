layout: post
title: How to return result with thread
date: 2016-04-16 11:00:56
tags:
- Java
- Threads
category:
- Java
---

As we know that we can implements Runnable to create a thread. And invoke run() to run it. If we extends class Thread, we need to call method start().

But how can we get a value from a thread? Like the thread info, running result, logs etc.

From 《Thinking of Java》, we can make a class implement Callable<T>, and implement method call(). Method call() returns Object, which means we can get anything we want.

Here is the code: https://github.com/bejondshao/personal/commit/754a5bb837e430fe2cb20cefad68abd33a330026