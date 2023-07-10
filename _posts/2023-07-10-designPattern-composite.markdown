---
layout: post
title: java设计模式代码-组合模式
keywords: 技术,java
date: 2023-07-10
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 组合模式
#### 目标接口
```java
public abstract class Node {
    public abstract void add(Node node);
    public abstract void remove(Node node);
    public abstract void display();
    public abstract List<Node> getChind();
}

```

#### 目标实现类
```java
public class Leaf extends Node{
    private String name;

    public Leaf(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void add(Node node) {

    }

    @Override
    public void remove(Node node) {

    }

    @Override
    public void display() {
        System.out.printf("leaf name is %s\n", name);
    }

    @Override
    public List<Node> getChind() {
        return null;
    }
}
public class Composite extends Node{

    private String name;
    private final List<Node> children = new ArrayList<>();

    public Composite(String name) {
        this.name = name;
    }

    @Override
    public void add(Node node) {
        children.add(node);
    }

    @Override
    public void remove(Node node) {
        children.remove(node);
    }

    @Override
    public void display() {
        System.out.printf("composite name is %s\n", name);
        for (Node node : children) {
            node.display();
        }
    }

    @Override
    public List<Node> getChind() {
        return children;
    }
}


```

#### 测试类
```java
public class App {
    public static void main(String[] args) {
        Node c0 = new Composite("c0");
        Node c1 = new Composite("c1");
        Node leaf1 = new Leaf("1");
        Node leaf2 = new Leaf("2");
        Node leaf3 = new Leaf("3");
        c0.add(leaf1);
        c0.add(c1);
        c1.add(leaf2);
        c1.add(leaf3);
        c0.display();
    }
}

```
#### 输出结果
```
composite name is c0
leaf name is 1
composite name is c1
leaf name is 2
leaf name is 3
```