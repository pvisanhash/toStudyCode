# 1 Sentinel基础应用

## 1.1 Sentinel介绍

### 1.1.1 什么是Sentinel

1\. **分布式系统的流量防卫兵**：随着微服务的普及，服务调用的稳定性变得越来越重要。[Sentinel](https://github.com/alibaba/Sentinel)以“流量”为切入点，在流量控制、断路、负载保护等多个领域开展工作，保障服务可靠性。

2\. 特点：
   1\. 
   2\. **+丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
   3\. **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
   4\. **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Apache Dubbo、gRPC、Quarkus 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。同时 Sentinel 提供 Java/Go/C++ 等多语言的原生实现。
   5\. **完善的 SPI 扩展机制**：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

3\. 官网文档：https://github.com/alibaba/Sentinel/wiki/%E4%BB%8B%E7%BB%8D



### 1.1.2 Sentinel好处

​	分布式系统面临的问题：复杂的体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免的失败，比如如下的例子中，当我们调用A、E、F、J、K这几个服务的时候如果其中一个服务出现问题会造成什么问题？其实就会出现整体系统效率全部下降，而且严重就会出现**服务雪崩**的问题！

![image-20211005013321645](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2aa610b6-84f9-592a-a891-78e8a83d2bca.png>)

​	**服务雪崩：**

​		多个微服务之间调用的时候，假设A调用B和C，B和C又调用其他的微服务，这就是所谓的**扇出**。如果扇出的某个链路上某个微服务调用的响应时间过程或者不可用，微服务A的调用就用占用越来越多的系统资源，从而引起系统崩溃，这也就是**服务雪崩**。其实就是服务的**高可用**遭到了破坏。

​		对于高流量的应用来说，单一的后端依赖可能会导致服务器上的所有资源都在几秒钟内饱和。同时还有可能造成这些应用程序导致服务之间的延迟增加，备份列队，线程和其他的系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系失败，不能取消整个应用程序或系统，所以通常发生了一个模块的某个实例失败后，这时候这个模块依然还会接受流量，然后这个有问题的模块还调用其他的模块，这样就会发生级联故障，或者叫做**雪崩**。

​		要解决这种问题的出现我们就需要用到服务降级，而Sentinel就可以保证在一个依赖出现问题的情况下，不会导致整体服务失败，避免级联故障，提高分布式系统的弹性。

​	**Sentinel的熔断降级通过断路器实现：**

​		断路器：它本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似于熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方法无法出的异常，这 样就保证了服务调用方的不会被长时间、不必要的占用，从而避免了故障在分布式系统中蔓延（类似于病毒传染），从而避免了故障在系统中蔓延，乃至崩溃。

​	**好处体现：**

​	对比与其他的产品而言，比如说Hystrix，他不需要我们自己手动搭建监控平台，而且它有一套类似于Nacos的Web界面，可以让我们进行更加细粒度的配置流控、速率、服务熔断、服务降级等

​	目前主流编程都是 约定>配置>代码，虽然我们的配置都可以写在代码中，但是我们还是要大面积的学习配置和注解的方式，尽量少些代码，这也是Sentinel的理念和初衷。

 

![image-20211005004311575](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9b07a12c-301a-5be8-8f5b-628387293f33.png>)

![image-20211005004335733](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cd593cf5-6afc-5c3d-9d32-e9cb25029fcd.png>)

### 1.1.3 Sentinel下载和安装

​	下载地址：https://github.com/alibaba/Sentinel/releases

![image-20211005005114846](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/948023f7-ee59-550d-ab4d-7de8cbc7b006.png>)

官方提供的手册：https://spring-cloud-alibaba-group.github.io/github-pages/hoxton/en-us/index.html#_spring_cloud_alibaba_sentinel



**Sentinel 分为两个部分**

1\. 核心库（Java客户端）不依赖任何框架/库，只需要Java运行时环境，同时对Dubbo/SpringCloud 等框架也有较好的支持。 
2\. 控制台（Dashboard）基于 SpringBoot开发，打包后可以直接运行，不需要额外的Tomcat等应用容器。

**启动步骤**

1\. 前提：jdk1.8环境和8080端口不能被占用

2\. 启动命令：java -jar sentinel-dashboard-1.8.2.jar

3\. 访问地址：localhost:8080

4\. 输入默认账号密码：sentinel/sentinel

  ![image-20211005010854771](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4bfe3c54-46dc-5b8a-9d8b-1ff106bc1d98.png>)

  ![image-20211005010930366](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c12efe56-5390-53c6-bc01-266783db05d9.png>)

![image-20211005011050291](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/33fc1099-1f2a-5e5c-bfc0-c9eb0cf6e3dc.png>)

到这里为止，我们的Sentinel安装成功。


---

## 1.2 Sentinel初始化监控



### 1.2.1 Sentinel初始化工程演示

我们现在通过一个案例来让大家了解Sentinel的初始化演示，现在我们需要做几件事：

1\. 启动Nacos8848成功
2\. 创建新的Module：cloudalibaba-sentinel-service8401
3\. 启动Sentinel8080
4\. 启动微服务8401
5\. 启动8401微服务后查看Sentinel控制台



### 1.2.2 搭建Sentinel项目

1\. Sentinel的官方文档网址：https://sentinelguard.io/zh-cn/docs/quick-start.html
2\. 创建项目cloudalibaba-sentinel-service8401
3\. 导入依赖：

```xml
<!-- Nacos客户端依赖 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!-- sentinel依赖 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

1\. 配置yaml文件，目的是让当前8401注册进Nacos，然后被Sentinel8080进行监控

```yaml
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        # 配置Sentinel dashboard地址
        dashboard: localhost:8080
        # 默认8719端口，键入被占用会自动从8719+1，直到找到未被占用的端口
        port: 8719

# Actuator 是 Spring Boot 提供的一套应用监控和管理功能，
# 通常通过 HTTP 接口暴露出来，用来查看服务运行状态、配置、指标、线程、日志等信息。
# 这里 通过 Web 暴露所有 Actuator 管理端点
management:
  endpoints:
    web:
      exposure:
        include: '*'
```



1\. 编写FlowLimitController

```java
package com.example.cloudalibabasentinel8401.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class FlowLimitController {
    @GetMapping("/testA")
    public String testA(){
        return "-----testA";
    }

    @GetMapping("/testB")
    public String testB(){
        return "-----testB";
    }
}
```



### 1.2.3 测试

1\. 当以上的这些配置配置好以后，我们就可以进行测试了，那我们的测试方式就是，首先保证Nacos和Sentinel都是启动状态，然后再来启动项目，按照我们的理解这个时候，就应该在Sentinel的dashboard上能体现出它监控的服务，但是此时并没有，原因是因为Sentinel本身采用的是懒加载机制，所以我们需要首先访问服务对应的接口，Sentinel才能工作。

```java
http://localhost:8401/testA
http://localhost:8401/testB
```

1\. 访问之后我们来查看Sentinel的dashboard

![image-20211008125726257](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d2384852-d6ea-5893-9473-2e85eee46576.png>)

1\. 那么这个时候我们频繁快速的访问testA或者testB那么我们再来查看实时监控的时候，就会出现波动，体现此时Sentinel正在监控这我们的8401这个服务

![image-20211008130011873](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c05c7aca-c492-5fb5-9514-ffc86a5ffcda.png>)


---

## 1.3 Sentinel流控规则

### 1.3.1 流控规则基本介绍

![image-20211009142204101](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9e7df8f5-a3a6-5d19-9737-8e70a91bff70.png>)



## 1.4 名词解释

1\. 资源名：唯一名称，默认请求路径
2\. 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
3\. 阈值类型/单机阈值：
  1\. QPS（每秒钟的请求数量）：当调用该API的QPS达到阈值的时候，进行限流
  2\. 线程数：当调用该API的线程数量达到阈值的时候，进行限流
4\. 是否集群：不勾选 / false：单机限流，每个服务实例自己单独计算 QPS；勾选 / true：集群限流，多个服务实例共享同一个限流额度，由 Sentinel 集群限流服务统一统计和分配。
5\. 流控模式：
  1\. 直接：API达到限流条件时，直接限流
  2\. 关联：当关联的资源达到阈值时，就限流自己
  3\. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）(API级别的针对来源)
6\. 流控效果：
  1\. 快速失败：直接失败，抛异常
  2\. Warm Up：根据codeFactor（冷加载因子，默认3）的值，从阈值/codeFacotor，经过预热时长，才达到设置的QPS阈值
  3\. 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效



### 1.4.1 具体操作

#### 1.4.1.1 新增流控

**QPS直接失败案例**

1\. 添加有两种方式，可以直接在流控规则选项中添加，也可以在簇点链路中添加，一般会采取第二种方式

![image-20211009153205839](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/917ad8c0-bec3-53c2-837a-263cfb9b9aaf.png>)

1\. 现在我们给"/testA"添加流控。

![image-20211009153539150](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/34edef16-aea6-5ae8-874e-a485bb9ebb95.png>)

1\. 这里的意思就是我们现在单机阈值设定为1，代表的是当前这个接口只能被1秒访问一次，超过这个阈值，就会被Sentinel阻塞，现在默认为直接失败，也就是会在前台有一个体现

![image-20211009153829675](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/41a5b076-34d3-59f8-bb84-c6cae0d2c08c.png>)

**线程数直接失败案例**

1\. 刚才我们可以的设置是通过QPS（每秒钟请求的数量）来设置的限流规则，但是我们这里其实还有一个线程数，是什么意思那？

![image-20211009154620844](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c544d57d-2023-5d2e-ad34-2963b0078b3f.png>)

1\. QPS和并发线程数规则详解

![未命名绘图.drawio](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/55c65f4e-1b2b-5d2d-8dbb-2c0885e63047.png>)

1\. 那我们要演示这种效果，我们就需要让一个线程再进来办理的时候需要0.8秒，但是这个时候后面的线程也在疯狂的访问，所以后面的线程就会不会生效。

```java
package com.example.cloudalibabasentinel8401.controller;

import com.alibaba.csp.sentinel.annotation.SentinelResource;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.TimeUnit;

@RestController
public class FlowLimitController {
    @GetMapping("/testA")
    public String testA(){
        //暂停0.8秒
        try {
            TimeUnit.MILLISECONDS.sleep(800);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "-----testA";
    }

    @GetMapping("/testB")
    public String testB(){
        return "-----testB";
    }
}
```

1\. 这个时候我们重启项目，然后重新通过访问testA接口，通过两个网页（线程）来快速访问，这个时候我们来看效果，这里要注意，要重新添加流控规则。

![image-20211009162325899](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d9b50919-1917-5a40-ada9-9dddbfdd0dd5.png>)

**注意：这个时候虽然效果一致，但是是两种完全不同的规则，一种是QPS，一种是并发线程，这点大家一定要分清！**


---

## 1.5 流控规则-关联

首先我们先来回顾一下之前讲过的一些概念

### 1.5.1 名词解释

1\. 资源名：唯一名称，默认请求路径
2\. 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
3\. 阈值类型/单机阈值：
  1\. QPS（每秒钟的请求数量）：当调用该API的QPS达到阈值的时候，进行限流
  2\. 线程数：当调用该API的线程数量达到阈值的时候，进行限流
4\. 是否集群：当前不需要集群
5\. 流控模式：
  1\. 直接：API达到限流条件时，直接限流
  2\. 关联：当关联的资源达到阈值时，就限流自己
  3\. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）(API级别的针对来源)
6\. 流控效果：
  1\. 快速失败：直接失败，抛异常
  2\. Wam Up：根据codeFactor（冷加载因子，默认3）的值，从阈值/codeFacotor，经过预热时长，才达到设置的QPS阈值
  3\. 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效



### 1.5.2 关联

​	官方解释：当关联的资源达到阈值时，就限流自己。

​	通俗解释来说，比如那我们的程序，现在有**testA**接口和**testB**接口，当A关联的资源B达到阈值后，就限流自己，也就是B到达阈值，限流A本身。就好像我家孩子在外面打架，我来处理一样。换到程序里面来说比如一个电商系统中，支付系统达到阈值，就限流下订单系统。

![image-20211101144934374](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e83a17ca-d5b4-5d64-8568-8b2b316052cf.png>)



### 1.5.3 具体演示

​	当关联资源**/testB**的qps阈值超时1时，就限流**/testA**的Rest访问地址，当关联资源到阈值后限制配置好的资源名，首先我们先把FlowLimitController接口恢复原样

```java
@RestController
public class FlowLimitController {
    @GetMapping("/testA")
    public String testA(){
        return "-----testA";
    }

    @GetMapping("/testB")
    public String testB(){
        return "-----testB";
    }
}
```

给testA添加流控规则

![image-20211101155951868](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a51d5e16-11de-53bd-9951-57501bfffe4f.png>)

​	为了演示效果，所以这里我们需要借助一个工具Postman，来模仿并发密集访问/testB，先来测试访问testB接口

![image-20211101163301396](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4524fc79-43cd-575c-9fb3-fbf2fb663096.png>)

​	这个时候我们需要多次密集访问TestB接口，所以我们需要添加配置，具体操作如下：

![image-20211101164057576](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c51ddd67-9d74-5a7a-9586-d27921a2b886.png>)

把数值修改为：

1\. Iterations：为20
2\. Delay：300

意思就是20个线程每间隔0.3秒访问一次，然后跑起来

![image-20211101164345389](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ede2ddb3-866f-5ea0-bafb-1eaa505396bb.png>)

​		这个时候我们来看网页中testA接口的效果

![image-20211101164427548](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4d664bdb-24c4-54df-b554-2db3f785d5fb.png>)


---

## 1.6 流控规则-链路

### 1.6.1 名词解释

1\. 资源名：唯一名称，默认请求路径
2\. 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
3\. 阈值类型/单机阈值：
  1\. QPS（每秒钟的请求数量）：当调用该API的QPS达到阈值的时候，进行限流
  2\. 线程数：当调用该API的线程数量达到阈值的时候，进行限流
4\. 是否集群：当前不需要集群
5\. 流控模式：
  1\. 直接：API达到限流条件时，直接限流
  2\. 关联：当关联的资源达到阈值时，就限流自己
  3\. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）(API级别的针对来源)
6\. 流控效果：
  1\. 快速失败：直接失败，抛异常
  2\. Wam Up：根据coldFactor（冷加载因子，默认3）的值，从阈值/codeFacotor，经过预热时长，才达到设置的QPS阈值
  3\. 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效



### 1.6.2 链路

​	链路流控模式指的是，当从某个接口过来的资源达到限流条件时，开启限流，它的功能有点类似于针对来源配置项，区别在于：针对来源是针对上级微服务，而链路流控是针对上级接口，也就是说它的粒度更细。

​	比如在一个微服务中，两个接口都调用了同一个Service中的方法，并且该方法用SentinelResource（用于定义资源）注解标注了，然后对该注解标注的资源（方法）进行配置，则可以选择链路模式。

​	![image-20211101190257683](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8141b38a-b246-55ff-a349-2905a24b8c06.png>)

### 1.6.3 具体演示

首先我们编写一个Service

```java
//service.TestService
@Service
public class TestService {
    // 定义限流资源
    @SentinelResource("common")
    public String common(){
        return "common";
    }
}
```

然后更改接口调用这个Service方法

```java
@RestController
public class FlowLimitController {
    @Autowired
    TestService testService;
    
    @GetMapping("/testA")
    public String testA(){
        return testService.common();
    }

    @GetMapping("/testB")
    public String testB(){
        return testService.common();
    }
}
```

接下来配置流控规则：

这里要注意不要对/testA或者/testB进行限流规则的配置，要给用SentinelResource注解标注的资源进行配置限流规则，这里的意思为当我们用入口资源访问被SentinelResource注解标注的资源方法时，当超过阈值就会被限流。



![image-20211101200050932](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5cca2f8e-8b17-57c5-ac1c-5afd1434fe02.png>)

![image-20211101195842516](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/00774058-ed66-5938-9daf-c1bf593f6fae.png>)

![image-20211101202216083](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e01c81dc-ef91-5f9e-8401-f80814ca5600.png>)

注意：

如果没有效果，原因是因为我们还需要添加配置，让Sentinel 源码中 CommonFilter 中的 WEB_CONTEXT_ UNIFY 参数为False，将其配置为false 即可根据不同的URL 进行链路限流，如果不配置将不会生效。

如果版本为，或以上：

```java
<spring-cloud-alibaba-version>2.2.5.RELEASE</spring-cloud-alibaba-version>
```

不需要再设置web-context-unify

```yaml
spring:
  cloud:
    sentinel:
			# 不要把所有 Web 请求统一到同一个上下文里，而是根据不同 URL 创建不同链路上下文
      web-context-unify: false
```




---

## 1.7 Sentinel流控效果-预热

### 1.7.1 名词解释

1\. 资源名：唯一名称，默认请求路径
2\. 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
3\. 阈值类型/单机阈值：
  1\. QPS（每秒钟的请求数量）：当调用该API的QPS达到阈值的时候，进行限流
  2\. 线程数：当调用该API的线程数量达到阈值的时候，进行限流
4\. 是否集群：当前不需要集群
5\. 流控模式：
  1\. 直接：API达到限流条件时，直接限流
  2\. 关联：当关联的资源达到阈值时，就限流自己
  3\. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）(API级别的针对来源)
6\. **流控效果：**
  1\. 快速失败：直接失败，抛异常
  2\. **Warm Up：根据coldFactor（冷加载因子，默认3）的值，从阈值/codeFacotor，经过预热时长，才达到设置的QPS阈值**
  3\. 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效



### 1.7.2 预热

官网手册地址：https://sentinelguard.io/zh-cn/docs/flow-control.html

概念：Warm Up方式，即预热/冷启动方式。该方式主要用于系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮。通过"冷启动"，让通过的流量缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮的情况。

​	

预热公式：阈值/coldFactor（默认值为3），经过预热时间后才会达到阈值。

​	

冷启动的过程系统允许通过的QPS曲线如下图：

![image-20211102163029355](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e2ff5569-e837-5dd4-8c51-c24a76f6ccd9.png>)

简单理解：

![image-20211102163937106](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5f92468d-fc08-592d-a7e7-7342a231b567.png>)



使用场景：一般秒杀系统中会有这样的流控设置，为了防止秒杀瞬间造成系统崩溃。



**注意：**

Warn Up系统刚开始时不允许流量一下子打满阈值，而是在一段预热时间内逐步升到你设置的阈值；如果后面长时间没有流量，令牌/通过能力会冷却回去，再次来流量时又会重新预热



### 1.7.3 案例

​	默认coldFactor为3，当发起请求即请求QPS从（阈值/3）开始，经过多长预热时长才逐步升至设定的QPS阈值，当前阈值设置为10，预热时长设置为5秒。

​	最终的效果，系统初始化时阈值/3约等于3，即阈值在此时为3，经过5秒后阈值才慢慢升高到10

首先我们先来设置流控效果：

![image-20211102165915771](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5a938a04-d64f-534f-9db2-d0962ccc5b88.png>)



测试，我们用最简单的方法进行测试，直接在浏览器上手动刷新，然后我们来看Sentinel的实时监控

![image-20211102182120756](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b8ab0258-89f7-551c-ad5b-40ea48a1bbbc.png>)


---

## 1.8 Sentinel流控效果-排队等待

### 1.8.1 名词解释

1\. 资源名：唯一名称，默认请求路径
2\. 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
3\. 阈值类型/单机阈值：
  1\. QPS（每秒钟的请求数量）：当调用该API的QPS达到阈值的时候，进行限流
  2\. 线程数：当调用该API的线程数量达到阈值的时候，进行限流
4\. 是否集群：当前不需要集群
5\. 流控模式：
  1\. 直接：API达到限流条件时，直接限流
  2\. 关联：当关联的资源达到阈值时，就限流自己
  3\. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）(API级别的针对来源)
6\. **流控效果：**
  1\. 快速失败：直接失败，抛异常
  2\. Warm Up：根据coldFactor（冷加载因子，默认3）的值，从阈值/codeFacotor，经过预热时长，才达到设置的QPS阈值
  3\. **排队等待（匀速器）：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效**

### 1.8.2 排队等待

​	官方文档：https://sentinelguard.io/zh-cn/docs/flow-control.html

​	概念：匀速排队方式会严格控制请求通过的间隔时间，也即是让请求以均匀的速度通过，对应的是漏桶算法。

​	这种方式主要用于处理间隔性突发的流量，例如消息队列。想象一下这样的场景，在某一秒有大量的请求到来，而接下来的几秒则处于空闲状态，我们希望系统能够在接下来的空闲期间逐渐处理这些请求，而不是在第一秒直接拒绝多余的请求（削峰填谷）。

​	例图：

![image-20211102175948987](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/985b9aa7-f75a-5ebb-a412-004b64e95782.png>)



#### 1.8.2.1 匀速器

​	它的中心思想是，以固定的间隔时间让请求通过。当请求到来的时候，如果当前请求距离上个通过的请求通过的时间间隔不小于预设值，则让当前请求通过。否则，计算当前请求的预期通过时间，如果该请求的预期通过时间小于规则预设的 timeout 时间，则该请求会等待直到预设时间到来通过（排队等待处理）；若预期的通过时间超出最大排队时长，则直接拒接这个请求。

![image-20211102182416282](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cc0928af-94fd-58b7-b982-9efa002bc2b6.png>)

Sentinel 匀速排队等待策略是漏桶算法结合虚拟队列等待机制实现的。



**注意：**匀速排队模式暂时不支持 QPS > 1000 的场景。



### 1.8.3 演示

流控规则：

![image-20211102182826152](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6daf759d-34e9-5fdd-8c31-8c988484e8ab.png>)

为了看到效果，我们在代码中进行打印，更改8401微服务中的FlowLimitController

```java
package com.example.cloudalibabasentinel8401.controller;

import com.alibaba.csp.sentinel.annotation.SentinelResource;
import com.example.cloudalibabasentinel8401.service.TestService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.TimeUnit;

@RestController
@Slf4j
public class FlowLimitController {
    @Autowired
    TestService testService;
    @GetMapping("/testA")
    public String testA(){
        log.info(Thread.currentThread().getName()+"：testA");
        return testService.common();
    }

    @GetMapping("/testB")
    public String testB(){
        return testService.common();
    }
}
```

最后我们可以通过Postman来进行测试，发送请求时没有延迟，同时发送10条请求，然后我们会发现就是排队效果1秒执行一个请求，同时我们在Idea中也可以看到打桩效果

![image-20211102184705742](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4e929387-3b1f-5e71-b324-2d2da0adf74a.png>)


---

## 1.9 Sentinel 熔断降级简介

### 1.9.1 基本介绍	

​	除了流量控制以外，对调用链路中不稳定的资源进行熔断降级也是保障高可用的重要措施之一。一个服务常常会调用别的模块，可能是另外的一个远程服务、数据库，或者第三方 API 等。例如，支付的时候，可能需要远程调用银联提供的 API；查询某个商品的价格，可能需要进行数据库查询。然而，这个被依赖服务的稳定性是不能保证的。如果依赖的服务出现了不稳定的情况，请求的响应时间变长，那么调用服务的方法的响应时间也会变长，线程会产生堆积，最终可能耗尽业务自身的线程池，服务本身也变得不可用。

![chain](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/863c6011-6d14-58ff-adea-b4ae678a1457.png>)

​	现代微服务架构都是分布式的，由非常多的服务组成。不同服务之间相互调用，组成复杂的调用链路。以上的问题在链路调用中会产生放大的效果。复杂链路上的某一环不稳定，就可能会层层级联，最终导致整个链路都不可用。因此我们需要对不稳定的**弱依赖服务调用**进行熔断降级，暂时切断不稳定调用，避免局部不稳定因素导致整体的雪崩。熔断降级作为保护自身的手段，通常在客户端（调用端）进行配置。



### 1.9.2 熔断策略

Sentinel 提供了一下几种熔断策略：

1\. 慢调用比例 (`SLOW_REQUEST_RATIO`)：选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。

2\. 异常比例 (`ERROR_RATIO`)：当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。
3\. 异常数 (`ERROR_COUNT`)：当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。

![image-20211103172422267](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e244f6b7-c7d6-5d70-bb60-8709f776e5cd.png>)



Sentinel在1.8.0版本对熔断降级做了大的调整，可以定义任意时长的熔断时间，引入了半开启恢复支持。下面梳理下相关特性。

熔断状态有三种状态，非别为OPEN、HALF_OPEN、CLOSED

| 状态      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| OPEN      | 表示熔断开启，拒绝所有请求                                   |
| HALF_OPEN | 探测恢复状态，如果接下来的一个请求顺利通过则表示结束熔断，否则继续熔断 |
| CLOSE     | 表示熔断关闭，请求顺利通过                                   |

### 1.9.3 熔断规则

熔断降级规则包含下面几个重要的属性：

| Field              | 说明                                                         | 默认值     |
| ------------------ | ------------------------------------------------------------ | ---------- |
| resource           | 资源名，即规则的作用对象                                     |            |
| grade              | 熔断策略，支持慢调用比例/异常比例/异常数策略                 | 慢调用比例 |
| count              | 慢调用比例模式下为慢调用临界 RT（超出该值计为慢调用）；异常比例/异常数模式下为对应的阈值 |            |
| timeWindow         | 熔断时长，单位为 s                                           |            |
| minRequestAmount   | 熔断触发的最小请求数，请求数小于该值时即使异常比率超出阈值也不会熔断（1.7.0 引入） | 5          |
| statIntervalMs     | 统计时长（单位为 ms），如 60*1000 代表分钟级（1.8.0 引入）   | 1000 ms    |
| slowRatioThreshold | 慢调用比例阈值，仅慢调用比例模式有效（1.8.0 引入）           |            |

官方文档网址：https://sentinelguard.io/zh-cn/docs/circuit-breaking.html


---

## 1.10 Sentinel熔断策略-慢调用比例



### 1.10.1 慢调用比例

​	概念：选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。

![image-20211103175357455](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4619ad42-87fa-5774-97a9-761df94f5d81.png>)

​	简单理解：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/01fae914-77d1-5efc-b30d-d56be1a57170.png" alt="image-20211103194830095" style="zoom: 33%;" />

举例：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/0b044d2d-72c4-51bc-994b-9881a1af3829.png" alt="image-20211103182809594" style="zoom:33%;" />



### 1.10.2 案例演示

首先我们先添加一个控制器方法：

```java
//FlowLimitController.java
@GetMapping("/testC")
public String testC(){
    try {
        TimeUnit.SECONDS.sleep(5);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return "----testC";
}
```

设置熔断策略，1QPS>5 并且这些请求的RT>300 并且大于比例阈值触发熔断

![image-20211103193843437](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d40c9936-7912-56b5-95b0-d53b11f8049d.png>)

### 1.10.3 测试

通过JMeter测试，1秒钟发起10个线程请求/testC，此时就会触发熔断效果，停止测试以后，10秒钟以后恢复正常

![image-20211103200622564](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ed774e24-2cf8-59e9-b5b8-615555d19626.png>)


---

## 1.11 Sentinel熔断策略-异常比例

### 1.11.1 异常比例

​	概念：异常比例 (`ERROR_RATIO`)：当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。

​	注意：异常降级**仅针对业务异常**，对 Sentinel 限流降级本身的异常（`BlockException`）不生效。

![image-20211104143705448](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/eac10819-d555-5a4c-87dd-f35d4851df31.png>)

简单理解：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1ad14113-7b0e-57eb-8550-fed91972b75d.png" alt="image-20211104150725704" style="zoom: 33%;" />

### 1.11.2 案例

编写测试接口

```java
//FlowLimitController
@GetMapping("/testD")
public String testD(Integer id){
    if(id != null && id > 1){
        throw new RuntimeException("异常比例测试");
    }
    return "------------testD";
}
```

设置熔断策略异常比例

![image-20211104202552823](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/210aa21a-8e12-524e-a881-b4277699623d.png>)

### 1.11.3 测试

我们通过JMeter来测试，设定HTTP请求地址

![image-20211104154856685](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9d94a3b5-04a6-5a09-814b-2da187394471.png>)

1秒钟发送10个请求

![image-20211104154931689](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fd6984ff-9b65-582d-bf5e-44c42a35d2cc.png>)

当启动JMeter的时候，就会触发熔断，因为此时我们1秒钟发送10个请求超过了最小请求数5，同时超过了阈值，满足了两个条件，当熔断时长过后就会恢复正常。

![image-20211104155351048](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/dc78fcea-48e9-582b-b4ea-aa7bb4cf9e6c.png)


---

## 1.12 Sentinel熔断策略-异常数

### 1.12.1 异常数

​	概念：异常数 (`ERROR_COUNT`)：当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。

​	注意：异常降级**仅针对业务异常**，对 Sentinel 限流降级本身的异常（`BlockException`）不生效。

![image-20211104160536648](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cb5f77ba-436e-5b3b-a7c6-46e3ecd31b3b.png>)

简单理解：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2b0836e9-b926-5857-b79b-0c56d70300a5.png" alt="image-20211104162446764" style="zoom:33%;" />

### 1.12.2 案例演示

编写接口

```java
//FlowLimitController
@GetMapping("/testE")
public String testE(Integer id){
    if(id != null && id > 1){
        throw new RuntimeException("异常数测试");
    }
    return "------------testE";
}
```

设置异常数策略，当1秒钟内请求超过5并且异常数大约5个的时候触发熔断

![image-20211104162844045](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/43043e23-85e8-5187-8af7-6a7c1bb30bfb.png>)

### 1.12.3 测试

通过JMeter来测试

![image-20211104163014944](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/eff7e5fd-d714-51ba-a1cb-a65fe9898523.png>)

1秒钟发送10个请求

![image-20211104163107494](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1a0e183b-9de9-5f2b-898f-cbeaa4b4a181.png>)

此时就会触发熔断

![image-20211104163209702](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7c333d74-6dd4-5b21-9636-3d1b6b91f3c2.png>)


---

## 1.13 Sentinel 热点规则（上）

### 1.13.1 概念

何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制。比如：

1\. 商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
2\. 用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效

官网：https://sentinelguard.io/zh-cn/docs/parameter-flow-control.html

![sentinel-hot-param-overview-1](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d24346b3-5119-5f62-b3ac-5e3701c2fcb5.png>)



![image-20211104175442912](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a2ab9317-26d3-5e99-99b9-5add3b90049d.png>)

这里还有相对应的高级选项，我们这里先了解基本规则。

### 1.13.2 使用@SentinelResource注解

其实这个热点限流其实就是更加细粒度的流控规则，那么如果想使用它就必须要配合对应SentinelResource注解。

Sentinel 提供了 @SentinelResource 注解用于定义资源，它有很多的参数，我们这里主要关注两个参数：

1\. value：代表资源名称，必需项，因为需要通过resource name找到对应的规则，这个是必须配置的
2\. blockHandler：blockHandler 对应处理 BlockException 的方法名称，可选项，访问范围需要是 public，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 BlockException。

### 1.13.3 案例讲解

![image-20211104185357465](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7679477c-9167-55a7-958a-6fd5089748d1.png>)

#### 1.13.3.1 @SentinelResource(value="xxx")

那现在我们要完成以上图中的效果，这个时候我们首先要编写代码，在FlowLimitController中编写代码

```java
@GetMapping("/testHotKey")
@SentinelResource("testHotKey")
public String testHotKey(@RequestParam(value = "hot1",required = false) String hot1,
                         @RequestParam(value = "hot2",required = false)String hot2,
                         @RequestParam(value = "hot13",required = false) String hot3){
    return "----testHotKey";
}
```

然后再来配置热点规则

![image-20211104185609520](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8ba0c963-63f0-5912-b9a4-e983849bc32d.png>)

这里要说明一下，参数索引0实际上代表的就是我们设置的hot1参数

![image-20211104190217984](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/214318c9-6002-5ec8-b49e-47627e31feb7.png>)



测试，此时如果我们传入参数hot1，并且超过阈值，就会出现限流，但是此时的限流效果为报错，显示不友好

![image-20211104192341003](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/49b230f3-dff4-5639-84d4-8c2dd3d7853c.png>)

#### 1.13.3.2 @SentinelResource(value="xxx",blockHandler="xxx")

刚才的演示中，我们明显发现这种限流方法的提示效果非常不友好，所以如果我们需要能够得到友好的提示，我们就需要使用@SentinelResource注解提供的另外一个参数blockHandler，这个参数是可以指定当出现异常时的处理方法，具体操作如下：

```java
@GetMapping("/testHotKey")
@SentinelResource(value = "testHotKey",blockHandler = "handler_HotKey")
public String testHotKey(@RequestParam(value = "hot1",required = false) String hot1,
                         @RequestParam(value = "hot2",required = false)String hot2,
                         @RequestParam(value = "hot13",required = false) String hot3){
    return "----testHotKey";
}

//处理异常方法，方法签名要和对应的接口方法保持一致
public String handler_HotKey(String hot1, String hot2,String hot3,BlockException exception){
    return "系统繁忙稍后重试。。";
}
```

然后热点规则不变，我们最终的到的限流效果如下：

![image-20211104195122220](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1a3ed181-363d-5c63-9d2c-215141378486.png>)


---

## 1.14 Sentinel 热点规则（下）

### 1.14.1 概念

何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制。比如：

1\. 商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
2\. 用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效

官网：https://sentinelguard.io/zh-cn/docs/parameter-flow-control.html

![sentinel-hot-param-overview-1](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2d672f11-a155-570a-8c37-a4963b59caa7.png>)

![image-20211105144025601](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8f8a9787-1926-5c60-b7f3-ecde0b6ff955.png>)

### 1.14.2 参数例外项

​	其实参数例外项就是可以达到更加细粒度的控制，比如我们当前的例子中，目前hot1参数在访问时超过阈值就会被限流，但是我们可以通过参数例外项设置hot1具体等于特殊的某个值的时候，触发不同的限流效果。假如hot1的值等于5时，它的阈值可以达到200。

​	**注意：**参数例外项中的参数类型仅支持一下7种数据类型

![image-20211105144912213](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f38513ac-1bc6-5feb-9dba-25813630b372.png>)

### 1.14.3 案例演示

当前我们需要让hot1的值为5的时候阈值可以达到200，首先Sentinel页面中修改对应热点规则（在这之前，先演示传递一个参数，否则配置失败）

![image-20211105145445284](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f38c556b-850f-5ab4-ad99-c505c76771d0.png>)

此时的规则为：如果当前hot1值为除5以外的其他值，都会走普通的阈值规则，但是如果一旦hot1的值为5的时候，将会走参数例外项，此时的阈值为200，我们通过浏览器测试，当hot1的值等于5是只要阈值不超过200就不会出现限流。

![image-20211105154930110](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2583ac8b-6a81-59af-8dc1-7a0089ec6026.png>)

​	注意：题我们到现在代码中使用了@SentinelResource注解，此注解处理的是**Sentinel控制台配置的异常**，通过blockHandler属性设置对应方法来处理和程序本身异常无关。

​	所以以下程序中如果hot1的值等于6还是会出现RuntimeException。

```
@SentinelResource(value = "testHotKey",blockHandler = "handler_HotKey")
public String testHotKey(@RequestParam(value = "hot1",required = false) String hot1,
                         @RequestParam(value = "hot2",required = false) String hot2,
                         @RequestParam(value = "hot3",required = false) String hot3){
    if("6".equals(hot1)){
        throw new RuntimeException("运行时异常");
    }
    return "-----testHotKey";
}
```


---

## 1.15 Sentinel 系统规则

​	Sentinel 系统自适应限流从整体维度对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

### 1.15.1 系统规则

​	系统保护规则是从应用级别的入口流量进行控制，从单台机器的 load、CPU 使用率、平均 RT、入口 QPS 和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

​	系统保护规则是应用整体维度的，而不是资源维度的，并且**仅对入口流量生效**。入口流量指的是进入应用的流量，比如 Web 服务或 Dubbo 服务端接收的请求，都属于入口流量。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/71aaae5c-258d-50b7-85d2-e9f3bb48234e.png" alt="image-20211108170904455" style="zoom:33%;" />

**系统规则支持一下的模式：**

1\. **Load 自适应**（仅对 Linux/Unix-like 机器生效）：系统的 load1（1分钟平均负载） 作为启发指标，进行自适应系统保护。当系统 load1（1分钟平均负载） 超过设定的启发值（阈值），且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 `maxQps(秒级统计的最大QPS) * minRt(秒级统计的最小响应时间)` 估算得出。设定参考值一般是 `CPU cores * 2.5`。
2\. **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。
3\. **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
4\. **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
5\. **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

![image-20211108162812623](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/df10fc5a-07ae-5cf0-9f6c-0857d0e9dfd6.png>)



### 1.15.2 案例演示

这里我们只通过入口QPS来进行测试，直接设置规则

![image-20211108173108215](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fb55e466-c587-5481-8f5a-e0dcb6478c2e.png>)

最后测试效果不管现在我们访问那个接口只要超过阈值就会被限流

![image-20211108173202180](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/136dec0b-be5c-5a53-ae42-de33d26aec96.png>)


---

## 1.16 @SentinelResource 自定义限流逻辑处理

​	Sentinel 提供了@SentinelResource注解用于定义资源,并提供了AspectJ的扩展用于自定义资源,处理BlockException等。

### 1.16.1 案例复习

之前我们用过这个注解，同时了解了它的两个属性：

1\. value：资源名称，必须项（唯一，不能为空）
2\. blockHandler：对应处理BlockException的函数名称可选项.blockHandler函数访问需要public,返回类型需要与原方法相匹配,参数类型需要和原方法相匹配并且最后加一个额外的参数,类型为BlockException.blockHandler 函数默认需要和原方法在同一个类中

我们之前利用这个注解完成了热点规则的学习，同时做了一个案例，我们简单复习一下，这个案例的核心思想就是我们传递一个指定参数，然后通过注解@SentinelResource注解标注资源进行限流，当出现限流以后，通过blockHandler属性设置限流以后的解决方法。

![image-20211104185357465](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a5526746-dc1b-53aa-a0c4-82e715426008.png>)

其实这个注解不仅仅可以用到热点规则上，还可以用到流控上，我们可以做一个资源的流控和一个请求的流控，通过此注解来解决限流之后问题。

### 1.16.2 @SentinelResource 资源限流

**核心点：**使用@SentinelResource注解的blockHandler属性，定义出现限流效果时的解决方法。

编写一个新的控制器类型SentinelResourceTestController，使用@SentinelResource注解同时使用blockHandler属性

```java
@GetMapping("/byResource")
@SentinelResource(value = "byResource",blockHandler = "handler_resource")
public String byResource(){
    return "-----byResource";
}

public String handler_resource(BlockException exception){
    return "系统繁忙";
}
```

这里要注意一定要给byResource资源添加流控

![image-20211108195609306](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1260d397-f40a-548e-9f47-516e4e76428f.png>)

具体规则

![image-20211108195638721](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7cc3fb9f-6063-5ff9-8345-8651d2ceb6ea.png>)

测试，测试我们去快速访问http://localhost:8401/byResource，就会出现我们使用@SentinelResource注解中blockHandler属性提供的解决限流异常的方法。

![image-20211108195926004](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/93c98a76-5f99-5925-9270-4ea9bcd218ed.png>)

### 1.16.3 @SentinelResource URL限流

**核心点：**使用@SentinelResource注解，但是不使用blockHandler属性，系统会调用默认限流异常处理方法。

其实这个注解，我们还可以更换请求地址为资源，比如我们在新建一个测试接口方法

```java
@GetMapping("/byRest")
@SentinelResource(value = "/byRest")
public String byRest(){
    return "-----byRest";
}
```

给这个接口地址添加流控

![image-20211108200724588](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7b7184ce-81a8-5770-a0b2-e752014e85c1.png>)

此时如果没有自己定义限流处理方法，会走系统默认的

![image-20211108200750521](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ebf2a07a-8ba7-5756-945e-eaac2b026bcf.png>)

### 1.16.4 结论

1\. @SentinelResource 既可以配置资源名称也可以配置URL
2\. 如果配置了@SentinelResource的blockHandler属性对应方法，出现限流会调用对应方法
3\. 如果没有配置@SentinelResource的blockHandler属性，系统会走默认的限流处理。



### 1.16.5 自定义限流处理逻辑

其实我们在使用@SentinelResource注解这两种方案的时候，会出现一些问题：

1\. 没有体现我们自己的业务要求。
2\. 自定义处理方法和业务代码耦合在一起。
3\. 每个业务方法都添加一个限流处理方法，代码将会加剧膨胀。
4\. 无法实现统一全局处理。 

解决：@SentinelResource除了blockHandler可以设置自定义限流处理逻辑方法以外，还提供另外一个属性来设置限流处理逻辑类型blockHandlerClass属性，此属性中设置的方法必需为 static 函数，否则无法解析。

#### 1.16.5.1 具体逻辑

**第一步**

创建CustomerBlockHandler类型用于处理自定义限流处理逻辑，首先创建myhandler.CustomerBlockHandler

```java
/**
 * 此类型用来处理限流自定义逻辑
 */
public class CustomerBlockHandler {
    public static String handlerException1(BlockException exception){
        return "handlerException1：系统异常，请稍后重试！";
    }
    public static String handlerException2(BlockException exception){
        return "handlerException2：网络崩溃了，请稍后重试！";
    }
}
```

**第二步**

我们在SentinelResourceTestController类型中添加一个接口方法，同时设置@SentinelResource注解和blockHandlerClass属性对应的类型和这个类型中对应的处理方法

```java
/**
* 此方法用到了自定义限流处理类型CustomerBlockHandler
* 中的handlerException1方法来处理限流逻辑。
*/
@GetMapping("/bycustomer")
@SentinelResource(value = "bycustomer",
                  blockHandlerClass = CustomerBlockHandler.class,
                  blockHandler = "handlerException1")
public String bycustomer(){
    return "-----bycustomer";
}
```

**第三步**

测试：给bycustomer资源添加限流规则，然后来测试在超过限流阈值时处理方法是否为CustomerBlockHandler中handlerException1来进行处理。

![image-20211108205553306](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/06bfe103-ccc8-5dd5-8126-d3ecb22e5d67.png>)

![image-20211108205606689](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fc043f87-a9f2-53ef-aa24-f934fccf8b78.png>)

添加流控规则以后，我们再来频繁访问http://localhost:8401/bycustomer，就会看见是CustomerBlockHandler类型的handlerException1方法来处理自定义限流逻辑

![image-20211108205735265](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7248a3ec-f0ab-5ead-9c4a-19b4ed68d008.png>)

#### 1.16.5.2 对应关系图

![image-20211108210142209](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/18f374a3-58a0-5aaf-8a47-961019c17302.png>)


---

## 1.17 Sentinel服务熔断环境搭建

服务熔断：应对微服务雪崩效应的一种链路保护机制，类似保险丝。

需要完成Sentinel整合Ribbon+openFeign，所以我们先要搭建环境，那么先从整合Ribbon开始

### 1.17.1 环境搭建

为了演示操作，所以在这里我们需要利用Ribbon进行负载均衡的调用，所以我们需要创建一个服务消费者cloudalibaba-consumer8084和两个服务提供者cloudalibaba-provider9003和cloudalibaba-provider9004，以下是结构图

![image-20211109170609356](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8ce28f03-f751-51b1-80e1-9f4b264791bc.png>)

其实我们之前就搭建过这种结构，比较简单，所以我们快速搭建

#### 1.17.1.1 新建cloudalibaba-provider-9003/9004

在建立9003和9004之前，先建立一个共享项目cloudalibaba-commons，在其中新建一个类型JsonResult，这个类型用于返回JSON数据类型

```java
package com.mashibing.cloudalibabacommons.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class JsonResult<T> {
    private Integer code;
    private T data;
}
```

创建服务提供者9003，9004基本上是一样的，所以我们建立9003复制就可以得到9004

**pom**

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.mashibing</groupId>
        <artifactId>SpringAlibabaMSB</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.mashibing</groupId>
    <artifactId>cloudalibaba-provider-9003</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>cloudalibaba-provider-9003</name>
    <description>cloudalibaba-provider-9003</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mashibing</groupId>
            <artifactId>cloudalibaba-commons</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

**yml**

```java
server:
  port: 9003

spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

**主启动添加注解**

```java
package com.mashibing.cloudalibabaprovider9003;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class CloudalibabaProvider9003Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaProvider9003Application.class, args);
    }

}
```

**控制器**

```java
package com.mashibing.cloudalibabaprovider9003.controller;

import com.mashibing.cloudalibabacommons.entity.JsonResult;
import lombok.extern.java.Log;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;

@RestController
public class DataController {
    @Value("${server.port}")
    private String serverPort;

    //模仿数据库存储数据
    public static HashMap<Long,String> hashMap = new HashMap<>();
    static {
        hashMap.put(1l,"鼠标");
        hashMap.put(2l,"键盘");
        hashMap.put(3l,"耳机");
    }

    @GetMapping("info/{id}")
    public JsonResult<String> msbSql(@PathVariable("id") Long id){
        JsonResult<String> result = new JsonResult(1L,200,hashMap.get(id));
        return result;
    }
}
```

**注意：**9004和9003 一致，但是要注意修改yml文件端口号

#### 1.17.1.2 新建cloudalibaba-consumer8084

**pom**

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.mashibing</groupId>
        <artifactId>SpringAlibabaMSB</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.mashibing</groupId>
    <artifactId>cloudalibaba-consumer8084</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>cloudalibaba-consumer8084</name>
    <description>cloudalibaba-consumer8084</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mashibing</groupId>
            <artifactId>cloudalibaba-commons</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

**yml**

```java
server:
  port: 8084
spring:
  application:
    name: nacos-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719

#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-provider
```

主启动添加注解和9003/9004一致

**控制器**

```java
package com.mashibing.cloudalibabaconsumer8084;

import com.mashibing.cloudalibabacommons.entity.JsonResult;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
@Slf4j
public class DemoController {
    //服务提供者URL
    @Value("${service-url.nacos-user-service}")
    private String SERVICE_URL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/fallback/{id}")
    public JsonResult<String> fallback(@PathVariable Long id){
        //通过Ribbon发起远程访问，访问9003/9004
        JsonResult<String> result = restTemplate.getForObject(SERVICE_URL+"/info/"+id,JsonResult.class);
    	return result;
    }
}
```

启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class CloudalibabaConsumer8084Application {


    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaConsumer8084Application.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate;
    }
}
```



### 1.17.2 最后测试

访问http://localhost:8084/consumer/fallback/1

查看最后结果是否为9003/9004切换调用


---

## 1.18 SentinelResource的fallback属性

### 1.18.1 fallback属性

**概念：**fallback 函数名称，可选项，用于在抛出异常的时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了 `exceptionsToIgnore` 里面排除掉的异常类型）进行处理。fallback 函数签名和位置要求：

1\. 返回值类型必须与原函数返回值类型一致；
2\. 方法参数列表需要和原函数一致，或者可以额外多一个 `Throwable` 类型的参数用于接收对应的异常。
3\. fallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `fallbackClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。

其实通过官网上提供的概念，我们不难看出这个属性类似于blockHandler，但是各位一定要注意他们有本质的不同。

**注意：**fallback属性和blockHandler属性的本质不同在于他们作用的异常不同：

1\. blockHandler：针对违反Sentinel控制台配置规则时触发BlockException异常时对应处理的属性
2\. fallback：针对Java本身出现的异常进行处理的对应属性。



### 1.18.2 案例演示

上节课我们已经完成环境的搭建，那我们就直接在8084项目的DemoController中编写对应代码

首先我们先来设置异常规则

```java
package com.mashibing.cloudalibabaconsumer8084;

import com.mashibing.cloudalibabacommons.entity.JsonResult;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
@Slf4j
public class DemoController {
    //服务提供者URL
    @Value("${service-url.nacos-user-service}")
    private String SERVICE_URL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/fallback/{id}")
    public JsonResult<String> fallback(@PathVariable Long id){
        if(id<=3){
            //通过Ribbon发起远程访问，访问9003/9004
            JsonResult<String> result = restTemplate.getForObject(SERVICE_URL+"/info/"+id,JsonResult.class);
            System.err.println(result.getData());
            return result;
        }else{
            throw new NullPointerException("没有对应的数据记录");
        }
    }
}
```

此时我们任务添加了异常，此时如果我们访问http://localhost:8084/consumer/fallback/4（id非法）地址时，就会出现对应的显示效果：

![image-20211111180018647](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8e617c21-4c18-5e70-98cf-8719a43490d1.png>)

明显此时显示效果非常不好，我们就可以通过@SentinelResource注解的fallback属性来解决这种java异常，给出友好提示

```java
@RestController
@Slf4j
public class DemoController {
    //服务提供者URL
    @Value("${service-url.nacos-user-service}")
    private String SERVICE_URL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/fallback/{id}")
    //添加SentinelResource注解的fallback属性，同时设置方法来解决Java异常
    @SentinelResource(value = "falllback",fallback = "fallbackHandler")
    public JsonResult<String> fallback(@PathVariable Long id){
        if(id<=3){
            //通过Ribbon发起远程访问，访问9003/9004
            JsonResult<String> result = restTemplate.getForObject(SERVICE_URL+"/info/"+id,JsonResult.class);
            System.err.println(result.getData());
            return result;
        }else{
            throw new NullPointerException("没有对应的数据记录");
        }
    }
    //保证方法签名基本保持一致，但是要添加异常类型参数
    public JsonResult<String> fallbackHandler(Long id,Throwable e){
        JsonResult<String> result = new JsonResult<>(444,"出现未知商品id");
        return result;
    }
}
```

到这里为止，我们就很清楚的知道了fallback属性的作用，同时它和blockHandler属性类似，也可以设置fallbackClass属性，来指定对应类型，来处理对应的Java异常，当然要注意和blockHandlerClass属性一样，也需要让所有的方法都必需为 static 函数，否则无法解析。

### 1.18.3 同时配置blockHandler和fallback属性

通过上述的内容，我们很清楚的知道了fallback属性的作用，但是大家现在想一个问题，如果我们在使用@SentinelResource属性的时候，同时设置blockHandler属性和fallback属性时，并且同时出现了Sentinel异常和Java异常，这个时候会执行哪个方法那。

我们还是回顾一下blockHandler属性的概念：

1\. `blockHandler` / `blockHandlerClass`: `blockHandler` 对应处理 `BlockException` 的函数名称，可选项。blockHandler 函数访问范围需要是 `public`，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 `BlockException`。blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `blockHandlerClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。



### 1.18.4 案例演示

我们现在同时在DemoController中设置fallback属性和blockHandler属性

```java
@RestController
@Slf4j
public class DemoController {
    //服务提供者URL
    @Value("${service-url.nacos-user-service}")
    private String SERVICE_URL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/fallback/{id}")
    //同时添加SentinelResource注解的fallback和blockHandler属性
    @SentinelResource(value = "falllback",fallback = "fallbackHandler",blockHandler = "blockHandler")
    public JsonResult<String> fallback(@PathVariable Long id){
        if(id<=3){
            //通过Ribbon发起远程访问，访问9003/9004
            JsonResult<String> result = restTemplate.getForObject(SERVICE_URL+"/info/"+id,JsonResult.class);
            System.err.println(result.getData());
            return result;
        }else{
            throw new NullPointerException("没有对应的数据记录");
        }
    }
    //处理Java异常
    public JsonResult<String> fallbackHandler(Long id,Throwable e){
        JsonResult<String> result = new JsonResult<>(444,"NullPointerException异常");
        return result;
    }

    //处理Sentinel限流
    public JsonResult<String> blockHandler(Long id, BlockException e){
        JsonResult<String> result = new JsonResult<>(445,"BlockException限流");
        return result;
    }
}
```

此时我们来设置Sentinel配置，我们通过熔断规则中的异常数来演示（当然也可以用其他的）

规则：在一秒内超过最小访问次数5次，并且异常数超过2的时候，就会触发熔断规则。

![image-20211111182629454](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a2ef9af3-396c-5347-8175-042d7c44fc3d.png>)

此时我们来访问http://localhost:8084/consumer/fallback/6看效果：

1\. 在没有触发熔断之前的异常交给fallback来处理

![image-20211111182834520](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ca9bf10a-b8a4-58a0-89ef-b35da5a97d5c.png>)

1\. 但是一旦触发熔断规则就变成了blockHandler来处理

![image-20211111183011670](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/de36eede-c6d7-5627-8b12-a77ff0de8abb.png>)

### 1.18.5 exceptionsToIgnore属性

1\. `exceptionsToIgnore`（since 1.6.0）：用于指定哪些异常被排除掉，不会计入异常统计中，也不会进入 fallback 逻辑中，而是会原样抛出。

```java
@RestController
@Slf4j
public class DemoController {
    //服务提供者URL
    @Value("${service-url.nacos-user-service}")
    private String SERVICE_URL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/fallback/{id}")
    //同时添加SentinelResource注解的fallback和blockHandler属性
    @SentinelResource(value = "falllback",fallback = "fallbackHandler",blockHandler = "blockHandler",
            exceptionsToIgnore = {NullPointerException.class})//被标注的异常将会被 原样抛出
    public JsonResult<String> fallback(@PathVariable Long id){
        if(id<=3){
            //通过Ribbon发起远程访问，访问9003/9004
            JsonResult<String> result = restTemplate.getForObject(SERVICE_URL+"/info/"+id,JsonResult.class);
            System.err.println(result.getData());
            return result;
        }else{
            throw new NullPointerException("没有对应的数据记录");
        }
    }
    //处理Java异常
    public JsonResult<String> fallbackHandler(Long id,Throwable e){
        JsonResult<String> result = new JsonResult<>(444,"NullPointerException异常");
        return result;
    }

    //处理Sentinel限流
    public JsonResult<String> blockHandler(Long id, BlockException e){
        JsonResult<String> result = new JsonResult<>(445,"BlockException限流");
        return result;
    }
}
```


---

## 1.19 OpenFeign基础应用

### 1.19.1 概念

OpenFeign是一种声明式、模板化的HTTP客户端。在Spring Cloud中使用OpenFeign，可以做到使用HTTP请求访问远程服务，就像调用本地方法一样的，开发者完全感知不到这是在调用远程方法，更感知不到在访问HTTP请求，用法其实就是编写一个接口，在接口上添加注解即可。

可以简单理解它是借鉴Ribbon的基础之上，封装的一套服务接口+注解的方式的远程调用器。

### 1.19.2 OpenFeign能干什么

它的宗旨是在编写Java Http客户端接口的时候变得更加容易，其底层整合了Ribbon，所以也支持负载均衡。

之前我们使用Ribbon的时候，利用RestTemplate对Http请求进行封装处理，但是在实际开发中，由于对服务依赖的调用不可能就一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以OpenFeign在此基础之上做了进一步的封装，由它来帮助我们定义和实现依赖服务接口的定义，我们只需创建一个接口并使用注解的方式来配置它，即可完成对微服务提供方的接口绑定，简化Ribbon的操作。

### 1.19.3 具体使用

 这里我们通过一个案例来演示，首先我们要明确使用OpenFeign是使用在消费者端去远程调用，就必须要是用FeignClient注解，来标注要调用的服务提供者名称，然后在通过一个接口来定义要调用的方法，所以我们首先新建一个Model：cloudalibaba-openFeign-consumer-8888



#### 1.19.3.1 pom

注意：需要在父级项目引入对应依赖坐标

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>${openfeign-version}</version>
</dependency>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.mashibing</groupId>
        <artifactId>SpringAlibabaMSB</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.mashibing</groupId>
    <artifactId>cloudalibaba-openFeign-consumer-8888</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>cloudalibaba-openFeign-consumer-8888</name>
    <description>cloudalibaba-openFeign-consumer-8888</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mashibing</groupId>
            <artifactId>cloudalibaba-commons</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

#### 1.19.3.2 YML配置

```java
server:
  port: 8888
spring:
  application:
    name: nacos-consumer-openFeign
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

#### 1.19.3.3 主启动中添加注解

```
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients//添加此注解
public class CloudalibabaOpenFeignConsumer8888Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaOpenFeignConsumer8888Application.class, args);
    }

}
```

#### 1.19.3.4 调用服务提供者对外提供接口

这里要调用的是服务提供者9003/9004

![image-20211112183106167](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3ec6e352-2108-5469-9e24-a2ed28e65ea1.png>)

```java
package com.mashibing.cloudalibabaopenFeignconsumer8888.service;

import com.mashibing.cloudalibabacommons.entity.JsonResult;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

/**
 * 此接口就是配合使用OpenFeign的接口，
 * 在此接口中添加@FeignClient接口同时标注
 * 要调用的服务端名称，同时使用与服务提供者
 * 方法签名一致的抽象方法来表示远程调用的
 * 具体内容
 */
@Service
//表示远程调用服务名称
@FeignClient("nacos-provider")
public interface openFeignService {
    /**
     * 此方法表示远程调用info/{id}接口
     */
    @GetMapping("info/{id}")
    public JsonResult<String> msbSql(@PathVariable("id") Long id);
}

```

#### 1.19.3.5 控制器

```java
package com.mashibing.cloudalibabaopenFeignconsumer8888.controller;

import com.mashibing.cloudalibabacommons.entity.JsonResult;
import com.mashibing.cloudalibabaopenFeignconsumer8888.service.OpenFeignService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class OpenFeignController {

    @Autowired
    private OpenFeignService openFeignService;

    @GetMapping("getInfo/{id}")
    public JsonResult<String> getInfo(@PathVariable("id") Long id){
        return openFeignService.msbSql(id);
    }

}
```

### 1.19.4 测试结果

能够远程调用的同时还有负载均衡效果

![image-20211112205138375](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1339ca38-91e5-56c2-93a8-6310d95cac3c.png>)


---

## 1.20 OpenFeign超时时间控制

### 1.20.1 概念

OpenFeign 客户端默认等待1秒钟，但是如果服务端业务超过1秒，则会报错。为了避免这样的情况，我们需要设置feign客户端的超时控制。

### 1.20.2 解决办法

由于OpenFeign 底层是ribbon 。所以超时控制由ribbon来控制。在yml文件中配置

### 1.20.3 超时案例演示

首先演示超时效果，我们现在9003/9004上设置一个延迟3秒执行的方法，来模仿长业务线调用。

```java
@GetMapping("/timeOut")
public String timeOut() {
    try {
        System.out.println("延迟响应");
        TimeUnit.SECONDS.sleep(3);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return serverPort;
}
```

客户端8888通过OpenFeign来进行调用

```java
//OpenFeginController
@GetMapping("/testTimeout")
    public String TestTimeout(){
        return openFeginService.timeOut();
    }
}
```

#### 1.20.3.1 测试结果

客户端报错：

![image-20211112195205208](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/37c2a363-acec-5a39-ad3e-8b12c0fe9877.png>)

![image-20211112195244240](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7597dbd5-5499-524a-97ee-9c126fa1b30c.png>)

### 1.20.4 设置超时控制案例演示

首先我们需要在8888消费者端的yml文件中配置超时时间，因为OpenFeign本身整合了Ribbon所以，这里其实我们用的是Ribbon来配置

#### 1.20.4.1 YML

```java
server:
  port: 8888
spring:
  application:
    name: nacos-consumer-openfegin
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

#### 1.20.4.2 测试结果

正常响应

![image-20211112195615566](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8aca37ad-cd37-5724-acab-bc852d051c94.png>)


---

## 1.21 OpenFeign日志打印

### 1.21.1 概念

Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。
简单理解，就是对Feign接口的调用情况进行监控和输出

**日志级别：**

1\. NONE：默认的，不显示任何日志；

2\. BASIC：仅记录请求方法、URL、响应状态码及执行时间；

3\. HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；

4\. FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。

### 1.21.2 具体使用

需要在启动类中通过@Bean注解注入OpenFeign的日志功能

```java
@SpringBootApplication
@EnableFeignClients
public class CloudalibabaOpenFeginConsumer8888Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaOpenFeginConsumer8888Application.class, args);
    }

    @Bean
    Logger.Level feignLoggerLevel(){
        //开启详细日志
        return Logger.Level.FULL;
    }
}

