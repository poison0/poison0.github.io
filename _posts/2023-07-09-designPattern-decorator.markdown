---
layout: post
title: java设计模式代码-装饰器模式
keywords: 技术,java
date: 2023-07-09
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 装饰器模式
#### 被装饰着抽象类
```java
public interface Computer {
    /**
     * 描述
     */
    String description();

    /**
     * 价格
     */
    double price();

}

```

#### 被装饰者实现类
```java
public class AppleComputer implements Computer{
    @Override
    public String description() {
        return "苹果电脑";
    }

    @Override
    public double price() {
        return 10000;
    }
}

``` 
#### 装饰器抽象类
```java

public abstract class AbstractComputer implements Computer{

    private Computer computer;

    public AbstractComputer(Computer computer) {
        this.computer = computer;
    }

    @Override
    public String description() {
        return this.computer.description();
    }

    @Override
    public double price() {
        return this.computer.price();
    }
}

```

#### 装饰器实现类
```java
public class KeyboardComputer extends AbstractComputer{

    public KeyboardComputer(Computer computer) {
        super(computer);
    }

    @Override
    public double price() {
        return super.price() + 20;
    }

    @Override
    public String description() {
        return super.description() + " + 键盘";
    }
}
public class MouseComputer extends AbstractComputer{

    public MouseComputer(Computer computer) {
        super(computer);
    }

    @Override
    public double price() {
        return super.price() + 10;
    }

    @Override
    public String description() {
        return super.description() + " + 鼠标";
    }
}

```
#### 测试类
```java
public class App {
    public static void main(String[] args) {
        Computer computer = new AppleComputer();
        System.out.println(computer.description() + " " + computer.price());
        computer = new KeyboardComputer(computer);
        System.out.println(computer.description() + " " + computer.price());
        computer = new MouseComputer(computer);
        System.out.println(computer.description() + " " + computer.price());
    }
}
```
#### 输出结果
```text
苹果电脑 10000.0
苹果电脑 + 键盘 10020.0
苹果电脑 + 键盘 + 鼠标 10030.0
```