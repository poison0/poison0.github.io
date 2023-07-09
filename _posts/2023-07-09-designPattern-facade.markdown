---
layout: post
title: java设计模式代码-外观模式
keywords: 技术,java
date: 2023-07-09
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 外观模式
#### 内部逻辑类
```java
public class PointsPaymentService {
    public boolean pay(Gift gift) {
        System.out.println("支付" + gift.getName() + "积分成功");
        return true;
    }
}
public class ShippingService {
    public String shipGift(Gift gift) {
        // 物流系统的对接逻辑
        System.out.println(gift.getName() + "进入物流系统");
        String shippingOrderNo = "666";
        return shippingOrderNo;
    }
}

```

#### 礼物类
```java

public class Gift {
    private String name;

    private Integer points;

    public void setName(String name) {
        this.name = name;
    }

    public Integer getPoints() {
        return points;
    }

    public void setPoints(Integer points) {
        this.points = points;
    }

    public Gift(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

``` 
#### 外观类
```java

public class ExchangeGift {
    private PointsPaymentService pointsPaymentService;
    private ShippingService shippingService;

    public void setPointsPaymentService(PointsPaymentService pointsPaymentService) {
        this.pointsPaymentService = pointsPaymentService;
    }

    public void setShippingService(ShippingService shippingService) {
        this.shippingService = shippingService;
    }

    public void exchange(Gift gift) {
        if(pointsPaymentService.pay(gift)) {
            String shippingOrderNo = shippingService.shipGift(gift);
            System.out.println("物流系统下单成功，订单号是：" + shippingOrderNo);
        }
    }
}

```

#### 测试类
```java
public class App {
    public static void main(String[] args) {
        //外观模式
        ExchangeGift exchangeGift = new ExchangeGift();
        exchangeGift.setShippingService(new ShippingService());
        exchangeGift.setPointsPaymentService(new PointsPaymentService());
        Gift gift = new Gift("手机");
        exchangeGift.exchange(gift);
    }
}

```
#### 输出结果
```
支付手机积分成功
手机进入物流系统
物流系统下单成功，订单号是：666
```