```

在yml中配置中配置

```java
server:
  port: 8888
spring:
  application:
    name: nacos-consumer-openfegin
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000

logging:
  level:
    # openfeign日志以什么级别监控哪个接口
    com.mashibing.cloudalibabaopenfeginconsumer8888.service.OpenFeginService: debug

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

测试效果，发起一次调用以后的日志内容：

![image-20211112201418950](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9f58d226-531a-5e62-a5ce-9e4ec2f85207.png>)


---

## 1.22 Sentinel整合OpenFegin

根据之前的学习，我们已经学习过了包括Sentinel整合Ribbon，包括对OpenFegin的基本学习，那么这节课，我们就需要通过Sentinel来进行整合OpenFegin

### 1.22.1 引入OpenFegin

我们需要在当前的8084项目中引入对应的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

激活Sentinel对OpenFeign的支持，所以配置yml

```yaml
# 激活Sentinel对OpenFeign的支持
feign:
  sentinel:
    enabled: true
```

主启动类要添加@EnableFeignClients注解

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients//注入Feign
public class CloudalibabaConsumer8084Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaConsumer8084Application.class, args);
    }
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

### 1.22.2 OpenFegin接口编写

这里我们的接口写法和之前保持一致，但是要注意，我们这里要多增加一个FeignClient的属性：

