# 1 02-SpringCloudAlibaba-Nacos服务注册发现与配置中心

## 1.1 Nacos基础操作

### 1.1.1 微服务简介

#### 1.1.1.1 什么是微服务

2014年，Martin Fowler（**马丁·福勒** ） 提出了微服务的概念，定义了微服务是由以单一应用程序构成的小服务，自己拥有自己的进程与轻量化处理，服务依业务功能设计，以全自动的方式部署，与其他服务使用 HTTP API 通信。同时服务会使用最小的规模的集中管理能力，服务可以用不同的编程语言与数据库等组件实现。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/0ddabd87-c851-5850-ad7d-33ee4ee65948.png" alt="WeChat13cb5e0a22944a3c85e6d91ca10babbc.png" style="zoom:33%;" />

马丁·福勒是敏捷联盟的成员，于2001年，同其他16名合著者一起协助创作了“敏捷软件开发宣言”。他负责维护一个bliki网站---一种blog和wiki的混合衍生物，他还使控制反转(Inversion of Control)“依赖注入模式(Dependency Injection)”一词得到普及。

**架构的演变**

随着互联网的发展，网站应用的规模也不断的扩大，进而导致系统架构也在不断的进行变化，从互联网早起到现在，系统架构大体经历了下面几个过程：

![WeChatb55c37f0bb53be8957590c529bb21e83.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ab18d4f8-7b38-5655-bea7-74390559717f.png)

**单体应用架构：**

把所有功能都集中在一个应用中，统一部署，开发成本、部署成本和维护成本低

* 优点：项目架构简单，适合用户量少的项目，开发成本低，项目部署在一个节点上，维护方便。
* 缺点：功能集中在一个工程中，对于大型项目比一开发和维护，项目模块紧耦合，单点容错率低，无法对不同的模块功能进行针对性的优化和水平拓展

**垂直应用架构：**

所谓垂直应用架构，其实就是把之前的单体应用拆分成多个应用，以提升效率，比如电商系统可以拆分成：电商系统、后台系统、CMS系统

* 优点：项目拆分实现了流量分担，解决了并发问题，而且可以针对不同模块进行优化和水平拓展，同时不同的系统之间不会互相影响，提高容错率
* 缺点：系统之间互相存在，无法进行相互调用，系统之间互相独立，会造成一部分功能的冗余

**分布式架构：**

随着业务的增加，在垂直应用架构中冗余的业务代码越来越多，就需要将冗余的部分抽取出来，统一做成业务层单独处理，变成一个单独的服务，控制层调用不同的业务层服务就能完成不同的业务功能，具体表现就是一个项目拆分成表现层和服务层两个部分，服务层中包含业务逻辑，表现层只需要处理和页面的交互，业务逻辑都是调用服务层的服务来实现，这就是分布式架构。

* 优点：抽取公共的功能作为服务层，提高代码复用行。
* 缺点：系统间耦合度变高，调用关系错综复杂，难以维护。

**SOA架构：**

分布式架构中的缺点就是调用复杂，而且当服务越来越多，或者当某一个服务压力过大需要水平拓展和负载均衡，对于资源调度和治理就需要用到治理中心SOA（Service Oriented Architecture）为核心来解决，同时治理中心还可以帮助我们解决服务之间协议不同的问题。

* 优点：使用治理中心（ESB\dubbo）解决了服务见调用关系的自动调节
* 缺点：服务间会有依赖关系，一旦某个环节出错会影响较大（服务雪崩），服务关系复杂，运维、测试部署困难。

**微服务架构：**

微服务架构在某种程度上面架构SOA继续发展的下一步，它更加强调服务的“彻底拆分”，目的就是提高效率，微服务架构中，每个服务必须独立部署同时互不影响，微服务架构更加轻巧，轻量级。

**微服务架构与SOA架构的不同**

1\. 微服务架构比SOA架构会更加的精细，让专业的人去做专业的。
2\. 目的是提高效率每个服务之间互不影响，微服务架构中，每个服务需要独立部署
3\. SOA架构中可能数据库存储会发生共享，微服务强调每个服务都是单独数据库，保证每个服务之间互不影响。
4\. 微服务项目架构比SOA架构更加适合与互联网公司迅捷开发、快速迭代版本，因为粒度非常精细。

为何使用微服务呢？

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6be79bd3-9cd5-5b9b-a24b-c0e63df372df.png" alt="WeChatc690c21a12ed31985403c3be5446e01c.png" style="zoom:50%;" />

微服务就像集群作战，可以提升性能

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/431b9e28-e88b-56aa-898c-7ca987f4de8d.png" alt="WeChate3645eab5fa6b9086451566cb00ebadb.png" style="zoom: 50%;" />

微服务架构：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b1b65a64-4ad6-5a86-b863-3aef9c71a62a.png" alt="WeChat16099fb7486aa74bf6f6c13e79467e67.png" style="zoom: 67%;" />

* 优点：服务原子化拆分，独立打包、部署和升级，保证每个微服务清晰的任务划分，利于扩展
* 缺点：分布式系统开发的技术成本高（容错、分布式事务等），复杂性更高，每个微服务进行分布式独立部署，当进行模块调用的时候，分布式将会变得更加麻烦。

**Spring Cloud 发展历史**

**Spring Cloud netflix -> Spring Cloud Alibaba**

1\. 2020-12-22日**Spring** 官方博客宣布，`Spring Cloud 2020.0.0`正式发布。`2020.0.0`是第一个使用新的版本号命名方案的**Spring Cloud** 发行版本。在此之前**Spring Cloud** 使用英国伦敦地铁站的命名方式来命名一个大版本（`train version`），如果不按照新的版本号命名的话，本次的版本号应该是Ilford。
2\. 更新版本没有什么大惊小怪的，但是本次更新却正式开启了**Spring Cloud Netflix** 体系的终结进程。**Netflix** 公司是目前微服务落地中最成功的公司。它开源了诸如**Eureka** 、**Hystrix** 、**Zuul** 、**Feign** 、**Ribbon** 等等广大开发者所知微服务套件，统称为**Netflix OSS** 。在当时**Netflix OSS** 成为微服务组件上事实的标准。但是在2018年**Netflix** 公司宣布其核心组件**Hystrix** 、**Ribbon** 、**Zuul** 、**Eureka** 等进入**维护状态** ，不再进行新特性开发，只修BUG。这直接影响了**Spring Cloud** 项目的发展路线，**Spring** 官方不得不采取了应对措施，在2019年的在 **SpringOne 2019** 大会中，**Spring Cloud** 宣布 **Spring Cloud Netflix项目进入维护模式** ，并在2020年移除相关的**Netflix OSS** 组件。
3\. Spring Cloud Aalibaba 成为了主流

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/16f2ae4c-51d5-5039-aac8-67d62657f570.png" alt="image.png" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8c085c1a-a029-5626-ae08-2af518050369.png" alt="image.png" style="zoom:50%;" />

#### 1.1.1.2 Spring Cloud Alibaba

**简介**

Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用微服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。

依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。

**主要功能**

* **服务限流降级** ：默认支持 WebServlet、WebFlux, OpenFeign、RestTemplate、Spring Cloud Gateway, Zuul, Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
* **服务注册与发现** ：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
* **分布式配置管理** ：支持分布式系统中的外部化配置，配置更改时自动刷新。
* **消息驱动能力** ：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
* **分布式事务** ：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
* **阿里云对象存储** ：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
* **分布式任务调度** ：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
* **阿里云短信服务** ：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

**组件**

**[Sentinel]** ：阿里巴巴源产品，把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

**[Nacos]** ：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

**[RocketMQ]** ：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。

**[Dubbo]** ：Apache Dubbo™ 是一款高性能 Java RPC 框架。

**[Seata]** ：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。

**[Alibaba Cloud OSS]** : 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。

**[Alibaba Cloud SchedulerX]**: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。

**[Alibaba Cloud SMS]** : 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

### 1.1.2 Spring Cloud Alibaba Nacos简介与下载安装



#### 1.1.2.1 Nacos介绍

​	Nacos（Naming Configuration Service） 是一个易于使用的动态服务发现、配置和服务管理平台，用于构建云原生应用程序

​	**服务发现是微服务架构中的关键组件之一**。Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

​	Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。



#### 1.1.2.2 什么是Nacos？

1\. Nacos = **注册中心+配置中心组合**

2\. Nacos支持几乎所有主流类型的“服务”的发现、配置和管理，常见的服务如下：

   Kubernetes Service

   gRPC & Dubbo RPC Service

   Spring Cloud RESTful Service

   

#### 1.1.2.3 为何使用Nacos？

​	为何使用注册中心呢？我们以入住酒店的前台为例子，稍微加以解释。先设想一个没有服务前台的酒店，客人入住需要自己寻找合适居住的房间，客人不知道每个房间的情况，无法确定那个房间是打扫干净可以入住，客人只能逐个房间寻找，如果遇到已经居住房客的房间一定很尴尬，显然这是不正常的情况。正常的情况是酒店会安排服务台，酒店打扫干净可以入住的房间会登记注册到服务台，这样客人来住店，只需要在前台就可以查找到可以入住的房间，这样就无需等待快速的入住。显然，服务器提供发注册和发现机制可以让房客快速找到合适的房间，快速解决入住问题。

![image-20210915144412020](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b24b8ad2-f5d4-50a2-bc18-10e575595713.png)



​	采用微服务以后，软件微服务组件各自独立，但最终还要组合为一个整体作为一个软件系统服务于最终客户，这时软件组件之间也需要彼此通讯，彼此调用方法。微服务架构内部发起通讯调用方法的一方成为“**服务消费者**”，提供远程方法调用的服务器称为“**服务提供者**”，往往为了提高系统性能，会提供多个服务器作为**服务提供者**，此时**服务消费者**找到**服务提供者**的过程，就类似于用户在找房间的过程。为了帮助**服务消费者**快速的发现**服务提供者**，在微服务框架中都会引入**注册中心。注册中心**类似于酒店的前台，提供在软件服务的注册和发现功能，**服务提供者**会先在注册中心进行**注册**，声明可以对外提供服务，而**服务消费者**只需要在注册中心就可以快速**发现**找到可以使用的服务，快速使用服务。注册中心实现了服务提供和服务消费的快速撮合功能。

![image-20210915151230064](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c0def547-11ad-5e6a-8552-e075eaaa594e.png)

#### 1.1.2.4 Nacos下载和安装

官网网址：https://nacos.io/zh-cn/index.html

![image-20210915113531807](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b89e4410-16de-5dcb-8c8f-439299f3af21.png)

官网文档网址：https://nacos.io/zh-cn/docs/quick-start.html

注意：使用官网推荐的稳定版本：

![image-20210915152238853](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/372c63ab-a7cb-5524-8247-d16d140c081e.png)

下载地址：https://github.com/alibaba/nacos/releases



![image-20210915161024807](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f2ae0f3d-c7f8-562d-8d33-4450dd1227be.png)

**下载之后安装过程**

1\. 解压以后找到bin目录

![image-20210915163508347](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/266e3cd4-80aa-53ca-be04-bf5b9aef623c.png)

2\. 执行指令：

**Linux/Unix/Mac**

   启动命令(standalone代表着单机模式运行，非集群模式):

   ```
   sh startup.sh -m standalone
   ```

**Windows**

   启动命令(standalone代表着单机模式运行，非集群模式):

   ```
   startup.cmd -m standalone
   ```

3\. 结果：

   ![image-20210915163836699](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/28bbe859-07e8-583c-8cba-ff05f95c2c45.png)

4\. 得到结果以后为了验证是否成功开启Nacos，我们需要访问：http://localhost:8848/nacos

![image-20210915163952277](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/82035f71-bf0d-5a99-b5fe-382f4e2a6bec.png)

5\. 出现此界面表示已经成功启动Nacos，默认的账号密码是：nacos/nacos

![image-20210915164224183](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/0e913e25-fc02-5638-89ef-511becc03a85.png)

6\. 到这里就是成功开启了Nacos服务了。

### 1.1.3 Nacos服务提供者注册

#### 1.1.3.1 Nacos代替Eureka

​	Nacos可以直接提供注册中心（Eureka）+配置中心（Config），所以它的好处显而易见，我们在上节课成功安装和启动了Nacos以后就可以发现Nacos本身就是一个小平台，它要比之前的Eureka更加方便，不需要我们在自己做配置。



#### 1.1.3.2 Nacos服务注册中心

​	服务发现是微服务架构中的关键组件之一。在这样的架构中，手动为每个客户端配置服务列表可能是一项艰巨的任务，并且使得动态扩展极其困难。Nacos Discovery 帮助您自动将您的服务注册到 Nacos 服务器，Nacos 服务器会跟踪服务并动态刷新服务列表。此外，Nacos Discovery 将服务实例的一些元数据，如主机、端口、健康检查 URL、主页等注册到 Nacos。

​	学习任何知识我们都需要从它的官方文档入手，所以我们直接来看官网给我们提供的文档：https://spring.io/projects/spring-cloud-alibaba#learn

![image-20210916145558297](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3021dfe3-126f-5aff-ba05-457e458bb80f.png)



![image-20210916145707907](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/23b824cf-69de-52ab-8af9-9dcc59894812.png)

​	

#### 1.1.3.3 如何引入Nacos Discovery进行服务注册/发现

**创建新项目**

聚合项目：由于聚合带来的诸多好处，在SpringBoot项目开发中也广泛采用，开发中将SpringBoot项目按照功能分成子模块开发，所以我们在使用Spring Cloud Alibaba完成项目的时候，也是采用聚合项目来完成。

​	父项目	

![image-20210916172456588](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ef8463e7-4ed5-5637-8126-c79a56ee0558.png)



​	子项目

![image-20210916175756108](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b62c7ddb-f27a-5134-8e10-c578502d33b8.png)

**POM**

​	对应依赖版本关系可以参考官网：https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E

​	父项目pom文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.13.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.mashibing</groupId>
    <artifactId>SpringCloudAlibabaMSB</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringCloudAlibabaMSB</name>
    <packaging>pom</packaging>
    <description>SpringCloudAlibabaMSB</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud-alibaba-version>2.2.5.RELEASE</spring-cloud-alibaba-version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba-version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <modules>
        <module>cloudAlibaba-Nacos-9001</module>
    </modules>

</project>
```

​	子项目pom文件

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
    <artifactId>cloudalibaba-nacos-9001</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>cloudalibaba-nacos-9001</name>
    <description>cloudalibaba-nacos-9001</description>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
</project>
```



**YML**

![image-20210916180029680](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bdbda512-c71e-50e9-adf2-45d67749a5c6.png)

```yaml
server:
  port: 9001
spring:
  application:
    name: nacos-provider
  cloud:
    discovery:
      server-addr: 127.0.0.1:8848

management:
  endpoint:
    web:
      exposure:
        include:'*'
```

**启动类**

```java
package com.mashibing.cloudalibabanacos9001;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient//加此注解
public class CloudalibabaNacos9001Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaNacos9001Application.class, args);
    }

}

```



**业务类**

```java
package com.mashibing.cloudalibabanacos9001.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/mashibing")
    public String getServerPort(){
        return "Hello Nacos Discovery"+serverPort;
    }

}
```



**测试**

**注意，在启动项目前一定要启动Nacos**

![image-20210916180252032](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bdea78ad-6a63-5cf5-b060-1c6df0c6f5c4.png)



**练习**

​	根据上面的练习演示，在创建一个新的9002端口作为服务注册到Nacos中，为了下节课来做准备

### 1.1.4 Nacos服务消费者注册和负载均衡



#### 1.1.4.1 服务消费者

​	上节课我们成功配置了Nacos服务提供者的注册，那么这节课我们将配置服务消费者，同时来验证Nacos自带负载均衡。



**具体配置**

**新建Module**

![image-20210917135655649](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b8f848c9-dc8c-5aa6-8a1e-25bc453a8ea6.png)

**pom**


```xml
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
    <artifactId>cloudalibaba-consumer-nacos-8083</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>cloudalibaba-consumer-nacos-8083</name>
    <description>cloudalibaba-consumer-nacos-8083</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
</project>
```


注意父项目要添加```<models>```标记

```xml
<modules>
    <module>cloudalibaba-nacos-9001</module>
    <module>cloudalibaba-nacos-9002</module>
    <module>cloudalibaba-consumer-nacos-8083</module>
</modules>
```

**yml**

```xml
server:
  port: 8083
spring:
  application:
    name: nacos-consumer
  cloud:
    discovery:
      server-addr: localhost:8848

```



**主启动**

```java
package com.mashibing.cloudalibabaconsumernacos8083;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class CloudalibabaConsumerNacos8083Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaConsumerNacos8083Application.class, args);
    }

}
```

**测试**

注意：要先启动Nacos，然后再来启动服务

![image-20210917140345089](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6c0c6dc9-eb8b-5b25-a9e7-61d2010218a2.png)



#### 1.1.4.2 远程调用与Ribbon

​	大家现在肯定很疑惑，这个服务消费者不是要调用具体服务吗？但是现在仅仅是创建出来了，和上节课创建的服务者也没有多大的区别啊？这具体怎么用那？

是这样的，我们现在想要让这个服务的消费者去调用服务提供者，我们就需要通过Ribbon来进行调用，那么首先我们先来了解Ribbon。



**什么是Ribbon**

​	它是一个基于HTTP和TCP**客户端**负载均衡器。它虽然只是一个工具类库，它却是**每一个微服务**的基础设施。因为实际上，对于服务间调用、API网关请求转发都需要经过Ribbon负载均衡来实现。总体来说，Ribbon的主要作用是：从注册服务器端拿到对应服务列表后以负载均衡的方式访问对应服务。

​	要注意的是Nacos已经整合了Ribbon，所以我们想要使用只需要导入Spring Cloud Alibaba Nacos的依赖就可以直接使用了。

![image-20210917145544074](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2027c148-6c3c-5879-8ab1-3dfdb0bf1298.png)



​	

**具体使用**

现在如果我们需要远程访问那么可以使用RestTemplate，其中getForObject是最常用方法，同时还要在服务消费者中配置RestTemplate：

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate(){
    return new RestTemplate();
}

