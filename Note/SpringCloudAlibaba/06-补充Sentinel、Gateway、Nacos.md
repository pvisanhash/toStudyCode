# 附加内容

## 目录

- [1 Sentinel 整合 Gateway](#1-sentinel-整合-gateway)
  - [1.1 网关限流概述](#1.1-网关限流概述)
  - [1.2 官方定义](#1.2-官方定义)
  - [1.3 网关流控控制台](#1.3-网关流控控制台)
    - [1.3.1 基础使用方式](#1.3.1-基础使用方式)
    - [1.3.2 API 分组](#1.3.2-api-分组)
    - [1.3.3 精准匹配演示](#1.3.3-精准匹配演示)
    - [1.3.4 前缀匹配演示](#1.3.4-前缀匹配演示)
    - [1.3.5 正则表达式匹配演示](#1.3.5-正则表达式匹配演示)
- [2 Nacos Config 深入与原理](#2-nacos-config-深入与原理)
  - [2.1 Nacos 服务配置中心基础](#2.1-nacos-服务配置中心基础)
    - [2.1.1 基础配置](#2.1.1-基础配置)
    - [2.1.2 POM 依赖](#2.1.2-pom-依赖)
    - [2.1.3 YML 配置](#2.1.3-yml-配置)
    - [2.1.4 主启动类](#2.1.4-主启动类)
    - [2.1.5 业务类](#2.1.5-业务类)
    - [2.1.6 Nacos 配置规则](#2.1.6-nacos-配置规则)
    - [2.1.7 Nacos 平台创建配置](#2.1.7-nacos-平台创建配置)
    - [2.1.8 自动配置更新](#2.1.8-自动配置更新)
    - [2.1.9 测试验证](#2.1.9-测试验证)
  - [2.2 多环境与共享配置进阶](#2.2-多环境与共享配置进阶)
    - [2.2.1 自定义 Data ID 配置](#2.2.1-自定义-data-id-配置)
    - [2.2.2 自定义 Data ID 具体配置](#2.2.2-自定义-data-id-具体配置)
    - [2.2.3 不同微服务共享配置](#2.2.3-不同微服务共享配置)
    - [2.2.4 shared-configs 方式实现](#2.2.4-shared-configs-方式实现)
    - [2.2.5 extension-configs 方式实现](#2.2.5-extension-configs-方式实现)
    - [2.2.6 整体配置优先级](#2.2.6-整体配置优先级)
  - [2.3 Nacos Config 动态刷新原理](#2.3-nacos-config-动态刷新原理)
    - [2.3.1 动态监听](#2.3.1-动态监听)
    - [2.3.2 长轮询刷新机制](#2.3.2-长轮询刷新机制)
    - [2.3.3 短轮询](#2.3.3-短轮询)
    - [2.3.4 长轮询](#2.3.4-长轮询)
  - [2.4 客户端源码分析](#2.4-客户端源码分析)
    - [2.4.1 入口分析](#2.4.1-入口分析)
    - [2.4.2 ClientWorker](#2.4.2-clientworker)
    - [2.4.3 checkConfigInfo 方法](#2.4.3-checkconfiginfo-方法)
    - [2.4.4 LongPollingRunnable.run()](#2.4.4-longpollingrunnable.run)
    - [2.4.5 checkLocalConfig](#2.4.5-checklocalconfig)
    - [2.4.6 checkListenerMd5()](#2.4.6-checklistenermd5)
    - [2.4.7 检测服务端配置](#2.4.7-检测服务端配置)
    - [2.4.8 checkUpdateDataIds()](#2.4.8-checkupdatedataids)
    - [2.4.9 checkUpdateConfigStr()](#2.4.9-checkupdateconfigstr)
    - [2.4.10 客户端长轮询机制总结](#2.4.10-客户端长轮询机制总结)
  - [2.5 服务端源码分析](#2.5-服务端源码分析)
    - [2.5.1 doPollingConfig()](#2.5.1-dopollingconfig)
    - [2.5.2 addLongPollingClient()](#2.5.2-addlongpollingclient)
    - [2.5.3 ClientLongPolling()](#2.5.3-clientlongpolling)
    - [2.5.4 allSubs](#2.5.4-allsubs)
    - [2.5.5 LongPollingService 构造方法](#2.5.5-longpollingservice-构造方法)
    - [2.5.6 DataChangeTask](#2.5.6-datachangetask)
- [3 Nacos Distro 一致性协议](#3-nacos-distro-一致性协议)
  - [3.1 协议简介](#3.1-协议简介)
  - [3.2 设计思想](#3.2-设计思想)
  - [3.3 Nacos 为什么需要一致性协议](#3.3-nacos-为什么需要一致性协议)
  - [3.4 数据初始化](#3.4-数据初始化)
  - [3.5 数据校验](#3.5-数据校验)
  - [3.6 写操作](#3.6-写操作)
  - [3.7 读操作](#3.7-读操作)
  - [3.8 总结](#3.8-总结)

---

## 1 Sentinel 整合 Gateway

> 来源：`附加/01/Sentinel整合GateWay.md`


### 1.1 网关限流概述

在微服务系统中，网关提供了微服务系统的统一入口，所以我们在做限流的时候，肯定是要在网关层面做一个流量的控制，Sentinel 支持对 Spring Cloud Gateway、Zuul 等主流的 API Gateway 进行限流。

![image-20220505163401151](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fc7b5588-58f8-57f2-9c04-270e417bb4b1.png)

![sentinel-api-gateway-common-arch](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/59ec8631-e8e6-51bb-9b70-68dacc34497c.png)

### 1.2 官方定义

Sentinel 1.6.0 引入了 Sentinel API Gateway Adapter Common 模块，此模块中包含网关限流的规则和自定义 API 的实体和管理逻辑：

- GatewayFlowRule：网关限流规则，针对 API Gateway 的场景定制的限流规则，可以针对不同 route 或自定义的 API 分组进行限流，支持针对请求中的参数、Header、来源 IP 等进行定制化的限流。
- ApiDefinition：用户自定义的 API 定义分组，可以看做是一些 URL 匹配的组合。比如我们可以定义一个 API 叫 my_api，请求 path 模式为 /foo/** 和 /baz/**的都归到 my_api 这个 API 分组下面。限流的时候可以针对这个自定义的 API 分组维度进行限流。

其中网关限流规则 GatewayFlowRule的字段解释如下：

- resource：资源名称，可以是网关中的 route 名称或者用户自定义的 API 分组名称。

- resourceMode：规则是针对 API Gateway 的 route（RESOURCE_MODE_ROUTE_ID）还是用户在 Sentinel 中定义的 API 分组（RESOURCE_MODE_CUSTOM_API_NAME），默认是 route。

- grade：限流指标维度，同限流规则的 grade 字段。

- count：限流阈值

- intervalSec：统计时间窗口，单位是秒，默认是 1 秒。

- controlBehavior：流量整形的控制效果，同限流规则的 `controlBehavior` 字段，目前支持快速失败和匀速排队两种模式，默认是快速失败。

- burst：应对突发请求时额外允许的请求数目。

- maxQueueingTimeoutMs：匀速排队模式下的最长排队时间，单位是毫秒，仅在匀速排队模式下生效。

- paramItem

  ：参数限流配置。若不提供，则代表不针对参数进行限流，该网关规则将会被转换成普通流控规则；否则会转换成热点规则。其中的字段：

  - parseStrategy：从请求中提取参数的策略，目前支持提取来源 IP（PARAM_PARSE_STRATEGY_CLIENT_IP）、Host（PARAM_PARSE_STRATEGY_HOST）、任意 Header（PARAM_PARSE_STRATEGY_HEADER）和任意 URL 参数（PARAM_PARSE_STRATEGY_URL_PARAM）四种模式。
  - fieldName：若提取策略选择 Header 模式或 URL 参数模式，则需要指定对应的 header 名称或 URL 参数名称。
  - pattern：参数值的匹配模式，只有匹配该模式的请求属性值会纳入统计和流控；若为空则统计该请求属性的所有值。（1.6.2 版本开始支持）
  - matchStrategy：参数值的匹配策略，目前支持精确匹配（PARAM_MATCH_STRATEGY_EXACT）、子串匹配（PARAM_MATCH_STRATEGY_CONTAINS）和正则匹配（PARAM_MATCH_STRATEGY_REGEX）。（1.6.2 版本开始支持）

用户可以通过 GatewayRuleManager.loadRules(rules) 手动加载网关规则，或通过 GatewayRuleManager.register2Property(property) 注册动态规则源动态推送（推荐方式）。

### 1.3 网关流控控制台

Sentinel 1.6.3 引入了网关流控控制台的支持，用户可以直接在 Sentinel 控制台上查看 API Gateway 实时的 route 和自定义 API 分组监控，管理网关规则和 API 分组配置。

#### 1.3.1 基础使用方式

首先我们现在有9999网关服务和9002库存服务

1. 首先在网关中添加依赖：

```java
<dependency>
     <groupId>com.alibaba.cloud</groupId>
     <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
<dependency>
     <groupId>com.alibaba.csp</groupId>
     <artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
</dependency>
<dependency>
     <groupId>com.alibaba.cloud</groupId>
     <artifactId>spring-cloud-alibaba-sentinel-gateway</artifactId>
</dependency>
```

2. 网关服务的配置

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
    sentinel:
      transport:
        # 配置Sentinel dashboard地址
        dashboard: localhost:8080
        # 默认8719端口，键入被占用会自动从8719+1，直到找到未被占用的端口
        port: 8719
    gateway:
      discovery:
        locator:
          enabled: true
        #enabled: true #开启注册中心路由功能
      routes:  # 路由
        - id: nacos-provider #路由ID，没有固定要求，但是要保证唯一，建议配合服务名
          uri: lb://nacos-provider # 匹配提供服务的路由地址
          predicates: # 断言
            - Path=/msb/** # 断言，路径相匹配进行路由
```

3. 然后在Sentinel控制台中增加Route ID对应的流控



此时的设置为针对 nacos-provider这个路由id对应的流控类型为qps，阈值为1。

间隔：当每秒的请求超过QPS值，启动间隔。2分钟内所有调用这个接口直接返回。不去调用业务代码。

Burst size: 应对突发请求时额外允许的请求数目。

4. 此时我们可以使用jmeter进行测试

![image-20220505185814947](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f918cc5b-e075-5929-81dc-17018bfbee90.png)

#### 1.3.2 API 分组

当然以上这是最简单的一种测试方式，那么在我们正常生产环境中，我们可以只会针对某个接口来进行限流，所以其实我们还可以配置对应的API分组

![image-20220505190031512](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f4e0e990-7eb7-5f43-aefb-6338f626d2e1.png)

这里对应API名称可以随意编写：分组的意思就是可以将一类的流控接口变成一个API分组，这里有三种方式

精准：精准匹配对应的请求地址

前缀：匹配对应的地址前缀

正则：通过正则的方式匹配

#### 1.3.3 精准匹配演示

![image-20220505191254350](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/91c9e1ec-d065-5c6a-95b4-2025073affad.png)

测试结果 http://localhost:9999/msb/get：

![image-20220505191309334](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/0e4e0abc-4299-5b32-862e-eece93cdead1.png)

#### 1.3.4 前缀匹配演示

![image-20220505191858688](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3d3f94b9-429d-59d1-8adb-aa3b35e52921.png)

测试 http://localhost:9999/msb/login/aaa：

![image-20220505191905113](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fdd70be6-f91f-5b39-bcfe-29e4b28aa2a9.png)

#### 1.3.5 正则表达式匹配演示

生产环境中可能还会出现这种路径：

```
msb/login/aaa
msb/login/v1/aaa
/msb/login/** 这种方式都可以匹配所以需要用到正则
```

所以想要解决这种问题，其实我们可以把详细的也就是msb/login/v1/aaa这种地址用前缀来匹配，那么对应的这种msb/login/aaa路径就可以直接使用正则来进行匹配，比如：

```java
/msb/login/([a-z]+)
```

具体演示：

![image-20220505193219147](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/96a8d618-9799-5362-ade6-b37800839b5b.png)

测试 http://localhost:9999/msb/login/15011339999：

![image-20220505193230598](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d31d03d7-76c8-54a7-9774-9b4324fbd513.png)

---

## 2 Nacos Config 深入与原理

> 来源：`附加/02/Nacos Config深入+原理.md`


### 2.1 Nacos 服务配置中心基础



#### 2.1.1 基础配置

Nacos不仅仅可以作为注册中心来使用，同时它支持作为配置中心

![image-20210924140930824](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f9f378c4-ce30-547b-9468-fb9767b0aa5e.png)



首先我们还是新建Model：cloudalibaba-config-3377

#### 2.1.2 POM 依赖

​	这里我们主要要引入的是此依赖，这个依赖依据在官网上可以找到:https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_an_example_of_using_nacos_discovery_for_service_registrationdiscovery_and_call

```java
<dependency> 
    <groupId> com.alibaba.cloud </groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-config </artifactId> 
</dependency>
```



#### 2.1.3 YML 配置

​	要注意的是这里我们要配置两个，因为Nacos同SpringCloud-config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置之后，才能保证项目的正常启动。

​	springboot中配置文件的加载是存在优先级顺序的，bootstrap优先级高于application

​	分别要配置的是，这里bootstrap.yml配置好了以后，作用是两个，第一个让3377这个服务注册到Nacos中，第二个作用就是去Nacos中去读取指定后缀为yaml的配置文件：

bootstrap.yml

```java
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
```

application.yml

```java
spring:
  profiles:
    active: dev # 表示开发环境
```



#### 2.1.4 主启动类

```java
package com.mashibing.cloudalibabaconfig3377;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class CloudalibabaConfig3377Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaConfig3377Application.class, args);
    }

}
```



#### 2.1.5 业务类

​	这里的@RefreshScope实现配置自动更新，意思为如果想要使配置文件中的配置修改后不用重启项目即生效，可以使用@RefreshScope配置来实现

```java
package com.mashibing.cloudalibabaconfig3377.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RefreshScope //支持Nacos的动态刷新功能
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    @Value("${/config/info}")
    public String getConfigInfo(){
        return configInfo;
    }

}
```

#### 2.1.6 Nacos 配置规则

​	在 Nacos Spring Cloud 中，`dataId` 的完整格式如下（详情可以参考官网 https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html）：

```java
${prefix}-${spring.profiles.active}.${file-extension}
```

	1. `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。
	2. `spring.profiles.active` 即为当前环境对应的 profile，注意：**当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`**（不能删除）
	3. `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。
	4. 通过 Spring Cloud 原生注解 `@RefreshScope` 实现配置自动更新：
	5. 所以根据官方给出的规则我们最终需要在Nacos配置中心添加的配置文件的名字规则和名字为：

```java
# ${spring.application.name}-${spring.profiles.active}.${file-extension}
# nacos-config-client-dev.yaml
# 微服务名称-当前环境-文件格式
```

![image-20210924160100192](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9b85c700-4e83-555d-9f76-bf2bf63bb8da.png)

#### 2.1.7 Nacos 平台创建配置

增加配置

![image-20210924154802374](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/34706fa4-c37a-5d38-aaa2-9e0d04c68b33.png)

![image-20210924160459681](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/066730f0-ed87-54be-9a30-b24cac3a56ba.png)

```java
config: 
    info: nacos config center,version = 1
```

然后在配置中心就会看到刚刚发布的配置

![image-20210924155315617](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c2be8767-49e7-57d4-9ff9-3bd32e97926d.png)



#### 2.1.8 自动配置更新

修改Nacos配置，不需要重启项目即可自动刷新

![image-20210924195241683](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/89155684-eab3-52a4-8263-c8c54ac55a40.png)

修改版本号为2，点击发布

![image-20210924195325905](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/dd0f1c50-3c2b-55d5-9a66-a6ddc33df23e.png)

#### 2.1.9 测试验证

 启动服务访问服务来测试（没有修改之前是1，修改之后不需要重启项目既可以直接获取最新配置）：http://localhost:3377/config/info

![image-20210924182159179](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/213ad84e-2f3a-57a7-9613-4b7dce589904.png)

### 2.2 多环境与共享配置进阶

#### 2.2.1 自定义 Data ID 配置

在实际的开发过程中，我们的项目所用到的配置参数有的时候并不需要根据不同的环境进行区分，生产、测试、开发环境所用到的参数值是相同的。那么解决同一服务在多环境中，引用相同的配置的问题？Nacos Config也提供了相应的解决方案。

![image-20220511171948719](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1f181312-c58b-57e8-bafe-f4edce0e5dc4.png)

那么我们可以通过服务名+拓展名的方式，来实现同一个微服务下不同的环境，共享的配置文件。

#### 2.2.2 自定义 Data ID 具体配置

我们在Nacos Config中添加配置，data_id为configdemo.yaml

![image-20220511173514123](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4bea1b90-3edc-52ae-b120-e8e3bc6c1185.png)



控制器代码更改

```java
@RestController
@RefreshScope //支持Nacos动态刷新功能
public class ConfigController {

    @Value("${config.info}")
    private String configInfo;

    //通用
    @Value("${config.common}")
    private String configCommon;

    @GetMapping("/config/info")
    public String getConfigInfo(){
        return configInfo;
    }

    //通用
    @GetMapping("/config/common")
    public String getCommon(){
        return configCommon;
    }
}
```

测试可以直接读取：http://localhost:3377/config/common

![image-20220511173806036](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4fc6df41-83d4-5b16-a5d6-4b765f6c9df2.png)

#### 2.2.3 不同微服务共享配置

当前这种配置方式是最基础的配置方式，但是在实际开发中骂我们一般会涉及到多个微服务之间共享配置。比如说redis地址，服务注册中心公共组件等等，那么这些组件是多个微服务共享的，所以我们可以使用Nacos Config提供的共享配置方式来配置共享的配置文件。

配置文件名字没有固定要求

![image-20220511180856987](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b81b19f7-383c-5d9e-8c87-c860f9a96ab3.png)



#### 2.2.4 shared-configs 方式实现


在nacos-config中添加 redis.yml，添加配置 redisip: 111.11.11.01

![image-20220511181556416](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/01931e5e-7b4d-5993-bb0b-50ae0d36f9ac.png)

更改yml配置

```
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        shared-configs[0]:  #shared-configs是一个列表，可以添加多项
          data_id: redis.yml #具体配置
          group: DEFAULT_GROUP #默认可以不写
          refresh: true #是否开启自动刷新，默认为false，必须搭配@RefreshScope注解
```

更改测试类

```java
@RestController
@RefreshScope //支持Nacos动态刷新功能
public class ConfigController {

    @Value("${config.info}")
    private String configInfo;

    //通用
    @Value("${config.common}")
    private String configCommon;

    @GetMapping("/config/info")
    public String getConfigInfo(){
        return configInfo;
    }

    //共享
    @Value("${redisip}")
    private String redisIp;

    //通用
    @GetMapping("/config/common")
    public String getCommon(){
        return configCommon;
    }

    //通用
    @GetMapping("/config/redisip")
    public String getRedisIp(){
        return redisIp;
    }
}
```

测试结果：

![image-20220511183237221](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e1b0f697-fa00-5994-af91-1bf14bd12b1b.png)

当然也支持多个配置，只需要在shared-configs[n]，增加n的数值即可

```java
cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        shared-configs[0]:  #shared-configs是一个列表，可以添加多项
          data_id: redis.yml #具体配置
          group: DEFAULT_GROUP #默认可以不写
          refresh: true #是否开启自动刷新，默认为false，必须搭配@RefreshScope注解
        shared-configs[1]: #shared-configs是一个列表，可以添加多项
          data_id: common.yml #具体配置
          group: DEFAULT_GROUP #默认可以不写
          refresh: true #是否开启自动刷新，默认为false，必须搭配@RefreshScope注解
```



**注意：多个 Data Id 同时配置时，他的优先级关系是 `spring.cloud.nacos.config.extension-configs[n].data-id` 其中 n 的值越大，优先级越高。**

#### 2.2.5 extension-configs 方式实现

其实以上的实现还可以通过extension-configs方式来完成，其实作用基本一致，只不过语义上可以更好的区分，如果我们需要在一个微服务上配置多个配置文件，可以使用extension-configs，如果需要多个配置文件共享，可以使用shard-configs配置方式，当然其实两种方式所实现的效果和配置方法基本一致。

所以通过自定义扩展的 Data Id 配置，既可以解决多个应用间配置共享的问题，又可以支持一个应用有多个配置文件。


更改yml，只需要将shared-configs改为extension-configs即可

```java
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        extension-configs[0]:  #shared-configs是一个列表，可以添加多项
          data_id: redis.yml #具体配置
          group: DEFAULT_GROUP #默认可以不写
          refresh: true #是否开启自动刷新，默认为false，必须搭配@RefreshScope注解
        extension-configs[1]: #shared-configs是一个列表，可以添加多项
          data_id: common.yml #具体配置
          group: DEFAULT_GROUP #默认可以不写
          refresh: true #是否开启自动刷新，默认为false，必须搭配@RefreshScope注解
```

#### 2.2.6 整体配置优先级

Spring Cloud Alibaba Nacos Config 目前提供了三种配置能力从 Nacos 拉取相关的配置。

- A: 通过 `spring.cloud.nacos.config.shared-configs[n].data-id` 支持多个共享 Data Id 的配置
- B: 通过 `spring.cloud.nacos.config.extension-configs[n].data-id` 的方式支持多个扩展 Data Id 的配置
- C: 通过内部相关规则(应用名、应用名+ Profile )自动生成相关的 Data Id 配置

当三种方式共同使用时，他们的一个优先级关系是:A < B < C



### 2.3 Nacos Config 动态刷新原理

#### 2.3.1 动态监听

所谓动态监听，简单理解就是指Nacos会自动找到那些服务已经注册，而对比来说静态监听，就是指需要有指定配置指定的服务。

其实在这里我们就要说一下客户端和服务端的交互方式，无非就是推和拉

- Push：表示服务端主动将数据变更信息推送给客户端
  - 服务需要维持客户端的长连接，因为需要知道具体推送的客户端
  - 客户端耗费内存高，因为需要保存所有客户端的连接，并且需要检测连接有效性（心跳机制）
- Pull：表示客户端主动去服务端拉取数据
  - 需要定时拉取数据
  - 缺点：时效性，数据实时性，无效请求

#### 2.3.2 长轮询刷新机制

![image-20220511192400867](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d25cae65-d685-500c-9115-b0368efbbc51.png)

核心：Nacos动态刷新机制，采用推和拉的优点，避免缺点。

Nacos做配置中心的时候，配置数据的交互模式是有服务端push推送的，还是客户端pull拉取的？

Nacos客户端发送一个请求连接到服务端，然后服务端中会有一个29.5+0.5s的一个hold期，然后服务端会将此次请求放入到allSubs队列中等待，触发服务端返回结果的情况只有两种，第一种是时间等待了29.5秒，配置未发生改变，则返回未发生改变的配置；第二种是操作Nacos Dashboard或者API对配置文件发生一次变更，此时会触发配置变更的事件，发送一条LocalDataEvent消息，此时服务端监听到消息，然后遍历allSubs队列，根据对应的groupId找到配置变更的这条ClientLongPolling任务，并且通过连接返回给客户端

Nacos动态刷新避免了服务端对客户端进行push操作时需要保持双方的心跳连接，同样也避免了客户端对服务端进行pull操作时数据的时效性问题，不必频繁去拉去服务端的数据

通过上面原理的初步了解，显而易见，答案是：客户端主动拉取的，通长轮询的方式（Long Polling）的方式来获取配置数据。

#### 2.3.3 短轮询

不管服务端的配置是否发生变化，不停发起请求去获取配置，比如支付订单场景中前端不断轮询订单支付的状态，这样的坏处显而易见，由于配置并不会频繁发生变更，如果是一直发请求，一定会对服务端造成很大的压力。还会造成数据推送的延迟，比如每10秒请求一次配置，如果在第11秒的时候配置更新，那么推送将会延迟9秒，等待下一次请求这就是短轮询，为了解决短轮询的问题，有了长轮询的方案

#### 2.3.4 长轮询

长轮询不是什么新技术，它其实就是由服务端控制响应客户端请求结果的返回时间，来减少客户端无效请求的一种优化手段，其实对于客户端来说，长轮询的使用并没有本质上的区别，客户端发起请求后，服务端不会立即返回请求结果，而是将请求hold挂起一段时间，如果此时间段内配置数据发生变更，则立即响应客户端，若一直无变更则等到指定超时时间后响应给客户端结果，客户端重新发起长链接

![Nacos config 动态刷新机制详解](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6d693f69-9915-5225-af46-c54b41d776d1.png)

### 2.4 客户端源码分析

#### 2.4.1 入口分析

分析的入口为NacosConfigBootstrapConfiguration，那么其实这个类型就是我们在写对应Nacos.config配置时候走的对应类型，从@ConditionalOnProperty注解中就可以看到，它对应加载的配置名称，就是我们在使用配置中心时候对应的配置，代表是否使用配置中心，默认为true

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnProperty(name = "spring.cloud.nacos.config.enabled", matchIfMissing = true)
public class NacosConfigBootstrapConfiguration {

	@Bean
	@ConditionalOnMissingBean
	public NacosConfigProperties nacosConfigProperties() {
		return new NacosConfigProperties();
	}

	@Bean
	@ConditionalOnMissingBean
	public NacosConfigManager nacosConfigManager(
			NacosConfigProperties nacosConfigProperties) {
		return new NacosConfigManager(nacosConfigProperties);
	}

	@Bean
	public NacosPropertySourceLocator nacosPropertySourceLocator(
			NacosConfigManager nacosConfigManager) {
		return new NacosPropertySourceLocator(nacosConfigManager);
	}

}
```

那么这里我们主要要关注的是nacosConfigManager这个类型，关注它的原因就是因为这其中创建了一个单例的ConfigService

```java
public class NacosConfigManager {

	private static final Logger log = LoggerFactory.getLogger(NacosConfigManager.class);

	private static ConfigService service = null;

	private NacosConfigProperties nacosConfigProperties;

	public NacosConfigManager(NacosConfigProperties nacosConfigProperties) {
		this.nacosConfigProperties = nacosConfigProperties;
		// Compatible with older code in NacosConfigProperties,It will be deleted in the
		// future.
		createConfigService(nacosConfigProperties);
	}

	/**
	 * Compatible with old design,It will be perfected in the future.
	 */
    // 静态方法，单例创建 NacosConfigProperties为我们在微服务中配置的对应内容
	static ConfigService createConfigService(
			NacosConfigProperties nacosConfigProperties) {
		if (Objects.isNull(service)) {
			synchronized (NacosConfigManager.class) {
				try {
					if (Objects.isNull(service)) {
						service = NacosFactory.createConfigService(
								nacosConfigProperties.assembleConfigServiceProperties());
					}
				}
				catch (NacosException e) {
					log.error(e.getMessage());
					throw new NacosConnectionFailureException(
							nacosConfigProperties.getServerAddr(), e.getMessage(), e);
				}
			}
		}
		return service;
	}

	public ConfigService getConfigService() {
		if (Objects.isNull(service)) {
			createConfigService(this.nacosConfigProperties);
		}
		return service;
	}

	public NacosConfigProperties getNacosConfigProperties() {
		return nacosConfigProperties;
	}
}    
```

此时我们通过这里可以了解到，其实说白了就是在这个位置会创建一个ConfigService，那么我们可以继续跟踪发现，这个ConfigService其实是一个接口，那么它对应的实现类就是NacosConfigService，首先我们关注这个类型的初始化（构造方法）

当这个类型被实例化后，会做两件事

1. 初始化一个HttpAgent，这里又用到了装饰器模式，实际工作的类是ServerHttpAgent, MetricsHttpAgent
2. ClientWorker， 客户端的一个工作类，agent作为参数传入到clientworker，可以基本猜测到里面会用到agent做一些远程相关的操作

```java
public NacosConfigService(Properties properties) throws NacosException {
    ValidatorUtils.checkInitParam(properties);
    String encodeTmp = properties.getProperty(PropertyKeyConst.ENCODE);
    if (StringUtils.isBlank(encodeTmp)) {
        this.encode = Constants.ENCODE;
    } else {
        this.encode = encodeTmp.trim();
    }
    initNamespace(properties);
	
    // 初始化网络通讯组件
    this.agent = new MetricsHttpAgent(new ServerHttpAgent(properties));
    this.agent.start();
    // 核心点：ClientWorker
    this.worker = new ClientWorker(this.agent, this.configFilterChainManager, properties);
}
```

#### 2.4.2 ClientWorker

既然找到了核心点ClientWorker，那么我们继续向下来看

第一个线程池是只拥有一个线程用来执行定时任务的 executor，executor 每隔 10ms 就会执行一次 checkConfigInfo() 方法，从方法名上可以知道是每 10 ms 检查一次配置信息。

第二个线程池是一个普通的线程池，从 ThreadFactory 的名称可以看到这个线程池是做长轮询的。

```java
public ClientWorker(final HttpAgent agent, final ConfigFilterChainManager configFilterChainManager,
                    final Properties properties) {
    this.agent = agent;
    this.configFilterChainManager = configFilterChainManager;//初始化配置过滤器

    // Initialize the timeout parameter
	//初始化配置
    init(properties);

--------------------先看init方法--------------------------------
private void init(Properties properties) {
	// 超时时间CONFIG_LONG_POLL_TIMEOUT 30秒
    timeout = Math.max(ConvertUtils.toInt(properties.getProperty(PropertyKeyConst.CONFIG_LONG_POLL_TIMEOUT),
                                          Constants.CONFIG_LONG_POLL_TIMEOUT), Constants.MIN_CONFIG_LONG_POLL_TIMEOUT);

    taskPenaltyTime = ConvertUtils
        .toInt(properties.getProperty(PropertyKeyConst.CONFIG_RETRY_TIME), Constants.CONFIG_RETRY_TIME);

    this.enableRemoteSyncConfig = Boolean
        .parseBoolean(properties.getProperty(PropertyKeyConst.ENABLE_REMOTE_SYNC_CONFIG));
} 
    
    
----------------------------------------------------
    

    // 初始化一个定时调度的线程池，线程数量为1
    this.executor = Executors.newScheduledThreadPool(1, new ThreadFactory() {
        @Override
        public Thread newThread(Runnable r) {
            Thread t = new Thread(r);
            t.setName("com.alibaba.nacos.client.Worker." + agent.getName());
            t.setDaemon(true);
            return t;
        }
    });

    //初始化一个定时调度的线程池，从里面的name名字来看，似乎和长轮训有关系。
    this.executorService = Executors
        .newScheduledThreadPool(Runtime.getRuntime().availableProcessors(), new ThreadFactory() {
            @Override
            public Thread newThread(Runnable r) {
                Thread t = new Thread(r);
                t.setName("com.alibaba.nacos.client.Worker.longPolling." + agent.getName());
                t.setDaemon(true);
                return t;
            }
        });

    // 设置定时任务的执行频率，并且调用checkConfigInfo这个方法
    // 首次执行延迟时间为1毫秒、延迟时间为10毫秒
    this.executor.scheduleWithFixedDelay(new Runnable() {
        @Override
        public void run() {
            try {
                checkConfigInfo();
            } catch (Throwable e) {
                LOGGER.error("[" + agent.getName() + "] [sub-check] rotate check error", e);
            }
        }
    }, 1L, 10L, TimeUnit.MILLISECONDS);
}
```

#### 2.4.3 checkConfigInfo 方法

ClientWorker构造初始化中，启动了一个定时任务去执行checkConfigInfo()方法，这个方法主要是定时检查本地配置和服务器上的配置的变更情况，这个方法定义如下.

```java
public void checkConfigInfo() {
    // Dispatch taskes.
    // 监听的配置数量：key：DataID+Group拼接的值 value:是对应Nacos服务器上配置的文件内容
    // 储存监听变更的缓存集合
    int listenerSize = cacheMap.get().size();
    // Round up the longingTaskCount.
    // 向上取整，监听的配置数量除以3000，得到一个整数，代表长轮训任务的数量
    int longingTaskCount = (int) Math.ceil(listenerSize / ParamUtil.getPerTaskConfigSize());
    if (longingTaskCount > currentLongingTaskCount) {
        for (int i = (int) currentLongingTaskCount; i < longingTaskCount; i++) {
            // The task list is no order.So it maybe has issues when changing.
            executorService.execute(new LongPollingRunnable(i));
        }
        currentLongingTaskCount = longingTaskCount;
    }
}
```

cacheMap: AtomicReference<Map<String, CacheData>> cacheMap 用来存储监听变更的缓存集合。key是根据dataID/group得到的值。Value是对应存储在nacos服务器上的配置文件的内容。

默认情况下，每个长轮训LongPullingRunnable任务默认处理3000个监听配置集。如果超过3000， 则需要启动多个LongPollingRunnable去执行。

currentLongingTaskCount保存已启动的LongPullingRunnable任务数

executorService就是在ClientWorker构造方法中初始化的线程池


#### 2.4.4 LongPollingRunnable.run()

LongPollingRunnable长轮训任务的实现逻辑，这里内容比较多，我们一点点分析

```java
class LongPollingRunnable implements Runnable {
    
    private final int taskId;//表示当前任务批次id
    
    public LongPollingRunnable(int taskId) {
        this.taskId = taskId;
    }
    
    @Override
    public void run() {
        
        List<CacheData> cacheDatas = new ArrayList<CacheData>();
        List<String> inInitializingCacheList = new ArrayList<String>();
        try {
            // check failover config
            // 遍历CacheMap，把CacheMap中和当前任务id相同的缓存没保存到caheDates中
            // 通过checkLocalConfig方法
            for (CacheData cacheData : cacheMap.get().values()) {
                if (cacheData.getTaskId() == taskId) {
                    cacheDatas.add(cacheData);
                    try {
                    	// 检查本地配置
                        checkLocalConfig(cacheData);
                        if (cacheData.isUseLocalConfigInfo()) { //这里表示数据有变化，需要通知监听器
                            cacheData.checkListenerMd5(); //通知所有针对当前配置设置了监听的监听器
                        }
                    } catch (Exception e) {
                        LOGGER.error("get local config info error", e);
                    }
                }
            }
            
           。。。。。。
        } catch (Throwable e) {
            
            // If the rotation training task is abnormal, the next execution time of the task will be punished
            LOGGER.error("longPolling error : ", e);
            executorService.schedule(this, taskPenaltyTime, TimeUnit.MILLISECONDS);
        }
    }
}
```

#### 2.4.5 checkLocalConfig

检查本地配置，这里面有三种情况

- 如果isUseLocalConfigInfo为false，表示不使用本地配置，但是本地缓存路径的文件是存在的，于是把isUseLocalConfigInfo设置为true，并且更新cacheData的内容以及文件的更新时间
- 如果isUseLocalConfigInfo为true，表示使用本地配置文件，但是本地缓存文件不存在，则设置为false，不通知监听器。
- 如果isUseLocalConfigInfo为true，并且本地缓存文件也存在，但是缓存的的时间和文件的更新时间不一致，则更新cacheData中的内容，并且isUseLocalConfigInfo设置为true。

```
private void checkLocalConfig(CacheData cacheData) {
    final String dataId = cacheData.dataId;
    final String group = cacheData.group;
    final String tenant = cacheData.tenant;
    File path = LocalConfigInfoProcessor.getFailoverFile(agent.getName(), dataId, group, tenant);
    // 没有本地文件->有
    if (!cacheData.isUseLocalConfigInfo() && path.exists()) {
        String content = LocalConfigInfoProcessor.getFailover(agent.getName(), dataId, group, tenant);
        final String md5 = MD5Utils.md5Hex(content, Constants.ENCODE);
        cacheData.setUseLocalConfigInfo(true);
        cacheData.setLocalConfigInfoVersion(path.lastModified());
        cacheData.setContent(content);	
        
        LOGGER.warn(
                "[{}] [failover-change] failover file created. dataId={}, group={}, tenant={}, md5={}, content={}",
                agent.getName(), dataId, group, tenant, md5, ContentUtils.truncateContent(content));
        return;
    }
    
    // If use local config info, then it doesn't notify business listener and notify after getting from server.
    // 有本地文件->没有，不会通知业务监听器，从server拿到配置后通知。
    if (cacheData.isUseLocalConfigInfo() && !path.exists()) {
        cacheData.setUseLocalConfigInfo(false);
        LOGGER.warn("[{}] [failover-change] failover file deleted. dataId={}, group={}, tenant={}", agent.getName(),
                dataId, group, tenant);
        return;
    }
    
    // When it changed.
    // 发生变更时
    if (cacheData.isUseLocalConfigInfo() && path.exists() && cacheData.getLocalConfigInfoVersion() != path
            .lastModified()) {
        String content = LocalConfigInfoProcessor.getFailover(agent.getName(), dataId, group, tenant);
        final String md5 = MD5Utils.md5Hex(content, Constants.ENCODE);
        cacheData.setUseLocalConfigInfo(true);
        cacheData.setLocalConfigInfoVersion(path.lastModified());
        cacheData.setContent(content);
        LOGGER.warn(
                "[{}] [failover-change] failover file changed. dataId={}, group={}, tenant={}, md5={}, content={}",
                agent.getName(), dataId, group, tenant, md5, ContentUtils.truncateContent(content));
    }
}
```

#### 2.4.6 checkListenerMd5()

遍历用户自己添加的监听器，如果发现MD5值不同，则发送通知

```java
void checkListenerMd5() {
    for (ManagerListenerWrap wrap : listeners) {
        if (!md5.equals(wrap.lastCallMd5)) {
            safeNotifyListener(dataId, group, content, type, md5, wrap);
        }
    }
}
```

#### 2.4.7 检测服务端配置

在LongPollingRunnable.run中，先通过本地配置的读取和检查来判断数据是否发生变化从而实现变化的通知，然后当前的线程还需要去远程服务器上获得最新的数据，检查哪些数据发生了变化

```java
// check server config
// 从服务端获取发生变化的数据DateID列表，保存在List<String>集合中
List<String> changedGroupKeys = checkUpdateDataIds(cacheDatas, inInitializingCacheList);
if (!CollectionUtils.isEmpty(changedGroupKeys)) {
    LOGGER.info("get changedGroupKeys:" + changedGroupKeys);
}

//遍历发生了变更的配置项
for (String groupKey : changedGroupKeys) {
    String[] key = GroupKey.parseKey(groupKey);
    String dataId = key[0];
    String group = key[1];
    String tenant = null;
    if (key.length == 3) {
        tenant = key[2];
    }
    try {
        //逐项根据这些配置项获取配置信息
        String[] ct = getServerConfig(dataId, group, tenant, 3000L);
        //把配置信息保存到CacheData
        CacheData cache = cacheMap.get().get(GroupKey.getKeyTenant(dataId, group, tenant));
        cache.setContent(ct[0]);
        if (null != ct[1]) {
            cache.setType(ct[1]);
        }
        LOGGER.info("[{}] [data-received] dataId={}, group={}, tenant={}, md5={}, content={}, type={}",
                    agent.getName(), dataId, group, tenant, cache.getMd5(),
                    ContentUtils.truncateContent(ct[0]), ct[1]);
    } catch (NacosException ioe) {
        String message = String
            .format("[%s] [get-update] get changed config exception. dataId=%s, group=%s, tenant=%s",
                    agent.getName(), dataId, group, tenant);
        LOGGER.error(message, ioe);
    }
}
//再遍历CacheData这个集合，找到发生变化的数据进行通知
for (CacheData cacheData : cacheDatas) {
    if (!cacheData.isInitializing() || inInitializingCacheList
        .contains(GroupKey.getKeyTenant(cacheData.dataId, cacheData.group, cacheData.tenant))) {
        cacheData.checkListenerMd5();
        cacheData.setInitializing(false);
    }
}
inInitializingCacheList.clear();
 //继续传递当前线程进行轮询
executorService.execute(this);
```

#### 2.4.8 checkUpdateDataIds()

- 这个方法从cacheDatas集合中找到isUseLocalConfigInfo为false的

- 把需要检查的配置项，拼接成一个字符串,调用checkUpdateConfigStr进行验证

```
List<String> checkUpdateDataIds(List<CacheData> cacheDatas, List<String> inInitializingCacheList) throws Exception {
    StringBuilder sb = new StringBuilder();
    for (CacheData cacheData : cacheDatas) {
        if (!cacheData.isUseLocalConfigInfo()) {
            sb.append(cacheData.dataId).append(WORD_SEPARATOR);
            sb.append(cacheData.group).append(WORD_SEPARATOR);
            if (StringUtils.isBlank(cacheData.tenant)) {
                sb.append(cacheData.getMd5()).append(LINE_SEPARATOR);
            } else {
                sb.append(cacheData.getMd5()).append(WORD_SEPARATOR);
                sb.append(cacheData.getTenant()).append(LINE_SEPARATOR);
            }
            if (cacheData.isInitializing()) {
                // It updates when cacheData occours in cacheMap by first time.
                // 当cacheData首次出现在cacheMap中首次更新
                inInitializingCacheList
                        .add(GroupKey.getKeyTenant(cacheData.dataId, cacheData.group, cacheData.tenant));
            }
        }
    }
    boolean isInitializingCacheList = !inInitializingCacheList.isEmpty();
    // 效验是否需要更新
    return checkUpdateConfigStr(sb.toString(), isInitializingCacheList);
}
```

#### 2.4.9 checkUpdateConfigStr()

效验更新和发起远程调用方法，并且更新数据

```java
List<String> checkUpdateConfigStr(String probeUpdateString, boolean isInitializingCacheList) throws Exception {

    Map<String, String> params = new HashMap<String, String>(2);
    params.put(Constants.PROBE_MODIFY_REQUEST, probeUpdateString);
    Map<String, String> headers = new HashMap<String, String>(2);
    headers.put("Long-Pulling-Timeout", "" + timeout);

    // told server do not hang me up if new initializing cacheData added in
    // 第一次获取，直接更新
    if (isInitializingCacheList) {
        headers.put("Long-Pulling-Timeout-No-Hangup", "true");
    }
	
    // 判断拼接的字符串如果为空，直接返回
    if (StringUtils.isBlank(probeUpdateString)) {
        return Collections.emptyList();
    }

    try {
        // In order to prevent the server from handling the delay of the client's long task,
        // increase the client's read timeout to avoid this problem.
		// 设置readTimeoutMs，也就是本次请求等待响应的超时时间，默认是30s
        long readTimeoutMs = timeout + (long) Math.round(timeout >> 1);
        // 发起远程调用
        HttpRestResult<String> result = agent
            .httpPost(Constants.CONFIG_CONTROLLER_PATH + "/listener", headers, params, agent.getEncode(),
                      readTimeoutMs);

        if (result.ok()) {//响应成功
            setHealthServer(true);
            //解析并且更新数据
            return parseUpdateDataIdResponse(result.getData());
        } else {//响应失败
            setHealthServer(false);
            LOGGER.error("[{}] [check-update] get changed dataId error, code: {}", agent.getName(),
                         result.getCode());
        }
    } catch (Exception e) {
        setHealthServer(false);
        LOGGER.error("[" + agent.getName() + "] [check-update] get changed dataId exception", e);
        throw e;
    }
    return Collections.emptyList();
}
```

#### 2.4.10 客户端长轮询机制总结

1. 对本地缓存的配置做任务拆分，每一个批次最多3000条
2. 每批次创建一个线程去执行
3. 把每一个批次的缓存和本地磁盘文件中的数据进行比较
   1. 如果和本地配置不一致，则表示该缓存发生了更新，直接通知客户端监听
   2. 如果本地缓存和磁盘数据一致，则需要发起远程请求检查配置变化
4. 以tenent/groupId/dataId拼接成字符串，发送到服务端进行检查，返回发生了变更的配置
5. 客户端收到变更配置列表，更新本地配置。



### 2.5 服务端源码分析

分析完客户端以后，我们来分析服务端，那么现在服务端这面的入口我们就需要从客户端访问的接口来进行分析

```java
 HttpRestResult<String> result = agent
            .httpPost(Constants.CONFIG_CONTROLLER_PATH + "/listener", headers, params, agent.getEncode(),readTimeoutMs);
```

其实这个接口的地址为/v1/cs/configs/listener，所以我们直接找到这个接口来查看 ConfigController

```java
@PostMapping("/listener")
@Secured(action = ActionTypes.READ, parser = ConfigResourceParser.class)
public void listener(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {

    request.setAttribute("org.apache.catalina.ASYNC_SUPPORTED", true);
    String probeModify = request.getParameter("Listening-Configs");
    if (StringUtils.isBlank(probeModify)) {
        LOGGER.warn("invalid probeModify is blank");
        throw new IllegalArgumentException("invalid probeModify");
    }

    probeModify = URLDecoder.decode(probeModify, Constants.ENCODE);

    Map<String, String> clientMd5Map;
    try {
        //解析客户端传递过来的可能发生变化的配置项目，转换完Map集合(key=DataID,value=md5)
        clientMd5Map = MD5Util.getClientMd5Map(probeModify);
    } catch (Throwable e) {
        throw new IllegalArgumentException("invalid probeModify");
    }

    // do long-polling
    // 开始长轮询
    inner.doPollingConfig(request, response, clientMd5Map, probeModify.length());
}
```

#### 2.5.1 doPollingConfig()

首先这个方法主要是用来做长轮询和短轮训的判断的

```java
// 判断当前请求是否是长轮询，通过判断请求头是否有超时时间标记
if (LongPollingService.isSupportLongPolling(request)) {
    longPollingService.addLongPollingClient(request, response, clientMd5Map, probeRequestSize);
    return HttpServletResponse.SC_OK + "";
}
```

#### 2.5.2 addLongPollingClient()

把客户端的请求，保存到长轮询执行引擎中

```java
public void addLongPollingClient(HttpServletRequest req, HttpServletResponse rsp, Map<String, String> clientMd5Map,
                                 int probeRequestSize) {
    // 获取客户端长轮询的超时时间
    String str = req.getHeader(LongPollingService.LONG_POLLING_HEADER);
    // 不允许断开的标记
    String noHangUpFlag = req.getHeader(LongPollingService.LONG_POLLING_NO_HANG_UP_HEADER);
    // 应用名称
    String appName = req.getHeader(RequestUtil.CLIENT_APPNAME_HEADER);
    String tag = req.getHeader("Vipserver-Tag");
    // 延期时间，默认为500ms
    int delayTime = SwitchService.getSwitchInteger(SwitchService.FIXED_DELAY_TIME, 500);

    // Add delay time for LoadBalance, and one response is returned 500 ms in advance to avoid client timeout.
    // 提前500ms返回一个响应，避免客户端出现超时
    long timeout = Math.max(10000, Long.parseLong(str) - delayTime);
    if (isFixedPolling()) {
        timeout = Math.max(10000, getFixedPollingInterval());
        // Do nothing but set fix polling timeout.
    } else {
        long start = System.currentTimeMillis();
        List<String> changedGroups = MD5Util.compareMd5(req, rsp, clientMd5Map);
        if (changedGroups.size() > 0) {
            generateResponse(req, rsp, changedGroups);
            LogUtil.CLIENT_LOG.info("{}|{}|{}|{}|{}|{}|{}", System.currentTimeMillis() - start, "instant",
                                    RequestUtil.getRemoteIp(req), "polling", clientMd5Map.size(), probeRequestSize,
                                    changedGroups.size());
            return;
        } else if (noHangUpFlag != null && noHangUpFlag.equalsIgnoreCase(TRUE_STR)) {
            LogUtil.CLIENT_LOG.info("{}|{}|{}|{}|{}|{}|{}", System.currentTimeMillis() - start, "nohangup",
                                    RequestUtil.getRemoteIp(req), "polling", clientMd5Map.size(), probeRequestSize,
                                    changedGroups.size());
            return;
        }
    }
    // 获取客户端ip
    String ip = RequestUtil.getRemoteIp(req);

    // Must be called by http thread, or send response.
    // 把当前请求转换称为一个异步请求（意味着此时Tomcat线程被释放，最后需要asyncContext来手动完成响应）
    final AsyncContext asyncContext = req.startAsync();

    // AsyncContext.setTimeout() is incorrect, Control by oneself
    asyncContext.setTimeout(0L);
    // 执行长轮询请求
    ConfigExecutor.executeLongPolling(
        new ClientLongPolling(asyncContext, clientMd5Map, ip, probeRequestSize, timeout, appName, tag));
}
```

#### 2.5.3 ClientLongPolling()

这里具体执行了什么那，其实我们可以按照之前讲的原理来分析一下

- 这个任务要阻塞29.5s才能执行，因为立刻执行就没有意义了
- 如果在29.5s之内，数据发生变化，需要提前通知，需要有一个监控机制

这里就是证明阻塞29.5s执行

```java
class ClientLongPolling implements Runnable {

    @Override
    public void run() {
        // 构建一个异步任务，延后29.5s执行 timeoutTime29.5
        asyncTimeoutFuture = ConfigExecutor.scheduleLongPolling(new Runnable() {
            @Override
            public void run() {//如果达到29.5s，说明这个期间没有做任何配置的修改，则自行触发执行
                try {
                    getRetainIps().put(ClientLongPolling.this.ip, System.currentTimeMillis());

                    // Delete subscriber's relations.
                    boolean removeFlag = allSubs.remove(ClientLongPolling.this);//移除订阅关系

                    if (removeFlag) {
                        if (isFixedPolling()) {
                            LogUtil.CLIENT_LOG
                                .info("{}|{}|{}|{}|{}|{}", (System.currentTimeMillis() - createTime), "fix",
                                      RequestUtil.getRemoteIp((HttpServletRequest) asyncContext.getRequest()),
                                      "polling", clientMd5Map.size(), probeRequestSize);
                            List<String> changedGroups = MD5Util
                                .compareMd5((HttpServletRequest) asyncContext.getRequest(),
                                            (HttpServletResponse) asyncContext.getResponse(), clientMd5Map);
                            // 判断如果发生变更响应客户端
                            if (changedGroups.size() > 0) {
                                sendResponse(changedGroups);
                            } else {//没有发生返回空
                                sendResponse(null);
                            }
                        } else {
                            LogUtil.CLIENT_LOG
                                .info("{}|{}|{}|{}|{}|{}", (System.currentTimeMillis() - createTime), "timeout",
                                      RequestUtil.getRemoteIp((HttpServletRequest) asyncContext.getRequest()),
                                      "polling", clientMd5Map.size(), probeRequestSize);
                            sendResponse(null);
                        }
                    } else {
                        LogUtil.DEFAULT_LOG.warn("client subsciber's relations delete fail.");
                    }
                } catch (Throwable t) {
                    LogUtil.DEFAULT_LOG.error("long polling error:" + t.getMessage(), t.getCause());
                }

            }

        }, timeoutTime, TimeUnit.MILLISECONDS);

        allSubs.add(this);//把当前线程添加到订阅时间队列中
    }
```

#### 2.5.4 allSubs

之前我们在讲原理的时候就提供这个队列，那么现在我们也看到这个队列了，那么这个队列缓存了客户端和服务的长轮询

当用户在nacos 控制台修改了配置之后，必须要从这个订阅关系中取出对应的客户端长连接，然后把变更的结果返回。于是我们去看LongPollingService的构造方法查找订阅关系

```java
/**
* ClientLongPolling subscibers.
* 长轮询订阅关系
*/
final Queue<ClientLongPolling> allSubs;
```

#### 2.5.5 LongPollingService 构造方法

在LongPollingService的构造方法中，使用了一个NotifyCenter订阅了一个事件，事件的实例为LocalDataChangeEvent也就是服务端数据发生变更的事件，就会执行一个DataChangeTask的线程。

```java
public LongPollingService() {
    allSubs = new ConcurrentLinkedQueue<ClientLongPolling>();
    
    ConfigExecutor.scheduleLongPolling(new StatTask(), 0L, 10L, TimeUnit.SECONDS);
    
    // Register LocalDataChangeEvent to NotifyCenter.
    NotifyCenter.registerToPublisher(LocalDataChangeEvent.class, NotifyCenter.ringBufferSize);
    
    // Register A Subscriber to subscribe LocalDataChangeEvent.
    // 注册LocalDataChangeEvent订阅时间
    NotifyCenter.registerSubscriber(new Subscriber() {
        
        @Override
        public void onEvent(Event event) {
            if (isFixedPolling()) {
                // Ignore.
            } else {
                // 如果出发了LocalDataChangeEvent，则执行下面代码
                if (event instanceof LocalDataChangeEvent) {
                    LocalDataChangeEvent evt = (LocalDataChangeEvent) event;
                    // 执行数据变更事件
                    ConfigExecutor.executeLongPolling(new DataChangeTask(evt.groupKey, evt.isBeta, evt.betaIps));
                }
            }
        }
        
        @Override
        public Class<? extends Event> subscribeType() {
            return LocalDataChangeEvent.class;
        }
    });
    
}
```

#### 2.5.6 DataChangeTask

此任务会立刻响应客户端，完成数据同步

如果 DataChangeTask 任务完成了数据的 “推送” 之后，ClientLongPolling 中的调度任务又开始执行了怎么办呢？
很简单，只要在进行 “推送” 操作之前，先将原来等待执行的调度任务取消掉就可以了，这样就防止了推送操作写完响应数据之后，调度任务又去写响应数据，这时肯定会报错的。所以，在ClientLongPolling方法中，最开始的一个步骤就是删除订阅事件

```java
class DataChangeTask implements Runnable {

    @Override
    public void run() {
        try {
            ConfigCacheService.getContentBetaMd5(groupKey);
            // 遍历所有订阅事件
            for (Iterator<ClientLongPolling> iter = allSubs.iterator(); iter.hasNext(); ) {
                // 得到ClientLongPolling
                ClientLongPolling clientSub = iter.next();
                //判断当前的ClientLongPolling中，请求的key是否包含当前修改的groupKey
                if (clientSub.clientMd5Map.containsKey(groupKey)) {
                    // If published tag is not in the beta list, then it skipped.
                    if (isBeta && !CollectionUtils.contains(betaIps, clientSub.ip)) {
                        continue;
                    }

                    // If published tag is not in the tag list, then it skipped.
                    if (StringUtils.isNotBlank(tag) && !tag.equals(clientSub.tag)) {
                        continue;
                    }

                    getRetainIps().put(clientSub.ip, System.currentTimeMillis());
                    // 移出当前客户端订阅关系
                    iter.remove(); // Delete subscribers' relationships.
                    LogUtil.CLIENT_LOG
                        .info("{}|{}|{}|{}|{}|{}|{}", (System.currentTimeMillis() - changeTime), "in-advance",
                              RequestUtil
                              .getRemoteIp((HttpServletRequest) clientSub.asyncContext.getRequest()),
                              "polling", clientSub.clientMd5Map.size(), clientSub.probeRequestSize, groupKey);
                    // 响应客户端
                    clientSub.sendResponse(Arrays.asList(groupKey));
                }
            }

        } catch (Throwable t) {
            LogUtil.DEFAULT_LOG.error("data change error: {}", ExceptionUtil.getStackTrace(t));
        }
    }
```


---

## 3 Nacos Distro 一致性协议

> 来源：`附加/03/Nacos一致性协议：Distro协议.md`


### 3.1 协议简介

Distro协议是Nacos社区字眼的一种AP分布式协议，是面向临时实例设计的一种分布式协议，其保证在某些Nacos节点宕机后，整个临时实例处理系统依旧可以正常工作。作为一种有状态的中间件应用内嵌协议，Distro保证了各个Nacos节点对于还亮注册请求的统一协调和储存。

### 3.2 设计思想

Distro协议的主要设计思想如下：

- Nacos 每个节点是平等的都可以处理写请求，同时把新数据同步到其他节点。
- 每个节点只负责部分数据，定时发送自己负责数据的校验值到其他节点来保持数据一致性。
- 每个节点独立处理读请求，及时从本地发出响应。

### 3.3 Nacos 为什么需要一致性协议

- Nacos 在开源支持就定下了⼀个目标，尽可能的减少用户部署以及运维成本，做到用户只需要⼀个程序包，就可以快速以单机模式启动 Nacos 或者以集群模式启动 Nacos。而Nacos 是⼀个需要存储数据的⼀个组件，因此，为了实现这个目标，就需要在 Nacos 内部实现数据存储。单机下其实问题不大，简单的内嵌关系型数据库即可；但是集群模式下，就需要考虑如何保障各个节点之间的数据⼀致性以及数据同步，而要解决这个问题，就不得不引入共识算法，通过算法来保障各个节点之间的数据的⼀致性。

- Distro 协议是阿里巴巴自研的⼀个最终⼀致性协议，而最终⼀致性协议有很多，比如Gossip（流行病协议）、Eureka 内的数据同步算法。而 Distro 算法是集 Gossip 以及 Eureka 协议的优点并加以优化而出来的，对于原生的 Gossip，由于随机选取发送消息的节点，也就不可避免的存在消息重复发送给同⼀节点的情况，增加了网络的传输的压力，也给消息节点带来额外的处理负载，而Distro算法引入了权威 Server 的概念，每个节点负责⼀部分数据以及将自己的数据同步给其他节点，有效的降低了消息冗余的问题。

### 3.4 数据初始化

新加入的 Distro 节点会进行全量数据拉取。具体操作是轮询所有的 Distro 节点，通过向其他的机器发送请求拉取全量数据。

![image-20220525173309895](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fe7ccc09-7fc3-51ad-8b76-0b74f6f98872.png)

在全量拉取操作完成之后，Nacos 的每台机器上都维护了当前的所有注册上来的非持久化实例数据。

### 3.5 数据校验

在 Distro 集群启动之后，各台机器之间会定期的发送心跳。心跳信息主要为各个机器上的所有数据的元信息（之所以使用元信息，是因为需要保证网络中数据传输的量级维持在⼀个较低水平）。这种数据校验会以心跳的形式进行，即每台机器在固定时间间隔会向其他机器发起⼀次数据校验请求。

![image-20220525184507960](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a374098a-2dad-5176-b577-104e5cfa7d79.png)

⼀旦在数据校验过程中，某台机器发现其他机器上的数据与本地数据不⼀致，则会发起⼀次全量拉取请求，将数据补齐。

### 3.6 写操作

对于⼀个已经启动完成的 Distro 集群，在⼀次客户端发起写操作的流程中，当注册非持久化的实例的写请求打到某台 Nacos 服务器时，Distro 集群处理的流程图如下。

![image-20220525192513504](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6b4d400f-887b-5d9c-8c6a-6406e8e6dbab.png)

整个步骤包括几个部分（图中从上到下顺序）：

1. 前置的 Filter 拦截请求，并根据请求中包含的 IP 和 port 信息计算其所属的Distro 责任节点，并将该请求转发到所属的 Distro 责任节点上。 
2. 责任节点上的 Controller 将写请求进行解析。
3. Distro 协议定期执行 Sync 任务，将本机所负责的所有的实例信息同步到其他节点上。



### 3.7 读操作

由于每台机器上都存放了全量数据，因此在每⼀次读操作中，Distro 机器会直接从本地拉取数据。快速响应。

![image-20220525192730255](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/056bbf7b-abc4-5fb0-957a-fefe6a801093.png)

这种机制保证了 Distro 协议可以作为⼀种 AP 协议，对于读操作都进行及时的响应。在网络分区的情况下，对于所有的读操作也能够正常返回；当网络恢复时，各个 Distro 节点会把各数据分片的数据进行合并恢复。



### 3.8 总结

Distro 协议是 Nacos 对于临时实例数据开发的⼀致性协议。其数据存储在缓存中，并且会在启动时进行全量数据同步，并定期进行数据校验。

在 Distro 协议的设计思想下，每个 Distro 节点都可以接收到读写请求。所有的Distro协议的请求场景主要分为三种情况：

1. 当该节点接收到属于该节点负责的实例的写请求时，直接写入。 
2. 当该节点接收到不属于该节点负责的实例的写请求时，将在集群内部路由，转发给对应的节点，从而完成读写。 
3. 当该节点接收到任何读请求时，都直接在本机查询并返回（因为所有实例都被同步到了每台机器上）。

Distro 协议作为 Nacos 的内嵌临时实例⼀致性协议，保证了在分布式环境下每个节点上面的服务信息的状态都能够及时地通知其他节点，可以维持数十万量级服务实例的存储和⼀致性。
