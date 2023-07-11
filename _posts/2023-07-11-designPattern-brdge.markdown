---
layout: post
title: java设计模式代码-桥接模式
keywords: 技术,java
date: 2023-07-11
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 桥接模式
#### coffee抽象类
```java
public abstract class Coffee {
    protected ICoffeeAdditives additives;

    public Coffee(ICoffeeAdditives additives) {
        this.additives = additives;
    }
    void addSomething(){
        additives.addSomething();
    }
    public abstract void makeCoffee();
}

```

#### 目标实现类
```java
//大杯
public class LargeCoffee extends Coffee{

    public LargeCoffee(ICoffeeAdditives additives) {
        super(additives);
    }

    @Override
    public void makeCoffee() {
        additives.addSomething();
        System.out.println("大杯");
    }
}
//中杯
public class MediumCoffee extends Coffee {
    public MediumCoffee(ICoffeeAdditives additives) {
        super(additives);
    }

    @Override
    public void makeCoffee() {
        additives.addSomething();
        System.out.println("中杯");
    }
}

``` 
#### 加料接口
```java

public interface ICoffeeAdditives {

    /**
     * 添加
     */
    void addSomething();
}

```
#### 加料接口实现类
```java
public class Milk implements ICoffeeAdditives {
    @Override
    public void addSomething() {
        System.out.println("加奶");
    }
}
public class Sugar implements ICoffeeAdditives{

    @Override
    public void addSomething() {
        System.out.println("加糖");
    }
}
    
```
#### 测试类
```java
public class App {
    public static void main(String[] args) {
        Coffee largeCoffee = new LargeCoffee(new Sugar());
        largeCoffee.makeCoffee();
    }
}

```
#### 输出结果
```
加糖
大杯
```