1\. fallback: 定义容错的处理类，当调用远程接口失败或超时时，会调用对应接口的容错逻辑，fallback指定的类必须实现@FeignClient标记的接口

```java
//当没有成功调用/info/{id}接口时会走fallback属性标注的类型的处理方法
// 这里的@Service不需要
@Service
@FeignClient(value = "nacos-provider",fallback = FeignServiceImpl.class)
public interface FeignService {
    /**
     * 远程调用对应方法
     */
    @GetMapping("info/{id}")
    public JsonResult<String> msbSql(@PathVariable("id") Long id);
}
```

实现类必须添加@Component注解，否则无法注入到容器中

```java
@Component
public class FeignServiceImpl implements FeignService{
    @Override
    public JsonResult<String> msbSql(Long id) {
        return new JsonResult<>(444,"服务降级返回！");
    }
}
```

这里完成后我们来编写控制器

```java
@Autowired
private FeignService feignService;

@GetMapping("getInfo/{id}")
public JsonResult<String> getInfo(@PathVariable("id") Long id){
    if(id > 3){
        throw new RuntimeException("没有该id");
    }
    return feignService.msbSql(id);
}
```

### 1.22.3 测试

此时如果我们访问http://localhost:8084/getInfo/1的地址，是没有问题的，但是如果此时我们人为结束9003/9004服务，这个时候就会触发fallback属性对应的处理类型，完成服务降级。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/042fbccb-4682-5d4e-a9b1-be2011bf8040.png" alt="image-20211115200122605" style="zoom:33%;" />

