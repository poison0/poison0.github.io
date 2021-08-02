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

#### 7.1 汇总

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

#### 7.2分组

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

### 8.自定义收集器

> 只需实现 Collectors 接口中的方法就可以自己实现一个收集器

```java
public class CollectorExa<T> implements Collector<T, List<T>, List<T>> {

    /**
     * 在调用时它会创建一个空的累加器实例，供数据收集过程使用。
     */
    @Override
    public Supplier<List<T>> supplier() {
        return ()->new ArrayList<>();
    }

    /**
     *  将元素添加到结果容器
     *  当遍历到流中第n个元素时，这个函数执行
     * 时会有两个参数：保存归约结果的累加器（已收集了流中的前 n1 个项目），还有第n个元素本身。
     * 该函数将返回void，因为累加器是原位更新，即函数的执行改变了它的内部状态以体现遍历的
     * 元素的效果。
     */
    @Override
    public BiConsumer<List<T>, T> accumulator() {
        return (list,item)->list.add(item);
    }

    /**
     * combiner方法会返回一个供归约操作使用的函数，它定义了对
     * 流的各个子部分进行并行处理时，各个子部分归约所得的累加器要如何合并。对于toList而言，
     * 这个方法的实现非常简单，只要把从流的第二个部分收集到的项目列表加到遍历第一部分时得到
     * 的列表后面就行了：
     */
    @Override
    public BinaryOperator<List<T>> combiner() {
        return  (list1, list2) -> {
                    list1.addAll(list2);
                    return list1; };
    }

    /**
     * 在遍历完流后，finisher方法必须返回在累积过程的最后要调用的一个函数，以便将累加
     * 器对象转换为整个集合操作的最终结果。
     */
    @Override
    public Function<List<T>, List<T>> finisher() {
        return Function.identity();
    }
    /**
     * 最后一个方法——characteristics会返回一个不可变的Characteristics集合，它定义
     * 了收集器的行为——尤其是关于流是否可以并行归约，以及可以使用哪些优化的提示。
     *
     * Characteristics是一个包含三个项目的枚举。
     *
     * UNORDERED——归约结果不受流中项目的遍历和累积顺序的影响。
     * CONCURRENT——accumulator函数可以从多个线程同时调用，且该收集器可以并行归
     * 约流。如果收集器没有标为UNORDERED，那它仅在用于无序数据源时才可以并行归约。
     * IDENTITY_FINISH——这表明完成器方法返回的函数是一个恒等函数，可以跳过。这种
     * 情况下，累加器对象将会直接用作归约过程的最终结果。这也意味着，将累加器A不加检
     * 查地转换为结果R是安全的。
     */
    @Override
    public Set<Characteristics> characteristics() {
        return Collections.unmodifiableSet(EnumSet.of(Collector.Characteristics.IDENTITY_FINISH));
    }
}
// 尝试使用
Stream.of(5,4,5,6,7).collect(new CollectorExa<>());

```



### 9.并行流

> 将流转为并行流只需要中间调用一个 .parallel() 方法即可，调用之后流内会设置一个标志，同样的调用 sequential()可以把它编程顺序流，但是下面的操作需要注意

```java
//此方法并不会先并行，在顺序执行，在并行，由于流是惰性计算的，所以是否并行只会看最后一次调用，所以这个流是整体是并行的
//使用的线程池大小为运行机器上的内核数，使用并行流不一定会比顺序流更加高效，有可能会更慢，因为并行本身会消耗资源
Stream.parallel() 
 .filter(...) 
 .sequential() 
 .map(...) 
 .parallel() 
 .reduce(); 
```



### 10.Java Stream流的基石 monad 、构造一个monad函数 

