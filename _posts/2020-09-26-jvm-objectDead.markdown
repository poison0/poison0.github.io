---
layout: post
title: jvm如何判断对象已死
keywords: 技术,java
date: 2020-09-26
Author: AzureWind
tags: [java]
comments: true
---
GC是jvm中最重要的组成部分之一，程序运行时，内存资源总是稀缺的，如何更好的管理这些资源，jvm垃圾收集器
给出了一份答案。  

***垃圾回收只需要完成三件事情：***
- 哪些内存需要回收？
- 什么时候回收？
- 怎么回收？   

本文只讨论第一个问题......   
<!-- more -->    

## 引用计数算法
顾名思义，引用计数是给对象的引用进行计数的。在对象中添加一个引用计数器，每当此对象被引用，计数器就加一，
引用失效，计数器就减一，如果计数器为0，说明对象已经没有用了，可以被清除来。乍一看方法高效优雅，在大多数
时候它是一个不错的算法，但是至少主流的虚拟机不会使用这种算法来管理内存。  
这个算法有一个致命的缺陷，就是无法判定***循环引用***的对象，它们实际上已经没有用了，但是却还一直存在，如果想要
算法持续生效，就需要配合大量额外的处理才能保证正确工作。

## 可达性分析算法
当前主流的商用程序语言的内存管理系统都是通过可达性分析算法来判断对象是否是存活的。可达性分析的基本思路是通过一系列的“GC Roots”的根对象
作为起始节点集，通过引用关系向下搜索，走过的路径称为“引用链”，如果某个对象不在“引用链”上，那么这个对象就会被认为不可达，可以被回收掉了。  

![引用链](https://github.com/poison0/poison0.github.io/blob/master/images/gcRoots.png?raw=true)

在java中，固定可作为GC Roots的对象包含以下几种：   
1.虚拟机栈引用的对象。  
2.方法区中静态属性引用的对象。  
3.方法区中常量引用的对象。  
4.本地方法栈中Native方法引用的对象。  
5.JVM内部引用，如基本数据类型对应的Class对象，一些常驻的异常对象，还有系统类加载器。  
6.所以被同步锁（synchronized关键字）持有的对象。  
7.反映Java虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地代码缓存等。  
## 回收方法区

## 思考