断开服务以后

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/02c57cfc-d4ed-5852-b670-e2cfddd38c55.png" alt="image-20211115200243827" style="zoom:33%;" />


---

## 1.23 Sentinel持久化配置

 我们首先需要知道：在Sentinel Dashboard中配置规则之后重启应用就会丢失，所以实际生产环境中需要配置规则的持久化实现，Sentinel提供多种不同的数据源来持久化规则配置，包括file，redis、nacos、zk。



### 1.23.1 Sentinel规则持久化到Nacos

将限流规则持久化进Nacos保存，只要刷新8401某个接口地址，Sentinel控制台的流控规则就能感应到，同时只要Nacos里面的配置不删除，针对8401上Sentinel的流控规则就持续有效。

![image-20211115204021997](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/789a25ea-092c-5892-a4a5-f9b6f4527b74.png)

其实就是实现Sentinel Dashboard与Nacos之间的相互通信

通过Nacos配置文件修改流控规则---拉取--->Sentinel Dashboard界面显示最新的流控规则

**注意：**在Nacos控制台上修改流控制，虽然可以同步到Sentinel Dashboard，但是Nacos此时应该作为一个流控规则的持久化平台，所以正常操作过程应该是开发者在Sentinel Dashboard上修改流控规则后同步到Nacos，遗憾的是目前Sentinel Dashboard不支持该功能。

### 1.23.2 具体操作

第一件事情我们首先要引入依赖：

```java
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
    <version>1.8.1</version>
</dependency>
```

第二件事情我们需要配置YML

```java
# 端口号
server:
  port: 8890
# 服务名
spring:
  application:
    name: order
  cloud:
    nacos:
      discovery:
        # nacos注册中心地址
        server-addr: localhost:8848
    sentinel:
      transport:
        dashboard: localhost:8080
      datasource: # 配置Sentinel的持久化
        nacos:
          nacos:
            serverAddr: localhost:8848
            groupId: DEFAULT_GROUP
            dataId: order-sentinel.json
            ruleType: flow
  profiles:
    active: dev


```

第三步我们需要进入到Nacos控制台，添加配置

![image-20211116200354133](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/06eb629d-a98a-5cc9-8f90-5324db1e0abc.png>)

具体配置内容：

```java
[   
    {
        "resource": "test1",
        "limitApp": "default",
        "grade": 1,
        "count": 2,
        "strategy": 0,
        "controlBehavior": 0
        "clusterMode": false
    }
]
---------------具体内容含义-----------------
resource：资源名称；
limitApp：来源应用；
grade：阈值类型，0表示线程数，1表示QPS；
count：单机阈值；
strategy：流控模式，0表示直接，1表示关联，2表示链路；
controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；
clusterMode：是否集群。
```

控制器

```java
@RestController
public class OrderController {
    @GetMapping("/order/test1")
    @SentinelResource(value = "test1")
    public String test1() throws InterruptedException {
        return "test1 ";
    }
}
```



### 1.23.3 测试

当我们重启项目以后，我们访问对应接口http://localhost:8890/order/test1，就会在Sentinel界面上看到对应的限流规则：

![image-20211116200654500](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e80c7609-7afe-5fa1-b887-6b923241803b.png>)


# 2 Sentinel源码分析

## 2.1 Sentinel核心源码分析-课程开篇

Sentinel是分布式系统的防御系统。以流量为切入点，通过动态设置的流量控制、服务熔断等手段达到 保护系统的目的，通过服务降级增强服务被拒后用户的体验。



### 2.1.1 Sentinel工作原理

**ProcessorSlotChain**

Sentinel 的核心骨架，将不同的 Slot 按照顺序串在一起（责任链模式），从而将不同的功能（限流、降级、系统保护）组合在一起。slot chain 其实可以分为两部分：统计数据构建部分（statistic）和判断部分（rule checking）。核心结构

![arch overview](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ef1f4647-0942-5767-9ad4-cac87109ccce.png>)

Sentinel 将 `ProcessorSlot` 作为 SPI 接口进行扩展，使得 Slot Chain 具备了扩展的能力。您可以自行加入自定义的 slot 并编排 slot 间的顺序，从而可以给 Sentinel 添加自定义的功能。

![Slot Chain SPI](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/48c40cbd-91f2-55f0-aa6e-63d4b7eae5d9.png>)



### 2.1.2 官方版本说明

通过官方网站我们可以看到现在应该使用的Sentinel版本，通过官方的要求我们下载对应版本的Sentinel源码：https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E

![image-20211116203605431](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/62f89f8e-5d1b-5898-b539-c32d1acceca5.png>)

### 2.1.3 Sentinel源码下载

下载地址：https://github.com/alibaba/Sentinel/tree/release-1.8

下载方式，我们可以下载zip包，也可以直接通过idea进行下载和打开

![image-20211116203821710](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d37146ee-1013-572f-ace9-8079c521973f.png>)

![image-20211116203908000](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/184ff249-9df8-5f8f-b59c-22bae94d17d3.png>)


---

## 2.2 Sentinel核心概念

Sentinel作为ali开源的一款轻量级流控框架，**主要以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度来帮助用户保护服务的稳定性**。相比于Hystrix，Sentinel的设计更加简单，在 Sentinel中资源定义和规则配置是分离的，也就是说用户可以先通过Sentinel API给对应的业务逻辑定义资源（埋点），然后在需要的时候再配置规则，通过这种组合方式，极大的增加了Sentinel流控的灵活性。

引入Sentinel带来的性能损耗非常小。只有在业务单机量级超过25W QPS的时候才会有一些显著的影响（5% - 10% 左右），单机QPS不太大的时候损耗几乎可以忽略不计。

Sentinel提供两种埋点方式：

1\. try-catch 方式（通过 SphU.entry(...)），用户在 catch 块中执行异常处理 

2\. if-else 方式（通过 SphO.entry(...)），当返回 false 时执行异常处理 

### 2.2.1 工作流程

在此之前，需要先了解一下Sentinel的工作流程

![sentinel-slot-chain-architecture](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/adccdb34-593c-5a89-b79b-e08eaad0b7a7.png>)

在 Sentinel里面，所有的资源都对应一个资源名称（resourceName），每次资源调用都会创建一个 Entry 对象。Entry 可以通过对主流框架的适配自动创建，也可以通过注解的方式或调用 SphU API 显式创建。Entry 创建的时候，同时也会创建一系列功能插槽（slot chain），这些插槽有不同的职责，例如默认情况下会创建一下7个插槽：

1\. NodeSelectorSlot 负责收集资源的路径，并将这些资源的调用路径，以树状结构存储起来，用于根据调用路径来限流降级；
2\. ClusterBuilderSlot 则用于存储资源的统计信息以及调用者信息，例如该资源的 RT, QPS, thread count 等等，这些信息将用作为多维度限流，降级的依据，对应簇点链路；
3\. StatisticSlot 则用于记录、统计不同纬度的 runtime 指标监控信息；
4\. FlowSlot 则用于根据预设的限流规则以及前面 slot 统计的状态，来进行流量控制，对应流控规则；
5\. AuthoritySlot 则根据配置的黑白名单和调用来源信息，来做黑白名单控制，对应授权规则；
6\. DegradeSlot 则通过统计信息以及预设的规则，来做熔断降级，对应熔断规则；
7\. SystemSlot 则通过系统的状态，例如 load1 等，来控制总的入口流量，对应系统规则；

重要的概念：

1\. slot chain：插槽
2\. Node：根节点
3\. Context：对资源操作时的上下文环境，每个资源操作(`针对Resource进行的entry/exit`)必须属于一个Context，如果程序中未指定Context，会创建name为"sentinel_default_context"的默认Context。一个Context生命周期内可能有多个资源操作，Context生命周期内的最后一个资源exit时会清理该Context，这也预示这真个Context生命周期的结束。
4\. Entry：表示一次资源操作，内部会保存当前调用信息。在一个Context生命周期中多次资源操作，也就是对应多个Entry，这些Entry形成parent/child结构保存在Entry实例中



### 2.2.2 官方案例演示

我们先从官方文档提供的演示代码来进行分析

![image-20211118223922490](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f794fccb-4e7d-5cd4-b4b0-dd93e00340bb.png>)

我们来改写一下，如果只有一个资源情况如下

```java
package demo;

import com.alibaba.csp.sentinel.Entry;
import com.alibaba.csp.sentinel.SphU;
import com.alibaba.csp.sentinel.context.ContextUtil;
import com.alibaba.csp.sentinel.slots.block.BlockException;

public class ContextDemo {
    public void ContextUtil(){
        //创建一个来自appA访问的Context
        //Context的名称为entrance1
        ContextUtil.enter("entrance1", "appA");
        // Entry就是一个资源操作对象
        Entry nodeA = null;
        try {
            //获取资源resource的entry
            nodeA = SphU.entry("resource1");//后续会展开这个位置
            // 如果代码走到这个位置，说明当前资源的请求通过了流控，可以继续进行相关业务处理
        } catch (BlockException e) {
            // 如果没有通过走到了这里，就表示请求被限流，这里进行降级操作
            e.printStackTrace();
        }finally {
            if (nodeA != null) {
                nodeA.exit();
            }
        }
        //释放Context
        ContextUtil.exit();
    }
}
```

