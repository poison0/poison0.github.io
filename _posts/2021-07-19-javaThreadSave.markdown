---
layout: post
title: 同步容器和并发容器
category: 技术
date: 2021-07-19
Author: AzureWind
tags: [java,多线程]
comments: true
---
### 线程安全的同步容器
    创建和jdk自带的同步容器
#### ArrayList -> Vector,Stack
Vector 一般情况下是线程安全的，但是在某些情况下是线程不安全的，如下代码会抛出异常，因为顺序问题
会导致get方法抛出异常
```java
        Vector<Integer> vector = new Vector<>();
        for(int i =0;i<10;i++){
            vector.add(i);
        }
        Thread thread1 = new Thread(()->{
            for (int i = 0; i < vector.size(); i++) {
                vector.remove(i);
            }
        });
        Thread thread2 = new Thread(()->{
            for(int i =0;i<vector.size();i++){
                vector.get(i);
            }
        });
        thread1.start();
        thread2.start();
```

#### HashMap -> HashTable(key、value不能位null)
HashTable 是线程安全的 方法中都加了synchronized修饰符

#### Collections.synchronizedXXX(List、Set、Map)
该方法是把一个普通的List Set Map 生成一个线程安全的相应对象 SynchronizedXXX
一些常用操作如put会被添加 synchronized 修饰
```java
    public V put(K key, V value) {
       synchronized (mutex) {return m.put(key, value);}
    }
```
 ### 并发容器
#### ArrayList -> CopyOnWriteArrayList
#### HashSet、TreeSet -> CopyOnWriteArraySet ConcurrentSkipListSet
#### HashMap、TreeMap -> ConcurrentHashMap ConcurrentSkipListMap