restTemplate.getForObject(arg1,arg2,arg3...);
```

**第一个参数url表示被调用的目标Rest接口位置**

1\. url的第一部分是在Nacos中注册的服务提供者名称，如果多个服务提供者注册相同名称，Ribbon会自动寻找其中一个服务提供者，并且调用接口方法。这个就是负载均衡功能。

2\. url后半部是控制器的请求路径。

**第二个参数是返回值类型**

1\. JavaBean类型或者JavaBean数组类型，如果控制器返回的是List集合，需要使用数组类型接收。

**第三个参数是可变参数**

1\. 是传递给url的动态参数，使用参数时候需要在url上需要使用{1}、{2}、{3}进行参数占位，这样传递的参数就会自动替换占位符。



#### 1.1.4.3 验证Nacos自带负载均衡

​	我们现在知道了如果我们想要让服务消费者consumer-nacos-8083调用服务提供者nacos-9001或者9002，那么必然要使用扶负载均衡Ribbon和远程调用RestTemplate，所以我们要做的第一件事情就是先让9001或者9002服务对外提供接口，用于访问，由于9001已经有对外提供的接口了，所以我们只需要仿照完成9002即可

```java
package com.mashibing.cloudalibabanacos9002.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/mashibing")
    public String getServerPort(){
        return "Hello Nacos Discovery"+serverPort;
    }

}
```

​	接下来我们就需要通过服务消费8083者来访问9001或者9002，但是在这之前，我们先在consumer-nacos-8083模块中的yml文件里添加一句话

```java
server:
  port: 8083
spring:
  application:
    name: nacos-consumer
  cloud:
    discovery:
      server-addr: localhost:8848

### 消费者将要去访问的微服务名称（注册成功的Nacos的微服务提供者）
service-url:
  nacos-user-service: http://nacos-provider
```

​	因为我们要远程调用，所以我们还需要在启动类上配置restTemplate

```java
package com.mashibing.cloudalibabaconsumernacos8083;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableDiscoveryClient
public class CloudalibabaConsumerNacos8083Application {