多个资源的情况

```java
public class ContextDemo {
    public void ContextUtil(){
        //创建一个来自appA访问的Context
        //Context的名称为entrance1
        ContextUtil.enter("entrance1", "appA");
        // Entry就是一个资源操作对象
        Entry nodeA = null;
        Entry nodeB = null;
        try {
            //获取资源resource1的entry
            nodeA = SphU.entry("resource1");
            // 如果代码走到这个位置，说明当前资源的请求通过了流控，可以继续进行相关业务处理

            //获取资源resource2的entry
            nodeB = SphU.entry("resource2");
            // 如果代码走到这个位置，说明当前资源的请求通过了流控，可以继续进行相关业务处理
        } catch (BlockException e) {
            // 如果没有通过走到了这里，就表示请求被限流，这里进行降级操作
            e.printStackTrace();
        }finally {
            if (nodeA != null) {
                nodeA.exit();
            }
            if (nodeB != null) {
                nodeB.exit();
            }
        }
        //释放Context
        ContextUtil.exit();
    }
}
```


---

## 2.3 Sentinel源码解析-Node之间的关系

在上节课我们通过官方演示代码，搞清楚一个Context之中，Resource和Entry的关系，一个Context中可以有多个Resource，每个资源都必须都应一个Entry，这节课我们来研究一下Node之间的关系



### 2.3.1 Node 关系官方图解

这张图的上班部分其实体现了Node之间的关系，官方提供的图不够明显，我们需要进行细化

![sentinel-slot-chain-architecture](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f665115e-7f33-5312-bae3-04df5abca5cc.png>)

要先理解这个官方提供的图，我们需要看一下源码的关系

![image-20211119175902973](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6a465516-9527-5244-8fdc-1e9cb17b1710.png>)

从这张图片我们能够提取出几个Node：

1\. Node：接口，Sentinel 里面的各种种类的统计节点
2\. StatisticNode：统计节点，是Node的实现类，用于完成数据统计
3\. EntranceNode：DefaultNode的子类，入口节点，一个Context会有一个入口节点，用于统计当前Context的总体流量数据，统计维度为Context
4\. DefaultNode：默认节点，用于统计一个resource在当前Context中的流量数据，DefaultNode持有指定的Context和指定的Resource的统计数据，意味着DefaultNode是以Context和Resource为维度的统计节点
5\. ClusterNode：ClusterNode保存的是同一个Resource的相关的统计信息，是以Resource为维度的，不区分Context，这个是和DefaultNode的区别

### 2.3.2 Node之间的关系

Node 接口定义了一个 Node 类所需要提供的各项指标数据统计的相关功能，为外部屏蔽滑动窗口的存在。提供记录请求被拒绝、请求被放行、请求处理异常、请求处理成功的方法，以及获取当前时间窗口统计的请求总数、平均耗时等方法。

![image-20211123135428060](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/412145c8-70ee-5cdd-b58d-5fdd5415b0a8.png>)

### 2.3.3 案例代码改进

昨天我们通过官方提供的演示案例写一个演示代码，那么我们为了理解这个Node之间的关系，我们再来增加一些代码，为了理解Node之间的关系

```java
//增加一个Context
package demo;

import com.alibaba.csp.sentinel.Entry;
import com.alibaba.csp.sentinel.Sph;
import com.alibaba.csp.sentinel.SphU;
import com.alibaba.csp.sentinel.context.ContextUtil;
import com.alibaba.csp.sentinel.slots.block.BlockException;

public class ContextDemo {
    public void ContextUtil(){
        // 创建一个来自appA访问的Context
        // Context的名称为entrance1
        ContextUtil.enter("entrance1", "appA");
        // Entry就是一个资源操作对象
        Entry nodeA = null;
        Entry nodeB = null;
        try {
            // 获取资源resource的entry
            nodeA = SphU.entry("resource1");//后续会展开这个位置
            // 如果代码走到这个位置，就说明当前请求通过了流控，可以继续记性相关业务处理
            nodeB = SphU.entry("resource2");
            // 如果代码走到这个位置，就说明当前请求通过了流控，可以继续记性相关业务处理
        } catch (BlockException e) {
            // 如果没有通过走到了这里，就表示请求被限流，这里进行降级操作
            e.printStackTrace();
        } finally {
            if (nodeA != null) {
                nodeA.exit();
            }
            if (nodeB != null) {
                nodeB.exit();
            }
        }
        // 释放Context
        ContextUtil.exit();

       // ---------------------创建另一个来自appA访问的Context------------------------------

        // 创建一个来自appA访问的Context
        // Context的名称为entrance1
        ContextUtil.enter("entrance2", "appA");
        // Entry就是一个资源操作对象
        Entry nodeC = null;
        try {
            nodeB = SphU.entry("resource2");
            // 如果代码走到这个位置，就说明当前请求通过了流控，可以继续记性相关业务处理
            nodeC = SphU.entry("resource3");
        } catch (BlockException e) {
            // 如果没有通过走到了这里，就表示请求被限流，这里进行降级操作
            e.printStackTrace();
        } finally {
            if (nodeB != null) {
                nodeB.exit();
            }
            if (nodeC != null) {
                nodeC.exit();
            }
        }
        // 释放Context
        ContextUtil.exit();
    }
}
```


---

## 2.4 Sentinel源码解析-源码入口

### 2.4.1 分析入口

在微服务的使用Sentinel实际工作场景中，我们只需要引入对应依赖：spring-cloud-starter-alibaba-sentinel，就会进行自动装配，所以我们之间看META-INF/spring.factories，然后我们这里从SentinelAutoConfiguration开始看起

![image-20211123175158774](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4706cafe-c2aa-55da-82c6-727bfd8eabb7.png>)

为了我们方便跟踪，我们在找到这个类型以后，直接通过源码进行查看，然后在这个类型中我们要找到以下代码，之前我们就说过Sentinel是通过AOP的方式进行切入的，从这里我们看到了Aspect关键字，所以我们就从这里跟进去

```java
@Bean
@ConditionalOnMissingBean
public SentinelResourceAspect sentinelResourceAspect() {
    return new SentinelResourceAspect();
}
```

跟进去以后，我们就会发现这里就是再利用AOP通过@SentinelResource为注解来作为切入点，进行切入

```java
@Aspect //切面
public class SentinelResourceAspect extends AbstractSentinelAspectSupport {

    //指定切入点为SentinelResource注解
    @Pointcut("@annotation(com.alibaba.csp.sentinel.annotation.SentinelResource)")
    public void sentinelResourceAnnotationPointcut() {
    }
...
}
```

再往下跟我们会发现，还有@Around注解进行环绕通知，根据我们之前学习的知道我们从这里可以得知，其实这就是是利用@SentinelResource注解作为切点，然后在通过AOP环绕通知，来进行增强，在执行原方法前，来执行对应操作，当然这里我们可以看出，一旦出现了限流或者限流就会走BlockException。

```java
// 环绕通知
@Around("sentinelResourceAnnotationPointcut()")
public Object invokeResourceWithSentinel(ProceedingJoinPoint pjp) throws Throwable {
    Method originMethod = resolveMethod(pjp);

    SentinelResource annotation = originMethod.getAnnotation(SentinelResource.class);
    if (annotation == null) {
        // Should not go through here.
        throw new IllegalStateException("Wrong state for SentinelResource annotation");
    }
    String resourceName = getResourceName(annotation.value(), originMethod);
    EntryType entryType = annotation.entryType();
    int resourceType = annotation.resourceType();
    Entry entry = null;
    try {
        // 创建资源操作对象
        entry = SphU.entry(resourceName, resourceType, entryType, pjp.getArgs());
        // 调用原方法
        return pjp.proceed();
    } catch (BlockException ex) {
        return handleBlockException(pjp, annotation, ex);
    } catch (Throwable ex) {
        Class<? extends Throwable>[] exceptionsToIgnore = annotation.exceptionsToIgnore();
        // The ignore list will be checked first.
        if (exceptionsToIgnore.length > 0 && exceptionBelongsTo(ex, exceptionsToIgnore)) {
            throw ex;
        }
        if (exceptionBelongsTo(ex, annotation.exceptionsToTrace())) {
            traceException(ex);
            return handleFallback(pjp, annotation, ex);
        }

        // No fallback function can handle the exception, so throw it out.
        throw ex;
    } finally {
        if (entry != null) {
            entry.exit(1, pjp.getArgs());
        }
    }
}
```

在以上的这段代码中还有一个问题，我们在之前就说过，在创建资源操作对象的时候我们需要先创建Context，但是明显这里没有显示创建，但是实际上我们如果看Context概念的话，就会知道，如果程序中未指定Context，会创建name为"sentinel_default_context"的默认Context，然后我们继续往下跟踪。

```java
public static Entry entry(String name, int resourceType, EntryType trafficType, Object[] args)
    throws BlockException {
    //限流方法
    return Env.sph.entryWithType(name, resourceType, trafficType, 1, args);
}
```

进入到entry方法中，这里的entryWithType方法就是我们要看的真正的限流的方法，具体的实现方法在**com.alibaba.csp.sentinel.CtSph.entryWithType**

```java
@Override
public Entry entryWithType(String name, int resourceType, EntryType entryType, int count, Object[] args)
    throws BlockException {
    return entryWithType(name, resourceType, entryType, count, false, args);
}

@Override
public Entry entryWithType(String name, int resourceType, EntryType entryType, int count, boolean prioritized,
                           Object[] args) throws BlockException {
    // 这里将资源的名称和信息封装称为资源对象
    StringResourceWrapper resource = new StringResourceWrapper(name, entryType, resourceType);
    // 返回一个Entry资源操作对象
    // prioritized属性表示优先级，默认值为false，表示当前请求不按照优先级执行，直接执行
    return entryWithPriority(resource, count, prioritized, args);
}
```

我们接下来来具体分析核心方法entryWithPriority

```java
private Entry entryWithPriority(ResourceWrapper resourceWrapper, int count, boolean prioritized, Object... args)
    throws BlockException {
    // 从当前线程中获取Context
    // 一个请求会占用一个线程，并且绑定一个Context
    Context context = ContextUtil.getContext();
    // 一个请求对应一个Context
   	// 如果当前类型为NullContext，表示此时请求已经超出了阈值，无需检测规则
    if (context instanceof NullContext) {
        // The {@link NullContext} indicates that the amount of context has exceeded the threshold,
        // so here init the entry only. No rule checking will be done.
        return new CtEntry(resourceWrapper, null, context);
    }
	
    // 此时如果获取Context为空，就创建默认的sentinel_default_context，并且会放入到当前线程中
    if (context == null) {
        // Using default context.
        context = InternalContextUtil.internalEnter(Constants.CONTEXT_DEFAULT_NAME);
    }

    // 判断全局开关，如果是关闭状态，直接返回无需检测规则
    // Global switch is close, no rule checking will do.
    if (!Constants.ON) {
        return new CtEntry(resourceWrapper, null, context);
    }
    /**
     * 这里是整个架构的核心所在，这里是在构建一个处理链，这个处理链是一个单向链表结构，类似于Filter一样，构建这个链条的
     * 原因是对业务进行解耦，像限流资源保护有很多，比如限流、降级、热点参数、系统降级等等，如果都写在一起就耦合很严重，我们知道oop的
     * 思想就是让每个类确定各自的职责，不要让他做不相干的事情，所以这里将业务进行全面解耦，然后在解耦的同时又通过链式编程将它们串起来
     */
    ProcessorSlot<Object> chain = lookProcessChain(resourceWrapper);

    /*
         * Means amount of resources (slot chain) exceeds {@link Constants.MAX_SLOT_CHAIN_SIZE},
         * so no rule checking will be done.
         */
    if (chain == null) {
        return new CtEntry(resourceWrapper, null, context);
    }

    Entry e = new CtEntry(resourceWrapper, chain, context);
    try {
        // 针对资源操作
        chain.entry(context, resourceWrapper, null, count, prioritized, args);
    } catch (BlockException e1) {
        e.exit(count, args);
        throw e1;
    } catch (Throwable e1) {
        // This should not happen, unless there are errors existing in Sentinel internal.
        RecordLog.info("Sentinel unexpected exception", e1);
    }
    return e;
}
```


---

## 2.5 Sentinel源码解析-构建Context

我们继续分析当前这个类型中的InternalContextUtil.internalEnter(Constants.CONTEXT_DEFAULT_NAME);方法

```java
/**
* This class is used for skip context name checking.
此类型是用于跳过Context名称的检测
*/
private final static class InternalContextUtil extends ContextUtil {
    static Context internalEnter(String name) {
        // 从这里继续跟踪
        return trueEnter(name, "");
    }

    static Context internalEnter(String name, String origin) {
        return trueEnter(name, origin);
    }
}
```

首先这里要明确一下，一个Context的组成实际上需要name(名称)和origin(来源)，所以方法上传入这两个参数

```java
protected static Context trueEnter(String name, String origin) {
    // 从当前线程中获取当前context名称
    Context context = contextHolder.get();
    // 如果当前context为空
    if (context == null) {
        // 从缓存中获取，当前缓存中key值为：Context名称，value值为：EntranceNode
        // （因为后续创建的是EntranceNode），需要它的原因是因为构建Context需要EntranceNode
        Map<String, DefaultNode> localCacheNameMap = contextNameNodeMap;
        // 在缓存中获取EntranceNode
        DefaultNode node = localCacheNameMap.get(name);
        // 如果node为空
        if (node == null) {
            // 当前缓存的size>Context的最大数量，返回NULL_Context类型
            if (localCacheNameMap.size() > Constants.MAX_CONTEXT_NAME_SIZE) {
                setNullContext();
                return NULL_CONTEXT;
            } else {
                // 加锁
                LOCK.lock();
                try {
                    node = contextNameNodeMap.get(name);
                    // 这里两次判断是采用了双重检测锁的机制：为了防止并发创建
                    if (node == null) {
                        if (contextNameNodeMap.size() > Constants.MAX_CONTEXT_NAME_SIZE) {
                            setNullContext();
                            return NULL_CONTEXT;
                        } else {
                            // node赋值为EntranceNode
                            node = new EntranceNode(new StringResourceWrapper(name, EntryType.IN), null);
                            // Add entrance node.
                            // 将新建的EntranceNode添加到ROOT中
                            Constants.ROOT.addChild(node);
                            // 将新建的EntranceNode添加到缓存中
                            Map<String, DefaultNode> newMap = new HashMap<>(contextNameNodeMap.size() + 1);
                            newMap.putAll(contextNameNodeMap);
                            newMap.put(name, node);
                            contextNameNodeMap = newMap;
                        }
                    }
                } finally {
                    LOCK.unlock();
                }
            }
        }
        // 将name和node封装成Context
        context = new Context(node, name);
        // 设定来源
        context.setOrigin(origin);
        // 将context写入到当前线程中
        contextHolder.set(context);
    }
	// 返回Context
    return context;
}
```


---

## 2.6 Sentinel源码解析-SlotChain入口解析

![arch overview](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ce900d57-3786-5e36-92f0-33efbf76e57a.png>)

### 2.6.1 默认Chain解析

​	我们从这里继续分析，这个位置的chain.entry方法，但是此时这个chain是谁？

```java
//CtSph中
try {
    // 针对资源操作
    chain.entry(context, resourceWrapper, null, count, prioritized, args);
} catch (BlockException e1) {
    e.exit(count, args);
    throw e1;
} catch (Throwable e1) {
    // This should not happen, unless there are errors existing in Sentinel internal.
    RecordLog.info("Sentinel unexpected exception", e1);
}
```

那么这个位置开始，我们就要分析一下如下代码

官方定义：Sentinel 将 ProcessorSlot作为 SPI 接口进行扩展（1.7.2 版本以前 SlotChainBuilder作为 SPI），使得 Slot Chain 具备了扩展的能力。您可以自行加入自定义的 slot 并编排 slot 间的顺序，从而可以给 Sentinel 添加自定义的功能。

lookProcessChain()用于构建一个责任链。Sentinel的处理核心都在这个责任链中，链中每一个节点是一个Slot实例，这个链通过BlockException异常来告知调用入口最终的执行情况

```java
// 获取chain链
ProcessorSlot<Object> chain = lookProcessChain(resourceWrapper);
//-------------------具体看lookProcessChain方法---------------------
ProcessorSlot<Object> lookProcessChain(ResourceWrapper resourceWrapper) {
    // 先从chainMap获取，若是存在，则直接返回
    ProcessorSlotChain chain = chainMap.get(resourceWrapper);
    if (chain == null) {
        synchronized (LOCK) {
            chain = chainMap.get(resourceWrapper);
            if (chain == null) {
                // Entry size limit.
                if (chainMap.size() >= Constants.MAX_SLOT_CHAIN_SIZE) {
                    return null;
                }
				// 通过SlotChainProvider创建一个slot链
                chain = SlotChainProvider.newSlotChain();
                Map<ResourceWrapper, ProcessorSlotChain> newMap = new HashMap<ResourceWrapper, ProcessorSlotChain>(
                    chainMap.size() + 1);
                newMap.putAll(chainMap);
                // 添加到Map缓存中
                newMap.put(resourceWrapper, chain);
                chainMap = newMap;
            }
        }
    }
    return chain;
}
```

这个位置我们要具体分析SlotChainProvider这个类型，它的主要作用就是通过已解析的槽链构建器，创建槽链的提供者

从这里我们可以看出SlotChainBuilder及ProcessorSlot 使用Java SPI技术实现可配置化，即在/META-INF/services/接口全限命名 的文件中配置实现类，然后由ServiceLoader实现加载，这种技术在SpringBoot中都有使用。不作讲解。

