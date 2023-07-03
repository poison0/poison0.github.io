---
layout: post
title: java设计模式代码-单例模式
keywords: 技术,java
date: 2023-07-03
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 饿汉式
```java
public class LazySingleton {
    private volatile static LazySingleton lazySingleton;
    private LazySingleton(){}
    public static LazySingleton getInstance(){
        if(lazySingleton == null){
            synchronized (LazySingleton.class) {
                if (lazySingleton == null) {
                    lazySingleton = new LazySingleton();
                }
            }
        }
        return lazySingleton;
    }
}

```
## 懒汉式
```java
public class HungrySingleton {
    private final static HungrySingleton HUNGRY_SINGLETON;
    static {
        HUNGRY_SINGLETON = new HungrySingleton();
    }
    private HungrySingleton(){}

    public static HungrySingleton getInstance(){
        return HUNGRY_SINGLETON;
    }
}
```
## 枚举式
```java
public enum EnumSingleton {
    INSTANCE(new Object());
    private Object data;

    EnumSingleton(Object data) {
        this.data = data;
    }

    public static EnumSingleton getInstance(){
        return INSTANCE;
    }
}
```