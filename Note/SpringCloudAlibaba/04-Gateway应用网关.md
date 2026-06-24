# Gateway 基础应用

本文档按 `GateWay基础应用` 目录下的章节顺序整合，内容来源于各子目录中的 Markdown 文件。

## 目录

- [1. 网关介绍](#1-网关介绍)
- [2. GateWay 工作流程与搭建](#2-gateway-工作流程与搭建)
- [3. GateWay 配置路由方式](#3-gateway-配置路由方式)
- [4. GateWay 实现负载均衡](#4-gateway-实现负载均衡)
- [5. GateWay 断言 Predicate](#5-gateway-断言-predicate)
- [6. GateWay 的 Filter](#6-gateway-的-filter)

## 1. 网关介绍

在微服务架构中，一个系统会被拆分为很多个微服务。那么作为客户端要如何去调用这么多的微服务呢？如果没有网关的存在，我们只能在客户端记录每个微服务的地址，然后分别去调用。这样的话会产生很多问题，例如：

- 客户端多次请求不同的微服务，增加客户端代码或配置编写的复杂性
- 认证复杂，每个微服务都有独立认证
- 存在跨域请求，在一定场景下处理相对复杂

为解决上面的问题所以引入了网关的概念：所谓的API网关，就是指系统的统一入口，提供内部服务的路由中转，为客户端提供统一服务，一些与业务本身功能无关的公共逻辑可以在这里实现，诸如认证、鉴权、监控、路由转发等。

![系统架构图](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/31b58164-e6a7-5b7c-b842-994f4eb9b518.png)

### 1.1 网关对比

- Zuul 1.x

  Netflix开源的网关，基于Servlet框架构建，功能丰富，使用JAVA开发，易于二次开发 问题：即一个线程处理一次连接请求，这种方式在内部延迟严重、设备故障较多情况下会引起存活的连接增多和线程增加的情况发生。

- Zuul 2.x

  Zuul2 采用了Netty实现异步非阻塞编程模型，每个 CPU 核一个线程，处理所有的请求和响应，请求和响应的生命周期是通过事件和回调来处理的，这种方式减少了线程数量，因此开销较小。

- GateWay

  Spring公司为了替换Zuul而开发的网关服务，底层为Netty，将在下面具体介绍。

- Nginx+lua

  使用nginx的反向代理和负载均衡可实现对api服务器的负载均衡及高可用，lua是一种脚本语言,可以来编写一些简单的逻辑, nginx支持lua脚本，问题在于：无法融入到微服务架构中

- Kong

  基于Nginx+Lua开发，性能高，稳定，有多个可用的插件(限流、鉴权等等)可以开箱即用。 问题：只支持Http协议；二次开发，自由扩展困难；提供管理API，缺乏更易用的管控、配置方式。

### 1.2 GateWay

Spring Cloud Gateway 基于Spring Boot 2.x、Spring WebFlux和Project Reactor，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。它的目标是替代Netflix Zuul，其不仅提供统一的路由方式，并且基于 Filter 链的方式提供了网关基本的功能，例如：安全，监控和限流。

特点：

1. 性能强劲：是Zuul的1.6倍
2. 功能强大：内置了很多实用的功能，例如转发、监控、限流等
3. 设计优雅，容易扩展

**基本概念**

路由(Route) 是 gateway 中最基本的组件之一，表示一个具体的路由信息载体。主要定义了下面的几个信息:

- id：路由标识、区别于其他route
- uri：路由指向的目的地uri，即客户端请求最终被转发到的微服务
- order：用于多个route之间的排序，数值越小排序越靠前，匹配优先级越高
- predicate：断言的作用是进行条件判断，只有断言都返回真，才会真正的执行路由
- filter：过滤器用于修改请求和响应信息

**执行流程**

1. Gateway Client向Gateway Server发送请求
2. 请求首先会被HttpWebHandlerAdapter进行提取组装成网关上下文
3. 然后网关的上下文会传递到DispatcherHandler，它负责将请求分发给RoutePredicateHandlerMapping
4. RoutePredicateHandlerMapping负责路由查找，并根据路由断言判断路由是否可用
5. 如果过断言成功，由FilteringWebHandler创建过滤器链并调用
6. 请求会一次经过PreFilter--微服务--PostFilter的方法，最终返回响应

### 1.3 总结

SpringCloud GateWay使用的是Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架。

## 2. GateWay 工作流程与搭建

核心流程图如下：

![Spring Cloud 网关图](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/af5db166-367d-56ab-866c-45c28ff7207a.png)

**核心概念：**

客户端向 Spring Cloud Gateway 发出请求。如果Gateway Handler Mapping确定请求与路由匹配，则将其发送到Gateway Web Handler 处理程序。此处理程序通过特定于请求的Fliter链运行请求。Fliter被虚线分隔的原因是Fliter可以在发送代理请求之前（pre）和之后（post）运行逻辑。执行所有pre过滤器逻辑。然后进行代理请求。发出代理请求后，将运行“post”过滤器逻辑。

**过滤器作用：**

- Filter在pre类型的过滤器可以做参数效验、权限效验、流量监控、日志输出、协议转换等。
- Filter在post类型的过滤器可以做响应内容、响应头的修改、日志输出、流量监控等
- 这两种类型的过滤器有着非常重要的作用

在GateWay的内部有三个核心点，我们先来看一下：

### 2.1 核心点

- Route（路由）

  路由是构建网关的基础模块，它由ID，目标URI，包括一些列的断言和过滤器组成，如果断言为true则匹配该路由

- Predicate（断言）

  参考的是Java8的java.util.function.Predicate，开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），请求与断言匹配则进行路由

- Filter（过滤）

  指的是Spring框架中GateWayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

- 三个核心点连起来：

  当用户发出请求到达GateWay，GateWay会通过一些匹配条件，定位到真正的服务节点，并在这个转发过程前后，进行一些及细化控制。其中Predicate就是我们匹配的条件，而Filter可以理解为一个拦截器，有了这两个点，再加上目标URI，就可以实现一个具体的路由了。

### 2.2 总结

GateWay核心的流程就是：路由转发+执行过滤器链

### 2.3 GateWay 搭建

那么我们现在已经了解了整体的基础概念以后，我们现在来搭建一个GateWay项目：cloudalibaba-gateway-9999

![image-20211228224951746](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/48a589e4-2087-531a-aa1c-6b6d25ac1526.png)

因为GateWay属于SpringCloud的，所以我们要导入对应依赖，一定要注意版本关系：

版本对应地址：https://spring.io/projects/spring-cloud

老师这里使用的是SpringBoot2.2.6的版本所以配合的是SpringCloud的Hoxton.SR5版本

**注意：引入GateWay一定要删除spring-boot-starter-web依赖，否则会有冲突无法启动**

父级项目引入

```xml
<!--spring cloud Hoxton.SR5-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-dependencies</artifactId>
    <version>${spring-cloud-gateway-varsion}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

子级项目，因为GateWay也需要注册进Nacos，所以也需要Nacos的依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

接着配置YML文件

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: true #让Spring Cloud Gateway自动从注册中心发现服务，并自动生成路由，
          # 开启后手写路由仍然可以用，下面是手写路由
      routes:  # 路由
        - id: nacos-provider #路由ID，没有固定要求，但是要保证唯一，建议配合服务名
          uri: http://localhost:9001/nacos-provider # 匹配提供服务的目标路由地址
          predicates: # 断言
            - Path=/msb/** # 断言，路径相匹配进行路由
```

更改9001的DemoController，加上一个入口

```java
@RestController
@RequestMapping("/msb")//路由路径
public class DemoController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/get")
    public String getServerPort(){
        return "库存-1："+serverPort;
    }

}
```

最后测试，启动Nacos、9001和9999网关，通过网关访问9001的/msb/get接口同时查看Nacos控制台

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8adbc479-e9e0-56bb-9b85-1b23ad60f277.png" alt="image-20211229000611111" style="zoom: 50%;" />

Nacos控制台成功注册GateWay网关

![image-20211229000705358](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/55b0aad3-ef66-563a-9424-4b1fac57a8f6.png)

## 3. GateWay 配置路由方式

经过上节课的讲解，我们现在知道了GateWay的基本配置路由的方式，通过YML来完成，但是实际上GateWay还提供了另外一种配置方式

### 3.1 GatewayConfig代码配置

其实这种配置方式就是通过代码的方式进行配置，也就是通过@Bean注入一个RouteLocator

那我们直接来操作一下

**具体操作**

首先我们在新建一个GateWayConfig，其实这里的配置对应的就是我们之前在YML中配置的对应内容

```java
package com.mashibing.com.cloudalibabagateway9999.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GateWayConfig {
    /*
    配置了一个id为path_msb1的路由规则
    当访问地址http://localhost:9999/msb/**
    就会转发到http://localhost:9001/nacos-provider/msb/任何地址
     */

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        // 构建多个路由routes
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        // 具体路由地址
        routes.route("path_msb",r -> r.path("/msb/**").uri("http://localhost:9001/nacos-provider")).build();
        // 返回所有路由规则
        return routes.build();
    }
}
```

我们在9001的DemoController中添加一个控制器

```java
@GetMapping(value = "/custom")
public String customTest(){
    return "网关配置测试~~costom";
}
```

这个时候我们就可以测试了，启动9999网关服务和9001微服务，然后访问地址：http://localhost:9999/msb/custom 就可以转发到9001中具体的接口中了

![image-20211230193640615](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fb3d9f68-3fac-5650-9403-d4b90e3628bf.png)

### 3.2 yaml配置

通过 `application.yml` 配置路由是 GateWay 最常用的方式，适合把路由规则、断言和过滤器集中放在配置文件中维护。

```yaml
server:
  port: 9999

spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      routes:
        - id: nacos-provider
          uri: http://localhost:9001/nacos-provider
          predicates:
            - Path=/msb/**
```

配置说明：

- `id`：路由唯一标识，建议结合服务名称命名。
- `uri`：目标服务地址，请求匹配成功后会转发到该地址。
- `predicates`：断言规则，满足条件后才会执行路由转发。
- `Path=/msb/**`：表示匹配 `/msb/` 开头的请求路径。

## 4. GateWay 实现负载均衡

在之前的学习中，我们已经掌握了GateWay的一些基本配置，包括两种配置方法，但是其中有很多的配置细节，我们没有细讲，包括我们现在的配置是写死的具体端口号，那么这节课，我们要实现通过GateWay实现负载均衡的两种方式

### 4.1 自动负载均衡

首先我们看一下我们之前的网关服务9999的yml配置，这里的配置信息，其实有一些，咱们目前是不清楚的，比如：

1. gateway.discovery.locator.enabled: true #开启自动路由功能
2. routes中的uri其实最后是不需要服务名称的，这个位置其实只需要指定的localhost:9001即可

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: true #开启自动路由功能，根据服务名称自动创建routes
      routes:  # 路由
        - id: nacos-provider #路由ID，没有固定要求，但是要保证唯一，建议配合服务名
          uri: http://localhost:9001/nacos-provider # 匹配提供服务的路由地址
          predicates: # 断言
            - Path=/msb/** # 断言，路径相匹配进行路由
```

所以这个位置我们可以把当前的配置优化为以下情况，它是一样可以启动的，我们可以进行测试启动9001和网关9999，通过网关来访问"localhost:9999/msb/**"

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      routes:  # 路由
        - id: nacos-provider #路由ID，没有固定要求，但是要保证唯一，建议配合服务名
          uri: http://localhost:9001 # 匹配提供服务的路由地址
          predicates: # 断言
            - Path=/msb/** # 断言，路径相匹配进行路由
```

GateWay还提供了和Zuul类似的自动路由规则，具体配置如下：

1. discovery.locator.enabled: true #这个配置默认为false，但是如果为true，就是开启了通过serviceId转发到具体的服务实例。“localhost:9999/ServiceID/msb/**”
2. 在配置好这些以后，我们可以直接通过服务名称来进行访问Nacos中注册的服务和对应的接口
3. 这个位置我们为了测试可以启动2个微服务9001、9002
4. GateWay在开启了自动路由以后，还自带负载均衡

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: true #是否与服务发现组件进行结合，通过serviceId转发到具体的服务实例。默认为false，设为true便开启通过服务中心的自动根据 serviceId 创建路由的功能。
```

9002和9001保持一致，Controller保持一致，负载均衡测试

```java
package com.mashibing.cloudalibabanacos9002.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/msb")
public class DemoController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/get")
    public String getServerPort(){
        return "库存-1："+serverPort;
    }

    @GetMapping(value = "custom")
    public String customTest(){
        return "测试网关配置类~~custom";
    }
}
```

测试结果：

访问地址：http://localhost:9999/nacos-provider/msb/get

| 图示                                                         |
| ------------------------------------------------------------ |
| ![image-20220105195136602](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a0b80907-6e54-5373-9481-7d010961c878.png) |
| ![image-20220105195151755](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5f13a3bc-bfa2-5aa6-b418-148b59fbe4c4.png) |

### 4.2 手动负载均衡

在以上的配置中，其实是有问题的，问题在于当前的服务名称暴露，并且太过于灵活，那么如果想解决的话，其实我们可以进行手动配置。

具体配置如下：

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: true #开启自动路由功能(此时可以关闭)
      routes: # 路由
        - id: nacos-provider #路由ID，没有固定要求，但是要保证唯一，建议配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址
          predicates: # 断言
            - Path=/msb/**
```

### 4.3 测试

我们现在开启9001/9002两个服务和9999网关服务，然后此时我们可以通过网关去访问：http://localhost:9999/msb/get

| 图示                                                         |
| ------------------------------------------------------------ |
| ![image-20220105195839464](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7ae506ef-6358-5812-a6b8-cfc81ab7ebc8.png) |
| ![image-20220105195849056](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/314172a4-86f6-598f-b4f2-cf77c38955a2.png) |

## 5. GateWay 断言 Predicate

这节课我们来研究一下GateWay的Predicate（断言）

每一个Predicate的使用，可以理解为：当满足条件后才会进行转发，如果十多个，那就是满足所有条件才会转发

### 5.1 断言种类

1. After：匹配在指定日期时间之后发生的请求。
2. Before：匹配在指定日期之前发生的请求。
3. Between：需要指定两个日期参数，设定一个时间区间，匹配此时间区间内的请求。
4. Cookie：需要指定两个参数，分别为name和regexp（正则表达式），也可以理解Key和Value，匹配具有给定名称且其值与正则表达式匹配的Cookie。
5. Header：需要两个参数header和regexp（正则表达式），也可以理解为Key和Value，匹配请求携带信息。
6. Host：匹配当前请求是否来自于设置的主机。
7. Method：可以设置一个或多个参数，匹配HTTP请求，比如GET、POST
8. Path：匹配指定路径下的请求，可以是多个用逗号分隔
9. Query：需要指定一个或者多个参数，一个必须参数和一个可选的正则表达式，匹配请求中是否包含第一个参数，如果有两个参数，则匹配请求中第一个参数的值是否符合正则表达式。
10. RemoteAddr：匹配指定IP或IP段，符合条件转发。根据客户端请求的来源 IP 地址判断是否允许匹配该路由。
11. Weight：需要两个参数group和weight（int），实现了路由权重功能，按照路由权重选择同一个分组中的路由

### 5.2 常用断言演示

以上这么多的断言，我们不可能一一的去演示，我们挑出一些比较常用的来给大家做演示，这些具体的演示在官网上都有提供，具体地址为：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories

**After**

匹配在指定时间之后发生的请求，可以对应提前上线业务

yml配置

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            - After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过，当前没有为题以后，故意改为1个小时以后
```

写一个测试类，来获取当前时间

```java
public class TestDateTime {
    public static void main(String[] args) {
        ZonedDateTime zbj = ZonedDateTime.now();//默认时区
        System.out.println(zbj);
    }
}
```

测试：

当前时间之后请求没有问题

![image-20220107151419442](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ec2153f9-3e6f-5168-96d2-7d925436ca52.png)

设置为1个小时会后访问404

![image-20220107151358287](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7f4892d5-6756-5fef-b2e5-5711a329bc89.png)

当这个After理解了以后，剩下的关于日期时间的设置Before、Between道理都是一样的，只不过是限定不同的日期时间区间

**Cookie**

需要指定两个参数，分别为name和regexp（正则表达式），也可以理解Key和Value，匹配具有给定名称且其值与正则表达式匹配的Cookie。

简单理解就是路由规则会通过获取Cookie name值和正则表达式去匹配，如果匹配上就会执行路由，如果匹配不上则不执行。

我们可以分为两种情况演示，Cookie匹配，Cookie不匹配

yml

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            # - After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过
            - Cookie=username,[a-z]+ # 匹配Cookie的key和value（正则表达式）
```

那么我们通过postman来进行测试

当Cookie匹配时：

![image-20220107162754644](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9bbef4ae-622d-5fa9-88d5-c6ab69138c67.png)

![image-20220107162819910](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9fe236a0-3f00-5a9c-b97c-648a8d959bb4.png)

当Cookie不匹配时：

![image-20220107162907000](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4245429f-3392-52fb-ae0c-0ca1a3dcf8f2.png)

![image-20220107162922622](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d9874358-84d3-5d57-ad28-61babb4d9d76.png)

**Header**

需要两个参数header和regexp（正则表达式），也可以理解为Key和Value，匹配请求携带信息。

实际上就是请求头携带的信息，官网给出的案例是X-Request-Id，那我们就用这个做实验

yml

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            #- After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过
            #- Cookie=username,[a-z]+
            - Header=X-Request-Id,\d+ #表示数字
```

测试

![image-20220107165412353](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5b0d903d-bba8-571b-a20f-d11d04d8d2a8.png)

**Host**

匹配当前请求是否来自于设置的主机。

这个比较比较简单，我们直接来试验

yml

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            #- After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过
            #- Cookie=username,[a-z]+
            #- Header=X-Request-Id,\d+ #表示数字
            - Host=**.mashibing.com #匹配当前的主机地址发出的请求
```

postman

![image-20220107165950636](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9517ba0f-30c3-5de3-ab1f-0abe13830929.png)

**Method**

可以设置一个或多个参数，匹配HTTP请求，比如GET、POST

yml

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            #- After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过
            #- Cookie=username,[a-z]+
            #- Header=X-Request-Id,\d+ #表示数字
            #- Host=**.mashibing.com #匹配当前的主机地址发出的请求
            - Method=GET,POST # 匹配GET请求或者POST请求
```

**Query**

需要指定一个或者多个参数，一个必须参数和一个可选的正则表达式，匹配请求中是否包含第一个参数，如果有两个参数，则匹配请求中第一个参数的值是否符合正则表达式。

yml

```java
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
            #- After=2022-01-07T14:39:10.529+08:00[Asia/Shanghai] # 在这个时间之后的请求都能通过
            #- Cookie=username,[a-z]+
            #- Header=X-Request-Id,\d+ #表示数字
            #- Host=**.mashibing.com #匹配当前的主机地址发出的请求
            #- Method=GET,POST
            - Query=id,.+ # 匹配请求参数，这里如果需要匹配多个参数，可以写多个Query
```

测试：

![image-20220107172856256](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a278dc94-8b45-54f9-a03c-cb8dbdd0e0a2.png)

**Weight**

需要两个参数group和weight（int），实现了路由权重功能，按照路由权重选择同一个分组中的路由

官网提供的演示yml

```java
spring:
  cloud:
    gateway:
      routes:
      - id: weight_high
        uri: https://weighthigh.org
        predicates:
        - Weight=group1, 8
      - id: weight_low
        uri: https://weightlow.org
        predicates:
        - Weight=group1, 2
```

该路由会将约 80% 的流量转发到[weighthigh.org](https://weighthigh.org/)，将约 20% 的流量[转发](https://weighlow.org/)到[weightlow.org](https://weighlow.org/)

### 5.3 总结

Predicate就是为了实现一组匹配规则，让请求过来找到对应的Route进行处理。

## 6. GateWay 的 Filter

路由过滤器允许以某种方式修改传入的 HTTP 请求或传出的 HTTP 响应。路由过滤器的范围是特定的路由。Spring Cloud Gateway 包含许多内置的 GatewayFilter 工厂。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/332403a7-54a6-5d2e-8030-6eb9da3f6cbe.png" alt="image-20220107175844624" style="zoom: 33%;" />

### 6.1 内置 Filter

1. GateWay内置的Filter生命周期为两种：pre（业务逻辑之前）、post（业务逻辑之后）
2. GateWay本身自带的Filter分为两种： GateWayFilter（单一）、GlobalFilter（全局）
3. 单一的有32种，全局的有9种
4. 官方网址：https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#global-filters

这个数量实在是太惊人了，而且用法都比较简单，所以这里大家可以自行根据官网给出的演示进行练习，在这里给大家举一个例子



>GatewayFilter：局部过滤器，跟着某条路由走。
>GlobalFilter：全局过滤器，所有请求都会经过。



**StripPrefix**

该StripPrefix有一个参数，parts。该parts参数指示在将请求发送到下游之前要从请求中剥离的路径中的部分数。

案例：比如我们现在在9001微服务上加一个context-path配置

```yml
server:
  port: 9001
  servlet:
    context-path: /nacos-provider
.....
```

现在9001的访问路径变为http://localhost:9001/nacos-provider/msb/get

目前的网关9999配置信息为

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
```

为了保证断言能够匹配，此时通过网关的访问地址应该改为：http://localhost:9999/msb/nacos-provider/msb/get，但是出现了404因为多了一层路径http://localhost:9001/msb/nacos-provider/msb/get

![image-20220107192432401](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cf09e118-cbfc-5915-bc27-775da9fee973.png)

那么如果想要解决，我们应该在转发的时候去地址中最前面的/msb，所以我们就需要使用FIlter：StripPrefix

yml

```yaml
server:
  port: 9999
spring:
  application:
    name: cloud-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: false # 是否与服务发现进行组合，通过ServiceID转发到具体的服务实例，默认为false，
                        # 设置为true便开启通过服务注册中心来自动根据SeviceID创建路由功能。
      routes:
        - id: nacos-provider # 路由ID，唯一不可重复，最好配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址 lb://代表开启负载均衡
          predicates: # 断言
            - Path=/msb/** # 匹配对应地址
          filters:
            - StripPrefix=1 # 去掉地址中的第一部分
          # http://localhost:9999/msb/nacos-provider/msb/get
          # http://localhost:9999/nacos-provider/msb/get
```

最后我们来看效果，成功转发

![image-20220107193321333](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e8fef5a9-1e2b-5def-996a-7861a5066570.png)

### 6.2 自定义 Filter

**6.2.1 自定义全局过滤器**

要实现GateWay自定义过滤器，那么我们需要实现两个接口

- GlobalFilter
- Ordered

**演示**

首先我们新建一个类MyFilter

```java
@Component
@Slf4j
public class MyFilter implements Ordered, GlobalFilter {
    /**
     * @param exchange 可以拿到对应的request和response
     * @param chain 过滤器链
     * @return 是否放行
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String username = exchange.getRequest().getQueryParams().getFirst("username");
        log.info("*************MyFilter:"+new Date());
        if(username == null){
            log.info("**********用户名为null，非法用户，请求被拒绝！");
            //如果username为空，返回状态码为406，不可接受的请求
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    /**
     * 加载过滤器的顺序
     * @return 整数数字越小优先级越高
     */
    @Override
    public int getOrder() {
        return 0;
    }
}
```

测试，此时我们的逻辑是在访问同时要传入username参数同时不能为空，否则不会放行本次请求。

传入正确参数：

![image-20220107201951485](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6369fd57-22b6-51e5-8aa2-c931a1c46df2.png)

未传入正确参数：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4b75ffd8-5950-5f98-8019-b2b77c387592.png" alt="image-20220107202003694" style="zoom:33%;" />

**6.2.2 自定义单个过滤器**

自定义单个过滤器也叫局部过滤器，它只作用于指定路由。和 `GlobalFilter` 不同，局部过滤器需要继承 `AbstractGatewayFilterFactory`，然后在路由配置的 `filters` 中引用。

下面定义一个 `CheckAuthGatewayFilterFactory`，用于检查请求参数中是否携带 `token`。

```java
@Component
public class CheckAuthGatewayFilterFactory
        extends AbstractGatewayFilterFactory<CheckAuthGatewayFilterFactory.Config> {

    public CheckAuthGatewayFilterFactory() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            String token = exchange.getRequest().getQueryParams().getFirst(config.getParamName());
            if (token == null || token.isEmpty()) {
                exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
                return exchange.getResponse().setComplete();
            }
            return chain.filter(exchange);
        };
    }

    @Override
    public List<String> shortcutFieldOrder() {
        return Collections.singletonList("paramName");
    }

    public static class Config {
        private String paramName;

        public String getParamName() {
            return paramName;
        }

        public void setParamName(String paramName) {
            this.paramName = paramName;
        }
    }
}
```

配置到指定路由中：

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: nacos-provider
          uri: lb://nacos-provider
          predicates:
            - Path=/msb/**
          filters:
            - CheckAuth=token
```

此时只有访问 `/msb/**` 这条路由时才会执行 `CheckAuth` 过滤器。请求中携带 `token` 参数时放行，未携带时返回 `401 Unauthorized`。