```java
package com.alibaba.csp.sentinel.slotchain;

import com.alibaba.csp.sentinel.log.RecordLog;
import com.alibaba.csp.sentinel.slots.DefaultSlotChainBuilder;
import com.alibaba.csp.sentinel.spi.SpiLoader;

/**
 * A provider for creating slot chains via resolved slot chain builder SPI.
 *
 * @author Eric Zhao
 * @since 0.2.0
 */
public final class SlotChainProvider {

    private static volatile SlotChainBuilder slotChainBuilder = null;

    /**
     * The load and pick process is not thread-safe, but it's okay since the method should be only invoked
     * via {@code lookProcessChain} in {@link com.alibaba.csp.sentinel.CtSph} under lock.
     *
     * @return new created slot chain
     */
    public static ProcessorSlotChain newSlotChain() {
        if (slotChainBuilder != null) {
            return slotChainBuilder.build();
        }

        // 读取配置文件在/META-INF/services/接口全限定命名的文件中配置实现类.
        slotChainBuilder = SpiLoader.of(SlotChainBuilder.class).loadFirstInstanceOrDefault();

        if (slotChainBuilder == null) {
            // Should not go through here.
            RecordLog.warn("[SlotChainProvider] Wrong state when resolving slot chain builder, using default");
            //使用默认的DefaultSlotChainBuilder来构建ProcessorSlotChain
            slotChainBuilder = new DefaultSlotChainBuilder();
        } else {
            RecordLog.info("[SlotChainProvider] Global slot chain builder resolved: {}",
                slotChainBuilder.getClass().getCanonicalName());
        }
        //使用DefaultSlotChainBuilder构建
        return slotChainBuilder.build();
    }

    private SlotChainProvider() {}
}
```

到这里我们就清楚的知道了其实现在使用的是使用DefaultSlotChainBuilder.build()来创建的，以下是源码

在这个其中，做了几件事：

	1\. 创建DefaultProcessorSlotChain
	2\. 读取/META-INF/services/中的配置文件
	3\. 强制转型为AbstractLinkedProcessorSlot（所有插槽的抽象父类）

```java
@Spi(isDefault = true)
public class DefaultSlotChainBuilder implements SlotChainBuilder {

    @Override
    public ProcessorSlotChain build() {
        // 创建DefaultProcessorSlotChain
        ProcessorSlotChain chain = new DefaultProcessorSlotChain();
		// 读取配置文件在/META-INF/services/接口全限定命名的文件
        List<ProcessorSlot> sortedSlotList = SpiLoader.of(ProcessorSlot.class).loadInstanceListSorted();
        for (ProcessorSlot slot : sortedSlotList) {
            if (!(slot instanceof AbstractLinkedProcessorSlot)) {
                RecordLog.warn("The ProcessorSlot(" + slot.getClass().getCanonicalName() + ") is not an instance of AbstractLinkedProcessorSlot, can't be added into ProcessorSlotChain");
                continue;
            }

            chain.addLast((AbstractLinkedProcessorSlot<?>) slot);
        }

        return chain;
    }
}
```

具体读取的内容如下：

```java
# Sentinel default ProcessorSlots 
com.alibaba.csp.sentinel.slots.nodeselector.NodeSelectorSlot
com.alibaba.csp.sentinel.slots.clusterbuilder.ClusterBuilderSlot
com.alibaba.csp.sentinel.slots.logger.LogSlot
com.alibaba.csp.sentinel.slots.statistic.StatisticSlot
com.alibaba.csp.sentinel.slots.block.authority.AuthoritySlot
com.alibaba.csp.sentinel.slots.system.SystemSlot
com.alibaba.csp.sentinel.slots.block.flow.FlowSlot
com.alibaba.csp.sentinel.slots.block.degrade.DegradeSlot
```

这些就是Sentinel提供的默认功能插槽

![image-20211209165106934](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5d6405f0-951b-5f0c-95bc-58014922b6c7.png>)

### 2.6.2 NodeSelectorSlot解析

分析到这里我们就可以回到CtSph中，查看entry方法这个时候我们就知道了实际上调用entry方法的是DefaultProcessorSlotChain

```java
 try {
     // 针对资源操作
     chain.entry(context, resourceWrapper, null, count, prioritized, args);
 } catch (BlockException e1) {
     e.exit(count, args);
     throw e1;
 } catch (Throwable e1) {
     // This should not happen, unless there are errors existing in Sentinel internal.
     RecordLog.info("Sentinel unexpected exception", e1);
 }
```

那我们向下跟踪

```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, Object t, int count, boolean prioritized, Object... args)
    throws Throwable {
    // 转到下一个节点
    first.transformEntry(context, resourceWrapper, t, count, prioritized, args);
}
```

这个位置是转入到下一个节点，那么下一个节点明显就是NodeSelectorSlot，那我们来看具体操作

```java
void transformEntry(Context context, ResourceWrapper resourceWrapper, Object o, int count, boolean prioritized, Object... args)
    throws Throwable {
    T t = (T)o;
    //NodeSelectorSlot
    entry(context, resourceWrapper, t, count, prioritized, args);
}
```

NodeSelectorSlot具体内容官网有给出解释：

这个 slot 主要负责收集资源的路径，并将这些资源的调用路径，以树状结构存储起来，用于根据调用路径来限流降级。

DefaultNode用于统计一个resource在当前Context中的流量数据，所以再结合NodeSelectorSlot，最终得出结论：处理不同的Context name，同一个Resource name的情况

![image-20211209171805499](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d92bd220-42d9-5a05-9879-d1a43ab3218a.png>)

![image-20211123135428060](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bad61676-d960-5f0d-b503-b74461831829.png>)

```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args)
    throws Throwable {

    // 从缓存中获取，创建DefaultNode
    DefaultNode node = map.get(context.getName());
    // 双重判断，如果判断为空
    if (node == null) {
        synchronized (this) {
            node = map.get(context.getName());
            if (node == null) {
                // 创建一个DefaultNode并且放入到缓存中
                node = new DefaultNode(resourceWrapper, null);
                HashMap<String, DefaultNode> cacheMap = new HashMap<String, DefaultNode>(map.size());
                cacheMap.putAll(map);
                cacheMap.put(context.getName(), node);
                map = cacheMap;
                // Build invocation tree
                // 将新建的Node添加到调用树中
                ((DefaultNode) context.getLastNode()).addChild(node);
            }

        }
    }
	
    context.setCurNode(node);
    // 触发下一个节点
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```


---

## 2.7 Sentinel源码解析-ClusterBuilderSlot解析

上节课我们分析了SlotChain入口和NodeSelectorSlot那么这节课分析ClusterBuilderSlot

### 2.7.1 官方定义

ClusterBuilderSlot：则用于存储资源的统计信息以及调用者信息，例如该资源的 RT, QPS, thread count 等等，这些信息将用作为多维度限流，降级的依据；

那我们来进行分析，首先我们先看上节课分析到的位置

```java
//NodeSelectorSlot.entry()
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args)
    throws Throwable {
    // 从缓存中获取，创建DefaultNode
    DefaultNode node = map.get(context.getName());
    // 双重判断，如果判断为空
    if (node == null) {
        synchronized (this) {
            node = map.get(context.getName());
            if (node == null) {
                // 创建一个DefaultNode并且放入到缓存中
                node = new DefaultNode(resourceWrapper, null);
                HashMap<String, DefaultNode> cacheMap = new HashMap<String, DefaultNode>(map.size());
                cacheMap.putAll(map);
                cacheMap.put(context.getName(), node);
                map = cacheMap;
                // Build invocation tree
                // 将新建的Node添加到调用树中
                ((DefaultNode) context.getLastNode()).addChild(node);
            }

        }
    }

    context.setCurNode(node);
    // 触发下一个节点
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

再触发下一个节点以后，调用的是父级AbstractLinkedProcessorSlot.fireEntry()方法，然后next调用transformEntry

```java
@Override
public void fireEntry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args)
    throws Throwable {
    if (next != null) {
        // 调用下一个节点
        next.transformEntry(context, resourceWrapper, obj, count, prioritized, args);
    }
}
//next就代表循环到下一个节点所以这里调用entry的就是ClusterBuilderSlot
@SuppressWarnings("unchecked")
void transformEntry(Context context, ResourceWrapper resourceWrapper, Object o, int count, boolean prioritized, Object... args)
        throws Throwable {
        T t = (T)o;
        entry(context, resourceWrapper, t, count, prioritized, args);
    }
