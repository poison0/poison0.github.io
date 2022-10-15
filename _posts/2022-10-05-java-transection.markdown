---
layout: post
title: spring @Transaction 传播特性 
keywords: 技术,java
date: 2022-10-05
Author: AzureWind
tags: [java,spring]
comments: true
---
## 7种传播特性

1.REQUIRED 如果外层有事务，就使用外层事务，如果没有就新建一个  
2.SUPPORTS 如果外层有事务，就使用外层事务，如果没有就不使用事务  
3.MANDATORY 如果外层有事务，就使用外层事务，如果外层没有事务，就抛出异常
4.REQUIRES_NEW 如果外层有事务，就将外层事务挂起，在新建一个，如果外层没有，就新建一个事务;  
5.NOT_SUPPORTED 如果外层有事务，就将外层事务挂起，如果外层没有事务，就以非事务方式执行
6.NEVER 如果外层有事务，就抛出异常，如果外层没有事务，就以非事务方式执行
7.NESTED 如果外层有事务，就加一个保存点，如果外层没有事务，就新建一个



## 每种传播特性测试

> 以下是针对嵌套事务针对内外方法选择不同的传播特性的测试结果（无catch的情况）

### 1.外层 propagation 是 SUPPORTS 或 REQUIRES_NEW 或 NESTED

|  外层   | 内层  | 结果 |
|  ----  | ----  | ----  |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | REQUIRED | 如果程序正常执行，则使用一个事务，如果内外层有异常，就都会回滚 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | SUPPORTS | 如果程序正常执行，则使用一个事务，如果内外层有异常，就都会回滚 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | MANDATORY | 如果程序正常执行，则使用一个事务，如果内外层有异常，就都会回滚 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | REQUIRES_NEW | 如果程序正常执行，内外层会各建立一个事务，如果内外层有异常，只会回滚各自事务 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | NOT_SUPPORTED | 如果程序正常执行，外层有事务，外层有异常会回滚外层，内层有异常，内层不会回滚 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | NEVER | 抛出异常 |
| REQUIRED 或 REQUIRES_NEW 或 NESTED | NESTED | 如果程序正常执行，则使用一个事务，单内层会见一个保存点，内层有异常，会回滚到保存点，外层会直接回滚，外层异常，就都会回滚 |

### 2.外层 propagation 是 SUPPORTS 或 NOT_SUPPORTED 或 NEVER

|  外层   | 内层  | 结果 |
|  ----  | ----  | ----  |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | REQUIRED | 如果程序正常执行，只有内层有事务，外层有异常，不会回滚，内层有异常，会回滚内层 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | SUPPORTS | 内外层都不会有事务 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | MANDATORY | 抛出异常 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | REQUIRES_NEW | 如果程序正常执行，只有内层有事务，外层有异常，不会回滚，内层有异常，会回滚内层 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | NOT_SUPPORTED | 内外层都不会有事务 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | NEVER | 内外层都不会有事务 |
| SUPPORTS 或 NOT_SUPPORTED 或 NEVER | NESTED | 如果程序正常执行，只有内层有事务，外层有异常，不会回滚，内层有异常，会回滚内层 |

### 3.外层 propagation 是 MANDATORY

|  外层   | 内层  | 结果 |
|  ----  | ----  | ----  |
| MANDATORY  | REQUIRED | 抛出异常 |
| MANDATORY  | SUPPORTS | 抛出异常 |
| MANDATORY  | MANDATORY | 抛出异常 |
| MANDATORY  | REQUIRES_NEW | 抛出异常 |
| MANDATORY  | NOT_SUPPORTED | 抛出异常 |
| MANDATORY  | NEVER | 抛出异常 |
| MANDATORY  | NESTED | 抛出异常 |


