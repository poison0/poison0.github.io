---
layout: post
title: Java Stream api介绍和原理讨论
category: 技术
date: 2021-07-31
Author: AzureWind
tags: [java,stream]
comments: true
---
## Java Stream(流) api介绍
流是Java API的新成员，它允许你以声明性方式处理数据集合（通过查询语句来表达，而不
是临时编写一个实现）。就现在来说，你可以把它们看成遍历数据集的高级迭代器。

### 1.筛选、切片  filter、distinct、limit、skip

```java
//filter 筛选
List<Integer> collect = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9)
    .filter(x -> x > 6)
    .collect(Collectors.toList());

//distinct 去重
List<Integer> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9)
    .distinct()
    .collect(Collectors.toList());

//limit 截取
List<Integer> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9)
    .limit(4)
    .collect(Collectors.toList());

//skip 跳过
List<Integer> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9)
    .skip(4)
    .collect(Collectors.toList());

//可以使用 limit 和 skip对集合进行分页
int pageNo = 2;
int pageSize = 5;
List<Integer> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9)
    .skip((pageNo - 1) * pageSize)
    .limit(pageSize).collect(Collectors.toList());

//peek 
List<Integer> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9)
    .peek(x-> System.out.println(x))
    .collect(Collectors.toList());

```

### 2.映射 map、flatmap

> map可以把一个对象映射成另一个不同的对象

```java
//map 把 Integer 映射成了 String
List<String> collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).map(x -> String.valueOf(x)).collect(Collectors.toList());
```

>  flatmap 扁平流 可以把不同层次的流合成一个流

```java
//建立一个对象
class Trade{
    private String tid;
    //包含另一个对象的列表
    private List<Order> orders;

    public List<Order> getOrders() {
        return orders;
    }

    public void setOrders(List<Order> orders) {
        this.orders = orders;
    }

    public String getTid() {
        return tid;
    }

    public void setTid(String tid) {
        this.tid = tid;
    }
}
class Order{
    private String oid;

    public Order(String oid) {
        this.oid = oid;
    }

    public String getOid() {
        return oid;
    }

    public void setOid(String oid) {
        this.oid = oid;
    }

    @Override
    public String toString() {
        return "Order{" +
                "oid='" + oid + '\'' +
                '}';
    }
}

public static void main(String[] args) {
        Trade trade = new Trade();
        ArrayList<Order> orders = new ArrayList<>();
        orders.add(new Order("order1"));
        orders.add(new Order("order2"));
        trade.setTid("trade");
        trade.setOrders(orders);

        Trade trade2 = new Trade();
        ArrayList<Order> orders2 = new ArrayList<>();
        orders2.add(new Order("order3"));
        orders2.add(new Order("order4"));
        trade2.setTid("trade2");
        trade2.setOrders(orders2);
    
    	ArrayList<Trade> trades = new ArrayList<>();
        trades.add(trade);
        trades.add(trade2);
    
    	//flatMap入参传入多个流组合成一个
        List<Order> collect = trades.stream().flatMap(x -> x.getOrders().stream()).collect(Collectors.toList());
        System.out.println(collect);
}

//输出 order 对象组成的集合
>> [Order{oid='order1'}, Order{oid='order2'}, Order{oid='order3'}, Order{oid='order4'}]
    
//还可以用扁平流拆分单词成单独的字母
Stream.of("add", "one").flatMap(x -> Arrays.stream(x.split(""))).collect(Collectors.toList());

```

### 3.查找、匹配（终端） allMatch、anyMatch、noneMatch、findFirst、findAny

```java
//anyMatch 流中是否有一个元素能匹配给定的谓词
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).anyMatch(x -> x == 4)
    
//allMatch 能不能匹配所有得元素
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).allMatch(x -> x < 1000);

//noneMatch 确保流中没有任何元素与给定的谓词匹配
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).noneMatch(x -> x < 1000);

//findAny findAny方法将返回当前流中的任意元素 在利用短路找到结果时立即结束
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).filter(x -> x < 1000).findAny();

//findFirst 方法将返回当前流中的第一个元素
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).filter(x -> x < 1000).findFirst();
```

> 以上操作有些回返回Optional对象
>
> Optional里面几种可以迫使你显式地检查值是否存在或处理值不存在，使用Optional可以避免和null检查相关的bug。
>
> - isPresent()将在Optional包含值的时候返回true, 否则返回false。 
>
> - ifPresent(Consumer block)会在值存在的时候执行给定的代码块。
>
> - T get()会在值存在时返回值，否则抛出一个NoSuchElement异常。
>
> - T orElse(T other)会在值存在时返回值，否则返回一个默认值。



### 4.归约（终端）reduce

> 使用reduce操作来表达更复杂的查询，此类查询需要将流中所有元素反复结合起来，得到一个值，比如一个Integer。

```java
//归约求和 
//没有初始值的情况返回一个 Optional
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).reduce((x, y) -> x + y);

//有初始值就直接返回一个数值 
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).reduce(0,(x, y) -> x + y);

//找最大或者最小值
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).reduce(0,(x, y) -> x < y ? x : y)  
    
//或者 
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).reduce(Integer::min);
```

> 广义的归约

```java
//还没看懂，跟并发有关
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).parallel().reduce(5, (x, y) -> x+y, (x, y) -> x);
```

### 5.数值流

> 如果 Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).reduce(Integer::sum); 语句中的数据类型是Integer，那么计算总和的时候里面暗含着装箱成本，每个Integer都必须拆箱成一个原始类型， 再进行求和。为了解决这个问题，java8引入了三个**原始类型特化流**来解决这个问题，分别是**IntStream**，**DoubleStream**，**LongStream**