> monad范畴学上的解释是 一个子函子上的幺半群 
>
> 我理解的是把类型封装起来，对原始类型的操作转变成对封装类型的操作  a+b ==> f(a)+f(b)，推荐[图解 Monad - 阮一峰的网络日志 (ruanyifeng.com)](https://www.ruanyifeng.com/blog/2015/07/monad.html)

```java
//一个简单的monad函数
public interface MonadExample<T> {

    static <T> MonadExampleImp<T> of(T value){
        return new MonadExampleImp<>(value);
    }

    <R> MonadExample<R> map(Function<T, R> function);

    Optional<T> Has(T value);

    class MonadExampleImp<T> implements MonadExample<T>{
        private T value;

        public MonadExampleImp(T value) {
            this.value = value;
        }

        @Override
        public <R> MonadExample<R> map(Function<T, R> function) {
            return new MonadExampleImp<>(function.apply(value));
        }

        @Override
        public Optional<T> Has(T value) {
            if(value.equals(this.value)){
                return Optional.of(this.value);
            }
            return Optional.empty();
        }

        public static void main(String[] args) {
            Optional<String> has = MonadExample.of(4)
                    .map(x -> String.valueOf(4))
                    .map(x -> String.valueOf(4))
                    .map(x -> String.valueOf(4))
                    .Has("3");
            System.out.println(has.orElse("-1"));
        }
    }
}
```

#### 10.1 封装try catch

> github有人写了一个对try catch进行monad封装的类库 [jasongoodwin/better-java-monads (github.com)](https://github.com/jasongoodwin/better-java-monads)
>
> 利用这个类库可以把try catch延后处理，让整个流的运算显得更加合理

```xml
<!--maven引入依赖-->
<dependency>
    <groupId>com.jason-goodwin</groupId>
    <artifactId>better-monads</artifactId>
    <version>0.4.0</version>
</dependency>
```

```java

//如果流里面有异常需要抛出，只能按下面的的方式写
List<Integer> old = Stream.of(5).map(x -> {
    try {
        Thread.sleep(4);
    } catch (InterruptedException e) {
        return -1;
    }
    return x;
}).collect(Collectors.toList());

//如果用类库之后可以，可以用一种更加合理的方式去处理异常
List<Integer> collect = Stream.of(5).map((x) -> Try.ofFailable(() -> {
    Thread.sleep(400);
    return x;
})).map(x -> x.orElse(-1)).collect(Collectors.toList());
```

#### 10.2复制流

> Java 8 中附录为了解决流只能执行一次的问题，封装了一个 StreamForker 方法，可以把流复制并且并行执行的方法，代码如下

```java
public class StreamForker<T> {

    private final Stream<T> stream;
    private final Map<Object, Function<Stream<T>, ?>> forks = new HashMap<>();

    public StreamForker(Stream<T> stream) {
        this.stream = stream;
    }

    public StreamForker<T> fork(Object key, Function<Stream<T>, ?> f) {
        forks.put(key, f);
        return this;
    }

    public Results getResults() {
        ForkingStreamConsumer<T> consumer = build();
        try {
            stream.sequential().forEach(consumer);
        } finally {
            consumer.finish();
        }
        return consumer;
    }

    private ForkingStreamConsumer<T> build() {
        List<BlockingQueue<T>> queues = new ArrayList<>();

        Map<Object, Future<?>> actions =
                forks.entrySet().stream().reduce(
                        new HashMap<>(),
                        (map, e) -> {
                            map.put(e.getKey(),
                                    getOperationResult(queues, e.getValue()));
                            return map;
                        },
                        (m1, m2) -> {
                            m1.putAll(m2);
                            return m1;
                        });

        return new ForkingStreamConsumer<>(queues, actions);
    }

    private Future<?> getOperationResult(List<BlockingQueue<T>> queues, Function<Stream<T>, ?> f) {
        BlockingQueue<T> queue = new LinkedBlockingQueue<>();
        queues.add(queue);
        Spliterator<T> spliterator = new BlockingQueueSpliterator<>(queue);
        Stream<T> source = StreamSupport.stream(spliterator, false);
        return CompletableFuture.supplyAsync( () -> f.apply(source) );
    }

    public static interface Results {
        public <R> R get(Object key);
    }

    private static class ForkingStreamConsumer<T> implements Consumer<T>, Results {
        static final Object END_OF_STREAM = new Object();

        private final List<BlockingQueue<T>> queues;
        private final Map<Object, Future<?>> actions;

        ForkingStreamConsumer(List<BlockingQueue<T>> queues, Map<Object, Future<?>> actions) {
            this.queues = queues;
            this.actions = actions;
        }

        @Override
        public void accept(T t) {
            queues.forEach(q -> q.add(t));
        }

        @Override
        public <R> R get(Object key) {
            try {
                return ((Future<R>) actions.get(key)).get();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }

        void finish() {
            accept((T) END_OF_STREAM);
        }
    }

    private static class BlockingQueueSpliterator<T> implements Spliterator<T> {
        private final BlockingQueue<T> q;

        BlockingQueueSpliterator(BlockingQueue<T> q) {
            this.q = q;
        }

        @Override
        public boolean tryAdvance(Consumer<? super T> action) {
            T t;
            while (true) {
                try {
                    t = q.take();
                    break;
                }
                catch (InterruptedException e) {
                }
            }

            if (t != ForkingStreamConsumer.END_OF_STREAM) {
                action.accept(t);
                return true;
            }

            return false;
        }

        @Override
        public Spliterator<T> trySplit() {
            return null;
        }

        @Override
        public long estimateSize() {
            return 0;
        }

        @Override
        public int characteristics() {
            return 0;
        }
    }
}
//测试代码如下，想要用一个流同时进行分页，并且输出总个数，调用非常简单

int pageNo = 2;
int pageSize = 3;
Stream<Integer> integerStream = Stream.of(1, 2, 3, 5, 5, 6, 7, 8, 9);
StreamForker.Results results = new StreamForker<Integer>(integerStream)
    .fork("count", Stream::count)
    .fork("list", s -> s.skip((pageNo - 1) * pageSize).limit(pageSize).collect(Collectors.toList()))
    .getResults();
Long count = results.get("count");
List<Integer> list = results.get("list");
System.out.println("count:"+count);
System.out.println(list);

>> count:9
>> [5, 5, 6]
```


------------ 本文内容参考如下-------------

[《Java8 实战》](https://book.douban.com/subject/26772632/)

[图解 Monad - 阮一峰的网络日志 (ruanyifeng.com)](https://www.ruanyifeng.com/blog/2015/07/monad.html)

[jasongoodwin/better-java-monads (github.com)](https://github.com/jasongoodwin/better-java-monads)

[JavaLambdaInternals](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/6-Stream%20Pipelines.md)