    public static void main(String[] args) {
        SpringApplication.run(CloudalibabaConsumerNacos8083Application.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```

​	配置好之后，我们就可以在8083的消费者上来通过Ribbon来自动的负载均衡调用9001或者9002的服务提供者了

```java
package com.mashibing.cloudalibabaconsumernacos8083.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
public class DemoController {

    @Resource
    private RestTemplate restTemplate;

    /**
     * 消费者去访问具体服务，这种写法可以实现
     * 配置文件和代码的分离
     */
    @Value("${service-url.nacos-user-service}")
    private String serverURL;


    @GetMapping(value = "consumer/nacos")
    public String getDiscovery(){
        System.err.println(serverURL);
        return restTemplate.getForObject(serverURL+"/mashibing",String.class);
    }
}
```

测试结果：

```java
访问：http://localhost:8083/consumer/nacos

结果：Hello Nacos Discovery9001/9002(负载均衡切换显示)
```



总结：因为Nacos中本身就集成了Ribbon所以它本身就自带负载均衡

### 1.1.5 Nacos服务注册中心对比提升



#### 1.1.5.1 各种服务注册中心对比

| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度      |
| ------------------ | ------- | ---------- | --------------- |
| Eureka             | AP      | 支持       | 低(2.x版本闭源) |
| Zookeeper          | CP      | 不支持     | 中              |
| Consul             | CP      | 支持       | 高              |
| Nacos              | AP/CP   | 支持       | 高              |

**CAP模型**

​	计算机专家 埃里克·布鲁尔（Eric Brewer）于 2000 年在 ACM 分布式计算机原理专题讨论会（简称：PODC）中提出的分布式系统设计要考虑的三个核心要素：

​		一致性（Consistency）：同一时刻的同一请求的实例返回的结果相同，所有的数据要求具有强一致性(Strong Consistency)

​		可用性（Availability）：所有实例的读写请求在一定时间内可以得到正确的响应

​		分区容错性（Partition tolerance）：在网络异常（光缆断裂、设备故障、宕机）的情况下，系统仍能提供正常的服务

​	以上三个特点就是CAP原则（又称CAP定理），但是三个特性不可能同时满足，所以分布式系统设计要考虑的是在满足P（分区容错性）的前提下选择C（一致性）还是A（可用性），即：CP或AP



**CP原则：一致性 + 分区容错性原则**

​	CP 原则属于强一致性原则，要求所有节点可以查询的数据随时都要保持一直（同步中的数据不可查询），即：若干个节点形成一个逻辑的共享区域，某一个节点更新的数据都会立即同步到其他数据节点之中，当数据同步完成后才能返回成功的结果，但是在实际的运行过程中网络故障在所难免，如果此时若干个服务节点之间无法通讯时就会出现错误，从而牺牲了以可用性原则（A），例如关系型数据库中的事务。



**AP原则：可用性原则 + 分区容错性原则**

​	AP原则属于弱一致性原则，在集群中只要有存活的节点那么所发送来的所有请求都可以得到正确的响应，在进行数据同步处理操作中即便某些节点没有成功的实现数据同步也返回成功，这样就牺牲一致性原则（C 原则）。

​	使用场景：对于数据的同步一定会发出指令，但是最终的节点是否真的实现了同步，并不保证，可是却可以及时的得到数据更新成功的响应，可以应用在网络环境不是很好的场景中。

​	

#### 1.1.5.2 Nacos支持CP和AP

​	Nacos无缝支持一些主流的开源生态，同时再阿里进行Nacos设计的时候重复的考虑到了市场化的运作（市面上大多都是以单一的实现形式为主，例如：Zookeeper使用的是 CP、而 Eureka采用的是AP），在Nacos中提供了两种模式的动态切换。

​	![image-20210923165642323](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/01d4325b-3ece-5e5f-b960-6891d792bdc8.png)



#### 1.1.5.3 Nacos 何时选择切换模式

1\. 一般来说，如果不需要储存服务界别的信息且服务实例通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。如Spring Cloud 和 Dubbo，都适用于AP模式，AP模式为了服务的可用性减弱了一致性，因此AP模式下只支持注册临时实例。
2\. 如果需要在服务级别编辑或者储存配置信息，那么CP是必须的，K8S服务和DNS服务则是用于CP模式。CP模式下则支持注册持久化实例，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

3\. 切换命令（默认是AP）：

```java
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
```



注意：临时和持久化的区别主要在健康检查失败后的表现，持久化实例健康检查失败后会被标记成不健康，而临时实例会直接从列表中被删除。

### 1.1.6 Nacos之服务配置中心



**基础配置**

Nacos不仅仅可以作为注册中心来使用，同时它支持作为配置中心

![image-20210924140930824](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9dd18769-754e-5b85-8f59-8a7825b078f2.png)



首先我们还是新建Model：cloudalibaba-config-3377

**pom文件**

​	这里我们主要要引入的是此依赖，这个依赖依据在官网上可以找到:https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_an_example_of_using_nacos_discovery_for_service_registrationdiscovery_and_call

```java
<dependency> 
    <groupId> com.alibaba.cloud </groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-config </artifactId> 
</dependency>
```



**YML配置**

​	要注意的是这里我们要配置两个，因为Nacos同SpringCloud-config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置之后，才能保证项目的正常启动。

​	springboot中配置文件的加载是存在优先级顺序的，bootstrap优先级高于application

​	分别要配置的是，这里bootstrap.yml配置好了以后，作用是两个，第一个让3377这个服务注册到Nacos中，第二个作用就是去Nacos中去读取指定后缀为yaml的配置文件：

bootstrap.yml

```java
### nacos配置
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



**主启动**

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



**业务类**

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

    @GetMapping("/config/info")
    public String getConfigInfo(){
        return configInfo;
    }

}
```

**Nacos配置规则**

​	在 Nacos Spring Cloud 中，`dataId` 的完整格式如下（详情可以参考官网 https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html）：

```java
${prefix}-${spring.profiles.active}.${file-extension}
```

1\. `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。
2\. `spring.profiles.active` 即为当前环境对应的 profile，注意：**当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`**（不能删除）
3\. `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。
4\. 通过 Spring Cloud 原生注解 `@RefreshScope` 实现配置自动更新：
5\. 所以根据官方给出的规则我们最终需要在Nacos配置中心添加的配置文件的名字规则和名字为：

```java
### ${spring.application.name}-${spring.profiles.active}.${file-extension}
### nacos-config-client-dev.yaml
### 微服务名称-当前环境-文件格式
```

![image-20210924160100192](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f2036ee9-9974-5ab2-b8d7-4406df4c89d4.png)

#### 1.1.6.1 Nacos平台创建配置操作

增加配置

![image-20210924154802374](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1dcdf286-00b6-53b1-aae6-8209be93b545.png)

![image-20210924160459681](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3b7201f9-323f-5178-973e-5cff23aad811.png)

```java
config: 
    info: nacos config center,version = 1
```

然后在配置中心就会看到刚刚发布的配置

![image-20210924155315617](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1d060863-a86a-5fc2-8ab4-414e669c0e3b.png)



#### 1.1.6.2 自动配置更新

修改Nacos配置，不需要重启项目即可自动刷新

![image-20210924195241683](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/803f8a37-55e4-59df-b601-a234e30f48f0.png)

修改版本号为2，点击发布

![image-20210924195325905](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6f3572f6-f55d-50ec-8924-bb8bd363ac3e.png)

**测试**

 启动服务访问服务来测试（没有修改之前是1，修改之后不需要重启项目既可以直接获取最新配置）：http://localhost:3377/config/info

![image-20210924182159179](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b55c2685-219f-5803-be40-8567620c5d18.png)

### 1.1.7 Nacos命名空间分组和DataID三者关系

#### 1.1.7.1 名词解释

**命名空间**（Namespace）

​	用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的 Group 或 Data ID 的配置。Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。

**配置分组**（Group）

​	Nacos 中的一组配置集，是组织配置的维度之一。通过一个有意义的字符串（如 Buy 或 Trade ）对配置集进行分组，从而区分 Data ID 相同的配置集。当您在 Nacos 上创建一个配置时，如果未填写配置分组的名称，则配置分组的名称默认采用 DEFAULT_GROUP 。配置分组的常见场景：不同的应用或组件使用了相同的配置类型，如 database_url 配置和 MQ_topic 配置。

**配置集 ID**（Data ID）

​	Nacos 中的某个配置集的 ID。配置集 ID 是组织划分配置的维度之一。Data ID 通常用于组织划分系统的配置集。一个系统或者应用可以包含多个配置集，每个配置集都可以被一个有意义的名称标识。Data ID 通常采用类 Java 包（如 com.taobao.tc.refund.log.level）的命名规则保证全局唯一性。此命名规则非强制。

​	配置集：一组相关或者不相关的配置项的集合称为配置集。在系统中，一个配置文件通常就是一个配置集，包含了系统各个方面的配置。例如，一个配置集可能包含了数据源、线程池、日志级别等配置项。



**三者关系**

​	这三者的关系类似于Java里面的package名和类名，最外层的Namespace是可以用于区分部署环境的，Group和DataID逻辑上区分两个目标对象。

![sanzheguanxi ](<https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/989033bf-8e75-5b8e-b50f-a9cc7cae6f67.png>)



**默认情况**

Namespace=public，Group=DEFAULT_GROUP，默认Cluster是DEFAULT



**具体情况**

​	Nacos默认的命名空间是public，我们就可以利用Namespace来实现隔离，比如我们现在有三个环境：开发、测试、生产环境，我们就可以创建三个Namespace，不同的Namespace之间是隔离的。

​	Group本身就是分组的意思，它可以把不同的微服务划分到同一个分组里面去。

​	剩下的就是具体微服务，一个Service可以包含多个Cluster，Nacos默认Cluster是DEFAULT，Cluster是对指定微服务的一个虚拟划分。比如说，将一个Service部署在北京和和杭州的机房中，北京机房的Service就可以起名为（BJ），杭州机房中的Service就可以起名为（HZ），这样就可以尽量让同一个机房的微服务互相调用，提升性能。



#### 1.1.7.2 切换不同环境

**DataID方案**

1\. 指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置，配置规则：默认空间+新建dev和test两个DataId：

1\. 新建dev配置DataID（已经创建完成）
2\. 新建test配置DataID

   ![image-20210926170016223](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b8080dd0-b411-506a-8540-f6b20a8ae948.png)

2\. 通过spring.profile.active属性就能进行多环境下配置文件的读取

   ![image-20210926170339921](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6635ab2b-3ae3-533c-a631-db5594c9f9cd.png)

   ![image-20210926170620844](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7f0ed826-2bea-516f-add0-3e150cb43249.png)

   ```java
   spring:
     profiles:
       # active: dev #表示开发环境
       active: test #表示测试环境
   ```

   

3\. 测试

   最后再次访问：http://localhost:3377/localhost/config/info

   ![image-20210926170942161](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c5279fb7-2c4f-57a0-b65f-50f63fba8755.png)

   

**Group方案**

​	根据之前的讲解我们都知道Group为分组默认是：DEFAULT_GROUP，所以现在我们就需要分出两组，一组是“Dev开发组”，一组是“test测试组”

1\. 新建Group

    DEV_GROUP

![image-20210926171610558](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/80b24884-d867-50ec-8198-f6d0499a5560.png)

​		TEST_GROUP

![image-20210926171904382](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5e8024b6-338a-5875-9d49-5615275dd7ff.png)

配置列表，两个DataID一致，但是分组不同的配置文件：

![image-20210926171954022](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b0f6d3f8-fb6b-54f2-a8c6-6cfa4afefff1.png)

2\. 完成以上配置以后，接下来就需要通过bootstrap+application来配合完成，具体方式：在config下增加一条Group的配置即可，可以配置为DEV_GROUP或TEST_GROUP

​	bootstrap:

```java
### nacos配置
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
        group: TEST_GROUP #增加分组

```

​	application:

```java
spring:
  profiles:
    # active: dev #表示开发环境	
    # active: test #表示测试环境
    active: info
```



3\. 测试（可以测试不同分组）：

   最后再次访问：http://localhost:3377/localhost/config/info

![image-20210926173730298](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5f6eccab-6d42-5519-babe-dbf6de10e2b1.png)

**Namespace空间方案**

​	我们已经把DataID方案和Group方案完成了，然后接下来我们来看一下Namespace（命名空间）方案，默认此方案为public，此方案不能删除，我们现在来新建两个命名空间方案

![image-20210926174051313](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4e41ad18-f066-59e1-a88a-fb05afef87d4.png)

1\. 新建dev/test的Namespace

![image-20210926174422526](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a244eb60-e9af-5b6d-8840-1241188fd438.png)

![image-20210926174807843](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5c823530-0f30-5e78-9bb4-77bf0c1aeae7.png)

1\. 服务管理-服务列表页面查看

   此时我们就可以切换对应的命名空间，然后再通过Group和DataID来进行切换，如果想要切换命名空间，我们就需要通过命名空间ID来进行切换

   ![image-20210926174422526](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a244eb60-e9af-5b6d-8840-1241188fd438.png)

2\. 按照域名配置（Namespace+Group+DataId）编写

   ​	先通过namespace命名空间ID来确认使用的命名空间

   ![image-20210926175605787](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a66eca15-3ed3-5d39-93aa-ba548492bee8.png)

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
           group: TEST_GROUP # 指定分组
           namespace: 4ba4bf0e-210c-41ce-954c-23538de1dcbc #指定命名空间
   ```

   ```java
   spring:
     profiles:
       active: dev #表示开发环境
       # active: test #表示测试环境
       # active: info
   ```

   ​	

   然后再回到Nacos配置列表，添加Dev命名空间下的配置，添加3条

   ![image-20210926180321810](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/61dd1ecb-7083-5bec-8240-f5a7126234af.png)

   ![image-20210926180120552](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2f3c8d28-6174-5027-8fb6-9c822daf56a0.png)

   ![image-20210926180520423](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/67360338-4af5-5785-8e0e-1cba201b5631.png)

3\. 测试：访问http://localhost:3377/localhost/config/info来进行测试

### 1.1.8 Nacos集群架构说明

​	到目前为止，我们已经对Nacos的一些基本使用和配置已经掌握，但是这些还不够，我们还需要了解一个非常重要的点，就是Nacos的集群相关的操作，那我们就先从Nacos集群配置的概念说起

#### 1.1.8.1 Nacos支持三种部署模式

1\. 单机模式 - 用于测试和单机试用
2\. 集群模式 - 用于生产环境，确保高可用
3\. 多集群模式 - 用于多数据中心场景

#### 1.1.8.2 集群部署说明

参考网站：https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html

**架构图**：部署生产使用的集群模式

![image-20210927163629652](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a4f931ca-1db0-569c-b5cd-cfca7be6ee6e.png)

**具体拆分**

![image-20210927165836853](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fc121c14-01f9-5e19-af34-26af2470c21c.png)



**预备环境**

​	默认Nacos使用嵌入式数据库实现数据的存储，所以，如果启动多个默认配置下的Nacos节点，数据储存存在一致性问题，为了解决这个问题，Nacos采用了集中存储方式来支持集群化部署，目前仅支持MySql的存储。

1\. 推荐使用Linux
2\. 可以使用内部数据源或者外部数据源（推荐使用外部）MySql

### 1.1.9 Nacos持久化切换配置



#### 1.1.9.1 Nacos持久化配置

​	Nacos默认自带嵌入式数据库derby，所以我们每次创建一个Nacos实例就会有一个derby，当有多个Nacos节点的时候，就会出现一致性问题，所以Nacos支持了外部数据库统一数据管理MySql。

![image-20210927174839244](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c7de8d4f-ca8b-5bcc-84b7-238d05aedbc0.png)

#### 1.1.9.2 Nacos默认derby切换MySql

具体配置方式可以参考官网：https://nacos.io/zh-cn/docs/deployment.html

1\. 我们需要找到Nacos安装目录下的conf目录中的Sql脚本，然后在数据库中进行执行

   注意：需要我们先创建好数据库并且使用：

   ```java
   CREATE DATABASE nacos_config;
   USE nacos_config;
   ```

![image-20210927175150183](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cf484e22-40a7-55d9-bd4c-f38f53b2e428.png)

2\. 修改conf/application.properties文件，增加支持mysql数据源配置（目前只支持mysql），添加mysql数据源的url、用户名和密码。

```java
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&serverTimezone=UTC
db.user=root
db.password=root
```

​	这里表示更改Nacos数据库为本机MySql数据库

**注意：**这里需要注意几个点，一个是url地址需要改，另外数据库的用户和密码也需要更改，同时还有一个是官网上在**db.url.0=jdbc:mysql**这个属性上少了一个属性为**serverTimezone=UTC**需要我们手动添加

**测试**

重启Nacos服务，此时之前所创建的Namespace+Group+DataID将全部消失，因为这些数据是保存在之前Nacos内嵌的derby数据库中，现在使用的是本地的MySql数据库，此时我们可以添加配置测试，在查看数据库nacos_config中的config_info表，此时就会有新添加的配置内容

### 1.1.10 Nacos之Linux版本安装



#### 1.1.10.1 Linux版Nacos+MySql生产环境配置

​	在之前的课程中我们已经给大家讲解过了Nacos生产环境下需要搭建集群配置，那么这里我们预计需要：1个Nginx+3个Nacos注册中心+1个MySql

![image-20210927165142680](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fc121c14-01f9-5e19-af34-26af2470c21c.png)



**具体配置：**

1\. 在官网上下载NacosLinux版本：https://github.com/alibaba/nacos/releases

![image-20210928163131899](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e9fd779e-f8ac-5acb-902b-6143eb68fc63.png)

2\. 在Linux系统中解压此文件，并且把其中nacos目录拷贝到nacos目录中

```java
tar -zxvf nacos-server-2.0.3.tar.gz
```

3\. 打开nacos目录下的bin目录输入命令即可执行

![image-20210928183219115](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d84a4a6d-618a-51e8-b78d-30a7b3766e06.png)

### 1.1.11 Nacos集群配置

 

#### 1.1.11.1 更改Nacos启动命令配置原理

我们现在知道，想要启动Naocs只需要启动startup.sh命令即可，但是如果启动3个Nacos那？所以如果我们需要启动多个Nacos，其实Nacos本身默认启动就是集群模式。

注意点：如果是linux虚拟机，需要分配至少4g以上内存

**具体配置**

**Linux服务器上MySql数据库配置**

1\. 在Linux系统上执行SQL脚本，具体位置在nacos目录下的conf中，这里的操作和之前是一样的，我们可以直接打开这个文件然后拷贝到数据库中执行，当然也是要创建数据库使用数据库然后在复制脚本内容，执行即可

```java
create database nacos_config;
use nacos_config;
```

![image-20210929175710498](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9f196f25-62d8-59d7-ba31-198f5f7af3fd.png)

2\. 修改application.properties配置文件，但是修改之前我们最好做一个备份。

```java
cp application.properties application.properties.init
```

3\. 这里的修改和我们之间的在win上的修改是完全一样的，所以我们只要打开这个文件，加上对应的内容即可

```java
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&serverTimezone=UTC
db.user=root
db.password=123456
```

4\. 还要注意一点，这个文件中的两个服务的端口号也要对应修改

```java
#*************** Spring Boot Related Configurations ***************#
##### Default web context path:
server.servlet.contextPath=/nacos
##### Default web server port:
server.port=8888/8868
```



**Linux服务器上Nacos的集群配置cluter.conf**

1\. 这里开始正式配置集群，首先我们要更改cluter.conf这个配置文件，当然我们也需要备份，但是这里它的原始名称为：cluster.conf.example，我们需要把它保留同时复制出一个cluster.conf来进行更改

```java
cp cluster.conf.example cluster.conf
```

![image-20210929182825202](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f4915817-bf2f-5469-9759-5c60dc4f7c4e.png)

2\. 具体配置内容，这里我们在配置集群的时候不能直接写127.0.0.1这样，这样分不清楚，所以我们需要知道具体的IP地址，我们可以通过：

```java
ip a #查看具体ip	
```

![image-20220321182422028](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ddc864c1-6c22-5e4a-bb41-961ebdcba138.png)

具体需修改内容，这里注意，我们在修改端口的时候一定要有一定的偏移量，因为Nacos2.0本身新增了占用端口，所以我们在设置端口号的时候注意要避开，不要占用端口，以下是官网截图：

![image-20220321175516671](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6e4f3f80-e028-5370-bffd-b37717bfb959.png)

```java
### 格式： ip地址:端口号
192.168.189.129:8848
192.168.189.129:8868
192.168.189.129:8888
```

**启动三个阶段的Nacos测试**

当上方配置完成之后，我们此时就可以启动三个节点的Nacos进行测试，启动方式和Win上区别不大：

1\. 但是要注意一点，因为我们要开放端口测试，所以我们在启动nacos之前，要进行关闭防火墙操作：

```java
// 关闭防火墙服务-但是开启还会自动启动
systemctl stop firewalld
// 彻底关闭-开机不会启动防火墙
systemctl disable firewalld
```

2\. 启动三个nacos节点，分别进入到三个Nacos节点的bin目录中进行启动：

![image-20220321180934633](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/80b9bd19-5a61-5461-8e06-c859d5b4df99.png)

```java
sh startup.sh //启动命令
```

3\. 启动完成以后，我们可以分别看一下启动日志，防止启动出现问题，启动路径：

![image-20220321181649453](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fefb9b29-d876-571a-9426-baffac244c7d.png)

4\. 这里我们可以通过cat命令或者tail -f命令

```java
cat /home/msb/opt/nacos3/logs/start.out
tail -f /home/msb/opt/nacos3/logs/start.out
```

![image-20220321181429986](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/050266a1-6924-556e-913d-489b8d9cd7de.png)

5\. 通过浏览器分别访问三个阶段的Nacos，测试是否成功启动

在看到所有服务都启动成功之后，我们通过浏览器访问对应地址，就可以进入到对应节点的Nacos控制台上，然后我们可以查看集群管理，节点列表

![image-20220321182820980](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f8350460-fea9-5a24-ae44-88bcb27332e7.png)

**Nginx配置**

1\. 我们需要找到Nginx的配置文件，然后做备份

```java
cd /usr/local/nginx
cp nginx.conf nginx.conf.bk
```

![image-20210929211042606](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2c922ef4-9c2a-5e9a-9478-4aa8e9893ec7.png)

2\. 修改nginx.conf

```java
worker_processes  1;

events {
    worker_connections  1024;
}

stream {
      upstream nacos {
        server 192.168.189.129:8848;
        server 192.168.189.129:8868;
        server 192.168.189.129:8888;
      }


     server {
        listen  81;
        proxy_pass nacos;
     }
}
```

**测试启动**

1\. 首先进入到Nginx目录下，启动Nginx，同时要带着我们设置过得配置文件启动

```java
cd /usr/local/nginx/sbin
./nginx
```

2\. 通过访问Nginx来测试是否能够访问到Nacos，在win系统浏览器网址上输入：

```java
http://192.168.189.129:81/nacos/
```

![image-20210930150017223](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/70e385fe-5d81-53e8-9abb-ff09d61cb1c4.png)

3\. 使用账号密码nacos，nacos成功登录就表示此时已经完成全部配置

![image-20210930153201511](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/537a57a6-6bc7-5683-8c76-ea710b424309.png)



**添加配置**

1\. 在Nacos平台上添加配置

![image-20210930153148673](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b7764b00-7877-5ca3-b966-d7e8985cc10c.png)

2\. 在数据库中检查是否有这一条配置，如果有表示成功添加

![image-20210930153709605](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f2c61a99-3989-58e3-b411-49fac0205fae.png)

**配置微服务为Linux版Nacos集群并注册进Nacos**

1\. 我们以9002为例，此时我们要修改application.yaml文件，把之前的Nacos端口换成Nacos集群

```java
server:
  port: 9002
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        # server-addr: localhost:8848
        # 换成nginx的81端口，做集群
        server-addr: http://192.168.189.129:81


management:
  endpoint:
    web:
      exponsure:
        include: '*'

```

2\. 配置完成启动服务，我们就可以在Naocs平台上看见对应的微服务了，此时表示服务注册成功

![image-20210930155527834](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/856d6738-10f4-5781-8dcb-4042994f9505.png)



**总结**

![image-20220321185952218](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f7fa314b-8f46-56d2-88da-647809c366f7.png)

## 1.2 Nacos源码分析

### 1.2.1 Nacos源码开篇



#### 1.2.1.1 为什么要分析源码

1\. **提升技术功底：**学习源码里面的优秀的设计思想，比如一些问题的解决问题思路，还有一些优秀的设计模式，提升自己的技术功底。
2\. **深度掌握框架：**源码看多了，对于一个新技术或者框架的掌握速度会有大幅度提升，看下框架的演示Demo就基本上知道了底层实现原理，学习框架的速度会非常快。
3\. **快速定位问题：**遇到问题，特别是框架源码的Bug问题，能够快速定位，这就是多看源码所带来的的好处和优势。
4\. **提高面试成功率：**面试一线互联网大厂，一般都会问题到框架源码级别的实现，如果掌握了源码，会大大提升面试成功几率和薪资待遇。
5\. **参与开源社区：**参与到开源项目的研发，结识更多大牛，对于自己以后的提升好处多多。



#### 1.2.1.2 看源码的方法

1\. **先使用：**先看官方网站提供的文档，快速掌握框架的基本使用
2\. **关注核心功能：**在使用的过程中关注框架的核心功能，然后来观察这些核心功能的代码
3\. **总结归纳：**总结源码中的一些核心点，同时最好能够跟着源码来做出核心流程图，这样就可以把源码中的核心亮点找出并且标记，后续就可能会借鉴到实际工作项目中，同时要善于用Debug，来观看源码的执行过程，观察一些关键变量的值的变化。当我们把框架的所有功能点的源码都分析完成后，回到主流程在梳理一遍，最后在自己脑袋中形成一个闭环，这样源码的核心内容和主流程就基本上理解了。



#### 1.2.1.3 Nacos服务注册与发现源码剖析

**Nacos核心功能点**

**服务注册**：Nacos Client会通过发送REST请求的方式向Nacos Server注册自己的服务，提供自身的元数据，比如ip地址、端口等信息。Nacos Server接收到注册请求后，就会把这些元数据信息存储在一个双层的内存Map中。 

**服务心跳**：在服务注册后，Nacos Client会维护一个定时心跳来持续通知Nacos Server，说明服务一直处于可用状态，防止被剔除。默认5s发送一次心跳。

**服务健康检查**：Nacos Server会开启一个定时任务用来检查注册服务实例的健康情况，对于超过15s没有收到客户端心跳的实例会将它的healthy属性置为false(客户端服务发现时不会发现)，如果某个实例超过30秒没有收到心跳，直接剔除该实例(被剔除的实例如果恢复发送心跳则会重新注册)

**服务发现**：服务消费者（Nacos Client）在调用服务提供者的服务时，会发送一个REST请求给Nacos Server，获取上面注册的服务清单，并且缓存在Nacos Client本地，同时会在Nacos Client本地开启一个定时任务定时拉取服务端最新的注册表信息更新到本地缓存

**服务同步**：Nacos Server集群之间会互相同步服务实例，用来保证服务信息的一致性。 



**Nacos服务端原理**



![在这里插入图片描述](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2ad5e811-27b9-5400-a086-70fb76723fd3.png)

**Nacos客户端原理**

![在这里插入图片描述](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/af77ab4f-ca35-504e-bc30-268d7cde3ff7.png)



​	其实从以上的两张图中我们就能够找到突破口了，其实核心内容就集中在nacos-console、nacos-naming、nacos-config，这几个模块中。那么知道了这些，现在我们就来进行Nacos的源码下载，然后我们具体分析。

**Nacos源码下载**

我们本次需要通过Nacos来运行Nacos，所以下载地址为：https://github.com/alibaba/nacos，我们需要通过Idea来进行下载，以下是具体下载步骤



![image-20211011183821099](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e2903ef2-bdc7-5314-8d2a-c640dd4c7e9f.png)

![image-20211011183927440](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/30cae650-cab3-569c-bed9-c3e25595ba23.png)

下载好之后我们需要启动Nacos，那么我们首先要找到nacos-console这个模块，找到启动类，同时还要配置启动模式为单机（我们还是以单机模式为演示）

![image-20211011184215671](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/502bed2b-fcdc-55c6-a1df-43ce0d0308a2.png)

设置单机运行

```java
-Dnacos.standalone=true -Dnacos.home=C:\\nacos
```



![image-20211012182029525](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e39a20ff-eb99-518e-bc51-77e92c3883b1.png)

项目配置好之后，我们来看一下Nacos所有的模块 

![在这里插入图片描述](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8859fd20-9053-5a37-8285-0076d200105e.png)



​	配置好之后就可以运行测试，和启动普通的SpringBoot聚合项目一样，启动之后我们也可以直接访问：http://localhost:8848/nacos，这个时候就能看到我们以前看到的对应客户端页面了，到此为止我们的Nacos源码启动就完成了。

### 1.2.2 Nacos客户端服务注册源码分析

#### 1.2.2.1 服务注册信息

​	我们从Nacos-Client开始说起，那么说到客户端就涉及到服务注册，我们先了解一下Nacos客户端都会将什么信息传递给服务器，我们直接从Nacos Client项目的NamingTest说起

```java
public class NamingTest {
    
    @Test
    public void testServiceList() throws Exception {
        
        Properties properties = new Properties();
        properties.put(PropertyKeyConst.SERVER_ADDR, "127.0.0.1:8848");
        properties.put(PropertyKeyConst.USERNAME, "nacos");
        properties.put(PropertyKeyConst.PASSWORD, "nacos");
        
        Instance instance = new Instance();
        instance.setIp("1.1.1.1");
        instance.setPort(800);
        instance.setWeight(2);
        Map<String, String> map = new HashMap<String, String>();
        map.put("netType", "external");
        map.put("version", "2.0");
        instance.setMetadata(map);
    
        NamingService namingService = NacosFactory.createNamingService(properties);
        namingService.registerInstance("nacos.test.1", instance);
        
        ThreadUtils.sleep(5000L);
        
        List<Instance> list = namingService.getAllInstances("nacos.test.1");
        
        System.out.println(list);
        
        ThreadUtils.sleep(30000L);
        //        ExpressionSelector expressionSelector = new ExpressionSelector();
        //        expressionSelector.setExpression("INSTANCE.metadata.registerSource = 'dubbo'");
        //        ListView<String> serviceList = namingService.getServicesOfServer(1, 10, expressionSelector);
        
    }
}
```

​	其实这就是客户端注册的一个测试类，它模仿了一个真实的服务注册进Nacos的过程，包括NacosServer连接、实例的创建、实例属性的赋值、注册实例，所以在这个其中包含了服务注册的核心代码，仅从此处的代码分析，可以看出，Nacos注册服务实例时，包含了两大类信息：Nacos Server连接信息和实例信息。



**Nacos Server连接信息**

Nacos Server连接信息，存储在Properties当中，包含以下信息：

- Server地址：Nacos服务器地址，属性的key为serverAddr；
- 用户名：连接Nacos服务的用户名，属性key为username，默认值为nacos；
- 密码：连接Nacos服务的密码，属性key为password，默认值为nacos；



**实例信息**

注册实例信息用Instance对象承载，注册的实例信息又分两部分：实例基础信息和元数据。

**实例基础信息包括**：

- instanceId：实例的唯一ID；
- ip：实例IP，提供给消费者进行通信的地址；
- port： 端口，提供给消费者访问的端口；
- weight：权重，当前实例的权限，浮点类型（默认1.0D）；
- healthy：健康状况，默认true；
- enabled：实例是否准备好接收请求，默认true；
- ephemeral：实例是否为瞬时的，默认为true；
- clusterName：实例所属的集群名称；
- serviceName：实例的服务信息；

Instance类包含了实例的基础信息之外，还包含了用于**存储元数据的metadata**（描述数据的数据），类型为HashMap，从当前这个Demo中我们可以得知存放了两个数据：

- netType：顾名思义，网络类型，这里的值为external，也就是外网的意思；
- version：版本，Nacos的版本，这里是2.0这个大版本。

除了Demo中这些“自定义”的信息，在Instance类中还定义了一些默认信息，这些信息通过get方法提供：

```java
public long getInstanceHeartBeatInterval() {
    return getMetaDataByKeyWithDefault(PreservedMetadataKeys.HEART_BEAT_INTERVAL,
                                       Constants.DEFAULT_HEART_BEAT_INTERVAL);
}

public long getInstanceHeartBeatTimeOut() {
    return getMetaDataByKeyWithDefault(PreservedMetadataKeys.HEART_BEAT_TIMEOUT,
                                       Constants.DEFAULT_HEART_BEAT_TIMEOUT);
}

public long getIpDeleteTimeout() {
    return getMetaDataByKeyWithDefault(PreservedMetadataKeys.IP_DELETE_TIMEOUT,
                                       Constants.DEFAULT_IP_DELETE_TIMEOUT);
}

public String getInstanceIdGenerator() {
    return getMetaDataByKeyWithDefault(PreservedMetadataKeys.INSTANCE_ID_GENERATOR,
                                       Constants.DEFAULT_INSTANCE_ID_GENERATOR);
}
```

上面的get方法在需要元数据默认值时会被用到：

- preserved.heart.beat.interval：心跳间隙的key，默认为5s，也就是默认5秒进行一次心跳；
- preserved.heart.beat.timeout：心跳超时的key，默认为15s，也就是默认15秒收不到心跳，实例将会标记为不健康；
- preserved.ip.delete.timeout：实例IP被删除的key，默认为30s，也就是30秒收不到心跳，实例将会被移除；
- preserved.instance.id.generator：实例ID生成器key，默认为simple；

这些都是Nacos默认提供的值，也就是当前实例注册时会告诉Nacos Server说：我的心跳间隙、心跳超时等对应的值是多少，你按照这个值来判断我这个实例是否健康。

有了这些信息，我们基本是已经知道注册实例时需要传递什么参数，需要配置什么参数了。

#### 1.2.2.2 NamingService接口

​	NamingService接口是Nacos命名服务对外提供的一个统一接口，看对应的源码就可以发现，它提供了大量实例相关的接口方法：

- 服务实例注册

  ```java
  void registerInstance(...) throws NacosException;
  ```

- 服务实例注销

  ```java
  void deregisterInstance(...) throws NacosException;
  ```

- 获取服务实例列表

  ```java
  List<Instance> getAllInstances(...) throws NacosException;
  ```

- 查询健康服务实例

  ```java
  List<Instance> selectInstances(...) throws NacosException;
  ```

- 查询集群中健康的服务实例

  ```java
  List<Instance> selectInstances(....List<String> clusters....)throws NacosException;
  ```

- 使用负载均衡策略选择一个健康的服务实例

  ```java
  Instance selectOneHealthyInstance(...) throws NacosException;
  ```

- 订阅服务事件

  ```java
  void subscribe(...) throws NacosException;
  ```

- 取消订阅服务事件

  ```java
  void unsubscribe(...) throws NacosException;
  ```

- 获取所有（或指定）服务名称

  ```java
  ListView<String> getServicesOfServer(...) throws NacosException;
  ```

- 获取所有订阅的服务

  ```java
   List<ServiceInfo> getSubscribeServices() throws NacosException;
  ```

- 获取Nacos服务的状态

  ```java
  String getServerStatus();
  ```

- 主动关闭服务

  ```java
  void shutDown() throws NacosException
  ```

在这些方法中提供了大量的重载方法，应用于不同场景和不同类型实例或服务的筛选，所以我们只需要在不同的情况下使用不同的方法即可。

NamingService的实例化是通过NamingFactory类和上面的Nacos服务信息，从代码中可以看出这里采用了反射机制来实例化NamingService，具体的实现类为NacosNamingService：

```java
public static NamingService createNamingService(Properties properties) throws NacosException {
    try {
        Class<?> driverImplClass = Class.forName("com.alibaba.nacos.client.naming.NacosNamingService");
        Constructor constructor = driverImplClass.getConstructor(Properties.class);
        return (NamingService) constructor.newInstance(properties);
    } catch (Throwable e) {
        throw new NacosException(NacosException.CLIENT_INVALID_PARAM, e);
    }
}
```

#### 1.2.2.3 NacosNamingService的实现

​	在示例代码中使用了NamingService的registerInstance方法来进行服务实例的注册，该方法接收两个参数，服务名称和实例对象。这个方法的最大作用是设置了当前实例的分组信息。我们知道，在Nacos中，通过Namespace、group、Service、Cluster等一层层的将实例进行环境的隔离。在这里设置了默认的分组为“DEFAULT_GROUP”。

```java
@Override
public void registerInstance(String serviceName, Instance instance) throws NacosException {
    registerInstance(serviceName, Constants.DEFAULT_GROUP, instance);
}
```

紧接着调用的registerInstance方法如下，这个方法实现了两个功能：

​	第一，检查心跳时间设置的对不对（心跳默认为5秒）

​	第二，通过NamingClientProxy这个代理来执行服务注册操作

```java
@Override
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);//检查心跳
    clientProxy.registerService(serviceName, groupName, instance);//通过代理执行服务注册操作
}
```

通过clientProxy我们发现NamingClientProxy这个代理接口的具体实现是有NamingClientProxyDelegate来完成的，这个可以从NacosNamingService构造方法中来看出。

```java
public NacosNamingService(Properties properties) throws NacosException {
    init(properties);
}
```

初始化在init方法中

```java
private void init(Properties properties) throws NacosException {
    ValidatorUtils.checkInitParam(properties);
    this.namespace = InitUtils.initNamespaceForNaming(properties);
    InitUtils.initSerialization();
    InitUtils.initWebRootContext(properties);
    initLogName(properties);

    this.changeNotifier = new InstancesChangeNotifier();
    NotifyCenter.registerToPublisher(InstancesChangeEvent.class, 16384);
    NotifyCenter.registerSubscriber(changeNotifier);
    this.serviceInfoHolder = new ServiceInfoHolder(namespace, properties);
    this.clientProxy = new NamingClientProxyDelegate(this.namespace, serviceInfoHolder, properties, changeNotifier);//在这里进行了初始化，并看出使用的是NamingClientProxyDelegate来完成的
}
```

#### 1.2.2.4 NamingClientProxyDelegate中实现

根据上方的分析和源码的阅读，我们可以发现NamingClientProxy调用registerService实际上调用的就是NamingClientProxyDelegate的对应方法：

```java
@Override
public void registerService(String serviceName, String groupName, Instance instance) throws NacosException {
    getExecuteClientProxy(instance).registerService(serviceName, groupName, instance);
}
```

真正调用注册服务的并不是代理实现类，而是根据当前实例是否为瞬时对象，来选择对应的客户端代理来进行请求的：

如果当前实例为瞬时对象，则采用gRPC协议（NamingGrpcClientProxy）进行请求，否则采用http协议（NamingHttpClientProxy）进行请求。默认为瞬时对象，也就是说，2.0版本中默认采用了gRPC协议进行与Nacos服务进行交互。

```java
private NamingClientProxy getExecuteClientProxy(Instance instance) {
    return instance.isEphemeral() ? grpcClientProxy : httpClientProxy;
}
```



#### 1.2.2.5 NamingGrpcClientProxy中实现

关于gRPC协议（NamingGrpcClientProxy），我们后续在做展开，我们主要关注一下registerService方法实现，这里其实做了两件事情

1\. 缓存当前注册的实例信息用于恢复，缓存的数据结构为ConcurrentMap<String, Instance>，key为“serviceName@@groupName”，value就是前面封装的实例信息。
2\. 另外一件事就是封装了参数，基于gRPC进行服务的调用和结果的处理。

```java
@Override
public void registerService(String serviceName, String groupName, Instance instance) throws NacosException {
    NAMING_LOGGER.info("[REGISTER-SERVICE] {} registering service {} with instance {}", namespaceId, serviceName,
                       instance);
    redoService.cacheInstanceForRedo(serviceName, groupName, instance);//缓存数据
    doRegisterService(serviceName, groupName, instance);//基于gRPC进行服务的调用
}
```



**总结流程**

汇总流程图：

![Nacos](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/05e6ffbb-a152-5102-9b23-bd4eedecfd64.awebp)

### 1.2.3 Nacos客户端实例注册源码分析

#### 1.2.3.1 实例客户端注册入口

流程图：

![20210415094809249](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6b80acaf-b420-518b-a7f5-0fb9f1714e43.png)

实际上我们在真实的生产环境中，我们要让某一个服务注册到Nacos中，我们首先要引入一个依赖：

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

在引入这个依赖以后，我们要找到SpringBoot自动装配文件META-INF/spring.factories文件

![image-20211018155352467](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/77083132-4b71-59b2-9399-992dd6856db2.png)

然后再通过SpingBoot的自动装配（首先找到）来加载EnableAutoConfiguration对应的类，然后这里我们就能看见很多Nacos相关的内容，那我们怎么能知道这个服务在注册的时候具体走的时候哪一个，其实一般这种文件我们都会找“Auto”关键子的文件来进行查看，然后我们现在要了解的是客户端的注册，所以我们要找“NacosServiceRegistryAutoConfiguration”。

![image-20211018153730166](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/919078fe-f153-56d4-8339-454555f27759.png)

然后在当前这个类中会有很多的Bean组件，这些都是Spring容器启动时候自动注入的，一般情况下可能我们会看一下每一个Bean组件初始化具体干了什么，但是实际上这里最核心的是“NacosAutoServiceRegistration”

```
/**
 * @author xiaojing
 * @author <a href="mailto:mercyblitz@gmail.com">Mercy</a>
 */
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties
@ConditionalOnNacosDiscoveryEnabled
@ConditionalOnProperty(value = "spring.cloud.service-registry.auto-registration.enabled",
      matchIfMissing = true)