```

### 2.7.2 ClusterBuilderSlot

```java
// ClusterBuilderSlot.entry
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count,
                  boolean prioritized, Object... args)
    throws Throwable {
    if (clusterNode == null) {
        synchronized (lock) {
            if (clusterNode == null) {
                // Create the cluster node.
                clusterNode = new ClusterNode(resourceWrapper.getName(), resourceWrapper.getResourceType());
                // key为资源 value为ClusterNode
                HashMap<ResourceWrapper, ClusterNode> newMap = new HashMap<>(Math.max(clusterNodeMap.size(), 16));
                newMap.putAll(clusterNodeMap);
                newMap.put(node.getId(), clusterNode);

                clusterNodeMap = newMap;
            }
        }
    }
 	// 添加节点
    node.setClusterNode(clusterNode);

    /*
         * if context origin is set, we should get or create a new {@link Node} of
         * the specific origin.
         */
    // 确认资源的来源
    if (!"".equals(context.getOrigin())) {
        Node originNode = node.getClusterNode().getOrCreateOriginNode(context.getOrigin());
        context.getCurEntry().setOriginNode(originNode);
    }

    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

### 2.7.3 总结：

ClusterNode作用就是与DefaultNode进行关联，即不同的DefaultNode都关联了一个ClusterNode，这样我们在不同上下文中都可以拿到当前资源一个总的流量统计情况。


---

## 2.8 Sentinel源码解析-Slot分析后续1

这节课我们继续分析Sentinel调用链路中的Slot，那么我们还是先来分析StatisticSlot：

### 2.8.1 StatisticSlot定义：

StatisticSlot 是 Sentinel 最为重要的类之一，用于根据规则判断结果进行相应的统计操作。

entry 的时候：依次执行后面的判断 slot。每个 slot 触发流控的话会抛出异常（BlockException的子类）。若有 BlockException抛出，则记录 block 数据；若无异常抛出则算作可通过（pass），记录 pass 数据。

exit 的时候：若无 error（无论是业务异常还是流控异常），记录 complete（success）以及 RT，线程数-1。

记录数据的维度：线程数+1、记录当前 DefaultNode 数据、记录对应的 originNode 数据（若存在 origin）、累计 IN 统计数据（若流量类型为 IN）。

### 2.8.2 分析源码核心方法

首先我们从ClusterNodeSlot中的entry方法中找到调用下一节点方法，然后向下跟踪，道理和之前的都一样，调用的是父级方法

```java
fireEntry(context, resourceWrapper, node, count, prioritized, args);
//-------------------父级方法-----------------------------
@Override
public void fireEntry(Context context, ResourceWrapper resourceWrapper, Object obj, int count, boolean prioritized, Object... args)
    throws Throwable {
    if (next != null) {
        next.transformEntry(context, resourceWrapper, obj, count, prioritized, args);
    }
}    
```

找到transformEntry方法，然后此时的下一个节点必然是StatisticSlot

```java
@SuppressWarnings("unchecked")
void transformEntry(Context context, ResourceWrapper resourceWrapper, Object o, int count, boolean prioritized, Object... args)
    throws Throwable {
    T t = (T)o;
    //StatisticSlot
    entry(context, resourceWrapper, t, count, prioritized, args);
}
```

然后我们来看StatisticSlot的核心方法，这里的核心思想就是：封装了基础的流量统计和获取方法

```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count,
                  boolean prioritized, Object... args) throws Throwable {
    try {
        // Do some checking.
        // 此位置会调用SlotChain中后续的所有Slot，完成所有规则检测。
        fireEntry(context, resourceWrapper, node, count, prioritized, args);

        // Request passed, add thread count and pass count.
        // 代码运行到这个位置，就证明之前的所有Slot检测都通过了，此时就可以统计请求的相应数据了。
        // 增加线程数（+1）
        node.increaseThreadNum();
        // 增加通过请求的数量(这里涉及到滑动窗口算法)
        node.addPassRequest(count);
		// .....以下全部是流量统计的方法
}
```

看到这里以后，我们对StatisticSlot作用已经比较了解了，那么现在我们主要要分析这个位置，也就是说向下分析一下的节点

```java
fireEntry(context, resourceWrapper, node, count, prioritized, args);
```

这里向下的节点调用方式和之前的都一样，所以剩下的节点非别为：

![image-20211213152938457](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9dace036-8aea-5a98-ac59-66e19e0f2da3.png>)

但是其实这里的ParamFlowSlot包括SystemSlot和AuthoritySlot我们在操作的时候其实就已经知道了它们就是根据不同维度进行统计和检测，那么这三个我们就不再详细分析，这条链路中我们要主要分析流控和熔断：FlowSlot、DegradeSlot

### 2.8.3 FlowSlot定义：

这个slot 主要根据预设的资源的统计信息，按照固定的次序，依次生效。如果一个资源对应两条或者多条流控规则，则会根据如下次序依次检验，直到全部通过或者有一个规则生效为止:

1\. 指定应用生效的规则，即针对调用方限流的；
2\. 调用方为 other 的规则；
3\. 调用方为 default 的规则。

### 2.8.4 分析核心方法

进入到核心方法的方式和之前的都一样，所以我们直接来看

```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count,
                  boolean prioritized, Object... args) throws Throwable {
    //检测并且应用流量规则
    checkFlow(resourceWrapper, context, node, count, prioritized);
    //触发下一个Slot
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

那我们先来分析checkFlow方法

```java
void checkFlow(ResourceWrapper resource, Context context, DefaultNode node, int count, boolean prioritized)
    throws BlockException {
    // 在这里继续跟踪
    checker.checkFlow(ruleProvider, resource, context, node, count, prioritized);
}
//---------------checkFlow具体实现----------------------
public void checkFlow(Function<String, Collection<FlowRule>> ruleProvider, ResourceWrapper resource,
                      Context context, DefaultNode node, int count, boolean prioritized) throws BlockException {
    // 判断规则和资源不能为空
    if (ruleProvider == null || resource == null) {
        return;
    }
    // 获取到指定资源的所有流控规则
    Collection<FlowRule> rules = ruleProvider.apply(resource.getName());
    // 逐个应用流控规则。若无法通过则抛出异常，后续规则不再应用
    if (rules != null) {
        for (FlowRule rule : rules) {
            if (!canPassCheck(rule, context, node, count, prioritized)) {
                // FlowException继承BlockException
                throw new FlowException(rule.getLimitApp(), rule);
            }
        }
    }
}
```

这里我们需要注意一下FlowRule（流控规则），它继承自AbstractRule，那我们来看一下AbstractRule其中的两个类型，分别是资源名称和资源的来源

```java
private String resource;
private String limitApp;
```

那么通过这里我们就可以得知，流控的规则设置其实就是通过这个FlowRule来完成的，同样它的数据来源，就是我们使用的Sentinel-dashboard，当然也可以通过代码来进行设置

```java
public class FlowRule extends AbstractRule {

    public FlowRule() {
        super();
        // 来源默认Default
        setLimitApp(RuleConstant.LIMIT_APP_DEFAULT);
    }

    public FlowRule(String resourceName) {
        super();
        // 资源名称
        setResource(resourceName);
        setLimitApp(RuleConstant.LIMIT_APP_DEFAULT);
    }

    /**
     * The threshold type of flow control (0: thread count, 1: QPS).
     */
    // 设置阈值类型0是线程1为QPS
    private int grade = RuleConstant.FLOW_GRADE_QPS;

    /**
     * Flow control threshold count.
     */
    // 单机阈值
    private double count;

    /**
     * Flow control strategy based on invocation chain.
     * 直接流控
     * {@link RuleConstant#STRATEGY_DIRECT} for direct flow control (by origin);
     * 关联流控
     * {@link RuleConstant#STRATEGY_RELATE} for relevant flow control (with relevant resource);
     * 链路流控
     * {@link RuleConstant#STRATEGY_CHAIN} for chain flow control (by entrance resource).
     */
    // 流控模式
    private int strategy = RuleConstant.STRATEGY_DIRECT;

    /**
     * Reference resource in flow control with relevant resource or context.
     */
    // 关联流控模式，关联的资源设置
    private String refResource;

    /**
     * Rate limiter control behavior.
     * 0. default(reject directly), 1. warm up, 2. rate limiter, 3. warm up + rate limiter
     */
    // 流控效果 0快速失败 1预热（令牌桶算法） 2排队等待（漏斗算法） 3预热+排队等待（目前控制台没有）
    private int controlBehavior = RuleConstant.CONTROL_BEHAVIOR_DEFAULT;

    // warp up预热时长
    private int warmUpPeriodSec = 10;

    /**
     * Max queueing time in rate limiter behavior.
     */
    // 排队等待的超时时间
    private int maxQueueingTimeMs = 500;

    // 是否为集群模式
    private boolean clusterMode;
    /**
     * Flow rule config for cluster mode.
     */
    // 集群模式配置
    private ClusterFlowConfig clusterConfig;

    /**
     * The traffic shaping (throttling) controller.
     */
    private TrafficShapingController controller;

   // ....省略以下方法
}

```

这里还涉及到两个算法：令牌桶算法和漏斗算法

1\. 令牌桶算法：令牌桶算法的原理是系统会以一个恒定的速度往桶里放入令牌，而如果请求需要被处理，则需要先从桶里获取一个令牌，当桶里没有令牌可取时，则拒绝服务。
2\. 漏斗算法：漏桶算法思路很简单，请求先进入到漏桶里，漏桶以固定的速度出水，也就是处理请求，当水加的过快，则会直接溢出，也就是拒绝请求，可以看出漏桶算法能强行限制数据的传输速率。


---

## 2.9 Sentinel源码解析-Slot分析后续2

这节课我们继续分析Sentinel调用链路中的Slot，那么我们还是继续分析FlowSlot

### 2.9.1 FlowSlot获取全部流控规则

上节课我们分析到这个位置，这节课我们主要研究一下如何获取全部的流控规则

```java
// 获取到指定资源的所有流控规则
Collection<FlowRule> rules = ruleProvider.apply(resource.getName());
// 逐个应用流控规则。若无法通过则抛出异常，后续规则不再应用
if (rules != null) {
    for (FlowRule rule : rules) {
        if (!canPassCheck(rule, context, node, count, prioritized)) {
            // FlowException继承BlockException
            throw new FlowException(rule.getLimitApp(), rule);
        }
    }
}
-------------------------------------------------------------------------------------------
// 这里调用的apply方法就是FlowSlot中的对应方法
private final Function<String, Collection<FlowRule>> ruleProvider = new Function<String, Collection<FlowRule>>() {
    @Override
    public Collection<FlowRule> apply(String resource) {
        // Flow rule map should not be null.
        // 获取所有资源和对应的流控规则 key为资源名称，value为该资源对应的所有流控规则
        Map<String, List<FlowRule>> flowRules = FlowRuleManager.getFlowRuleMap();
        // 获取指定资源的流控规则
        return flowRules.get(resource);
    }
};
```

那我们来看apply方法，要注意的是其中的FlowRuleManager其实如果我们通过代码的方式来设置流控就会使用到它，因为它提供了对应的方法FlowRuleManager.loadRules()

```java
/**
* Load {@link FlowRule}s, former rules will be replaced.
* 添加新的流控规则
* @param rules new rules to load.
*/
public static void loadRules(List<FlowRule> rules) {
    currentProperty.updateValue(rules);
}
```

但是现在这个位置源码用的FlowRuleManager.getFlowRuleMap()，其实通过这个方法我们就可以得知，FlowRuleManager的作用就是设置对应资源的流控规则，资源对应的流控规则(list集合)组成一个Map

```java
// 获取所有资源和对应的流控规则 key为资源名称，value为该资源对应的所有流控规则
Map<String, List<FlowRule>> flowRules = FlowRuleManager.getFlowRuleMap();
```

那我们现在来看FlowRuleChecker.checkFlow()方法如何应用规则，这里我们要注意遍历

```java
public void checkFlow(Function<String, Collection<FlowRule>> ruleProvider, ResourceWrapper resource,
                      Context context, DefaultNode node, int count, boolean prioritized) throws BlockException {
    // 判断规则和资源不能为空
    if (ruleProvider == null || resource == null) {
        return;
    }
    // 获取到指定资源的所有流控规则
    Collection<FlowRule> rules = ruleProvider.apply(resource.getName());
    // 逐个应用流控规则。若无法通过则抛出异常，后续规则不再应用
    if (rules != null) {
        for (FlowRule rule : rules) {
            // 遍历规则，应用规则，判断是否能够通过规则
            if (!canPassCheck(rule, context, node, count, prioritized)) {
                // FlowException继承BlockException
                throw new FlowException(rule.getLimitApp(), rule);
            }
        }
    }
}
```

那我们需要跟进去看一下canPassCheck方法

```java
public boolean canPassCheck(/*@NonNull*/ FlowRule rule, Context context, DefaultNode node, int acquireCount,boolean prioritized) {
    // 从规则中获取要限定的来源
    String limitApp = rule.getLimitApp();
    // 如果来源为空，则请求直接通过
    if (limitApp == null) {
        return true;
    }

    // 处理集群流控
    if (rule.isClusterMode()) {
        return passClusterCheck(rule, context, node, acquireCount, prioritized);
    }

    // 处理单机流控
    return passLocalCheck(rule, context, node, acquireCount, prioritized);
}
```

那么这里我们要关注的是处理单机流控的方法passLocalCheck

```java
private static boolean passLocalCheck(FlowRule rule, Context context, DefaultNode node, int acquireCount,
                                      boolean prioritized) {
    // 根据请求选择节点
    Node selectedNode = selectNodeByRequesterAndStrategy(rule, context, node);
    if (selectedNode == null) {
        return true;
    }

    // 根据配置FlowRule配置的流控效果(流控效果:直接拒绝、排队等待、预热)，选择不同的Controller，判断是否通过
    return rule.getRater().canPass(selectedNode, acquireCount, prioritized);
}
```

在这里我们来看一下根据请求来选择节点

```java
static Node selectNodeByRequesterAndStrategy(/*@NonNull*/ FlowRule rule, Context context, DefaultNode node) {
    // The limit app should not be empty.
    // 获取限定来源(调用方)、流控模式（直接）、获取context中的调用发（从当前上下文环境中获取）
    String limitApp = rule.getLimitApp();
    int strategy = rule.getStrategy();
    String origin = context.getOrigin();

    // 判断来源如果不是Default（默认）和Other（其他）
    if (limitApp.equals(origin) && filterOrigin(origin)) {
        if (strategy == RuleConstant.STRATEGY_DIRECT) {
            // Matches limit origin, return origin statistic node.
            return context.getOriginNode();
        }

        return selectReferenceNode(rule, context, node);
        //如果是默认来源
    } else if (RuleConstant.LIMIT_APP_DEFAULT.equals(limitApp)) {
        // 是否是直接流控模式
        if (strategy == RuleConstant.STRATEGY_DIRECT) {
            // Return the cluster node.
            // 返回统计数据
            return node.getClusterNode();
        }

        return selectReferenceNode(rule, context, node);
        // 其他来源other
    } else if (RuleConstant.LIMIT_APP_OTHER.equals(limitApp)
               && FlowRuleManager.isOtherOrigin(origin, rule.getResource())) {
        if (strategy == RuleConstant.STRATEGY_DIRECT) {
            return context.getOriginNode();
        }

        return selectReferenceNode(rule, context, node);
    }

    return null;
}
```

那到这为止，我们就知道如何获取流控规则和针对不同来源的不同操作，那么现在还有一个问题就是，就是这个位置的解析

```java
// 根据配置FlowRule配置的流控效果(流控效果:直接拒绝、排队等待、预热)，选择不同的Controller，判断是否通过
return rule.getRater().canPass(selectedNode, acquireCount, prioritized);
```

### 2.9.2 流控效果对应的Controller

其实这个位置的rule.getRater()的返回值类型为TrafficShapingController，而这里的类结构为：

```java
TrafficShapingController getRater() {
    return controller;
}
```

![image-20211216210615881](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5c7b3a60-dbb8-5d53-b59b-a2dc3bf9d706.png>)

其实从这里我们就能看出针对不同的流控模式，针对不同的控制器来进行处理，那么我们这里演示一个DefaultController

![image-20211216210800196](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4db699ec-ba57-5544-94f3-b5b34290137b.png>)

那我们现在来看一下具体DefaultController的操作，在DefaultController中，首先获取当前的线程数或者QPS数，如果当前的线程数或者QPS+申请的数量>配置的总数，则不通过，如果当前线程数或者QPS+申请的数量<=配置的总数，则直接通过

```java
public class DefaultController implements TrafficShapingController {

    private static final int DEFAULT_AVG_USED_TOKENS = 0;

    private double count;
    private int grade;

    public DefaultController(double count, int grade) {
        this.count = count;
        this.grade = grade;
    }

    @Override
    public boolean canPass(Node node, int acquireCount) {
        return canPass(node, acquireCount, false);
    }

    @Override
    public boolean canPass(Node node, int acquireCount, boolean prioritized) {
        //获取当前node节点的线程数或者请求通过的qps总数
        int curCount = avgUsedTokens(node);
        //当前请求数（请求的令牌）+申请总数是否（已经消耗的令牌）>该资源配置的总数（阈值）
        if (curCount + acquireCount > count) {
            if (prioritized && grade == RuleConstant.FLOW_GRADE_QPS) {
                long currentTime;
                long waitInMs;
                currentTime = TimeUtil.currentTimeMillis();
                waitInMs = node.tryOccupyNext(currentTime, acquireCount, count);
                if (waitInMs < OccupyTimeoutProperty.getOccupyTimeout()) {
                    node.addWaitingRequest(currentTime + waitInMs, acquireCount);
                    node.addOccupiedPass(acquireCount);
                    sleep(waitInMs);

                    // PriorityWaitException indicates that the request will pass after waiting for {@link @waitInMs}.
                    throw new PriorityWaitException(waitInMs);
                }
            }
            return false;
        }
        return true;
    }

    private int avgUsedTokens(Node node) {
        if (node == null) {
            return DEFAULT_AVG_USED_TOKENS;
        }
        return grade == RuleConstant.FLOW_GRADE_THREAD ? node.curThreadNum() : (int)(node.passQps());
    }

    private void sleep(long timeMillis) {
        try {
            Thread.sleep(timeMillis);
        } catch (InterruptedException e) {
            // Ignore.
        }
    }
}
```

### 2.9.3 流程总结

![image-20211216214823684](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8f702c15-d660-55ba-ac5b-a0ea4d9faa8f.png>)


---

## 2.10 Sentinel源码解析-熔断降级DegradeSlot解析

上节课我们分析完了限流FlowSlot，那么我们继续想下一个链路中的元素来看，下一个就是熔断降级的Slot，那我们直接来看核心方法

```java
//DegradeSlot.entry
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count,
                  boolean prioritized, Object... args) throws Throwable {
    // 熔断降级检测
    performChecking(context, resourceWrapper);
    // 触发下一个节点
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}
```

那我们先来跟踪熔断降级检测的方法，在这里我们可以看见，这里其实就是对熔断器的状态进行判断

```java
void performChecking(Context context, ResourceWrapper r) throws BlockException {
    // 获取所有资源的熔断器
    List<CircuitBreaker> circuitBreakers = DegradeRuleManager.getCircuitBreakers(r.getName());
    // 判断是否获取到熔断器，如果为空直接结束
    if (circuitBreakers == null || circuitBreakers.isEmpty()) {
        return;
    }
    for (CircuitBreaker cb : circuitBreakers) {
        // 判断所有熔断器的状态，如果是开启状态直接抛出异常
        if (!cb.tryPass(context)) {
            // 此异常继承于BlockException
            throw new DegradeException(cb.getRule().getLimitApp(), cb.getRule());
        }
    }
}
```

而真正判断是否需要开启熔断器是在exit方法中进行的，这个方法是在业务方法执行以后调用了，熔断器需要手机业务异常或者业务的执行时间来判断是开启熔断

```java
@Override
public void exit(Context context, ResourceWrapper r, int count, Object... args) {
    // 如果当前其他的Slot已经有了BlockException，就直接跳过
    Entry curEntry = context.getCurEntry();
    if (curEntry.getBlockError() != null) {
        fireExit(context, r, count, args);
        return;
    }
    // 通过资源名称获取熔断器
    List<CircuitBreaker> circuitBreakers = DegradeRuleManager.getCircuitBreakers(r.getName());
    if (circuitBreakers == null || circuitBreakers.isEmpty()) {
        fireExit(context, r, count, args);
        return;
    }

    if (curEntry.getBlockError() == null) {
        // passed request
        // 调用CircuitBreaker的onRequestComplete()方法
        for (CircuitBreaker circuitBreaker : circuitBreakers) {
            circuitBreaker.onRequestComplete(context);
        }
    }

    fireExit(context, r, count, args);
}
```

在这个代码中，有一个比较关键的地方就是CircuitBreaker（熔断器），那我们来分析一下：

### 2.10.1 CircuitBreaker熔断器

首先我们要知道其实CircuitBreaker是一个接口

```java
/**
 * 熔断器中将三种熔断策略封装（慢调用比例/异常比例/异常数）为两种熔断器：
 * 响应时间熔断器、异常熔断器
 */
public interface CircuitBreaker {

    /**
     * Get the associated circuit breaking rule.
     *
     * @return associated circuit breaking rule
     * 获取熔断规则
     */
    DegradeRule getRule();

    /**
     * Acquires permission of an invocation only if it is available at the time of invoking.
     *
     * @param context context of current invocation
     * @return {@code true} if permission was acquired and {@code false} otherwise
     * 判断是否需要降级 返回值为false开启降级
     */
    boolean tryPass(Context context);

    /**
     * Get current state of the circuit breaker.
     *
     * @return current state of the circuit breaker
     * 当前熔断器状态
     */
    State currentState();

    /**
     * <p>Record a completed request with the context and handle state transformation of the circuit breaker.</p>
     * <p>Called when a <strong>passed</strong> invocation finished.</p>
     *
     * @param context context of current invocation
     * 回调方法 当请求通过后触发
     */
    void onRequestComplete(Context context);

    /**
     * Circuit breaker state.
     * 三种熔断器状态：
     * OPEN开启
     * HALF_OPEN半开启
     * CLOSED关闭
     */
    enum State {
        /**
         * In {@code OPEN} state, all requests will be rejected until the next recovery time point.
         */
        OPEN,
        /**
         * In {@code HALF_OPEN} state, the circuit breaker will allow a "probe" invocation.
         * If the invocation is abnormal according to the strategy (e.g. it's slow), the circuit breaker
         * will re-transform to the {@code OPEN} state and wait for the next recovery time point;
         * otherwise the resource will be regarded as "recovered" and the circuit breaker
         * will cease cutting off requests and transform to {@code CLOSED} state.
         */
        HALF_OPEN,
        /**
         * In {@code CLOSED} state, all requests are permitted. When current metric value exceeds the threshold,
         * the circuit breaker will transform to {@code OPEN} state.
         */
        CLOSED
    }
}

```

以上代码中其中三种熔断状态对应的原理如下（这个原理在基础应用讲过）

![image-20211103194830095](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/0be397c2-07fd-5fdd-86ec-f907c85aa597.png>)

### 2.10.2 熔断策略

对应的实现类有两个ExceptionCircuitBreaker（异常熔断器）、ResponseTimeCircuitBreaker（响应时间熔断器），这个我们可以从类图看到

![image-20211221193548988](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/17e2edff-9d40-5fcf-8b10-432d85851374.png>)

那我们这里来看看ExceptionCircuitBreaker(异常熔断器)，对应的策略是

![image-20211221225941518](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fc39f2c3-d0d0-51e5-ae4d-5a3c36617c5c.png>)

我们来看对应回调方法ExceptionCircuitBreaker.onRequestComplete

```java
@Override
public void onRequestComplete(Context context) {
    Entry entry = context.getCurEntry();
    if (entry == null) {
        return;
    }
    Throwable error = entry.getError();
    // 异常事件窗口计数器
    SimpleErrorCounter counter = stat.currentWindow().value();
    // 如果有异常，异常数+1
    if (error != null) {
        counter.getErrorCount().add(1);
    }
    // 总异常数+1
    counter.getTotalCount().add(1);

    handleStateChangeWhenThresholdExceeded(error);
}

