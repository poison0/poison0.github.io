---
layout: post
title: java设计模式代码-工厂模式
keywords: 技术,java
date: 2023-07-02
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 简单工厂模式
> 水的接口和实现类
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
> 工厂类
```java
public class WaterFactory {
    public static Water getWater(WaterType type) {
        if (WaterType.JT.equals(type)) {
            return new JingTianWater();
        } else if (WaterType.BSS.equals(type)) {
            return new BaisuishanWater();
        } else {
            return null;
        }
    }
}
```
> 水的类型
```java
public enum WaterType {
    JT("景田纯净水"),
    BSS("百岁山纯净水");
    private final String name;
    WaterType(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
}
```
> 测试类
```java
public class App 
{
    public static void main( String[] args )
    {
        //简单工厂模式
        WaterFactory factory = new WaterFactory();
        System.out.println(factory.getWater(WaterType.JT).getName());

    }
}
```
#### 输出结果
```java
景田纯净水
```

## 工厂模式
> 水的接口和实现类
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
> 工厂类
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

> 测试类
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
```java
百岁山纯净水
```