@AutoConfigureAfter({ AutoServiceRegistrationConfiguration.class,
      AutoServiceRegistrationAutoConfiguration.class,
      NacosDiscoveryAutoConfiguration.class })
public class NacosServiceRegistryAutoConfiguration {

   @Bean
   public NacosServiceRegistry nacosServiceRegistry(
         NacosDiscoveryProperties nacosDiscoveryProperties) {
      return new NacosServiceRegistry(nacosDiscoveryProperties);
   }

   @Bean
   @ConditionalOnBean(AutoServiceRegistrationProperties.class)
   public NacosRegistration nacosRegistration(
         ObjectProvider<List<NacosRegistrationCustomizer>> registrationCustomizers,
         NacosDiscoveryProperties nacosDiscoveryProperties,
         ApplicationContext context) {
      return new NacosRegistration(registrationCustomizers.getIfAvailable(),
            nacosDiscoveryProperties, context);
   }

   @Bean
   @ConditionalOnBean(AutoServiceRegistrationProperties.class)
   public NacosAutoServiceRegistration nacosAutoServiceRegistration(
         NacosServiceRegistry registry,
         AutoServiceRegistrationProperties autoServiceRegistrationProperties,
         NacosRegistration registration) {
      return new NacosAutoServiceRegistration(registry,
            autoServiceRegistrationProperties, registration);
   }

}
```



#### 1.2.3.2 NacosAutoServiceRegistration

​	其实这个类就是注册的核心，所以我们来看一下它的继承关系：

![image-20211018164305887](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ecb33368-22c9-5e9a-ac33-b7c8150bc9e9.png)

通过这里我们可以清楚的知道：

​	NacosAutoServiceRegistration继承了AbstractAutoServiceRegistration而这个类型实现了ApplicationListener接口，所以我们由此得出一般实现ApplicationListener接口的类型都会实现一个方法"onApplicationEvent"，这个方法会在项目启动的时候触发

```java
public void onApplicationEvent(WebServerInitializedEvent event) {
    bind(event);
}

@Deprecated
public void bind(WebServerInitializedEvent event) {
    ApplicationContext context = event.getApplicationContext();
    if (context instanceof ConfigurableWebServerApplicationContext) {
        if ("management".equals(((ConfigurableWebServerApplicationContext) context)
                                .getServerNamespace())) {
            return;
        }
    }
    this.port.compareAndSet(0, event.getWebServer().getPort());
    this.start();
}
```

然后在start()方法中调用register()方法来注册服务

```java
public void start() {
    if (!isEnabled()) {
        if (logger.isDebugEnabled()) {
            logger.debug("Discovery Lifecycle disabled. Not starting");
        }
        return;
    }

    // only initialize if nonSecurePort is greater than 0 and it isn't already running
    // because of containerPortInitializer below
    if (!this.running.get()) {
        this.context.publishEvent(
            new InstancePreRegisteredEvent(this, getRegistration()));
        register();
        if (shouldRegisterManagement()) {
            registerManagement();
        }
        this.context.publishEvent(
            new InstanceRegisteredEvent<>(this, getConfiguration()));
        this.running.compareAndSet(false, true);
    }

}
```



#### 1.2.3.3 serviceRegistry.register

​	分析到这里，我们已经知道了真实服务注册的入口和具体调用那个方法来注册，那我们再来分析一下register这个方法

```java
protected void register() {
    this.serviceRegistry.register(getRegistration());
}
```

但是这里要注意serviceRegistry实际上是一个接口，所以我们来看一下它的具体实现类NacosServiceRegistry:

![image-20211018174528632](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/aba00bdf-5cff-5b88-a6b6-970a0156381b.png)

找到这个实现类然后我们来查看register方法，到这里其实大家应该已经明白了，因为这里调用了我们上节课讲过的registerInstance注册实例方法

```java
@Override
public void register(Registration registration) {

    if (StringUtils.isEmpty(registration.getServiceId())) {
        log.warn("No service to register for nacos client...");
        return;
    }
	
    NamingService namingService = namingService();
    String serviceId = registration.getServiceId();
    String group = nacosDiscoveryProperties.getGroup();
	//构建instance实例
    Instance instance = getNacosInstanceFromRegistration(registration);

    try {
        //向服务端注册此服务
        namingService.registerInstance(serviceId, group, instance);
        log.info("nacos registry, {} {} {}:{} register finished", group, serviceId,
                 instance.getIp(), instance.getPort());
    }
    catch (Exception e) {
        log.error("nacos registry, {} register failed...{},", serviceId,
                  registration.toString(), e);
        // rethrow a RuntimeException if the registration is failed.
        // issue : https://github.com/alibaba/spring-cloud-alibaba/issues/1132
        rethrowRuntimeException(e);
    }
}
```



**调用接口**

​	其实到这里大家应该已经明白Nacos客户端的服务注册过程了，但是其实再给大家补充一点，就是其实注册本身就是访问了Nacos提供的一个接口，我们可以在官网上看到

![image-20211018181341616](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ce387317-c2b4-532b-94e6-311a7f60b40d.png)

那我们可以通过deBug来看一下，在NacosServiceRegistry中的register方法中，在注册实例方法中打断点

![image-20211018181758997](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/28101aae-e515-5ad4-83c1-6726c8302f5c.png)

然后在NamingService的实现类NacosNamingService中registerInstance方法中打断点

![image-20211018182041864](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d4aabcd4-56a3-5a43-b7a9-a09df17d8773.png)

然后进入到这个registerService方法中进行查看，就会发现这里就会把实例信息放到散列表中然后调用reqApi方法来发送请求访问接口/nacos/v1/ns/instance

![image-20211018182244138](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e2cb6b77-a632-5cd6-a6e1-241027716e0d.png)



总结：

![20210415094809249](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6b80acaf-b420-518b-a7f5-0fb9f1714e43.png)

### 1.2.4 Nacos服务端服务注册源码分析



#### 1.2.4.1 服务端调用接口

​	通过上节课的学习我们已经知道客户端在注册服务的时候实际上是调用的**NamingService.registerInstance**这个方法来完成实例的注册，而且在最后我们也告诉了大家实际上从本质上讲服务注册就是调用的对应接口nacos/v1/ns/instance，那咱们现在就在服务端先找到这个接口，然后来看具体服务端的操作。

![nacos_arch.jpg](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7a2f614b-9545-5a8f-9abc-8690f3609c6d.jpeg)

​	这是从Nacos官网上我们看到的Nacos架构图，其实在这里我们已经就能分析出我们要找的接口应该在NamingService这个服务中，从源码角度来看，其实通过一下这个项目结构图中我们也能清楚的看见naming这个子模块，而且在源码的第一节课就和大家分析过这个naming实际上就是实现服务的注册的。

![20190703005808640](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8859fd20-9053-5a37-8285-0076d200105e.png)

​	那我们接着来向下看这个项目中的**controller**，因为我们知道所有的接口其实都在controller中，从这些Controller中我们就会明显的看到一个**InstanceController**，所以很明显注册实例一定和它有关

![image-20211019160948545](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/646b84a7-8d66-5f56-b1e5-3033a775debf.png)

​		所以我们打开**InstanceController**来深入研究一下，这个时候会发现@RequestMapping注解中的值就是我们访问的**注册接口**

![image-20211019161614434](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f180e03d-a7bc-5c96-890c-2acae5645f23.png)

![image-20211019161726808](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e39fd7b1-9618-5c3b-8969-2e8d87c4e495.png)

​	接下来我们再来寻找RESTful API接口POST请求类型的方法register，在这个方法中实际上就是接受用户请求，把收到的信息进行解析，还原成Instance，然后调用**registerInstance**方法来完成注册，这个方法才是服务端注册的核心

```java
@CanDistro
@PostMapping
@Secured(parser = NamingResourceParser.class, action = ActionTypes.WRITE)
public String register(HttpServletRequest request) throws Exception {

    final String namespaceId = WebUtils
        .optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
    final String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
    NamingUtils.checkServiceNameFormat(serviceName);

    final Instance instance = HttpRequestInstanceBuilder.newBuilder()
        .setDefaultInstanceEphemeral(switchDomain.isDefaultInstanceEphemeral()).setRequest(request).build();
	//注册服务实例
    getInstanceOperator().registerInstance(namespaceId, serviceName, instance);
    return "ok";
}
```

​	各位这个位置我们注意一下的这个方法

```java
getInstanceOperator().registerInstance(namespaceId, serviceName, instance);
```

​	其中的getInstanceOperator()，就是判断是否采用Grpc协议，很明显这个位置走的是instanceServiceV2

```java
private InstanceOperator getInstanceOperator() {
    return upgradeJudgement.isUseGrpcFeatures() ? instanceServiceV2 : instanceServiceV1;
}
```

#### 1.2.4.2 服务注册

**instanceServiceV2.registerInstance**

​	实际上instanceServiceV2就是InstanceOperatorClientImpl，所以我们来看这里面的registerInstance方法

```java
@Override
public void registerInstance(String namespaceId, String serviceName, Instance instance) {
    //判断是否为瞬时对象（临时客户端）
    boolean ephemeral = instance.isEphemeral();
    //获取客户端ID
    String clientId = IpPortBasedClient.getClientId(instance.toInetAddr(), ephemeral);
    //通过客户端ID创建客户端连接
    createIpPortClientIfAbsent(clientId);
    //获取服务
    Service service = getService(namespaceId, serviceName, ephemeral);
    //具体注册服务
    clientOperationService.registerInstance(service, instance, clientId);
}
```

​	在这里我们要分析一些细节，其实Nacos2.0以后新增**Client模型**。**一个客户端gRPC长连接对应一个Client，每个Client有自己唯一的id（clientId）**。Client负责管理一个客户端的服务实例注册Publish和服务订阅Subscribe。我们可以看一下这个模型其实就是一个接口（为了大家看着方便，注释改成了中文）

```java
public interface Client {
    // 客户端id/gRPC的connectionId
    String getClientId();

    // 是否临时客户端
    boolean isEphemeral();
    // 客户端更新时间
    void setLastUpdatedTime();
    long getLastUpdatedTime();

    // 服务实例注册/注销/查询
    boolean addServiceInstance(Service service, InstancePublishInfo instancePublishInfo);
    InstancePublishInfo removeServiceInstance(Service service);
    InstancePublishInfo getInstancePublishInfo(Service service);
    Collection<Service> getAllPublishedService();

    // 服务订阅/取消订阅/查询订阅
    boolean addServiceSubscriber(Service service, Subscriber subscriber);
    boolean removeServiceSubscriber(Service service);
    Subscriber getSubscriber(Service service);
    Collection<Service> getAllSubscribeService();
    // 生成同步给其他节点的client数据
    ClientSyncData generateSyncData();
    // 是否过期
    boolean isExpire(long currentTime);
    // 释放资源
    void release();
}
```

**EphemeralClientOperationServiceImpl.registerInstance**

​	**EphemeralClientOperationServiceImpl**实际负责处理服务注册，那我们来看具体方法

```java
 @Override
public void registerInstance(Service service, Instance instance, String clientId) {
    //确保Service单例存在
    Service singleton = ServiceManager.getInstance().getSingleton(service);
    //根据客户端id，找到客户端
    Client client = clientManager.getClient(clientId);
    if (!clientIsLegal(client, clientId)) {
        return;
    }
    //客户端Instance模型，转换为服务端Instance模型
    InstancePublishInfo instanceInfo = getPublishInfo(instance);
    //将Instance储存到Client里
    client.addServiceInstance(singleton, instanceInfo);
    client.setLastUpdatedTime();
    //建立Service与ClientId的关系
    NotifyCenter.publishEvent(new ClientOperationEvent.ClientRegisterServiceEvent(singleton, clientId));
    NotifyCenter
        .publishEvent(new MetadataEvent.InstanceMetadataEvent(singleton, instanceInfo.getMetadataId(), false));
}
```

**ServiceManager**

​	Service的容器是**ServiceManager**，但是在com.alibaba.nacos.naming.core.v2包下，容器中Service都是单例。

```java
public class ServiceManager {
    
