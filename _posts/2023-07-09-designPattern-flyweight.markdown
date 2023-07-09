---
layout: post
title: java设计模式代码-享元模式
keywords: 技术,java
date: 2023-07-09
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 享元模式
#### 目标接口
```java
public interface Bullet {

    void fire(String weaponName);
}

```

#### 目标实现类
```java
public class ArmorPiercingShells implements Bullet{

    private String name = "穿甲弹";
    public String getName() {
        return name;
    }
    @Override
    public void fire(String weaponName) {
        System.out.println(weaponName + "使用穿甲弹");
    }
}
public class Cannonball implements Bullet{

    private String name = "炮弹";

    public String getName() {
        return name;
    }

    @Override
    public void fire(String weaponName) {
        System.out.println(weaponName + "使用炮弹");
    }
}

``` 
#### 工厂类
```java

public class BulletFactory {
    public static final Map<String, Bullet> BULLET_MAP = Map.of(
            "穿甲弹", new ArmorPiercingShells(),
            "炮弹", new Cannonball()
    );
    private BulletFactory() {
    }

    public static BulletFactory getInstance() {
        return new BulletFactory();
    }

    public Bullet getBullet(String bulletName) {
        return BULLET_MAP.get(bulletName);
    }

}

```

#### 测试类
```java
public class App {
    public static void main(String[] args) {
        BulletFactory bulletFactory = BulletFactory.getInstance();
        bulletFactory.getBullet("穿甲弹").fire("AK47");
    }
}

```
#### 输出结果
```
AK47使用穿甲弹
```