---
layout: post
title: 利用DelayQueue实现一个可定时的缓存
category: 技术
date: 2021-08-05
Author: AzureWind
tags: [java,blockingQueue]
comments: true
---
利用DelayQueue实现一个可定时的缓存，使用单例模式，以便调用的时候都是同一个对象，定时器用DelayQueue来实现，还可以支持多线程情况下的调用
<!-- more -->

#### DelayQueue简介
> DelayQueue是一个无界的BlockingQueue，用于放置实现了Delayed接口的对象，其中的对象只能在其到期时才能从队列中取走。这种队列是有序的，即队头对象的延迟到期时间最长。注意：不能将null元素放置到这种队列中。  
> 因为DelayQueue是基于PriorityQueue实现的,PriorityQueue底层是一个堆,可以按时间排序，所以等待队列本身只需要维护根节点的一个定时器就可以了，而且插入和删除都是时间复杂度都是logn，资源消耗很少，作为一个缓存的定时装置是非常适合的  
> 使用 DelayQueue 需要传入一个Delayed对象，要实现两个方法  
> 1 getDelay(TimeUnit unit) 表示当前对象关联的延时，在本例中表示生存时间  
> 2 compareTo(Delayed o) 堆在进行删除和插入时需要进行对比，所以要传入一个比较器

#### 实例展示
```java
public class ExpiryMap {

    private static final Logger log = LoggerFactory.getLogger(ExpiryMap.class);

    private ExpiryMap() {
    }
    private static volatile ExpiryMap expiryMap;
    /**
     * 键值对集合
     */
    private final static Map<String, Object> map = new HashMap<>();
    /**
     * 使用阻塞队列中的等待队列，因为DelayQueue是基于PriorityQueue实现的，而PriorityQueue底层是一个最小堆，可以按过期时间排序，
     * 所以等待队列本身只需要维护根节点的一个定时器就可以了，而且插入和删除都是时间复杂度都是logn，资源消耗很少
     */
    private final static DelayQueue<DelayData<String>> delay = new DelayQueue<>();

    //使用单例模式，加上双重验证，可适用于多线程高并发情况
    public static  ExpiryMap getInstance() {
        if (expiryMap == null) {
            synchronized (ExpiryMap.class) {
                if (expiryMap == null) {
                    expiryMap = new ExpiryMap();
                    //生成一个线程扫描等待队列的值
                    ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
                    scheduledExecutorService.execute(()->{
                        while (true) {
                            try {
                                //此方法是阻塞的，没有到过期时间就阻塞在这里，直到取到数据
                                DelayData<String> take = delay.take();
                                map.remove(take.data);
                            } catch (InterruptedException e) {
                                log.error("ExpiryMap线程被打断");
                            }
                        }
                    });
                }
            }
        }
        return expiryMap;
    }

    /**
     * 添加缓存
     *
     * @param key    键
     * @param data   值
     * @param expire 过期时间，单位：毫秒， 0表示无限长
     */
    public void put(String key, Object data, long expire) {
        map.put(key,data);
        //当等于0时，就不把过期时间放进队列里了，值在代码运行期间会一直存在
        if (expire != 0) {
            delay.offer(new DelayData<>(key, System.currentTimeMillis() + expire));
        }
    }

    /**
     * 读取缓存
     *
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return map.get(key);
    }

    /**
     * 清除缓存
     *
     * @param key 键
     * @return 值
     */
    public Object remove(String key) {
        return map.remove(key);
    }

    /**
     * 查询当前缓存的键值对数量
     * @return 数量
     */
    public int size() {
        return map.size();
    }
    //试下Delayed接口
    static class DelayData<T> implements Delayed{
        private T data;
        //到期时间
        private long expire;

        public DelayData(T data, long expire) {
            this.data = data;
            this.expire = expire;
        }

        //如果返回小于0就代表过期了
        @Override
        public long getDelay(TimeUnit unit) {
            //expire是过期时的时间
            long diffTime= expire- System.currentTimeMillis();
            return unit.convert(diffTime,TimeUnit.MILLISECONDS);
        }

        @Override
        public int compareTo(Delayed o) {
            return (int)(this.expire - ((DelayData<T>) o).getExpire());
        }

        public T getData() {
            return data;
        }

        public void setData(T data) {
            this.data = data;
        }

        public long getExpire() {
            return expire;
        }

        public void setExpire(long expire) {
            this.expire = expire;
        }
    }
```
#### 写一个测试方法
分别像缓存中放入3个值，时间分别是1s，2s，3s，每过1s，打印下结果
```java
    public static void main(String[] args) throws InterruptedException {
        ExpiryMap instance = getInstance();
        instance.put("key1","key1",1000);
        instance.put("key2","key2",2000);
        instance.put("key3","key3",3000);
        System.out.println(instance.get("key1"));
        System.out.println(instance.get("key2"));
        System.out.println(instance.get("key3"));
        Thread.sleep(1000);
        System.out.println("-----------------");
        System.out.println(instance.get("key1"));
        System.out.println(instance.get("key2"));
        System.out.println(instance.get("key3"));
        Thread.sleep(1000);
        System.out.println("-----------------");
        System.out.println(instance.get("key1"));
        System.out.println(instance.get("key2"));
        System.out.println(instance.get("key3"));
        Thread.sleep(1000);
        System.out.println("-----------------");
        System.out.println(instance.get("key1"));
        System.out.println(instance.get("key2"));
        System.out.println(instance.get("key3"));
    }
```
#### 输出结果
可以看出，缓存每过一秒就会被删除一个元素，删除时间是根据传入的过期时间清除的
```java
key1
key2
key3
-----------------
null
key2
key3
-----------------
null
null
key3
-----------------
null
null
null
```
CREATE TABLE `tbl_flop_setting` (
`id` varchar(20) NOT NULL COMMENT 'id',
`nick` varchar(50) NOT NULL COMMENT '用户nick',
`module_name` varchar(1024) DEFAULT NULL COMMENT '模块名称',
`card_num` int DEFAULT NULL COMMENT '卡片个数',
`act_start_time` varchar(20) NOT NULL COMMENT '活动开始时间',
`act_end_time` varchar(20) NOT NULL COMMENT '活动结束时间',
`create_time` varchar(20) DEFAULT NULL COMMENT '创建时间',
`open_init` int DEFAULT 0 COMMENT '是否开启初始赠送，0:不开启 1:开启',
`open_attentive` int DEFAULT 0 COMMENT '是否开启关注店铺赠送，0:不开启 1:开启',
`open_member` int DEFAULT 0 COMMENT '是否开启入会赠送，0:不开启 1:开启',
`open_share` int DEFAULT 0 COMMENT '是否开启分享店铺赠送，0:不开启 1:开启',
`init_num` int DEFAULT 0 COMMENT '初始赠送个数',
`attentive_num` int DEFAULT 0 COMMENT '关注店铺赠送个数',
`member_num` int DEFAULT 0 COMMENT '入会赠送个数',
`share_num` int DEFAULT 0 COMMENT '分享店铺赠送个数',
`template_type` int DEFAULT 1 COMMENT '模板类型 1:模板1，2:模板2，3:模板3，0:自定义模板',
`template_title` varchar(512) DEFAULT NULL COMMENT '卡片主标题',
`custom_title_color` varchar(20) DEFAULT NULL COMMENT '自定义模板的标题颜色色号，只有模板为自定义时有效',
`custom_theme_color` varchar(20) DEFAULT NULL COMMENT '自定义模板的主题颜色色号，只有模板为自定义时有效',
`custom_bg_img` varchar(512) DEFAULT NULL COMMENT '自定义模板的背景图片，只有模板为自定义时有效',
`custom_card_img` varchar(512) DEFAULT NULL COMMENT '自定义模板的卡片图片，只有模板为自定义时有效',
PRIMARY KEY (`id`)
) comment '翻牌优惠设置';

