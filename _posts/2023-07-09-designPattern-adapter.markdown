---
layout: post
title: java设计模式代码-适配器模式
keywords: 技术,java
date: 2023-07-09
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 类适配器模式
#### 目标类
```java
public class Demo {
    public void demo(){
        System.out.println("目标方法");
    }
}

```

#### 目标接口
```java

public interface Target {
    /**
     * 目标方法
     */
    void targetMethod();
}


``` 
#### 适配器类
```java

public class Adapter extends Demo implements Target{
    @Override
    public void targetMethod() {
        System.out.println("适配器方法");
        demo();
    }
}

```

#### 测试类
```java
public class App {
    public static void main(String[] args) {
        //外观模式
        Adapter adapter = new Adapter();
        adapter.targetMethod();
    }
}

```
#### 输出结果
```
适配器方法
目标方法
```
## 对象适配器模式
#### 目标类
```java
public class Demo {
    public void demo(){
        System.out.println("目标方法");
    }
}

```

#### 目标接口
```java

public interface Target {
    /**
     * 目标方法
     */
    void targetMethod();
}


``` 
#### 适配器类
```java

public class Adapter implements Target{
    private Demo demo = new Demo();

    @Override
    public void targetMethod() {
        System.out.println("适配器方法");
        demo.demo();
    }
}

```

#### 测试类
```java
public class App {
    public static void main(String[] args) {
        //外观模式
        Adapter adapter = new Adapter();
        adapter.targetMethod();
    }
}

```
#### 输出结果
```
适配器方法
目标方法
```