    private static final ServiceManager INSTANCE = new ServiceManager();
    //单例Service，可以查看Service的equals和hasCode方法
    private final ConcurrentHashMap<Service, Service> singletonRepository;
    //namespace下的所有service
    private final ConcurrentHashMap<String, Set<Service>> namespaceSingletonMaps;
    .....
}
```

​	所以从这个位置可以看出，当调用这个注册方法的时候**ServiceManager**负责管理Service单例

```java
//通过Map储存单例的Service
public Service getSingleton(Service service) {
    singletonRepository.putIfAbsent(service, service);
    Service result = singletonRepository.get(service);
    namespaceSingletonMaps.computeIfAbsent(result.getNamespace(), (namespace) -> new ConcurrentHashSet<>());
    namespaceSingletonMaps.get(result.getNamespace()).add(result);
    return result;
}
```

**clientManager**

​	这是一个接口这里我们要看它对应的一个实现类**ConnectionBasedClientManager**，这个实现类负责管理长连接clientId与Client模型的映射关系

```java
// 根据clientId查询Client
public Client getClient(String clientId) {
    return clients.get(clientId);
}
```

**Client实例**AbstractClient****

负责存储当前客户端的服务注册表，即Service与Instance的关系。注意**对于单个客户端来说，同一个服务只能注册一个实例**。

```java
//
@Override
public boolean addServiceInstance(Service service, InstancePublishInfo instancePublishInfo) {
    if (null == publishers.put(service, instancePublishInfo)) {
        MetricsMonitor.incrementInstanceCount();
    }
    NotifyCenter.publishEvent(new ClientEvent.ClientChangedEvent(this));
    Loggers.SRV_LOG.info("Client change for service {}, {}", service, getClientId());
    return true;
}
```

**ClientOperationEvent.ClientRegisterServiceEvent**

​	这里的目的是为了过滤目标服务得到最终Instance列表建立Service与Client的关系，建立Service与Client的关系就是为了加速查询。

​	发布**ClientRegisterServiceEvent**事件，ClientServiceIndexesManager监听，ClientServiceIndexesManager维护了两个索引：

- Service与发布clientId
- Service与订阅clientId

```java
private final ConcurrentMap<Service, Set<String>> publisherIndexes = new ConcurrentHashMap<>();
    
private final ConcurrentMap<Service, Set<String>> subscriberIndexes = new ConcurrentHashMap<>();

private void handleClientOperation(ClientOperationEvent event) {
    Service service = event.getService();
    String clientId = event.getClientId();
    if (event instanceof ClientOperationEvent.ClientRegisterServiceEvent) {
        addPublisherIndexes(service, clientId);
    } else if (event instanceof ClientOperationEvent.ClientDeregisterServiceEvent) {
        removePublisherIndexes(service, clientId);
    } else if (event instanceof ClientOperationEvent.ClientSubscribeServiceEvent) {
        addSubscriberIndexes(service, clientId);
    } else if (event instanceof ClientOperationEvent.ClientUnsubscribeServiceEvent) {
        removeSubscriberIndexes(service, clientId);
    }
}

//建立Service与发布Client的关系
private void addPublisherIndexes(Service service, String clientId) {
    publisherIndexes.computeIfAbsent(service, (key) -> new ConcurrentHashSet<>());
    publisherIndexes.get(service).add(clientId);
    NotifyCenter.publishEvent(new ServiceEvent.ServiceChangedEvent(service, true));
}
```

这个索引关系建立以后，还会触发**ServiceChangedEvent**，代表服务注册表变更。对于注册表变更紧接着还要做两个事情：**1.通知订阅客户端 2.Nacos集群数据同步**。这两点后续再说。



### 1.2.5 Nacos服务端健康检查

#### 1.2.5.1 长连接

概念：长连接，指在一个连接上可以连续发送多个[数据包](https://baike.baidu.com/item/数据包/489739)，在连接保持期间，如果没有数据包发送，需要双方发链路检测包

注册中心客户端2.0之后使用gRPC代替http，会与服务端建立长连接，但仍然保留了对旧http客户端的支持。

**NamingClientProxy**接口负责底层通讯，调用服务端接口。有三个实现类：

- **NamingClientProxyDelegate**：代理类，对所有NacosNamingService中的方法进行**代理**，根据实际情况选择http或gRPC协议请求服务端。
- **NamingGrpcClientProxy**：底层通讯基于gRPC长连接。
- **NamingHttpClientProxy**：底层通讯基于http短连接。使用的都是老代码基本没改，原来1.0NamingProxy重命名过来的。

以客户端**服务注册**为例，**NamingClientProxyDelegate**代理了registerService方法。

```java
// NacosNamingService.java
private NamingClientProxy clientProxy; // NamingClientProxyDelegate
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    clientProxy.registerService(serviceName, groupName, instance);
}
```

NamingClientProxyDelegate会**根据instance实例是否是临时节点而选择不同的协议**。

​	临时instance：gRPC

​	持久instance：http

```java
public class NamingClientProxyDelegate implements NamingClientProxy {
   private final NamingHttpClientProxy httpClientProxy;
   private final NamingGrpcClientProxy grpcClientProxy;
   @Override
    public void registerService(String serviceName, String groupName, Instance instance) throws NacosException {
      getExecuteClientProxy(instance).registerService(serviceName, groupName, instance);
    }
  // 临时节点，走grpc长连接；持久节点，走http短连接
  private NamingClientProxy getExecuteClientProxy(Instance instance) {
      return instance.isEphemeral() ? grpcClientProxy : httpClientProxy;
  }
}
```



#### 1.2.5.2 健康检查

​	在之前的1.x版本中临时实例走Distro协议内存存储，客户端向注册中心发送心跳来维持自身healthy状态，持久实例走Raft协议持久化存储，服务端定时与客户端建立tcp连接做健康检查。

​	但是2.0版本以后持久化实例没有什么变化，但是2.0临时实例不在使用心跳，而是通过长连接是否存活来判断实例是否健康。

**ConnectionManager**负责管理所有客户端的长连接。

**每3s**检测所有超过**20s没发生过通讯**的客户端，向客户端发起**ClientDetectionRequest探测请求**，如果客户端在**1s内成功响应，则检测通过**，否则执行unregister方法移除Connection。

**如果客户端持续与服务端通讯，服务端是不需要主动探活的**

```java
Map<String, Connection> connections = new ConcurrentHashMap<String, Connection>();
@PostConstruct
public void start() {

    // 启动不健康连接排除功能.
    RpcScheduledExecutor.COMMON_SERVER_EXECUTOR.scheduleWithFixedDelay(new Runnable() {
        @Override
        public void run() {
            try {

                int totalCount = connections.size();
                Loggers.REMOTE_DIGEST.info("Connection check task start");
                MetricsMonitor.getLongConnectionMonitor().set(totalCount);
                //统计过时（20s）连接
                Set<Map.Entry<String, Connection>> entries = connections.entrySet();
                int currentSdkClientCount = currentSdkClientCount();
                boolean isLoaderClient = loadClient >= 0;
                int currentMaxClient = isLoaderClient ? loadClient : connectionLimitRule.countLimit;
                int expelCount = currentMaxClient < 0 ? 0 : Math.max(currentSdkClientCount - currentMaxClient, 0);

                Loggers.REMOTE_DIGEST
                    .info("Total count ={}, sdkCount={},clusterCount={}, currentLimit={}, toExpelCount={}",
                          totalCount, currentSdkClientCount, (totalCount - currentSdkClientCount),
                          currentMaxClient + (isLoaderClient ? "(loaderCount)" : ""), expelCount);

                List<String> expelClient = new LinkedList<>();

                Map<String, AtomicInteger> expelForIp = new HashMap<>(16);

                //1. calculate expel count  of ip.
                for (Map.Entry<String, Connection> entry : entries) {

                    Connection client = entry.getValue();
                    String appName = client.getMetaInfo().getAppName();
                    String clientIp = client.getMetaInfo().getClientIp();
                    if (client.getMetaInfo().isSdkSource() && !expelForIp.containsKey(clientIp)) {
                        //get limit for current ip.
                        int countLimitOfIp = connectionLimitRule.getCountLimitOfIp(clientIp);
                        if (countLimitOfIp < 0) {
                            int countLimitOfApp = connectionLimitRule.getCountLimitOfApp(appName);
                            countLimitOfIp = countLimitOfApp < 0 ? countLimitOfIp : countLimitOfApp;
                        }
                        if (countLimitOfIp < 0) {
                            countLimitOfIp = connectionLimitRule.getCountLimitPerClientIpDefault();
                        }

                        if (countLimitOfIp >= 0 && connectionForClientIp.containsKey(clientIp)) {
                            AtomicInteger currentCountIp = connectionForClientIp.get(clientIp);
                            if (currentCountIp != null && currentCountIp.get() > countLimitOfIp) {
                                expelForIp.put(clientIp, new AtomicInteger(currentCountIp.get() - countLimitOfIp));
                            }
                        }
                    }
                }

                Loggers.REMOTE_DIGEST
                    .info("Check over limit for ip limit rule, over limit ip count={}", expelForIp.size());

                if (expelForIp.size() > 0) {
                    Loggers.REMOTE_DIGEST.info("Over limit ip expel info, {}", expelForIp);
                }

                Set<String> outDatedConnections = new HashSet<>();
                long now = System.currentTimeMillis();
                //2.get expel connection for ip limit.
                for (Map.Entry<String, Connection> entry : entries) {
                    Connection client = entry.getValue();
                    String clientIp = client.getMetaInfo().getClientIp();
                    AtomicInteger integer = expelForIp.get(clientIp);
                    if (integer != null && integer.intValue() > 0) {
                        integer.decrementAndGet();
                        expelClient.add(client.getMetaInfo().getConnectionId());
                        expelCount--;
                    } else if (now - client.getMetaInfo().getLastActiveTime() >= KEEP_ALIVE_TIME) {
                        outDatedConnections.add(client.getMetaInfo().getConnectionId());
                    }

                }

                //3. if total count is still over limit.
                if (expelCount > 0) {
                    for (Map.Entry<String, Connection> entry : entries) {
                        Connection client = entry.getValue();
                        if (!expelForIp.containsKey(client.getMetaInfo().clientIp) && client.getMetaInfo()
                            .isSdkSource() && expelCount > 0) {
                            expelClient.add(client.getMetaInfo().getConnectionId());
                            expelCount--;
                            outDatedConnections.remove(client.getMetaInfo().getConnectionId());
                        }
                    }
                }

                String serverIp = null;
                String serverPort = null;
                if (StringUtils.isNotBlank(redirectAddress) && redirectAddress.contains(Constants.COLON)) {
                    String[] split = redirectAddress.split(Constants.COLON);
                    serverIp = split[0];
                    serverPort = split[1];
                }

                for (String expelledClientId : expelClient) {
                    try {
                        Connection connection = getConnection(expelledClientId);
                        if (connection != null) {
                            ConnectResetRequest connectResetRequest = new ConnectResetRequest();
                            connectResetRequest.setServerIp(serverIp);
                            connectResetRequest.setServerPort(serverPort);
                            connection.asyncRequest(connectResetRequest, null);
                            Loggers.REMOTE_DIGEST
                                .info("Send connection reset request , connection id = {},recommendServerIp={}, recommendServerPort={}",
                                      expelledClientId, connectResetRequest.getServerIp(),
                                      connectResetRequest.getServerPort());
                        }

                    } catch (ConnectionAlreadyClosedException e) {
                        unregister(expelledClientId);
                    } catch (Exception e) {
                        Loggers.REMOTE_DIGEST.error("Error occurs when expel connection, expelledClientId:{}", expelledClientId, e);
                    }
                }

                //4.client active detection.
                Loggers.REMOTE_DIGEST.info("Out dated connection ,size={}", outDatedConnections.size());
                //异步请求所有需要检测的连接
                if (CollectionUtils.isNotEmpty(outDatedConnections)) {
                    Set<String> successConnections = new HashSet<>();
                    final CountDownLatch latch = new CountDownLatch(outDatedConnections.size());
                    for (String outDateConnectionId : outDatedConnections) {
                        try {
                            Connection connection = getConnection(outDateConnectionId);
                            if (connection != null) {
                                ClientDetectionRequest clientDetectionRequest = new ClientDetectionRequest();
                                connection.asyncRequest(clientDetectionRequest, new RequestCallBack() {
                                    @Override
                                    public Executor getExecutor() {
                                        return null;
                                    }

                                    @Override
                                    public long getTimeout() {
                                        return 1000L;
                                    }

                                    @Override
                                    public void onResponse(Response response) {
                                        latch.countDown();
                                        if (response != null && response.isSuccess()) {
                                            connection.freshActiveTime();
                                            successConnections.add(outDateConnectionId);
                                        }
                                    }

                                    @Override
                                    public void onException(Throwable e) {
                                        latch.countDown();
                                    }
                                });

                                Loggers.REMOTE_DIGEST
                                    .info("[{}]send connection active request ", outDateConnectionId);
                            } else {
                                latch.countDown();
                            }

                        } catch (ConnectionAlreadyClosedException e) {
                            latch.countDown();
                        } catch (Exception e) {
                            Loggers.REMOTE_DIGEST
                                .error("[{}]Error occurs when check client active detection ,error={}",
                                       outDateConnectionId, e);
                            latch.countDown();
                        }
                    }

                    latch.await(3000L, TimeUnit.MILLISECONDS);
                    Loggers.REMOTE_DIGEST
                        .info("Out dated connection check successCount={}", successConnections.size());
					// 对于没有成功响应的客户端，执行unregister移出
                    for (String outDateConnectionId : outDatedConnections) {
                        if (!successConnections.contains(outDateConnectionId)) {
                            Loggers.REMOTE_DIGEST
                                .info("[{}]Unregister Out dated connection....", outDateConnectionId);
                            unregister(outDateConnectionId);
                        }
                    }
                }

                //reset loader client

                if (isLoaderClient) {
                    loadClient = -1;
                    redirectAddress = null;
                }

                Loggers.REMOTE_DIGEST.info("Connection check task end");

            } catch (Throwable e) {
                Loggers.REMOTE.error("Error occurs during connection check... ", e);
            }
        }
    }, 1000L, 3000L, TimeUnit.MILLISECONDS);

}

//注销（移出）连接方法
public synchronized void unregister(String connectionId) {
    Connection remove = this.connections.remove(connectionId);
    if (remove != null) {
        String clientIp = remove.getMetaInfo().clientIp;
        AtomicInteger atomicInteger = connectionForClientIp.get(clientIp);
        if (atomicInteger != null) {
            int count = atomicInteger.decrementAndGet();
            if (count <= 0) {
                connectionForClientIp.remove(clientIp);
            }
        }
        remove.close();
        Loggers.REMOTE_DIGEST.info("[{}]Connection unregistered successfully. ", connectionId);
        clientConnectionEventListenerRegistry.notifyClientDisConnected(remove);
    }
}
```

移除connection后，继承ClientConnectionEventListener的**ConnectionBasedClientManager**会移除Client，发布**ClientDisconnectEvent事件**。

```java
@Override
public boolean clientDisconnected(String clientId) {
    Loggers.SRV_LOG.info("Client connection {} disconnect, remove instances and subscribers", clientId);
    ConnectionBasedClient client = clients.remove(clientId);
    if (null == client) {
        return true;
    }
    client.release();
    NotifyCenter.publishEvent(new ClientEvent.ClientDisconnectEvent(client));
    return true;
}
```

ClientDisconnectEvent会触发几个事件：

**1）Distro协议**：同步移除的client数据

**2）清除两个索引缓存**：ClientServiceIndexesManager中Service与发布Client的关系；ServiceStorage中Service与Instance的关系

**3）服务订阅**：ClientDisconnectEvent会**间接触发ServiceChangedEvent事件**，将服务变更通知客户端。

### 1.2.6 Nacos客户端服务发现源码分析

#### 1.2.6.1 总体流程

首先我们先通过一个图来直观的看一下，Nacos客户端的服务发现，其实就是封装参数、调用服务接口、获得返回实例列表。

![image-20211022150934273](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a7a3033c-8f47-536f-b618-8f80ad06a483.png)

​	但是如果我们要是细化这个流程，会发现不仅包括了通过NamingService获取服务列表，在获取服务列表的过程中还涉及到通信流程协议（Http or gPRC）、订阅流程、故障转移流程等。下面我们来详细的捋一捋。

​	入口，其实这个入口我们在之前看过，就在NamingTest中可以看到：

```java
public class NamingTest {
    