```java
//该方法将会返回一个IntStream的原始类型的流，在操作计算就不会有装箱成本了，DubleStream，LongStream也是类似
IntStream intStream = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).mapToInt(x -> x);

//原始类型流特化计算
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).mapToInt(x->x).sum();
```

### 6.构建流

> 除了用集合生成流，还有如下其他方式生成

#### 6.1 由值创建流

> 你可以使用静态方法Stream.of，通过显式值创建一个流。它可以接受任意数量的参数。例如，以下代码直接使用Stream.of创建了一个字符串流

```java
//把字符串转大写，并打印出来
Stream<String> stream = Stream.of("Java 8 ", "Lambdas ", "In ", "Action"); 
stream.map(String::toUpperCase).forEach(System.out::println); 

// 可以使用empty得到一个空流
Stream<String> emptyStream = Stream.empty();
```

#### 6.2 数组创建流

> 你可以使用静态方法Arrays.stream从数组创建一个流。它接受一个数组作为参数。例如， 你可以将一个原始类型int的数组转换成一个IntStream

```java
int[] numbers = {2, 3, 5, 7, 11, 13}; 
int sum = Arrays.stream(numbers).sum(); 
```

#### 6.1 文件流

```java
//java8 io支持从文件读取数据生成流，下面可以查询读取文件里有多少个不同的单词
long uniqueWords = 0;
try(Stream<String> lines = Files.lines(Paths.get("d://test.txt"), Charset.defaultCharset())){
    uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" ")))
        .distinct()
        .count();
} catch(IOException e){

}
```

#### 6.1 无限流

> 可以使用函数创建无限流，Stream API提供了两个静态方法来从函数生成流：Stream.iterate和Stream.generate。

```java
//生成一个无穷的偶数列表，iterate是可以根据上一个参数不断进行迭代
Stream.iterate(0, n -> n + 2).limit(10).forEach(System.out::println);

//生成一个无穷的随机数列表，与iterate不一样的是，函数是不需要入参的，每一个都是有方法生成
Stream.generate(Math::random).limit(5).forEach(System.out::println);
```

### 7.收集器和高级归约

> collect 不仅仅是用来把Stream中所有的元素结合成一个List，还是一个归约操作，就像reduce一样可以接 受各种做法作为参数，将流中的元素累积成一个汇总结果。
>
> collect 是归约操作
> collector 收集器接口
> collectors 是java内置的已经实现的collector接口的工具类方法集合，提供了很多静态工厂方法

#### 6.1 汇总

```java
//返回数据元素的个数 
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.counting()); >> 9
    
//返回最小值，需要传入一个比较器，返回的是一个Option，防止stream为空时抛出异常
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.minBy(Comparable::compareTo)); >> 1
    
//返回最大值，其他同上
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.MaxBy(Comparable::compareTo)); >> 9
    
//求和，同类型的 还有 summigLong summingDouble
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.summingInt(x -> x));

//平均数，不存在值时返回0 同样还有 averagingLong averagingDouble
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.averagingInt(x -> x));

//还有一个更强大的收集器，Collectors.summarizingInt 返回一个IntSummaryStatistics对象，打印出来如下,返回当前数据的总个数，和，最小值，最大值和平均数
//同样的还有 summarizingDouble summarizingDouble
//>> IntSummaryStatistics{count=9, sum=46, min=1, average=5.111111, max=9}
IntSummaryStatistics collect = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).collect(Collectors.summarizingInt(x -> x));
```

> 连接字符串

```java
//可以把字符串连接起来
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).map(String::valueOf).collect(Collectors.joining()); >> 123556789
//一个参数可以指定连接符
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).map(String::valueOf).collect(Collectors.joining("-")); >> 1-2-3-5-5-6-7-8-9
//三个参数可以指定连接符和前缀后缀
Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9).map(String::valueOf).collect(Collectors.joining("-","@","&")); >> @1-2-3-5-5-6-7-8-9&
```

### 8.分组

> 可以用Collectors.groupingBy工厂方法返回的收集器对数据进行分组，groupingBy也可以和其他收集器合起来实现一些非常复杂的效果

```java
//使用trade作为例子
Trade trade = new Trade();
trade.setTid("trade");

Trade trade2 = new Trade();
trade2.setTid("trade2");

Trade trade3 = new Trade();
trade3.setTid("trade");

ArrayList<Trade> trades = new ArrayList<>();
trades.add(trade);
trades.add(trade2);
trades.add(trade3);

// groupingBy 会把集合按tid进行归约 groupingBy可以生成一个map
Map<String, List<Trade>> collect1 = trades.stream().collect(Collectors.groupingBy(Trade::getTid));

//可以传入第二个分组函数进行二级分组，理论上还可以一直嵌套下去进行多级分组
Map<String, Map<String, List<Trade>>> collect = trades.stream().collect(Collectors.groupingBy(Trade::getTid, Collectors.groupingBy(Trade::getTid)));

//还可以联合其他收集器一起使用
trades.stream().collect(Collectors.groupingBy(Trade::getTid, Collectors.counting()));

//还可以把groupingBy和mapping收集器结合起来，映射成一个list，甚至一个set
Map<String, List<Integer>> trade1 = trades.stream().collect(Collectors.groupingBy(Trade::getTid, Collectors.mapping(x -> 		{
    if (x.getTid().equals("trade")) {
        return 1;
    }
    return 0;
}, Collectors.toList())));
```

### ９.自定义收集器



### 10.并行流

### 11.Java Stream流的基石 monad 、构造一个monad函数 `探讨`










