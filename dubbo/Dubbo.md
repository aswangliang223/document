# Dubbo

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200418224219418.png)

## Dubbo 内置的容错策略

Failover：故障转移策略。当消费者调用提供者集群中的某个服务器失败的时候，会自动调用其他的服务器。该策略主要用于读操作。

Failfast: 快速失败策略。消费者端只发起一次调用。如果失败立马报错。

- 幂等性：在请求参数相同的前提下，提交一次请求与发起N次请求，对系统产生的影响是相同的。
  - GET:幂等
  - POST:非幂等
  - PUT:幂等
  - DELETE:幂等

Failsafe:失败安全策略。例如：写入审计日志等操作

Failback:失败自动恢复策略

Forking:并行策略。消费者对同一个服务并行调用多个提供者服务器，只要一个成功即调用结束并返回结果。通常用于实时性要求较高的读操作，但是会浪费较多的服务器资源。

Broadcast:广播策略。广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或者日志等本地资源信息。

## dobbo 服务降级

解决高并发的三把利器：缓存，限流，降级

dobbo ，springcloud 一般采用 消费者端进行服务降级

服务降级方式：

- 部分服务暂停
- 全部服务暂停
- 随机拒绝服务
- 部分服务延迟

服务降级埋点

![](http://q8sats5bw.bkt.clouddn.com/20200422103606.png)

服务降级与Mock机制

Mock null降级处理

Mock class降级处理

​	Mock class 和对应的业务接口在一个包中，定义一个接口实现类，名称为 接口名+'Mock'。



## 服务调用超时

![](http://q8sats5bw.bkt.clouddn.com/20200422110319.png)





## 服务限流

直接限流：通过对连接数量直接进行限制来达到限流的目的

- executes限流：只能加在provider端，限制的是服务（方法）并发执行的数量
- accepts限流：仅可以设置在提供者端的<dubbo:provider />  或者 <dubbo:protocol />,用于对指定协议的连接数量进行限制
- actives 限流：既可以设置存在提供者端口也可以设置在消费者端口
  - 提供者端限流：根据消费者与提供者建立的连接类型的不同，意义也不同
    - 长连接：当前长连接最多可以处理的请求的个数。与长连接的数量没有关系。
    - 短连接：表示当前服务可以同时处理的短连接的数量
  - 消费者端限流：
    - 长连接：当前消费者所发出的长连接中最多可以提交的请求的个数，与长连接的个数没有关系
    - 短连接：当前消费者它可以提交的短连接的数量
- connections限流：限定连接的个数。一般情况下 connections 与 actives 联用。让 connections 限制长连接的个数，actives 限制一个长连接中可以处理的请求的个数，联用的前提:使用默认的Dubbo服务暴露协议

间接限流：

- 延迟连接：仅消费者端，不能设置到方法级别。仅用于dubbo服务暴露协议。将长连接的建立推迟到消费者真正调用提供者的时候。
- 粘连连接：仅消费者端，仅作用于dubbo服务暴露协议。会使得消费者端尽量向同一个提供者发起调用，除非该提供者挂了，只要启动了粘连连接，就会启动延迟连接。
- 负载均衡：可以设置在消费者端，可以以设置在提供者端；可以设置在接口级别，也可以设置在方法级别，其限制的是流向，而非流量

## 申明式缓存

保存在消费者端：默认会缓存1000个结果

```java
package com.abc.consumer;

import com.abc.service.SomeService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ConsumerRun2 {
    public static void main(String[] args) {
        ApplicationContext ac =
                new ClassPathXmlApplicationContext("spring-consumer.xml");
        SomeService service = (SomeService) ac.getBean("someService");

        for (int i=1; i<=1000; i++) {
            service.hello("i==" + i);
        }
        // 调用提供者，并将其执行结果存放到缓存，根据
        // LRU（最近最少使用）策略，其会将i==1的缓存结果挤出去,缓存淘汰策略
        System.out.println(service.hello("Tom"));
        // 由于缓存中已经没有了i==1的缓存，所以其会调用提供者，
        // 并将其执行结果存放到缓存，根据LRU（最近最少使用）策略，其会将i==2的缓存结果挤出去
        System.out.println(service.hello("i==1"));
        // 缓存中存在i==3的内容，所以其不会调用提供者
        System.out.println(service.hello("i==3"));
    }
}
```

申明式缓存应用于查询结果不会发生改变的情况。例如：查询某个产品的序列号，订单号，身份证号等。

# 多注册中心

consumer:

```java
    <dubbo:application name="08-consumer-registers"/>

    <!--声明注册中心-->
    <dubbo:registry id="bjCenter" address="zookeeper://bjZK:2181"/>
    <dubbo:registry id="gzCenter" address="zookeeper://gzZK:2181"/>
    <dubbo:registry id="cqCenter" address="zookeeper://cqZK:2181"/>

    <!--指定调用bjCenter注册中心微信服务-->
    <dubbo:reference id="weixin"  group="pay.weixin" registry="bjCenter"
                     interface="com.abc.service.SomeService"/>

    <!--指定调用gzCenter与cqCenter注册中心支付宝服务-->
    <dubbo:reference id="gzZhifubao"  group="pay.zhifubao" registry="gzCenter"
                     interface="com.abc.service.SomeService"/>
    <dubbo:reference id="cqZhifubao"  group="pay.zhifubao" registry="cqCenter"
                     interface="com.abc.service.SomeService"/>
```

provider:

```java

<dubbo:application name="08-provider-registers"/>

<!--声明注册中心-->
<dubbo:registry id="bjCenter" address="zookeeper://bjZK:2181"/>  <!--北京中心-->
<dubbo:registry id="shCenter" address="zookeeper://shZK:2181"/>  <!--上海中心-->
<dubbo:registry id="gzCenter" address="zookeeper://gzZK:2181"/>  <!--广州中心-->
<dubbo:registry id="cqCenter" address="zookeeper://cqZK:2181"/>  <!--重庆中心-->

<!--注册Service实现类-->
<bean id="weixinService" class="com.abc.provider.WeixinServiceImpl"/>
<bean id="zhifubaoService" class="com.abc.provider.ZhifubaoServiceImpl"/>

<!--暴露服务：同一个服务注册到不同的中心；不同的服务注册到不同的中心-->
<dubbo:service interface="com.abc.service.SomeService"
               ref="weixinService" group="pay.weixin" register="bjCenter, shCenter"/>
<dubbo:service interface="com.abc.service.SomeService"
               ref="zhifubaoService" group="pay.zhifubao" register="gzCenter, cqCenter"/>
```

## 单功能注册中心

仅订阅,仅注册

```java
    <!--声明注册中心：仅订阅-->
	<dubbo:registry id="gzCenter" address="zookeeper://gzZK:2181" register="false"/>
    <!--声明注册中心：仅注册-->
    <dubbo:registry id="gzCenter" address="zookeeper://gzZK:2181" subscribe="false"/>
```

## 服务暴露延迟



