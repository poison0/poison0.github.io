---
layout: post
title: java设计模式代码-原型模式
keywords: 技术,java
date: 2023-07-04
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 原型模式
#### 用户类  
```java
public class User implements Cloneable{
    private String name;

    private Date birthday;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    @Override
    public User clone() {
        try {
            User clone = (User) super.clone();
            if (this.birthday != null) {
                // 深拷贝
                clone.birthday = (Date) this.birthday.clone();
            }
            return clone;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}

```
