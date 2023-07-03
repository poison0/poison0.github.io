---
layout: post
title: java设计模式代码-抽象工厂模式
keywords: 技术,java
date: 2023-07-02
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 抽象工厂模式
> 抽象工厂用于创建一个产品族，这个产品族中的多个对象要一起使用时使用抽象工厂模式，相当于工厂模式扩展了一个维度
  
#### 电器工厂接口和实现类  
```java
public interface ElectricalFactory {
    /**
     * 生产电视机
     * @return
     */
    Television createTelevision();

    /**
     * 生产洗衣机
     * @return
     */
    WashingMachine createWashingMachine();
}
public class GLFactory implements ElectricalFactory{
    @Override
    public Television createTelevision() {
        return new GLTelevision();
    }

    @Override
    public WashingMachine createWashingMachine() {
        return new GLWashingMachine();
    }
}
public class MDFactory implements ElectricalFactory{
    @Override
    public Television createTelevision() {
        return new MDTelevision();
    }

    @Override
    public WashingMachine createWashingMachine() {
        return new MDWashingMachine();
    }
}

```
#### 产品接口和实现类  
```java
public interface Television {
    void getName();
}
public interface WashingMachine {
    void getName();
}
public class GLTelevision implements Television{
    @Override
    public void getName() {
        System.out.println("格力电视机");
    }
}
public class GLWashingMachine implements WashingMachine{
    @Override
    public void getName() {
        System.out.println("格力洗衣机");
    }
}
public class MDTelevision implements Television{
    @Override
    public void getName() {
        System.out.println("美的电视机");
    }
}
public class MDWashingMachine implements WashingMachine{
    @Override
    public void getName() {
        System.out.println("美的洗衣机");
    }
}
```
#### 测试类  
```java
public class App 
{
    public static void main( String[] args )
    {
        //抽象工厂模式
        MDFactory mdFactory = new MDFactory();
        mdFactory.createTelevision().getName();

    }
}
```
#### 输出结果  
```
景田纯净水
```

## 工厂模式  
#### 水的接口和实现类  
```java
public interface Water {
    String getName();
}

public class BaisuishanWater implements Water{
    public String getName() {
        return "百岁山纯净水";
    }
}

public class JingTianWater implements Water {
    public String getName() {
        return "景田纯净水";
    }
}

```
#### 工厂类  
```java
public interface WaterFactory {
    Water createWater();
}
public class BaisuishanWaterFactory implements WaterFactory{
    @Override
    public Water createWater() {
        return new BaisuishanWater();
    }
}
public class JingTianWaterFactory implements WaterFactory{
    @Override
    public Water createWater() {
        return new JingTianWater();
    }
}

```

#### 测试类  
```java
public class App 
{
    public static void main( String[] args )
    {
        //工厂方法模式
        BaisuishanWaterFactory baisuishanWaterFactory = new BaisuishanWaterFactory();
        System.out.println(baisuishanWaterFactory.createWater().getName());

    }
}
```
#### 输出结果  
```
美的电视机
```