    @Test
    public void testServiceList() throws Exception {
   	......
    
        NamingService namingService = NacosFactory.createNamingService(properties);
        namingService.registerInstance("nacos.test.1", instance);
        
        ThreadUtils.sleep(5000L);
        
        List<Instance> list = namingService.getAllInstances("nacos.test.1");
        
        System.out.println(list);
        
    }
}
```

​	在这里我们主要要关注getAllInstances方法，那我们就需要看一下这个方法的具体操作，当然这其中需要经过一系列的重载方法调用

​	其实这里的方法比入口多出了几个参数，这里不仅有服务名称，还有分组名、集群列表、是否订阅，重载方法中的其他参数已经在各种重载方法的调用过程中设置了默认值，比如：

​	分组名称默认：DEFAULT_GROUOP

​	集群列表：默认为空数组

​	是否订阅：订阅

```java
@Override
public List<Instance> getAllInstances(String serviceName, String groupName, List<String> clusters,
                                      boolean subscribe) throws NacosException {
    ServiceInfo serviceInfo;
    String clusterString = StringUtils.join(clusters, ",");
    // 是否是订阅模式
    if (subscribe) {
        // 先从客户端缓存获取服务信息
        serviceInfo = serviceInfoHolder.getServiceInfo(serviceName,  , clusterString);
        if (null == serviceInfo) {
            // 如果本地缓存不存在服务信息，则进行订阅
            serviceInfo = clientProxy.subscribe(serviceName, groupName, clusterString);
        }
    } else {
        // 如果未订阅服务信息，则直接从服务器进行查询
        serviceInfo = clientProxy.queryInstancesOfService(serviceName, groupName, clusterString, 0, false);
    }
    // 从服务信息中获取实例列表
    List<Instance> list;
    if (serviceInfo == null || CollectionUtils.isEmpty(list = serviceInfo.getHosts())) {
        return new ArrayList<Instance>();
    }
    return list;
}
```

​	此方法的流程图：

![image-20211022171827857](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/dae273bc-133c-5e82-a8a4-83eb71ab62dc.png)



​	这个流程基本逻辑为：

​		如果是订阅模式，则直接从本地缓存获取服务信息(ServiceInfo)，然后从中获取实例列表，这是因为订阅机制会自动同步服务器实例的变化到本地。如果本地缓存中没有，那说明是首次调用，则进行订阅，在订阅完成后会获得服务信息。

​		如果是非订阅模式，那就直接请求服务器端，获得服务信息。

#### 1.2.6.2 订阅处理流程

​	在刚才的流程中，涉及到了订阅的逻辑，入口代码为获取实例列表中的方法：

```java
serviceInfo = clientProxy.subscribe(serviceName, groupName, clusterString);
```

​	以下是具体分析。首先这里的clientProxy是NamingClientProxy类的对象，对应的实现类是NamingClientProxyDelegate对应subscribe实现如下：

```java
@Override
public ServiceInfo subscribe(String serviceName, String groupName, String clusters) throws NacosException {
    String serviceNameWithGroup = NamingUtils.getGroupedName(serviceName, groupName);
    String serviceKey = ServiceInfo.getKey(serviceNameWithGroup, clusters);
    // 定时调度UpdateTask
    serviceInfoUpdateService.scheduleUpdateIfAbsent(serviceName, groupName, clusters);
    // 获取缓存中的ServiceInfo
    ServiceInfo result = serviceInfoHolder.getServiceInfoMap().get(serviceKey);
    if (null == result) {
        // 如果为null，则进行订阅逻辑处理，基于gRPC协议
        result = grpcClientProxy.subscribe(serviceName, groupName, clusters);
    }
    // ServiceInfo本地缓存处理
    serviceInfoHolder.processServiceInfo(result);
    return result;
}
```

​	在这段代码中，可以看到在获取服务实例列表时（特别是首次），也进行了订阅逻辑的拓展，基本流程图如下：

![image-20211025155023146](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ca9a8649-e7d8-5eb9-92b2-56ced5258dac.png)

​	具体流程分析：

1\. 订阅方法先开启定时任务，这个定时任务的主要作用就是用来定时同步服务端的实例信息，并进行本地缓存更新等操作，但是如果是首次这里将会直接返回来走下一步。
2\. 判断本地缓存是否存在，如果本地缓存存在ServiceInfo信息，则直接返回。如果不存在，则默认采用gRPC协议进行订阅，并返回ServiceInfo。
3\. grpcClientProxy的subscribe订阅方法就是直接向服务器发送了一个订阅请求，并返回结果。
4\. 最后，ServiceInfo本地缓存处理。这里会将获得的最新ServiceInfo与本地内存中的ServiceInfo进行比较，更新，发布变更时间，磁盘文件存储等操作。其实，这一步的操作，在订阅定时任务中也进行了处理。

### 1.2.7 Nacos客户端服务订阅机制的核心流程

​	说起Nacos的服务订阅机制，大家会觉得比较难理解，那我们就来详细分析一下，那我们先从Nacos订阅的概述说起



#### 1.2.7.1 Nacos订阅概述

​	Nacos的订阅机制，如果用一句话来描述就是：Nacos客户端通过一个定时任务，每6秒从注册中心获取实例列表，当发现实例发生变化时，发布变更事件，订阅者进行业务处理（更新实例，更改本地缓存）。

​	以下是订阅方法的主线流程，涉及内容比较多，细节比较复杂，所以这里我们主要学习核心部分。

![image-20211025182722346](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7da6c01f-adf3-5bba-bb12-f3c88ed3bf2a.png)



#### 1.2.7.2 定时任务开启

​	其实订阅本质上就是服务发现的一种方式，也就是在服务发现的时候执行订阅方法，触发定时任务去拉取服务端的数据。

​	NacosNamingService中暴露的许多重载的subscribe，重载的目的就是让大家少写一些参数，这些参数呢，Nacos给默认处理了。最终这些重载方法都会调用到下面这个方法：

```java
@Override
public void subscribe(String serviceName, String groupName, List<String> clusters, EventListener listener)
    throws NacosException {
    if (null == listener) {
        return;
    }
    String clusterString = StringUtils.join(clusters, ",");
    changeNotifier.registerListener(groupName, serviceName, clusterString, listener);
    clientProxy.subscribe(serviceName, groupName, clusterString);
}
```

​	这里我们先来看subscribe方法，大家可能有些眼熟它是clientProxy类型调用的方法，实际上就是NamingClientProxyDelegate.subscribe()，所以其实这里和之前的服务发现中调用的是一个方法，这里其实是在做服务列表的查询，所以得出结论查询和订阅都调用了同一个方法

```java
@Override
public ServiceInfo subscribe(String serviceName, String groupName, String clusters) throws NacosException {
    String serviceNameWithGroup = NamingUtils.getGroupedName(serviceName, groupName);
    String serviceKey = ServiceInfo.getKey(serviceNameWithGroup, clusters);
    // 定时调度UpdateTask
    serviceInfoUpdateService.scheduleUpdateIfAbsent(serviceName, groupName, clusters);
    // 获取缓存中的ServiceInfo
    ServiceInfo result = serviceInfoHolder.getServiceInfoMap().get(serviceKey);
    if (null == result) {
        // 如果为null，则进行订阅逻辑处理，基于gRPC协议
        result = grpcClientProxy.subscribe(serviceName, groupName, clusters);
    }
    // ServiceInfo本地缓存处理
    serviceInfoHolder.processServiceInfo(result);
    return result;
}
```

​	但是这里我们要关注这里的任务调度，该方法包含了构建serviceKey、通过serviceKey判断重复、最后添加UpdateTask，而其中的addTask的实现就是发起了一个定时任务：

```java
public void scheduleUpdateIfAbsent(String serviceName, String groupName, String clusters) {
    String serviceKey = ServiceInfo.getKey(NamingUtils.getGroupedName(serviceName, groupName), clusters);
    if (futureMap.get(serviceKey) != null) {
        return;
    }
    synchronized (futureMap) {
        if (futureMap.get(serviceKey) != null) {
            return;
        }
		//构建UpdateTask
        ScheduledFuture<?> future = addTask(new UpdateTask(serviceName, groupName, clusters));
        futureMap.put(serviceKey, future);
    }
}
```

​	定时任务延迟一秒执行：

```java
private synchronized ScheduledFuture<?> addTask(UpdateTask task) {
    return executor.schedule(task, DEFAULT_DELAY, TimeUnit.MILLISECONDS);
}
```

​	所以在这里我们得出结论，核心为：调用订阅方法和发起定时任务。

#### 1.2.7.3 定时任务执行内容

​	UpdateTask封装了订阅机制的核心业务逻辑，我们来看一下流程图：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5799d1a1-d718-5984-82e2-ac52287dbe88.jpg" alt="流程" style="zoom: 50%;" />

​	当我们知道了整体流程以后，我们再来看对应源码：

```java
@Override
public void run() {
    long delayTime = DEFAULT_DELAY;

    try {
        // 判断是服务是否订阅和未开启过定时任务，如果订阅过直接不在执行
        if (!changeNotifier.isSubscribed(groupName, serviceName, clusters) && !futureMap.containsKey(serviceKey)) {
            NAMING_LOGGER
                .info("update task is stopped, service:{}, clusters:{}", groupedServiceName, clusters);
            return;
        }
		
        // 获取缓存的service信息
        ServiceInfo serviceObj = serviceInfoHolder.getServiceInfoMap().get(serviceKey);
        // 如果为空
        if (serviceObj == null) {
            // 根据serviceName从注册中心服务端获取Service信息
            serviceObj = namingClientProxy.queryInstancesOfService(serviceName, groupName, clusters, 0, false);
            // 处理本地缓存
            serviceInfoHolder.processServiceInfo(serviceObj);
            lastRefTime = serviceObj.getLastRefTime();
            return;
        }
		
        // 过期服务，服务的最新更新时间小于等于缓存刷新（最后一次拉取数据的时间）时间，从注册中心重新查询
        if (serviceObj.getLastRefTime() <= lastRefTime) {
            serviceObj = namingClientProxy.queryInstancesOfService(serviceName, groupName, clusters, 0, false);
            // 处理本地缓存
            serviceInfoHolder.processServiceInfo(serviceObj);
        }
        //刷新更新时间
        lastRefTime = serviceObj.getLastRefTime();
        if (CollectionUtils.isEmpty(serviceObj.getHosts())) {
            incFailCount();
            return;
        }
        // 下次更新缓存时间设置，默认6秒
        // TODO multiple time can be configured.
        delayTime = serviceObj.getCacheMillis() * DEFAULT_UPDATE_CACHE_TIME_MULTIPLE;
        // 重置失败数量为0(可能会出现失败情况，没有ServiceInfo，连接失败)
        resetFailCount();
    } catch (Throwable e) {
        incFailCount();
        NAMING_LOGGER.warn("[NA] failed to update serviceName: {}", groupedServiceName, e);
    } finally {
        // 下次调度刷新时间，下次执行的时间与failCount有关，failCount=0，则下次调度时间为6秒，最长为1分钟
        // 即当无异常情况下缓存实例的刷新时间是6秒
        executor.schedule(this, Math.min(delayTime << failCount, DEFAULT_DELAY * 60), TimeUnit.MILLISECONDS);
    }
}
```

​	业务逻辑最后会计算下一次定时任务的执行时间，通过delayTime来延迟执行。delayTime默认为 1000L * 6，也就是6秒。而在finally里面真的发起下一次定时任务。当出现异常时，下次执行的时间与失败次数有关，但最长不超过1分钟。

**总结：**

1\. 订阅方法的调用，并进行EventListener的注册，后面UpdateTask要用来进行判断；

2\. 通过委托代理类来处理订阅逻辑，此处与获取实例列表方法使用了同一个方法；

3\. 通过定时任务执行UpdateTask方法，默认执行间隔为6秒，当发生异常时会延长，但不超过1分钟；

5\. UpdateTask方法中会比较本地是否存在缓存，缓存是否过期。当不存在或过期时，查询注册中心，获取最新实例，更新最后获取时间，处理ServiceInfo。

6\. 重新计算定时任务时间，循环执行流程。

### 1.2.8 Nacos客户端服务订阅的事件机制剖析

​	上节课我们已经分析了Nacos客户端订阅的核心流程：Nacos客户端通过一个定时任务，每6秒从注册中心获取实例列表，当发现实例发生变化时，发布变更事件，订阅者进行业务处理，然后更新内存中和本地的缓存中的实例。

​	这节课我们来分析，定时任务获取到最新实例列表之后，整个事件机制是如何处理的，首先我们先回顾整体流程

![image-20211025182722346](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7da6c01f-adf3-5bba-bb12-f3c88ed3bf2a.png)

​	在第一步调用subscribe方法时，会订阅一个EventListener事件。而在定时任务UpdateTask定时获取实例列表之后，会调用ServiceInfoHolder.processServiceInfo方法对ServiceInfo进行本地处理，这其中就包括和事件处理。



#### 1.2.8.1 监听事件的注册

​	在subscribe方法中，通过了下面的源码进行了监听事件的注册：

```java
@Override
public void subscribe(String serviceName, String groupName, List<String> clusters, EventListener listener)
    throws NacosException {
    if (null == listener) {
        return;
    }
    String clusterString = StringUtils.join(clusters, ",");
    changeNotifier.registerListener(groupName, serviceName, clusterString, listener);
    clientProxy.subscribe(serviceName, groupName, clusterString);
}
```

​	在这其中我们主要要关注的就是changeNotifier.registerListener，此监听就是进行具体事件注册逻辑的，我们来看一下源码：

​	可以看出，事件的注册便是将EventListener存储在InstancesChangeNotifier的listenerMap属性当中了。同时这里的数据结构为ConcurrentHashMap，key为服务实例的信息的拼接，value为监听事件的集合。

```java
public void registerListener(String groupName, String serviceName, String clusters, EventListener listener) {
    String key = ServiceInfo.getKey(NamingUtils.getGroupedName(serviceName, groupName), clusters);
    ConcurrentHashSet<EventListener> eventListeners = listenerMap.get(key);
    if (eventListeners == null) {
        synchronized (lock) {
            eventListeners = listenerMap.get(key);
            if (eventListeners == null) {
                eventListeners = new ConcurrentHashSet<EventListener>();
                //将EventListener缓存到listenerMap
                listenerMap.put(key, eventListeners);
            }
        }
    }
    eventListeners.add(listener);
}
```

#### 1.2.8.2 ServiceInfo处理

​	上面的源码中已经完成了事件的注册，现在就来追溯触发事件的来源，UpdateTask中获取到最新的实例会进行本地化处理，部分源码如下：

```java
// ServiceInfoUpdateService>UpdateTask>run()
ServiceInfo serviceObj = serviceInfoHolder.getServiceInfoMap().get(serviceKey);
if (serviceObj == null) {
    serviceObj = namingClientProxy.queryInstancesOfService(serviceName, groupName, clusters, 0, false);
    // 本地缓存处理
    serviceInfoHolder.processServiceInfo(serviceObj);
    lastRefTime = serviceObj.getLastRefTime();
    return;
}
```

​	这个run方法的详细逻辑昨天已经给大家分析过了，今天我们主要来看其中本地缓存处理的方法serviceInfoHolder.processServiceInfo，我们先来分析流程：

​	这个逻辑简单来说：判断新的ServiceInfo数据是否正确，是否发生了变化。如果数据格式正确，且发生变化，那就发布一个InstancesChangeEvent事件，同时将ServiceInfo写入本地缓存。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/323e469c-3c3f-5d14-af7b-204944895ad9.png" alt="流程" style="zoom: 80%;" />

```java
public ServiceInfo processServiceInfo(ServiceInfo serviceInfo) {
    String serviceKey = serviceInfo.getKey();
    if (serviceKey == null) {
        return null;
    }
    ServiceInfo oldService = serviceInfoMap.get(serviceInfo.getKey());
    if (isEmptyOrErrorPush(serviceInfo)) {
        //empty or error push, just ignore
        return oldService;
    }
    // 缓存服务信息
    serviceInfoMap.put(serviceInfo.getKey(), serviceInfo);
    // 判断注册的实例信息是否已变更
    boolean changed = isChangedServiceInfo(oldService, serviceInfo);
    if (StringUtils.isBlank(serviceInfo.getJsonFromServer())) {
        serviceInfo.setJsonFromServer(JacksonUtils.toJson(serviceInfo));
    }
    // 监控服务监控缓存Map的大小
    MetricsMonitor.getServiceInfoMapSizeMonitor().set(serviceInfoMap.size());
    // 服务实例以更变
    if (changed) {
        NAMING_LOGGER.info("current ips:({}) service: {} -> {}", serviceInfo.ipCount(), serviceInfo.getKey(),
                           JacksonUtils.toJson(serviceInfo.getHosts()));
        // 添加实例变更事件，会被订阅者执行
        NotifyCenter.publishEvent(new InstancesChangeEvent(serviceInfo.getName(), serviceInfo.getGroupName(),
                                                           serviceInfo.getClusters(), serviceInfo.getHosts()));
        // 记录Service本地文件
        DiskCache.write(serviceInfo, cacheDir);
    }
    return serviceInfo;
}
```

​	分析到这里我们发现其实这个重点应该在服务信息变更之后，发布的InstancesChangeEvent事件，这个事件是NotifyCenter进行发布的，我们来追踪一下源码



#### 1.2.8.3 事件追踪

​	NotifyCenter通知中心的核心流程如下：

![image-20211026161714936](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b31c5fbb-3189-51eb-a073-cbaf446ba2b7.png)

​	NotifyCenter中进行事件发布，发布的核心逻辑是：

1\. 根据InstancesChangeEvent事件类型，获得对应的CanonicalName
2\. 将CanonicalName作为key，从NotifyCenter.publisherMap中获取对应的事件发布者(EventPublisher)
3\. EventPublisher将InstancesChangeEvent事件进行发布

​	核心代码如下：

```java
private static boolean publishEvent(final Class<? extends Event> eventType, final Event event) {
    if (ClassUtils.isAssignableFrom(SlowEvent.class, eventType)) {
        return INSTANCE.sharePublisher.publish(event);
    }
	
    // 根据InstancesChangeEvent事件类型，获得对应的CanonicalName
    final String topic = ClassUtils.getCanonicalName(eventType);

    // 将CanonicalName作为Key，从NotifyCenter#publisherMap中获取对应的事件发布者（EventPublisher）
    EventPublisher publisher = INSTANCE.publisherMap.get(topic);
    if (publisher != null) {
        // 事件发布者publisher发布事件（InstancesChangeEvent）
        return publisher.publish(event);
    }
    LOGGER.warn("There are no [{}] publishers for this event, please register", topic);
    return false;
}
```

​	在这个源码中，其实INSTANCE为NotifyCenter的单例实现，那么这里的publisherMap中key(CanonicalName)和value(EventPublisher)之间的关系是什么时候建立的？

​	其实是在NacosNamingService实例化时调用init初始化方法中进行绑定的：

```java
// Publisher的注册过程在于建立InstancesChangeEvent.class与EventPublisher的关系。
NotifyCenter.registerToPublisher(InstancesChangeEvent.class, 16384);
```

​	这里再继续跟踪registerToPublisher方法就会发现默认采用了DEFAULT_PUBLISHER_FACTORY（默认发布者工厂）来进行构建，我们再继续跟踪会发现，在NotifyCenter中静态代码块，会发现DEFAULT_PUBLISHER_FACTORY默认构建的EventPublisher为DefaultPublisher。

```java
//NotifyCenter
public static EventPublisher registerToPublisher(final Class<? extends Event> eventType, final int queueMaxSize) {
    return registerToPublisher(eventType, DEFAULT_PUBLISHER_FACTORY, queueMaxSize);
}
--------------------------------------------------------------------------------------------
//NotifyCenter>static中部分代码
DEFAULT_PUBLISHER_FACTORY = (cls, buffer) -> {
    try {
        EventPublisher publisher = clazz.newInstance();
        publisher.init(cls, buffer);
        return publisher;
    } catch (Throwable ex) {
        LOGGER.error("Service class newInstance has error : ", ex);
        throw new NacosRuntimeException(SERVER_ERROR, ex);
    }
};
```

​	所以我们得出结论NotifyCenter中它维护了事件名称和事件发布者的关系，而默认的事件发布者为DefaultPublisher。



#### 1.2.8.4 DefaultPublisher的事件发布

​	我们现在来看一下默认事件发布者的源码，查看以后我们会发现它继承自Thread，也就是说它是一个线程类，同时，它又实现了EventPublisher，也就是发布者

```java
public class DefaultPublisher extends Thread implements EventPublisher
```

​	接下来我们来看它的init初始化方法，从这里我们可以看出当DefaultPublisher被初始化时，是以守护线程的方式运作的，其中还初始化了一个阻塞队列。

```java
@Override
public void init(Class<? extends Event> type, int bufferSize) {
    // 守护线程
    setDaemon(true);
    // 设置线程名字
    setName("nacos.publisher-" + type.getName());
    this.eventType = type;
    this.queueMaxSize = bufferSize;
    // 阻塞队列初始化
    this.queue = new ArrayBlockingQueue<>(bufferSize);
    start();
}
```

​	最后调用了start()方法：在这其中调用了super.start()启动线程

```java
@Override
public synchronized void start() {
    if (!initialized) {
        // start just called once
        super.start();
        if (queueMaxSize == -1) {
            queueMaxSize = ringBufferSize;
        }
        initialized = true;
    }
}
```

​	run()方法调用openEventHandler()方法

​	这里写了两个死循环，第一个死循环可以理解为延时效果，也就是说线程启动时最大延时60秒，在这60秒中每隔1秒判断一下当前线程是否关闭，是否有订阅者，是否超过60秒。如果满足一个条件，就可以提前跳出死循环。

​	而第二个死循环才是真正的业务逻辑处理，会从阻塞队列中取出一个事件，然后通过receiveEvent方法进行执行。

```java
@Override
public void run() {
    openEventHandler();
}

