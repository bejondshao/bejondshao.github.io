layout: post
title: pandas - DataFrame
date: 2016-08-21 21:39:43
tags:
- Python
- pandas
category:
- Python
---
最近在研究股票，想通过股票API获取数据并分析。发现[TuShare](http://tushare.org/index.html)是个很好的工具。TuShare返回的绝大部分的数据格式都是[pandas](http://pandas.pydata.org/pandas-docs/stable/) DataFrame类型。
DataFrame是二维的数据结构，其本质是Series的容器。可以把DataFrame类比成一个二维表格。在这里介绍下DataFrame的概念，记录常用方法。

@ Series：一维数组，与Numpy中的一维array类似。二者与Python基本的数据结构List也很相近，其区别是：List中的元素可以是不同的数据类型，而Array和Series中则只允许存储相同的数据类型，这样可以更有效的使用内存，提高运算效率。

@ DataFrame：二维的表格型数据结构。很多功能与R中的data.frame类似。可以将DataFrame理解为Series的容器。

@ 下标索引选取的是DataFrame的记录，与List相同DataFrame的下标也是从0开始，区间索引的话，为一个左闭右开的区间，即[0：3]选取的为1-3三条记录。

df['a':'b']
@ 数据切片

通过下标选取数据：

df['one']
df.one
以上两个语句是等效的，都是返回df名称为one列的数据，返回的为一个Series。

df[0:3]
df[0]
下标索引选取的是DataFrame的记录，与List相同DataFrame的下标也是从0开始，区间索引的话，为一个左闭右开的区间，即[0：3]选取的为1-3三条记录。与此等价，还可以用起始的索引名称和结束索引名称选取数据：

df['a':'b']
﻿有一点需要注意的是使用起始索引名称和结束索引名称时，也会包含结束索引的数据。以上两种方式返回的都是DataFrame。

使用标签选取数据：

df.loc[行标签,列标签]
df.loc['a':'b']#选取ab两行数据
df.loc[:,'one']#选取one列的数据
df.loc的第一个参数是行标签，第二个参数为列标签（可选参数，默认为所有列标签），两个参数既可以是列表也可以是单个字符，如果两个参数都为列表则返回的是DataFrame，否则，则为Series。

使用位置选取数据：

df.iloc[行位置,列位置]
df.iloc[1,1]#选取第二行，第二列的值，返回的为单个值
df.iloc[0,2],:]#选取第一行及第三行的数据
df.iloc[0:2,:]#选取第一行到第三行（不包含）的数据
df.iloc[:,1]#选取所有记录的第一列的值，返回的为一个Series
df.iloc[1,:]#选取第一行数据，返回的为一个Series
PS：loc为location的缩写，iloc则为integer & location的缩写

更广义的切片方式是使用.ix，它自动根据你给到的索引类型判断是使用位置还是标签进行切片

df.ix[1,1]
df.ix['a':'b']
通过逻辑指针进行数据切片：

df[逻辑条件]
df[df.one >= 2]#单个逻辑条件
df[(df.one >=1 ) & (df.one < 3) ]#多个逻辑条件组合
这种方式获得的数据切片都是DataFrame。




