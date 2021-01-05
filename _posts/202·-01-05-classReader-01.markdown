---
layout: post
title: class结构阅读器（简介）
category: 技术
date: 2021-01-05
Author: AzureWind
tags: [java,electron]
comments: true
---
NClassReader是一个图形化的class文件分析工具，功能和javap类似，参考了Java Class Viewer,使用前端编写，利用electron封装成应用。
写这个的原因主要是因为要熟悉class文件结构。  
1 遵循java8jvm规范解析，java8以后的暂时没有添加，以后可能会加上。  
2 博客内容只包含如何解析class结构，不包含electron和相关依赖的使用方式。  
3 本文参考了《Java虚拟机规范（Java SE 8版）》《深入理解Java虚拟机（第3版）》

<!-- more -->


```