void openEventHandler() {
    try {

        // This variable is defined to resolve the problem which message overstock in the queue.
        int waitTimes = 60;
        // To ensure that messages are not lost, enable EventHandler when
        // waiting for the first Subscriber to register
        
        // 死循环延迟，线程启动最大延时60秒，这个主要是为了解决消息积压的问题。
        for (; ; ) {
            if (shutdown || hasSubscriber() || waitTimes <= 0) {
                break;
            }
            ThreadUtils.sleep(1000L);
            waitTimes--;
        }
		
        // 死循环不断的从队列中取出Event，并通知订阅者Subscriber执行Event
        for (; ; ) {
            if (shutdown) {
                break;
            }
            // 从队列中取出Event
            final Event event = queue.take();
            receiveEvent(event);
            UPDATER.compareAndSet(this, lastEventSequence, Math.max(lastEventSequence, event.sequence()));
        }
    } catch (Throwable ex) {
        LOGGER.error("Event listener exception : ", ex);
    }
}
```

​	队列中的事件哪里来的？其实就是DefaultPublisher的发布事件方法被调用了publish往阻塞队列中存入事件，如果存入失败，会直接调用receiveEvent。

​	可以理解为，如果向队列中存入失败，则立即执行，不走队列了。

```java
@Override
public boolean publish(Event event) {
    checkIsStart();
    // 向队列中插入事件元素
    boolean success = this.queue.offer(event);
    // 判断是否成功插入
    if (!success) {
        LOGGER.warn("Unable to plug in due to interruption, synchronize sending time, event : {}", event);
        // 失败直接执行
        receiveEvent(event);
        return true;
    }
    return true;
}
```

​	最后再来看receiveEvent方法的实现：这里其实就是遍历DefaultPublisher的subscribers（订阅者集合），然后执行通知订阅者的方法。

```java
void receiveEvent(Event event) {
    final long currentEventSequence = event.sequence();

    if (!hasSubscriber()) {
        LOGGER.warn("[NotifyCenter] the {} is lost, because there is no subscriber.", event);
        return;
    }

    // Notification single event listener
    // 通知订阅者执行Event
    for (Subscriber subscriber : subscribers) {
        // Whether to ignore expiration events
        if (subscriber.ignoreExpireEvent() && lastEventSequence > currentEventSequence) {
            LOGGER.debug("[NotifyCenter] the {} is unacceptable to this subscriber, because had expire",
                         event.getClass());
            continue;
        }

        // Because unifying smartSubscriber and subscriber, so here need to think of compatibility.
        // Remove original judge part of codes.
        notifySubscriber(subscriber, event);
    }
}
```

​	但是这里还有一个疑问，就是subscribers中订阅者哪里来的，这个还要回到NacosNamingService的init方法中：

```java
// 将Subscribe注册到Publisher
NotifyCenter.registerSubscriber(changeNotifier);
```

​	registerSubscriber方法最终会调用NotifyCenter的addSubscriber方法：核心逻辑就是将订阅事件、发布者、订阅者三者进行绑定。而发布者与事件通过Map进行维护、发布者与订阅者通过关联关系进行维护。

```java
private static void addSubscriber(final Subscriber consumer, Class<? extends Event> subscribeType,
                                  EventPublisherFactory factory) {

    final String topic = ClassUtils.getCanonicalName(subscribeType);
    synchronized (NotifyCenter.class) {
        // MapUtils.computeIfAbsent is a unsafe method.
        MapUtil.computeIfAbsent(INSTANCE.publisherMap, topic, factory, subscribeType, ringBufferSize);
    }
    // 获取事件对应的Publisher
    EventPublisher publisher = INSTANCE.publisherMap.get(topic);
    if (publisher instanceof ShardedEventPublisher) {
        ((ShardedEventPublisher) publisher).addSubscriber(consumer, subscribeType);
    } else {
        // 添加到subscribers集合
        publisher.addSubscriber(consumer);
    }
}
```

​	关系都已经梳理明确了，事件也有了，最后我们看一下DefaulePublisher中的notifySubscriber方法，这里就是真正的订阅者执行事件了。

```java
@Override
public void notifySubscriber(final Subscriber subscriber, final Event event) {

    LOGGER.debug("[NotifyCenter] the {} will received by {}", event, subscriber);
	//执行订阅者事件
    final Runnable job = () -> subscriber.onEvent(event);
    // 执行者
    final Executor executor = subscriber.executor();

    if (executor != null) {
        executor.execute(job);
    } else {
        try {
            job.run();
        } catch (Throwable e) {
            LOGGER.error("Event callback exception: ", e);
        }
    }
}
```

**总结**

​	整体服务订阅的事件机制还是比较复杂的，因为用到了事件的形式，逻辑比较绕，并且其中还有守护线程，死循环，阻塞队列等。

​	需要重点理解NotifyCenter对事件发布者、事件订阅者和事件之间关系的维护，而这一关系的维护的入口就位于NacosNamingService的init方法当中。

**核心流程梳理**

**ServiceInfoHolder中通过NotifyCenter发布了InstancesChangeEvent事件**

**NotifyCenter中进行事件发布，发布的核心逻辑是：**

- 根据InstancesChangeEvent事件类型，获得对应的CanonicalName
- 将CanonicalName作为Key，从NotifyCenter.publisherMap中获取对应的事件发布者（EventPublisher）
- EventPublisher将InstancesChangeEvent事件进行发布

**InstancesChangeEvent事件发布：**

- 通过EventPublisher的实现类DefaultPublisher进行InstancesChangeEvent事件发布
- DefaultPublisher本身以守护线程的方式运作，在执行业务逻辑前，先判断该线程是否启动
- 如果启动，则将事件添加到BlockingQueue中，队列默认大小为16384
- 添加到BlockingQueue成功，则整个发布过程完成
- 如果添加失败，则直接调用DefaultPublisher.receiveEvent方法，接收事件并通知订阅者
- 通知订阅者时创建一个Runnable对象，执行订阅者的Event
- Event事件便是执行订阅时传入的事件

**如果添加到BlockingQueue成功，则走另外一个业务逻辑：**

- DefaultPublisher初始化时会创建一个阻塞（BlockingQueue）队列，并标记线程启动
- DefaultPublisher本身是一个Thread，当执行super.start方法时，会调用它的run方法
- run方法的核心业务逻辑是通过openEventHandler方法处理的
- openEventHandler方法通过两个for循环，从阻塞队列中获取时间信息
- 第一个for循环用于让线程启动时在60s内检查执行条件
- 第二个for循环为死循环，从阻塞队列中获取Event，并调用DefaultPublisher#receiveEvent方法，接收事件并通知订阅者
- Event事件便是执行订阅时传入的事件

![服务订阅机制](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9907c3ee-8af2-565e-8a6b-93be0eb1b3d2.png)

### 1.2.9 Nacos客户端本地缓存及故障转移

​	在Nacos本地缓存的时候有的时候必然会出现一些故障，这些故障就需要进行处理，涉及到的核心类为ServiceInfoHolder和FailoverReactor。

![image-20211027191504884](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/dec47e1a-1942-5244-bfa0-df345d2620a2.png)

​	本地缓存有两方面，第一方面是从注册中心获得实例信息会缓存在内存当中，也就是通过Map的形式承载，这样查询操作都方便。第二方面便是通过磁盘文件的形式定时缓存起来，以备不时之需。

​	故障转移也分两方面，第一方面是故障转移的开关是通过文件来标记的；第二方面是当开启故障转移之后，当发生故障时，可以从故障转移备份的文件中来获得服务实例信息。

#### 1.2.9.1 ServiceInfoHolder功能概述

​	ServiceInfoHolder类，顾名思义，服务信息的持有者。每次客户端从注册中心获取新的服务信息时都会调用该类，其中processServiceInfo方法来进行本地化处理，包括更新缓存服务、发布事件、更新本地文件等。

![image-20211027152442627](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9ce6a668-aabc-5020-a37c-ac86b6888cfe.png)

​	除了这些核心功能以外，该类在实例化的时候，还做了本地缓存目录初始化、故障转移初始化等操作，下面我们来分析。

#### 1.2.9.2 ServiceInfo的本地内存缓存

​	ServiceInfo，注册服务的信息，其中包含了服务名称、分组名称、集群信息、实例列表信息，上次更新时间等，所以我们由此得出客户端从服务端注册中心获得到的信息在本地都以ServiceInfo作为承载者。

​	而ServiceInfoHolder类又持有了ServiceInfo，通过一个ConcurrentMap来储存

```java
// ServiceInfoHolder
private final ConcurrentMap<String, ServiceInfo> serviceInfoMap;
```

​	这就是Nacos客户端对服务端获取到的注册信息的第一层缓存，并且之前的课程中我们分析processServiceInfo方法时，我们已经看到，当服务信息变更时会第一时间更新ServiceInfoMap中的信息

```java
public ServiceInfo processServiceInfo(ServiceInfo serviceInfo) {
 	....
    //缓存服务信息
    serviceInfoMap.put(serviceInfo.getKey(), serviceInfo);
    // 判断注册的实例信息是否更改
    boolean changed = isChangedServiceInfo(oldService, serviceInfo);
    if (StringUtils.isBlank(serviceInfo.getJsonFromServer())) {
        serviceInfo.setJsonFromServer(JacksonUtils.toJson(serviceInfo));
    }
    ....
    return serviceInfo;
}
```

​	serviceInfoMap的使用就是这样，当变动实例向其中put最新数据即可。当使用实例时，根据key进行get操作即可。

​	serviceInfoMap在ServiceInfoHolder的构造方法中进行初始化，默认创建一个空的ConcurrentMap。但当配置了启动时从缓存文件读取信息时，则会从本地缓存进行加载。

```java
public ServiceInfoHolder(String namespace, Properties properties) {
    initCacheDir(namespace, properties);
    // 启动时是否从缓存目录读取信息，默认false。
    if (isLoadCacheAtStart(properties)) {
        this.serviceInfoMap = new ConcurrentHashMap<String, ServiceInfo>(DiskCache.read(this.cacheDir));
    } else {
        this.serviceInfoMap = new ConcurrentHashMap<String, ServiceInfo>(16);
    }
    this.failoverReactor = new FailoverReactor(this, cacheDir);
    this.pushEmptyProtection = isPushEmptyProtect(properties);
}
```

​	这里我们要注意一下，涉及到了**本地缓存目录**，在我们上节课的学习中我们知道，processServiceInfo方法中，当服务实例变更时，会看到通过DiskCache#write方法向该目录写入ServiceInfo信息。

```java
public ServiceInfo processServiceInfo(ServiceInfo serviceInfo) {
   	.....
    // 服务实例已变更
    if (changed) {
        NAMING_LOGGER.info("current ips:({}) service: {} -> {}", serviceInfo.ipCount(), serviceInfo.getKey(),
                           JacksonUtils.toJson(serviceInfo.getHosts()));
        // 添加实例变更事件InstancesChangeEvent，订阅者
        NotifyCenter.publishEvent(new InstancesChangeEvent(serviceInfo.getName(), serviceInfo.getGroupName(),
                                                           serviceInfo.getClusters(), serviceInfo.getHosts()));
        // 记录Service本地文件
        DiskCache.write(serviceInfo, cacheDir);
    }
    return serviceInfo;
}
```



#### 1.2.9.3 本地缓存目录

​	本地缓存目录cacheDir是ServiceInfoHolder的一个属性，用于指定本地缓存的根目录和故障转移的根目录。

![image-20211027163722859](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1aa9b52d-dac4-5853-9925-5a0d37e01020.png)

​	在ServiceInfoHolder的构造方法中，初始化并且生成缓存目录

![image-20211027163940247](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5360e205-fe7b-5c0d-98fc-af1158c4cf1e.png)

​	这个initCacheDir就不用了细看了，就是生成缓存目录的操作，默认路径：${user.home}/nacos/naming/public，也可以自定义，通过System.setProperty("JM.SNAPSHOT.PATH")自定义

​	这里初始化完目录之后，故障转移信息也存储在该目录下。

```java
private void initCacheDir(String namespace, Properties properties) {
    String jmSnapshotPath = System.getProperty(JM_SNAPSHOT_PATH_PROPERTY);

    String namingCacheRegistryDir = "";
    if (properties.getProperty(PropertyKeyConst.NAMING_CACHE_REGISTRY_DIR) != null) {
        namingCacheRegistryDir = File.separator + properties.getProperty(PropertyKeyConst.NAMING_CACHE_REGISTRY_DIR);
    }

    if (!StringUtils.isBlank(jmSnapshotPath)) {
        cacheDir = jmSnapshotPath + File.separator + FILE_PATH_NACOS + namingCacheRegistryDir
            + File.separator + FILE_PATH_NAMING + File.separator + namespace;
    } else {
        cacheDir = System.getProperty(USER_HOME_PROPERTY) + File.separator + FILE_PATH_NACOS + namingCacheRegistryDir
            + File.separator + FILE_PATH_NAMING + File.separator + namespace;
    }
}
```

#### 1.2.9.4 故障转移

​	在ServiceInfoHolder的构造方法中，还会初始化一个FailoverReactor类，同样是ServiceInfoHolder的成员变量。FailoverReactor的作用便是用来处理故障转移的。

![image-20211027170511242](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/aba3d367-d9ec-5881-8e55-9a7e1839323e.png)

```
public ServiceInfoHolder(String namespace, Properties properties) {
    ....
    // this为ServiceHolder当前对象，这里可以立即为两者相互持有对方的引用
    this.failoverReactor = new FailoverReactor(this, cacheDir);
    .....
}
```

​	我们来看一下FailoverReactor的构造方法，FailoverReactor的构造方法基本上把它的功能都展示出来了：

1\. 持有ServiceInfoHolder的引用
2\. 拼接故障目录：${user.home}/nacos/naming/public/failover，其中public也有可能是其他的自定义命名空间
3\. 初始化executorService（执行者服务）
4\. init方法：通过executorService开启多个定时任务执行

```java
public FailoverReactor(ServiceInfoHolder serviceInfoHolder, String cacheDir) {
    // 持有ServiceInfoHolder的引用
    this.serviceInfoHolder = serviceInfoHolder;
    // 拼接故障目录：${user.home}/nacos/naming/public/failover
    this.failoverDir = cacheDir + FAILOVER_DIR;
    // 初始化executorService
    this.executorService = new ScheduledThreadPoolExecutor(1, new ThreadFactory() {
        @Override
        public Thread newThread(Runnable r) {
            Thread thread = new Thread(r);
            // 守护线程模式运行
            thread.setDaemon(true);
            thread.setName("com.alibaba.nacos.naming.failover");
            return thread;
        }
    });
    // 其他初始化操作，通过executorService开启多个定时任务执行
    this.init();
}
```

#### 1.2.9.5 init方法执行

​	在这个方法中开启了三个定时任务，这三个任务其实都是FailoverReactor的内部类：

1\. 初始化立即执行，执行间隔5秒，执行任务SwitchRefresher
2\. 初始化延迟30分钟执行，执行间隔24小时，执行任务DiskFileWriter
3\. 初始化立即执行，执行间隔10秒，执行核心操作为DiskFileWriter

```java
public void init() {
	// 初始化立即执行，执行间隔5秒，执行任务SwitchRefresher
    executorService.scheduleWithFixedDelay(new SwitchRefresher(), 0L, 5000L, TimeUnit.MILLISECONDS);
	// 初始化延迟30分钟执行，执行间隔24小时，执行任务DiskFileWriter
    executorService.scheduleWithFixedDelay(new DiskFileWriter(), 30, DAY_PERIOD_MINUTES, TimeUnit.MINUTES);

    // backup file on startup if failover directory is empty.
    // 如果故障目录为空，启动时立即执行，立即备份文件
    // 初始化立即执行，执行间隔10秒，执行核心操作为DiskFileWriter
    executorService.schedule(new Runnable() {
        @Override
        public void run() {
            try {
                File cacheDir = new File(failoverDir);

                if (!cacheDir.exists() && !cacheDir.mkdirs()) {
                    throw new IllegalStateException("failed to create cache dir: " + failoverDir);
                }

                File[] files = cacheDir.listFiles();
                if (files == null || files.length <= 0) {
                    new DiskFileWriter().run();
                }
            } catch (Throwable e) {
                NAMING_LOGGER.error("[NA] failed to backup file on startup.", e);
            }

        }
    }, 10000L, TimeUnit.MILLISECONDS);
}
```

​	这里我们先看DiskFileWriter，这里的逻辑不难，就是获取ServiceInfo中缓存的ServiceInfo，判断是否满足写入磁盘，如果条件满足，就将其写入拼接的故障目录，因为后两个定时任务执行的都是DiskFileWriter，但是第三个定时任务是有前置判断的，只要文件不存在就会立即执行把文件写入到本地磁盘中。

```java
class DiskFileWriter extends TimerTask {