CREATE TABLE `tbl_flop_prize` (
`id` varchar(20) NOT NULL COMMENT 'id',
`nick` varchar(255) NOT NULL COMMENT '用户旺旺',
`flop_id` text DEFAULT NULL COMMENT 'tbl_flop_setting表对应id',
`type` int DEFAULT 0 COMMENT '类型 0:优惠券',
'last_num' int DEFAULT 0 COMMENT '剩余个数 -1代表无限个',
`create_time` varchar(20) DEFAULT NULL COMMENT '创建时间',
`total_num` int DEFAULT 0 COMMENT '奖品数量',
`probability` int DEFAULT 0 COMMENT '中奖概率，单位%，值为0-100的正整数',
PRIMARY KEY (`id`)
) comment ' 翻牌优惠设置奖品';

CREATE TABLE `tbl_flop_prize_coupon` (
`id` varchar(20) NOT NULL COMMENT 'id',
`nick` varchar(255) NOT NULL COMMENT '用户旺旺',
`flop_prize_id` varchar(20) DEFAULT NULL COMMENT 'tbl_flop_prize  对应id',
`title` varchar(255) DEFAULT NULL COMMENT '优惠券名称',
`create_time` varchar(20) DEFAULT NULL COMMENT '创建时间',
`expire_time` varchar(20) DEFAULT NULL COMMENT '优惠券过期时间',
`start_time` varchar(20) DEFAULT NULL COMMENT '优惠券开始时间',
`coupon_url` varchar(255) DEFAULT NULL COMMENT '优惠券链接',
`status` int DEFAULT NULL COMMENT '1:有效，2:失效',
PRIMARY KEY (`id`)
) comment '翻牌优惠设置的优惠券';

CREATE TABLE `tbl_flop_prize_record` (
`id` varchar(20) NOT NULL COMMENT 'id',
`nick` varchar(255) NOT NULL COMMENT '用户旺旺',
`buyer_nick` varchar(255) NOT NULL COMMENT '买家旺旺',
`type` int DEFAULT 0 COMMENT '类型 0:优惠券',
`status` int DEFAULT 0 COMMENT '0:已经领完，1:已经领取，2:未领取',
`position` int DEFAULT 1 COMMENT '翻出的牌所处的位置，从左至右，从上到下依次排列',
`flop_id` varchar(20) DEFAULT NULL COMMENT 'tbl_flop_setting  对应id',
`flop_prize_coupon_id` varchar(20) DEFAULT NULL COMMENT 'tbl_flop_prize_coupon  对应id',
`tbl_flop_prize` varchar(20) DEFAULT NULL COMMENT 'tbl_flop_prize 对应id',
`create_time` varchar(20) DEFAULT NULL COMMENT '创建时间',
PRIMARY KEY (`id`)
) comment '翻牌优惠设置的买家领取记录';

CREATE TABLE `tbl_flop_prize_buyer` (
`id` varchar(20) NOT NULL COMMENT 'id',
`nick` varchar(255) NOT NULL COMMENT '用户旺旺',
`buyer_nick` varchar(255) NOT NULL COMMENT '买家旺旺',
`flop_id` varchar(20) DEFAULT NULL COMMENT 'tbl_flop_setting  对应id',
`last_num` int DEFAULT 0 COMMENT '买家剩余翻牌次数',
`use_num` int DEFAULT 0 COMMENT '买家已经使用翻牌次数',
PRIMARY KEY (`id`)
) comment '翻牌优惠设置买家记录';