private void handleStateChangeWhenThresholdExceeded(Throwable error) {
    // 如果熔断器已经开启直接返回
    if (currentState.get() == State.OPEN) {
        return;
    }

    // 进入办开启状态
    if (currentState.get() == State.HALF_OPEN) {
        // In detecting request
        if (error == null) {
            // 本次请求没有出现异常，关闭熔断器
            fromHalfOpenToClose();
        } else {
            // 本次请求出现异常，打开熔断
            fromHalfOpenToOpen(1.0d);
        }
        return;
    }

    List<SimpleErrorCounter> counters = stat.values();
    long errCount = 0; //异常数量
    long totalCount = 0; //总异常数
    for (SimpleErrorCounter counter : counters) {
        errCount += counter.errorCount.sum();
        totalCount += counter.totalCount.sum();
    }
    // 请求数量<最小的请求数量时不开启熔断
    if (totalCount < minRequestAmount) {
        return;
    }
    double curCount = errCount;
    // 熔断策略为慢调用比例
    if (strategy == DEGRADE_GRADE_EXCEPTION_RATIO) {
        // Use errorRatio
        // 计算百分比
        curCount = errCount * 1.0d / totalCount;
    }
    // 错误率或者错误数大于阈值时开启熔断
    if (curCount > threshold) {
        transformToOpen(curCount);
    }
}
```


---

## 2.11 Sentinel源码分析-滑动时间窗口算法原理

在之前的学习中，我们已经学习完成了Sentinel源码的Node关系、责任链调用，那么这节课我们就要学习Sentinel核心源码中的一个非常重要的算法“滑动时间窗口算法”

![image-20211222170933011](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/942c3bba-15db-5724-94c6-33c8b9d67b3d.png>)

### 2.11.1 时间窗算法

那么在了解滑动时间窗算法之前，我们先要来了解时间窗算法，也可以称之为：固定时间窗算法

概念：固定时间窗口计数器算法思想：在固定的时间窗口内，可以允许固定数量的请求进入。超过数量就拒绝或者排队，等下一个时间段进入。

那我们来看图分析：

![image-20211222174050592](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8e5997bb-7c95-5dcc-86dd-52616fbe59d0.png>)



具体分析一下：

1\. 将当前的时间分为10t大小的几个时间窗
2\. 规则是阈值为100个请求数，每个时间窗里面的请求数量不能超过阈值100
3\. 10t到16t进入请求10个，16t到20t进入请求50个，总数60个请求，没有超过阈值100
4\. 20t到26t进入请求60个，26t到30t进入请求20个，总数80个请求，没有超过阈值100
5\. 30t到40t之间进入请求120个，超过阈值20个，所以20个请求无法进入

存在问题：16t到26t之间也是10t大小的一个时间窗，但是请求总数为110，超过阈值，这种固定时间窗无法处理这部分超出的请求，解决办法就是使用滑动时间窗。

### 2.11.2 滑动时间窗算法

使用滑动时间窗的原因，就在于虽然以上提到超出阈值的部分分别在两个时间窗中，但是实际上我们要清楚，我们系统限流的目的是要在任意时间都要能应对突然的流量暴增，如果使用以上的算法，就会造成在16t和26t之间的请求无法限流，从而严重会导致服务雪崩。

要解决的话，我们就需要使用滑动时间窗算法，具体原理如下：

滑动时间窗限流算法解决了固定时间窗限流算法的问题。其没有划分固定的时间窗起点与终点，而是将每一次请求的到来时间点作为统计时间窗的终点，起点则是终点向前推时间窗长度的时间点。这种时间窗称为“滑动时间窗”

看图分析：

此图中我们可以分析中，实际上当前的时间窗不再是固定的，而是可以从时间的起始位置一直向右滑动

![image-20211222190154070](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1d57b0ff-c52d-5b10-af07-3f87941db3bd.png>)

这样的话就可以解决固定时间窗带来的问题，如图所示：

其原理就是：

1\. 当前时间窗口为滑动窗口，可以从左向右按照时间顺序进行滑动，并且大小为10t，同时此时的阈值为100
2\. 红色线的位置进入一个请求，此时想要判断这个请求是否能够正常通过，就要看当前滑动窗口中的请求数量是否达到阈值，如果当前没有达到阈值100，就可以正常通过，但是如果一旦超过阈值，就会被进行限流。

没有超过阈值：

![image-20211222191157347](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6e2157fe-4c85-5a97-bba5-3c9d36e92dd5.png>)

超过阈值：

![image-20211222191224880](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6fddd21d-75af-5688-89a8-3ac59ee0e0bc.png>)

但是此时滑动时间窗还是有问题的，问题就是会出现大量的重复统计，造成系统效率下降，如下图所示：

在此图中我们就可以看出，这个蓝色的区域就是重复统计的区域，也就是说每一次移动时间窗口，都需要重新统计重复区域的请求数量，从而导致浪费大量的系统资源。

![image-20211222193546553](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2d5a9c44-f751-579a-8f92-9987bf5c506d.png>)



 ### 2.11.3 滑动时间窗口算法-改进

想要解决以上的问题，我们就需要更加细粒度话的计算，增加多个子时间窗口：样本窗口

概念：

1\. 样本窗口的长度必须小于滑动窗口长度，如果等于滑动窗口长度就会变成固定时间窗口
2\. 一般滑动窗口长度是样本窗口的整数倍，比如：4*样本窗口=1个滑动窗口
3\. 每个样本窗口在到达终点时间时，会统计本样本窗口中的流量数据并且记录下来。
4\. 当一个请求达到时，会统计当前请求时间点所在的样本窗口中的流量数据，然后在获取当前请求时间的样本窗口以外的同一个滑动窗口中的样本窗口的统计数据，进行求和，如果没有超出阈值，则通过，否则就会被限流。

原理图：

![image-20211222201207217](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/23a2e6f6-55cd-596e-8c84-c7d5dda9e28c.png>)

下一个进入请求的时间点

此时这个请求将不会被限流，因为本次请求的时间的对应的样本窗口只有5个请求加上之前重复的样本窗口统计的流量值，没有超过阈值100，所以本次请求会通过。

![image-20211222202102495](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c1660093-39ab-532e-9cb2-1aaac263ce84.png>)


---

## 2.12 Sentinel源码分析-Sentinel滑动时间窗口算法源码解析

上节课我们分析了Sentinel的滑动时间窗口算法原理，那么这节课我们来研究一下源码中的具体实现

### 2.12.1 整体流程图

![滑动时间窗算法实现](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b2968627-0422-5c3a-be8d-0ddf36a66780.png>)

### 2.12.2 源码分析

那我们就按照这个流程图来从头分析

首先看StatisticSlot.entry方法中node.addPassRequest(count)方法，这里我之前就提到过用到了滑动窗口算法，那我们来具体分析

```java
// 增加通过请求的数量(这里涉及到滑动窗口算法)
node.addPassRequest(count);
```

进入方法DefaultNode.addPassRequest

```java
@Override
public void addPassRequest(int count) {
    super.addPassRequest(count);
    this.clusterNode.addPassRequest(count);
}
```

继续向下跟踪

```java
@Override
public void addPassRequest(int count) {
    // 为滑动计数器增加本次的访问数据
    rollingCounterInSecond.addPass(count);
    rollingCounterInMinute.addPass(count);
}
```

最后进入ArrayMetric.addPass，这是一个使用数组保存数据的计量器类

```java
@Override
public void addPass(int count) {
    // 获取当前时间点所在的样本窗口
    WindowWrap<MetricBucket> wrap = data.currentWindow();
    // 将当前请求的计数量添加到当前样本窗口的统计数据中
    wrap.value().addPass(count);
}
```

先来跟踪data.currentWindow();

```java
public WindowWrap<T> currentWindow() {
    // 获取当前时间所在的样本窗口
    return currentWindow(TimeUtil.currentTimeMillis());
}
```

这里就会进入LeapArray（环形数组）中的currentWindow方法中,这个环形数组，其实就是Sentinel官方提供的原理图中的环形数组WindowLeapArray

![image-20211223172002547](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c79984ac-b63e-5bff-ad93-fbdd23bb2ed3.png>)

```java
// 环形数组
public abstract class LeapArray<T> {
    // 样本窗口长度
    protected int windowLengthInMs;
    // 一个时间窗中包含的时间窗数量
    protected int sampleCount;
    // 时间窗长度
    protected int intervalInMs;
    private double intervalInSecond;
 
    // 这个一个数组，元素为WindowWrap样本窗口
    // 注意，这里的泛型 T 实际为 MetricBucket 类型
    protected final AtomicReferenceArray<WindowWrap<T>> array;
 ......   
}  
```

这里要注意这个数组，这个数组里面所存放的类型就是WindowWrap窗口类型，泛型T是MetricBucket这里我们来看一下这个类型

```java
public WindowWrap(long windowLengthInMs, long windowStart, T value) {
    //样本窗口长度
    this.windowLengthInMs = windowLengthInMs;
    //样本窗口的起始时间戳
    this.windowStart = windowStart;
    //当前样本窗口的统计数据 其类型为MetricBucket
    this.value = value;
}
```

```java
//..LeapArray
public WindowWrap<T> currentWindow() {
    // 获取当前时间所在的样本窗口
    return currentWindow(TimeUtil.currentTimeMillis());
}
//------------------------------------------------------------
public WindowWrap<T> currentWindow(long timeMillis) {
        if (timeMillis < 0) {
            return null;
        }
        // 计算当前时间所在的样本窗口id，即在计算数组LeapArray中的索引
        int idx = calculateTimeIdx(timeMillis);
        // Calculate current bucket start time.
     	// 计算当前样本窗口的开始时间点
        long windowStart = calculateWindowStart(timeMillis);
     .....
 }
```

在这里我们先分析calculateTimeIdx方法

```java
private int calculateTimeIdx(/*@Valid*/ long timeMillis) {
    // 计算当前时间在那个样本窗口（样本窗口下标），当前时间/样本窗口长度
    long timeId = timeMillis / windowLengthInMs;
    // Calculate current index so we can map the timestamp to the leap array.
    // 计算具体索引，这个array就是装样本窗口的数组
    return (int)(timeId % array.length());
}
```

timeId（样本窗口下标）原理如下：

![image-20211223202827754](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/aeb7502f-3788-5ef7-8d31-bb57b79a1a7d.png>)

具体索引原理：

![image-20211223204246750](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5b7bed9c-990f-5ce3-89b5-097c606b1875.png>)

这里分析完成我们接着分析这里，计算当前样本窗口的起点

```java
// 计算当前样本窗口的开始时间点
long windowStart = calculateWindowStart(timeMillis);
//------------------------------
protected long calculateWindowStart(/*@Valid*/ long timeMillis) {
    // 计算当前样本窗口的起点 当前时间点-（当前时间点%样本窗口长度）
    return timeMillis - timeMillis % windowLengthInMs;
}
```

原理如下：

![image-20211223205936680](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4cc23dbe-cdb4-5564-81b2-b7874e6b256b.png>)

这里分析完成之后我们继续向下分析

```java
while (true) {
    // 获取到当前时间所在的样本窗口
    WindowWrap<T> old = array.get(idx);
    // 如果获取不到，表示没有创建
    if (old == null) {
        /*
                 *     B0       B1      B2    NULL      B4
                 * ||_______|_______|_______|_______|_______||___
                 * 200     400     600     800     1000    1200  timestamp
                 *                             ^
                 *                          time=888
                 *            bucket is empty, so create new and update
                 *
                 * If the old bucket is absent, then we create a new bucket at {@code windowStart},
                 * then try to update circular array via a CAS operation. Only one thread can
                 * succeed to update, while other threads yield its time slice.
                 */
        // 创建新的时间窗口
        WindowWrap<T> window = new WindowWrap<T>(windowLengthInMs, windowStart, newEmptyBucket(timeMillis));
        // 通过CAS方式将新建窗口放入Array
        if (array.compareAndSet(idx, null, window)) {
            // Successfully updated, return the created bucket.
            return window;
        } else {
            // Contention failed, the thread will yield its time slice to wait for bucket available.
            Thread.yield();
        }
        // 若当前样本窗口的起始时间点与计算出的样本窗口起始点相同，则说明两个是同一个样本窗口
    } else if (windowStart == old.windowStart()) {
        /*
                 *     B0       B1      B2     B3      B4
                 * ||_______|_______|_______|_______|_______||___
                 * 200     400     600     800     1000    1200  timestamp
                 *                             ^
                 *                          time=888
                 *            startTime of Bucket 3: 800, so it's up-to-date
                 *
                 * If current {@code windowStart} is equal to the start timestamp of old bucket,
                 * that means the time is within the bucket, so directly return the bucket.
                 */
        return old;
        // 若当前样本窗口的起始时间点 大于 计算出的样本窗口起始时间点，说明计算出的样本窗口已经过时了，
        // 需要将原来的样本窗口替换
    } else if (windowStart > old.windowStart()) {
        /*
                 *   (old)
                 *             B0       B1      B2    NULL      B4
                 * |_______||_______|_______|_______|_______|_______||___
                 * ...    1200     1400    1600    1800    2000    2200  timestamp
                 *                              ^
                 *                           time=1676
                 *          startTime of Bucket 2: 400, deprecated, should be reset
                 *
                 * If the start timestamp of old bucket is behind provided time, that means
                 * the bucket is deprecated. We have to reset the bucket to current {@code windowStart}.
                 * Note that the reset and clean-up operations are hard to be atomic,
                 * so we need a update lock to guarantee the correctness of bucket update.
                 *
                 * The update lock is conditional (tiny scope) and will take effect only when
                 * bucket is deprecated, so in most cases it won't lead to performance loss.
                 */
        if (updateLock.tryLock()) {
            try {
                // Successfully get the update lock, now we reset the bucket.
                // 替换掉老的样本窗口
                return resetWindowTo(old, windowStart);
            } finally {
                updateLock.unlock();
            }
        } else {
            // Contention failed, the thread will yield its time slice to wait for bucket available.
            Thread.yield();
        }
        // 当前样本窗口的起始时间点 小于 计算出的样本窗口起始时间点，
        // 这种情况一般不会出现，因为时间不会倒流。除非人为修改了系统时钟
    } else if (windowStart < old.windowStart()) {
        // Should not go through here, as the provided time is already behind.
        return new WindowWrap<T>(windowLengthInMs, windowStart, newEmptyBucket(timeMillis));
    }
}
```

这里的原理如下：

![image-20211223213543463](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/adaf180f-28fb-5791-9611-42eef18a5f28.png>)

具体是如何替换的拿，我们来看源码

```java
// 替换掉老的样本窗口
return resetWindowTo(old, windowStart);
//------------------------------------------------------------
// BucketLeapArray.resetWindowTo
@Override
protected WindowWrap<MetricBucket> resetWindowTo(WindowWrap<MetricBucket> w, long startTime) {
    // Update the start time and reset value.
    // 更新窗口起始时间
    w.resetTo(startTime);
    // 将多维度统计数据清零
    w.value().reset();
    return w;
}
```

更新数据分析

```java
public MetricBucket reset() {
    // 将每个维度的统计数据清零
    for (MetricEvent event : MetricEvent.values()) {
        counters[event.ordinal()].reset();
    }
    initMinRt();
    return this;
}
```

最后我们再来看一下具体是那个维度，其实是通过维度

```java
@Override
public void addPass(int count) {
    // 获取当前时间点所在的样本窗口
    WindowWrap<MetricBucket> wrap = data.currentWindow();
    // 将当前请求的计数量添加到当前样本窗口的统计数据中
    wrap.value().addPass(count);
}
//----------------------------------------
public void addPass(int n) {
    add(MetricEvent.PASS, n);
}
```

![image-20211223221413399](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1eef2dbc-b634-55c1-9362-6cf5b87317ed.png>)


---

## 2.13 Sentinel 源码分析-滑动窗口数据统计解析

### 2.13.1 源码结构图

![滑动窗口数据统计](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8a1d400a-f420-5e11-ba38-2e789c28e6e2.png>)

### 2.13.2 分析源码

上节课我们通过分析源码已经知道了滑动窗口算法在Sentinel中的应用，那么这节课我们来研究一些其中的数据统计，首先入口我们就从限流的Slot：FlowSlot的entry方法入手：

```java
@Override
public void entry(Context context, ResourceWrapper resourceWrapper, DefaultNode node, int count,
                  boolean prioritized, Object... args) throws Throwable {
    //检测并且应用流控规则
    checkFlow(resourceWrapper, context, node, count, prioritized);
    //触发下一个Slot
    fireEntry(context, resourceWrapper, node, count, prioritized, args);
}

void checkFlow(ResourceWrapper resource, Context context, DefaultNode node, int count, boolean prioritized)
    throws BlockException {
    // 从这里进入
    checker.checkFlow(ruleProvider, resource, context, node, count, prioritized);
}
```

checkFlow方法里面找到遍历所有规则的canPassCheck方法然后在进入canPass方法，找到DefaultController对应实现，快速失败的流控效果，我们从这里来看，这里我们要关注的是avgUsedTokens方法，这个方法实际上就是获取当前时间窗里面的已经统计的数据

```java
@Override
public boolean canPass(Node node, int acquireCount, boolean prioritized) {
    //获取当前node节点的线程数或者请求通过的qps总数
    // 获取当前时间窗已经统计的数据
    int curCount = avgUsedTokens(node);
    //当前请求数（请求的令牌）+申请总数是否（已经消耗的令牌）>该资源配置的总数（阈值）
    // 以前的数据+新的数据
    if (curCount + acquireCount > count) {
        if (prioritized && grade == RuleConstant.FLOW_GRADE_QPS) {
            long currentTime;
            long waitInMs;
            currentTime = TimeUtil.currentTimeMillis();
            waitInMs = node.tryOccupyNext(currentTime, acquireCount, count);
            if (waitInMs < OccupyTimeoutProperty.getOccupyTimeout()) {
                node.addWaitingRequest(currentTime + waitInMs, acquireCount);
                node.addOccupiedPass(acquireCount);
                sleep(waitInMs);

                // PriorityWaitException indicates that the request will pass after waiting for {@link @waitInMs}.
                throw new PriorityWaitException(waitInMs);
            }
        }
        return false;
    }
    return true;
}
```

所以这里我们跟进一下，看如何获取已经统计的数据

```java
private int avgUsedTokens(Node node) {
    // 如果没有选出node，代表没有做统计工作，直接返回0
    if (node == null) {
        return DEFAULT_AVG_USED_TOKENS;
    }
    // 判断阈值类型，如果为QPS，则返回当前统计的QPS
    // 如果为线程数，则返回当前的线程数总量
    return grade == RuleConstant.FLOW_GRADE_THREAD ? node.curThreadNum() : (int)(node.passQps());
}
```

这里实际上就是判断阈值类型，我们这里看QPS类型的统计

![image-20211227174205035](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6fa58704-0e97-50ba-8839-5effcbbcadfb.png>)

```java
@Override
public double passQps() {
    // rollingCounterInSecond.pass() 当前时间窗中统计的通过请求数量
    // rollingCounterInSecond.getWindowIntervalInSec() 时间窗口长度
    // 这两个数相除，计算出的就是QPS
    return rollingCounterInSecond.pass() / rollingCounterInSecond.getWindowIntervalInSec();
}
```

那么这里就需要查看pass方法，看是如何统计通过请求总量的

```java
@Override
public long pass() {
    // 更新array中当前时间点所在样本窗口实例中的数据
    data.currentWindow();
    long pass = 0;
    // 将当前时间窗口中的所有样本窗口统计的value读取出来，并且记录
    List<MetricBucket> list = data.values();
	
    for (MetricBucket window : list) {
        pass += window.pass();
    }
    return pass;
}
```

那么这里我们先跟踪values()，其实这里就是拿出没有过时有效的样本窗口数据

```java
public List<T> values() {
    return values(TimeUtil.currentTimeMillis());
}
// ----------------------------
public List<T> values(long timeMillis) {
    if (timeMillis < 0) {
        return new ArrayList<T>();
    }
    int size = array.length();
    List<T> result = new ArrayList<T>(size);
    // 这个遍历array中的每一个样本窗口实例
    for (int i = 0; i < size; i++) {
        WindowWrap<T> windowWrap = array.get(i);
        // 若当前遍历实例为空或者已经过时，则继续下一个
        if (windowWrap == null || isWindowDeprecated(timeMillis, windowWrap)) {
            continue;
        }
        result.add(windowWrap.value());
    }
    return result;
}
```

判断位置过时判断逻辑：

```java
public boolean isWindowDeprecated(long time, WindowWrap<T> windowWrap) {
    // 当前时间-样本窗口起始时间>时间窗口  说明过时了
    return time - windowWrap.windowStart() > intervalInMs;
}
```

那么这里分析完成以后我们再回到ArrayMetric中的pass方法中，看遍历方法，就是将所有的有效并且是通过维度的数据统计出来进行求和，看是否超过阈值。

```java
@Override
public long pass() {
    // 更新array中当前时间点所在样本窗口实例中的数据
    data.currentWindow();
    long pass = 0;
    // 将当前时间窗口中的所有样本窗口统计的value读取出来，并且记录
    List<MetricBucket> list = data.values();
	
    // 将List中所有pass维度的统计数据并取出求和
    for (MetricBucket window : list) {
        // 通过维度
        pass += window.pass();
    }
    return pass;
}
```