    @Override
    public void run() {
        Map<String, ServiceInfo> map = serviceInfoHolder.getServiceInfoMap();
        for (Map.Entry<String, ServiceInfo> entry : map.entrySet()) {
            ServiceInfo serviceInfo = entry.getValue();
            if (StringUtils.equals(serviceInfo.getKey(), UtilAndComs.ALL_IPS) || StringUtils
                .equals(serviceInfo.getName(), UtilAndComs.ENV_LIST_KEY) || StringUtils
                .equals(serviceInfo.getName(), UtilAndComs.ENV_CONFIGS) || StringUtils
                .equals(serviceInfo.getName(), UtilAndComs.VIP_CLIENT_FILE) || StringUtils
                .equals(serviceInfo.getName(), UtilAndComs.ALL_HOSTS)) {
                continue;
            }
			// 将缓存写入磁盘
            DiskCache.write(serviceInfo, failoverDir);
        }
    }
}
```

​	接下来，我们再来看第一个定时任务SwitchRefresher的核心实现，具体逻辑如下：

1\. 如果故障转移文件不存在，则直接返回（文件开关）
2\. 比较文件修改时间，如果已经修改，则获取故障转移文件中的内容。
3\. 故障转移文件中存储了0和1标识。0表示关闭，1表示开启。
4\. 当为开启状态时，执行线程FailoverFileReader。

```java
class SwitchRefresher implements Runnable {

    long lastModifiedMillis = 0L;

    @Override
    public void run() {
        try {
            File switchFile = new File(failoverDir + UtilAndComs.FAILOVER_SWITCH);
            // 文件不存在则退出
            if (!switchFile.exists()) {
                switchParams.put(FAILOVER_MODE_PARAM, Boolean.FALSE.toString());
                NAMING_LOGGER.debug("failover switch is not found, {}", switchFile.getName());
                return;
            }

            long modified = switchFile.lastModified();
			
            if (lastModifiedMillis < modified) {
                lastModifiedMillis = modified;
                // 获取故障转移文件内容
                String failover = ConcurrentDiskUtil.getFileContent(failoverDir + UtilAndComs.FAILOVER_SWITCH,
                                                                    Charset.defaultCharset().toString());
                if (!StringUtils.isEmpty(failover)) {
                    String[] lines = failover.split(DiskCache.getLineSeparator());

                    for (String line : lines) {
                        String line1 = line.trim();
                        // 1 表示开启故障转移模式
                        if (IS_FAILOVER_MODE.equals(line1)) {
                            switchParams.put(FAILOVER_MODE_PARAM, Boolean.TRUE.toString());
                            NAMING_LOGGER.info("failover-mode is on");
                            new FailoverFileReader().run();
                        // 0 表示关闭故障转移模式
                        } else if (NO_FAILOVER_MODE.equals(line1)) {
                            switchParams.put(FAILOVER_MODE_PARAM, Boolean.FALSE.toString());
                            NAMING_LOGGER.info("failover-mode is off");
                        }
                    }
                } else {
                    switchParams.put(FAILOVER_MODE_PARAM, Boolean.FALSE.toString());
                }
            }

        } catch (Throwable e) {
            NAMING_LOGGER.error("[NA] failed to read failover switch.", e);
        }
    }
}
```

#### 1.2.9.6 FailoverFileReader

​	顾名思义，故障转移文件读取，基本操作就是读取failover目录存储的**备份服务信息文件**内容，然后转换成ServiceInfo，并且将所有的ServiceInfo储存在FailoverReactor的ServiceMap属性中。

​	流程如下：

1\. 读取failover目录下的所有文件，进行遍历处理
2\. 如果文件不存在跳过
3\. 如果文件是故障转移开关标志文件跳过
4\. 读取文件中的备份内容，转换为ServiceInfo对象
5\. 将ServiceInfo对象放入到domMap中
6\. 最后判断domMap不为空，赋值给serviceMap

```java
class FailoverFileReader implements Runnable {

    @Override
    public void run() {
        Map<String, ServiceInfo> domMap = new HashMap<String, ServiceInfo>(16);

        BufferedReader reader = null;
        try {

            File cacheDir = new File(failoverDir);
            if (!cacheDir.exists() && !cacheDir.mkdirs()) {
                throw new IllegalStateException("failed to create cache dir: " + failoverDir);
            }

            File[] files = cacheDir.listFiles();
            if (files == null) {
                return;
            }

            for (File file : files) {
                if (!file.isFile()) {
                    continue;
                }
				// 如果是故障转移标志文件，则跳过
                if (file.getName().equals(UtilAndComs.FAILOVER_SWITCH)) {
                    continue;
                }

                ServiceInfo dom = new ServiceInfo(file.getName());

                try {
                    String dataString = ConcurrentDiskUtil
                        .getFileContent(file, Charset.defaultCharset().toString());
                    reader = new BufferedReader(new StringReader(dataString));

                    String json;
                    if ((json = reader.readLine()) != null) {
                        try {
                            dom = JacksonUtils.toObj(json, ServiceInfo.class);
                        } catch (Exception e) {
                            NAMING_LOGGER.error("[NA] error while parsing cached dom : {}", json, e);
                        }
                    }

                } catch (Exception e) {
                    NAMING_LOGGER.error("[NA] failed to read cache for dom: {}", file.getName(), e);
                } finally {
                    try {
                        if (reader != null) {
                            reader.close();
                        }
                    } catch (Exception e) {
                        //ignore
                    }
                }
                if (!CollectionUtils.isEmpty(dom.getHosts())) {
                    domMap.put(dom.getKey(), dom);
                }
            }
        } catch (Exception e) {
            NAMING_LOGGER.error("[NA] failed to read cache file", e);
        }
		
        // 读入缓存
        if (domMap.size() > 0) {
            serviceMap = domMap;
        }
    }
}
```

​	但是这里还有一个问题就是serviceMap是哪里用到的，这个其实是我们之前读取实例时候用到的getServiceInfo方法

​	其实这里就是一旦开启故障转移就会先调用failoverReactor.getService方法，此方法便是从serviceMap中获取ServiceInfo

```java
public ServiceInfo getService(String key) {
    ServiceInfo serviceInfo = serviceMap.get(key);

    if (serviceInfo == null) {
        serviceInfo = new ServiceInfo();
        serviceInfo.setName(key);
    }

    return serviceInfo;
}
```

​	调用serviceMap方法getServiceInfo方法就在ServiceInfoHolder中

```java
// ServiceInfoHolder
public ServiceInfo getServiceInfo(final String serviceName, final String groupName, final String clusters) {
    NAMING_LOGGER.debug("failover-mode: {}", failoverReactor.isFailoverSwitch());
    String groupedServiceName = NamingUtils.getGroupedName(serviceName, groupName);
    String key = ServiceInfo.getKey(groupedServiceName, clusters);
    if (failoverReactor.isFailoverSwitch()) {
        return failoverReactor.getService(key);
    }
    return serviceInfoMap.get(key);
}
```

### 1.2.10 Nacos集群数据同步

​		当我们有服务进行注册以后，会写入注册信息同时会触发**ClientChangedEvent**事件，通过这个事件，就会开始进行Nacos的集群数据同步，当然这其中只有有一个Nacos节点来处理对应的客户端请求，其实这其中还涉及到一个**负责节点**和**非负责节点**

#### 1.2.10.1 负责节点

​	这是首先我们要查看的是**DistroClientDataProcessor**（客户端数据一致性处理器）类型，这个类型会处理当前节点负责的Client，那我们要查看其中的syncToAllServer方法。

```java
private void syncToAllServer(ClientEvent event) {
    Client client = event.getClient();
    // 判断客户端是否为空，是否是临时实例，判断是否是负责节点
    if (null == client || !client.isEphemeral() || !clientManager.isResponsibleClient(client)) {
        return;
    }
    if (event instanceof ClientEvent.ClientDisconnectEvent) {
        // 客户端断开连接
        DistroKey distroKey = new DistroKey(client.getClientId(), TYPE);
        distroProtocol.sync(distroKey, DataOperation.DELETE);
    } else if (event instanceof ClientEvent.ClientChangedEvent) {
        // 客户端新增/修改
        DistroKey distroKey = new DistroKey(client.getClientId(), TYPE);
        distroProtocol.sync(distroKey, DataOperation.CHANGE);
    }
}
```

​	distroProtocol会循环所有其他nacos节点，提交一个异步任务，这个异步任务会延迟1s，其实这里我们就可以看到这里涉及到客户端的断开和客户端的新增和修改，对于Delete操作，由**DistroSyncDeleteTask**处理，对于Change操作，由**DistroSyncChangeTask**处理，这里我们从**DistroSyncChangeTask**来看

```java
public class DistroSyncChangeTask extends AbstractDistroExecuteTask {
    
    private static final DataOperation OPERATION = DataOperation.CHANGE;
    
    public DistroSyncChangeTask(DistroKey distroKey, DistroComponentHolder distroComponentHolder) {
        super(distroKey, distroComponentHolder);
    }
    
    @Override
    protected DataOperation getDataOperation() {
        return OPERATION;
    }
    
    // 无回调
    @Override
    protected boolean doExecute() {
        String type = getDistroKey().getResourceType();
        DistroData distroData = getDistroData(type);
        if (null == distroData) {
            Loggers.DISTRO.warn("[DISTRO] {} with null data to sync, skip", toString());
            return true;
        }
        return getDistroComponentHolder().findTransportAgent(type)
                .syncData(distroData, getDistroKey().getTargetServer());
    }
    
    // 有回调
    @Override
    protected void doExecuteWithCallback(DistroCallback callback) {
        String type = getDistroKey().getResourceType();
        DistroData distroData = getDistroData(type);
        if (null == distroData) {
            Loggers.DISTRO.warn("[DISTRO] {} with null data to sync, skip", toString());
            return;
        }
        getDistroComponentHolder().findTransportAgent(type)
                .syncData(distroData, getDistroKey().getTargetServer(), callback);
    }
    
    @Override
    public String toString() {
        return "DistroSyncChangeTask for " + getDistroKey().toString();
    }
    
    // 从DistroClientDataProcessor获取DistroData
    private DistroData getDistroData(String type) {
        DistroData result = getDistroComponentHolder().findDataStorage(type).getDistroData(getDistroKey());
        if (null != result) {
            result.setType(OPERATION);
        }
        return result;
    }
}
```

​	获取到的DistroData，其实是从ClientManager实时获取Client。

```java
// DistroClientDataProcessor
@Override
public DistroData getDistroData(DistroKey distroKey) {
    Client client = clientManager.getClient(distroKey.getResourceKey());
    if (null == client) {
        return null;
    }
    // 把生成的同步数据放入到数组中
    byte[] data = ApplicationUtils.getBean(Serializer.class).serialize(client.generateSyncData());
    return new DistroData(distroKey, data);
}
```

​	AbstractClient继承了Client，同时给DistroClientDataProcessorClient提供Client的注册信息，包括客户端注册了哪些namespace，哪些group，哪些service，哪些instance。

```java
@Override
public ClientSyncData generateSyncData() {
    List<String> namespaces = new LinkedList<>();
    List<String> groupNames = new LinkedList<>();
    List<String> serviceNames = new LinkedList<>();
    List<InstancePublishInfo> instances = new LinkedList<>();
    for (Map.Entry<Service, InstancePublishInfo> entry : publishers.entrySet()) {
        namespaces.add(entry.getKey().getNamespace());
        groupNames.add(entry.getKey().getGroup());
        serviceNames.add(entry.getKey().getName());
        instances.add(entry.getValue());
    }
    return new ClientSyncData(getClientId(), namespaces, groupNames, serviceNames, instances);
}
```

​	这里我们在回过头来看syncData方法，这个方法实际上是由**DistroClientTransportAgent**封装为**DistroDataRequest**调用其他Nacos节点。

```java
@Override
public boolean syncData(DistroData data, String targetServer) {
    if (isNoExistTarget(targetServer)) {
        return true;
    }
    DistroDataRequest request = new DistroDataRequest(data, data.getType());
    Member member = memberManager.find(targetServer);
    if (checkTargetServerStatusUnhealthy(member)) {
        Loggers.DISTRO.warn("[DISTRO] Cancel distro sync caused by target server {} unhealthy", targetServer);
        return false;
    }
    try {
        Response response = clusterRpcClientProxy.sendRequest(member, request);
        return checkResponse(response);
    } catch (NacosException e) {
        Loggers.DISTRO.error("[DISTRO-FAILED] Sync distro data failed! ", e);
    }
    return false;
}
```

#### 1.2.10.2 非负责节点

​	当负责节点将数据发送给非负责节点以后，将要处理发送过来的Client数据。这里我们要看DistroClientDataProcessor.processData方法

```java
@Override
public boolean processData(DistroData distroData) {
    switch (distroData.getType()) {
        case ADD:
        case CHANGE:
            ClientSyncData clientSyncData = ApplicationUtils.getBean(Serializer.class)
                .deserialize(distroData.getContent(), ClientSyncData.class);
            //处理同步数据
            handlerClientSyncData(clientSyncData);
            return true;
        case DELETE:
            String deleteClientId = distroData.getDistroKey().getResourceKey();
            Loggers.DISTRO.info("[Client-Delete] Received distro client sync data {}", deleteClientId);
            clientManager.clientDisconnected(deleteClientId);
            return true;
        default:
            return false;
    }
}
```

​	然后来查看具体处理方法handlerClientSyncData

```java
private void handlerClientSyncData(ClientSyncData clientSyncData) {
    Loggers.DISTRO.info("[Client-Add] Received distro client sync data {}", clientSyncData.getClientId());
    // 同步客户端连接
    clientManager.syncClientConnected(clientSyncData.getClientId(), clientSyncData.getAttributes());
    // 获取Client（此时注册到的是ConnectionBasedClient）
    Client client = clientManager.getClient(clientSyncData.getClientId());
    // 更新Client数据
    upgradeClient(client, clientSyncData);
}
```

​	DistroClientDataProcessor的**upgradeClient**方法，更新Client里的注册表信息，发布对应事件

```java
private void upgradeClient(Client client, ClientSyncData clientSyncData) {
    List<String> namespaces = clientSyncData.getNamespaces();
    List<String> groupNames = clientSyncData.getGroupNames();
    List<String> serviceNames = clientSyncData.getServiceNames();
    List<InstancePublishInfo> instances = clientSyncData.getInstancePublishInfos();
    Set<Service> syncedService = new HashSet<>();
    for (int i = 0; i < namespaces.size(); i++) {
        Service service = Service.newService(namespaces.get(i), groupNames.get(i), serviceNames.get(i));
        Service singleton = ServiceManager.getInstance().getSingleton(service);
        syncedService.add(singleton);
        InstancePublishInfo instancePublishInfo = instances.get(i);
        if (!instancePublishInfo.equals(client.getInstancePublishInfo(singleton))) {
            client.addServiceInstance(singleton, instancePublishInfo);
            NotifyCenter.publishEvent(
                new ClientOperationEvent.ClientRegisterServiceEvent(singleton, client.getClientId()));
        }
    }
    for (Service each : client.getAllPublishedService()) {
        if (!syncedService.contains(each)) {
            client.removeServiceInstance(each);
            NotifyCenter.publishEvent(
                new ClientOperationEvent.ClientDeregisterServiceEvent(each, client.getClientId()));
        }
    }
}
```

​	**注意：**这里要注意下此时的Client实现类ConnectionBasedClient，只不过它的isNative属性为false，这是非负责节点和负责节点的主要区别。	

​	其实判断当前nacos节点是否为负责节点的依据就是这个**isNative属性**，如果是客户端直接注册在这个nacos节点上的ConnectionBasedClient，它的isNative属性为true；如果是由Distro协议，同步到这个nacos节点上的ConnectionBasedClient，它的isNative属性为false。

​	那其实我们都知道2.x的版本以后使用了长连接，所以**通过长连接建立在哪个节点上，哪个节点就是责任节点，客户端也只会向这个责任节点发送请求**。



#### 1.2.10.3 Distro协议负责集群数据统一

​	Distro为了确保集群间数据一致，不仅仅依赖于数据发生改变时的实时同步，后台有定时任务做数据同步。

​	在1.x版本中，责任节点每5s同步所有Service的Instance列表的摘要（md5）给非责任节点，非责任节点用对端传来的服务md5比对本地服务的md5，如果发生改变，需要反查责任节点。

​	在2.x版本中，对这个流程做了改造，责任节点会发送Client全量数据，非责任节点定时检测同步过来的Client是否过期，减少1.x版本中的反查。

​	责任节点每5s向其他节点发送DataOperation=VERIFY类型的DistroData，来维持非责任节点的Client数据不过期。

```java
//DistroVerifyTimedTask 
@Override
public void run() {
    try {
        // 所有其他节点
        List<Member> targetServer = serverMemberManager.allMembersWithoutSelf();
        if (Loggers.DISTRO.isDebugEnabled()) {
            Loggers.DISTRO.debug("server list is: {}", targetServer);
        }
        for (String each : distroComponentHolder.getDataStorageTypes()) {
            // 遍历想这些节点发送Client.isNative=true的DistroData，type = VERIFY
            verifyForDataStorage(each, targetServer);
        }
    } catch (Exception e) {
        Loggers.DISTRO.error("[DISTRO-FAILED] verify task failed.", e);
    }
}
```

​	非责任节点每5s扫描isNative=false的client，如果client30s内没有被VERIFY的DistroData更新过续租时间，会删除这个同步过来的Client数据。

```java
//ConnectionBasedClientManager->ExpiredClientCleaner
private static class ExpiredClientCleaner implements Runnable {

    private final ConnectionBasedClientManager clientManager;

    public ExpiredClientCleaner(ConnectionBasedClientManager clientManager) {
        this.clientManager = clientManager;
    }

    @Override
    public void run() {
        long currentTime = System.currentTimeMillis();
        for (String each : clientManager.allClientId()) {
            ConnectionBasedClient client = (ConnectionBasedClient) clientManager.getClient(each);
            if (null != client && client.isExpire(currentTime)) {
                clientManager.clientDisconnected(each);
            }
        }
    }
} 
-------------------------------------------------------------------------------------------
@Override
public boolean isExpire(long currentTime) {
    // 判断30s内没有续租 认为过期
    return !isNative() && currentTime - getLastRenewTime() > ClientConfig.getInstance().getClientExpiredTime();
}
```
