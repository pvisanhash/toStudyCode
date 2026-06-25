# Seata

## 1 Seata基础应用

### 1.1 分布式事务简介

#### 1.1.1 概念

##### 1.1.1.1 事务ACID

1\. A（Atomic）：原子性，构成事务的所有操作，要么都执行完成，要么全部不执行，不可能出现部分成功部分失 败的情况。
2\. C（Consistency）：一致性，在事务执行前后，数据库的一致性约束没有被破坏。比如：张三向李四转100元， 转账前和转账后的数据是正确状态这叫一致性，如果出现张三转出100元，李四账户没有增加100元这就出现了数 据错误，就没有达到一致性。
3\. I（Isolation）：隔离性，数据库中的事务一般都是并发的，隔离性是指并发的两个事务的执行互不干扰，一个事 务不能看到其他事务运行过程的中间状态。通过配置事务隔离级别可以避脏读、重复读等问题。
4\. D（Durability）：持久性，事务完成之后，该事务对数据的更改会被持久化到数据库，且不会被回滚。

##### 1.1.1.2 事务

1\. 本地事务：由单个事务资源管理器管理的事务，称为本地事务

  由单个事务资源管理器管理的事务，典型场景是在同一个数据库连接或同一个数据库实例中，依赖数据库自身的 ACID 能力完成提交或回滚。数据库可以部署在远程服务器上，并不要求和应用在同一台机器。

2\. 分布式事务：
  分布式事务指事务的参与者、支持事务的服务器、资源服务器以及事务管理器分别位于**不同的分布式系统**的不同节点之上，且属于不同的应用，分布式事务需要保证这些操作要么全部成功，要么全部失败。本质上来说，分布式事务就是为了保证不同数据库的数据一致性。

3\. 举例：
  分布式系统会把一个应用系统拆分为可独立部署的多个服务，因此需要服务与服务之间远程协作才能完成事务操 作，这种分布式系统环境下由不同的服务之间通过网络远程协作完成事务称之为分布式事务，例如用户注册送积分事务、创建订单减库存事务，银行转账事务等都是分布式事务。

![image20220111162727304.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ccd8b493-f680-5550-abed-7f384ed27fd5.png)

通过以上的图中我们可以看出，其实只要涉及到操作多个数据源，就可能会产生事务问题，当然在实际开发中我们要尽量避免这种问题的出现，当然如果避免不了，我们就需要进行解决，在我们的微服务系统架构中，目前比较好，比较常用的解决方案就是Seata。



##### 1.1.1.3 事务管理器

本地事务和分布式事务里的“事务管理器”，本质上都是**负责事务生命周期和提交/回滚决策的组件**，但管理范围不同。

**本地事务管理器**
指管理**单个资源**事务的组件，比如一个数据库连接上的事务。

它负责：

1\. 开启事务：`BEGIN`
2\. 提交事务：`COMMIT`
3\. 回滚事务：`ROLLBACK`
4\. 维护事务状态
5\. 确保 ACID 在单个数据库内成立

例子：

```
BEGIN;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

这里的事务管理器可以理解为：

1\. MySQL / PostgreSQL / Oracle 等数据库自身的事务模块
2\. Spring 里的 `DataSourceTransactionManager`
3\. JDBC 连接上的事务控制逻辑

也就是说，本地事务通常是：

> 一个应用 + 一个数据库资源 + 一个事务管理器

------

**分布式事务管理器**
指管理**多个事务资源**之间一致性的组件，比如多个数据库、消息队列、微服务、跨系统调用等。

它负责协调多个参与者：

1\. 通知各资源准备提交
2\. 收集各资源状态
3\. 决定全局提交或全局回滚
4\. 处理部分失败、超时、恢复等问题

典型场景：

```
订单服务：创建订单
库存服务：扣减库存
账户服务：扣款
消息系统：发送订单消息
```

这些操作可能涉及不同数据库或服务。分布式事务管理器要保证它们整体上要么都成功，要么按某种策略保持一致。

常见实现包括：

1\. XA 事务管理器
2\. JTA 事务管理器，比如 Atomikos、Narayana
3\. Seata 的 TC，Transaction Coordinator
4\. TCC 框架里的事务协调器
5\. Saga 编排器

可以理解为：

> 分布式事务管理器是“全局事务协调者”，本地事务管理器是“单个资源的事务控制者”。

------

一个简单对比：

| 类型             | 管理范围        | 管理对象                   | 常见组件                                             |
| ---------------- | --------------- | -------------------------- | ---------------------------------------------------- |
| 本地事务管理器   | 单个数据库/资源 | 一个连接、一个数据库事务   | DB 引擎、JDBC、Spring `DataSourceTransactionManager` |
| 分布式事务管理器 | 多个资源/服务   | 多个本地事务组成的全局事务 | XA/JTA、Seata TC、Saga Coordinator                   |

一句话总结：

**本地事务管理器管一个资源内部的提交回滚；分布式事务管理器协调多个本地事务，让它们在全局层面达成一致。**



#### 1.1.2 分布式事务理论

随着互联化的蔓延，各种项目都逐渐向分布式服务做转换。如今微服务已经普遍存在，本地事务已经无法满足分布式的要求，由此分布式事务问题诞生。 分布式事务被称为世界性的难题，目前分布式事务存在两大理论依据：CAP定律 BASE理论。

##### 1.1.2.1 CAP定律

这个定理的内容是指的是在一个分布式系统中、Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可得兼。

1\. 一致性（C）

  在分布式系统中的所有数据备份，在同一时刻是否同样的值。（等同于所有节点访问同一份最新的数据副本）

2\. 可用性（A）

  在集群中一部分节点故障后，集群整体是否还能响应客户端的读写请求。（对数据更新具备高可用性）

3\. 分区容错性（P）

  当分布式系统发生**网络分区**时，系统仍然能够继续对外提供服务。**网络分区**可以理解为：系统里的节点还活着，但节点之间的网络断了，导致一部分节点无法和另一部分节点通信。

CAP是无法同时存在的，一下通过这个例子来说明

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/290574a6-2c64-5cf4-96c4-b99e77363aa3.png" alt="image-20220113155408713" style="zoom: 25%;" />

1\. 当库存服务减库存以后，那么需要将数据同步到其他的服务上，这是为了保证数据一致性C，但是网络是不可靠的，所以我们系统就需要保证分区容错性P，也就是我们必须容忍网络所带来的的一些问题，此时如果我们想保证C那么就需要舍弃A，也就是说我们在保证C的情况下，就必须舍弃A，也就是CP无法保证高可用。
2\. 如果为了保证A，高可用的情况下，也就是必须在限定时间内给出响应，同样由于网络不可靠P，订单服务就有可能无法拿到新的数据，但是也要给用户作出响应，那么也就无法保证C一致性。所以AP是无法保证强一致性的。
3\. 如果我们想保证CA，也就是高可用和一致性，也就是必须保证网络良好才能实现，那么也就是说我们需要将库存、订单、用户放到一起，但是这种情况也就丧失了P这个保证，这个时候系统也就不是分布式系统了。
4\. 总结：在分布式系统中，p是必然的存在的，所以我们只能在C和A之间进行取舍，在这种条件下就诞生了BASE理论

##### 1.1.2.2 BASE理论

BASE是Basically Available（基本可用）、Soft state（软状态）和 Eventually consistent（最终一致性）三个短语的缩写。BASE理论是对CAP中一致性和可用性权衡的结果，其来源于对大规模互联网系统分布式实践的总结， 是基于CAP定理逐步演化而来的。BASE理论的核心思想是：即使无法做到强一致性，但每个应用都可以根据自身业务特点，采用适当的方式来使系统达到**最终一致性**。

1\. 基本可用

  基本可用是指分布式系统在出现不可预知故障的时候，允许损失部分可用性-注意，这绝不等价于系统不可用。比如：

  （1）响应时间上的损失。正常情况下，一个在线搜索引擎需要在0.5秒之内返回给用户相应的查询结果，但由于出现故障，查询结果的响应时间增加了1~2秒

  （2）系统功能上的损失：正常情况下，在一个电子商务网站上进行购物的时候，消费者几乎能够顺利完成每一笔订单，但是在一些节日大促购物高峰的时候，由于消费者的购物行为激增，为了保护购物系统的稳定性，部分消费者可能会被引导到一个降级页面

2\. 软状态

  软状态指允许系统中的数据存在中间状态，并认为该中间状态的存在不会影响系统的整体可用性，即允许系统在不同节点的数据副本之间进行数据同步的过程存在延时。系统中的数据状态允许在一段时间内处于**中间状态、不一致状态或变化状态**，不要求每一时刻都强一致。

3\. 最终一致性

  最终一致性强调的是所有的数据副本，在经过一段时间的同步之后，最终都能够达到一个一致的状态。因此，最终一致性的本质是需要系统保证最终数据能够达到一致，而不需要实时保证系统数据的强一致性。

那这个位置我们依旧可以用我们刚才的例子来进行说明

**基本可用：**保证核心服务是可以使用的，至于其他的服务可以适当的降低响应时间，甚至是服务降级

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d4daf524-72ac-585c-8601-afdd42f5fb8c.png" alt="image-20220113162344531" style="zoom: 33%;" />

**软状态：**存在中间状态，不影响整体系统使用，数据同步存在延时

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c1f4d8ed-598e-587a-98bd-0567a55001ee.png" alt="image-20220113162554630" style="zoom:33%;" />

**最终一致性：**再过了流量高峰期以后，经过一段时间的同步，保持各服务数据的一致

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8d0165ea-ec1b-5166-9a74-ec184367c01e.png" alt="image-20220113162813542" style="zoom:33%;" />

#### 1.1.3 分布式事务解决方案

**主要是思想！！！2PC其实是思想**

2PC即两阶段提交协议，是将整个事务流程分为两个阶段，P是指准备阶段，C是指提交阶段。

1\. 准备阶段（Prepare phase）
2\. 提交阶段（commit phase）

举例：比如说相亲对象两个人去吃饭，店老板要求，先付钱在吃饭，这时男女双方提出了AA，也就是说只有男女双方都付钱，才能落座吃饭，但是只要两个人中有一个不统一付款就不能落座吃饭。

1\. 准备阶段：老板要求男方付款，男方付款。老板要求女方付款，女方付款
2\. 提交阶段：老板出餐，两人纷纷落座

其实此例子就形成了一个事务，如果男女双方有一个人拒绝付款，那么老板就不会出餐，并且会把已收取的钱原路退回。

真个事务过程是由事务管理器和参与者组成的，店老板就是事务管管理器，男女双发就是参与者，事务管理器决策整个分布式事务在计算机中关系数据库支持的两阶段提交协议：

1\. 准备阶段（Prepare phase）：事务管理器给每个参与者发送Prepare消息，每个数据库参与者在本地执行事务，并写本地的Undo/Redo日志，此时事务没有提交。（Undo日志是记录修改前的数据，用于数据库回滚，Redo日志是记录修改后的数据，用于提交事务后写入数据文件）
2\. 提交阶段（commit phase）：如果事务管理器收到了参与者的执行失败或者超时消息时，直接给每个参与者发送回滚(Rollback)消息；否则，发送提交(Commit)消息；参与者根据事务管理器的指令执行提交或者回滚操作，并释放事务处理过程中使用的资源。

具体步骤图例：

成功：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/36cfcce7-eed0-55ac-9d94-db52c318f065.png" alt="image-20220113180953684" style="zoom: 50%;" />

失败：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3b5126de-4894-5590-85d0-cde3f7184da0.png" alt="image-20220113181028153" style="zoom:50%;" />

>**TM**：**Transaction Manager**，事务管理器
>
>**RM**：**Resource Manager**，资源管理器

### 1.2 Seata-Server简介与安装

#### 1.2.1 Seata简介

官网：https://seata.io/zh-cn/docs/overview/what-is-seata.html

概念：Seata 是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata 将为用户提供了 AT、TCC、SAGA 和 XA 事务模式，为用户打造一站式的分布式解决方案。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6b31f055-b951-5f45-96ec-a46a9d994401.png" alt="145942191-7a2d469f-94c8-4cd2-8c7e-46ad75683636" style="zoom:33%;" />

在我们的微服务系统中，对应业务被对应的拆分成独立模块，在官方提供的架构图中，我们可以看出当前是三个服务：

1\. 仓储服务：对给定的商品进行增删操作记录数量
2\. 订单服务：根据采购者的需求创建订单
3\. 账户服务：从用户账户中扣除余额、积分等

在这套架构中，用户下单购买商品的业务，就需要三个服务来完成，每个服务内部的数据一致性由本地事务来保证，但是全局的数据一致性问题就没办法保证，Seata就是来进行解决这种问题的解决方案。

#### 1.2.2 Seata术语

官网地址：https://seata.io/zh-cn/docs/overview/terminology.html

要了解Seata，首先我们要了解一下Seata的几个关键的概念：

1\. TC (Transaction Coordinator) - 事务协调者

  维护全局和分支事务的状态，驱动全局事务提交或回滚。



2\. TM (Transaction Manager) - 事务管理器（发起者，同时也是RM的一种）

  定义全局事务的范围：开始全局事务、提交或回滚全局事务。



3\. RM (Resource Manager) - 资源管理器（每个参与事务的微服务）

  管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

![image-20220111191919517](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1b433b81-a205-59a6-a5d0-2425db3da1f9.png)

#### 1.2.3 Seata Server安装启动

##### 1.2.3.1 Seata-Server下载

官方下载地址：https://github.com/seata/seata/releases

![image-20220112191252083](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3c094846-34f5-53e7-b5cb-4afab7a25095.png)

下载完成之后需要解压

![image-20220112191449051](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bb576d79-39d9-5994-98e9-5e0b2ccdb6ee.png)

##### 1.2.3.2 Seata-Server配置

1\. 需要打开conf目录

![image-20220112192512498](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a7071088-e056-55ab-ba9f-d63fd82f0aec.png)

1\. 先配置registry.conf配置文件，修改Seata的注册中心和配置中心为Nacos

![image-20220112192010321](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/830376b2-69fe-5ce5-9b5f-71a88d725532.png)

![image-20220112192038314](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2c3be79d-eb3d-5729-8fd9-125f6f622411.png)

```json
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = ""
    cluster = "default"
    username = ""
    password = ""
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = 0
    password = ""
    cluster = "default"
    timeout = 0
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
    aclToken = ""
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"

  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
    dataId = "seataServer.properties"
  }
  consul {
    serverAddr = "127.0.0.1:8500"
    aclToken = ""
  }
  apollo {
    appId = "seata-server"
    ## apolloConfigService will cover apolloMeta
    apolloMeta = "http://192.168.1.204:8801"
    apolloConfigService = "http://192.168.1.204:8080"
    namespace = "application"
    apolloAccesskeySecret = ""
    cluster = "seata"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
    nodePath = "/seata/seata.properties"
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}
```

1\. 接着我们需要修改Seata的存储模式，修改file.conf文件，把Seata的默认存储模式修改为数据库"DB"，同时需要配置JDBC

![image-20220112192451611](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/72e7c0d1-269b-5363-9ffb-a1266ee8c3da.png)

![image-20220112192620774](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/08d5a1ee-9d31-5df3-a167-caeb07415672.png)

![image-20220112193120164](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3b50b76c-41cb-5035-a5ad-69c50301b578.png)

```json
## transaction log store, only used in seata-server
store {
  ## store mode: file、db、redis
  mode = "db"
  ## rsa decryption public key
  publicKey = ""
  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    maxBranchSessionSize = 16384
    # globe session size , if exceeded throws exceptions
    maxGlobalSessionSize = 512
    # file buffer size , if exceeded allocate new buffer
    fileWriteBufferCacheSize = 16384
    # when recover batch read size
    sessionReloadReadSize = 100
    # async, sync
    flushDiskMode = async
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.jdbc.Driver"
    ## if using mysql to store the data, recommend add rewriteBatchedStatements=true in jdbc connection param
    url = "jdbc:mysql://localhost:3306/seata?useUnicode=true&characterEncoding=UTF-8"
    user = "root"
    password = "root"
    minConn = 5
    maxConn = 100
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }

  ## redis store property
  redis {
    ## redis mode: single、sentinel
    mode = "single"
    ## single mode property
    single {
      host = "127.0.0.1"
      port = "6379"
    }
    ## sentinel mode property
    sentinel {
      masterName = ""
      ## such as "10.28.235.65:26379,10.28.235.65:26380,10.28.235.65:26381"
      sentinelHosts = ""
    }
    password = ""
    database = "0"
    minConn = 1
    maxConn = 10
    maxTotal = 100
    queryLimit = 100
  }
}

```

##### 1.2.3.3 启动

启动步骤为，先启动nacos然后在启动Seata-Server

启动Seata-Server的方式非常简单，直接双击此文件即可:seata-server-1.4.2\bin\seata-server.bat

![image-20220112193257424](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bfafdb53-7afc-504b-b6f2-ab78d8888210.png)

启动完成效果

![image-20220112193410847](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a99b97b4-5b97-5977-a708-b54f7315a3d9.png)

然后在nacos控制台上就可以看到Seata-Server

![image-20220112193500935](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/44ea95b9-0c09-5b86-94c0-11334469ca73.png)

##### 1.2.3.4 Seata Server（TC）环境搭建详解

Server端存储模式（store.mode）支持三种：

1\. file：单机模式，全局事务会话信息内存中读写并持久化本地文件root.data，性能较高（默认）
2\. DB：高可用模式，全局事务会话信息通过DB共享，相对性能差一些
3\. redis：Seata-Server1.3及以上版本支持，性能较高，存在事务信息丢失风险，需要配合实际场景使用。

**具体操作**

1\. 修改Seata-Server模式为DB高可用模式

![image-20220118173041657](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3086a07b-569c-5bc2-bdd6-8940fbe7922a.png)

找到以下对应的db配置，要修改其中的jdbc连接，以及要注意其中涉及到了三个表，分别是global_table，branch_table，lock_table分别是全局事务会话表，分支事务会话表，锁数据表；

![image-20220118231142489](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/06889ecc-92af-5c7b-9f62-f4f19b8696ca.png)

1\. 建表语句地址：https://github.com/seata/seata/blob/develop/script/server/db/mysql.sql

```java
-- the table to store GlobalSession data
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(128),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `status`         TINYINT      NOT NULL DEFAULT '0' COMMENT '0:locked ,1:rollbacking',
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_status` (`status`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;
```

1\. 重启Seata即可生效。

### 1.3 Seata配置Nacos注册中心和配置中心

Seata支持注册服务到Nacos，以及支持Seata所有配置放到Nacos配置中心，在Nacos中统一维护；

高可用模式下就需要配合Nacos来完成

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/79e37e12-38d1-5b8d-bd52-be42b8dbc80a.png" alt="image-20220118234555417" style="zoom: 50%;" />

#### 1.3.1 具体配置如下

**注册中心**

Seata-server端配置注册中心，在registry.conf中加入配置注册中心nacos

**注意：确保client与server的注册处于同一个namespace和group，不然会找不到服务。**

```java
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP" # 这里的配置要和客户端保持一致
    namespace = "" # 这里的配置要和客户端保持一致
    cluster = "default"
    username = "nacos"
    password = "nacos"
  }
    ......
```

**配置中心**

1\. Seata-Server配置配置中心，在registry.conf中加入配置使用nacos作为配置中心

```java
config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"

  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
    dataId = "seataServer.properties"
  }
............
```

1\. 我们需要把Seata的一些配置上传到Nacos中，配置比较多，所以官方给我们提供了一个config.txt，我们下载并且修改其中参数，上传到Nacos中

   下载地址：https://github.com/seata/seata/tree/develop/script/config-center

2\. 具体修改：

   **注意：**事务分组：用于防护机房停电，来启用备用机房，或者异地机房，容错机制，当然如果Seata-Server配置了对应的事务分组，Client也需要配置相同的事务分组

   ```java
   service.vgroupMapping.可以自定义=default
   default这里必须等于 registry.config 中的cluster="default"(当然可以更改 )
   ```

```properties
transport.type=TCP
transport.server=NIO
transport.heartbeat=true
transport.enableTmClientBatchSendRequest=false
transport.enableRmClientBatchSendRequest=true
transport.rpcRmRequestTimeout=5000
transport.rpcTmRequestTimeout=10000
transport.rpcTcRequestTimeout=10000
transport.threadFactory.bossThreadPrefix=NettyBoss
transport.threadFactory.workerThreadPrefix=NettyServerNIOWorker
transport.threadFactory.serverExecutorThreadPrefix=NettyServerBizHandler
transport.threadFactory.shareBossWorker=false
transport.threadFactory.clientSelectorThreadPrefix=NettyClientSelector
transport.threadFactory.clientSelectorThreadSize=1
transport.threadFactory.clientWorkerThreadPrefix=NettyClientWorkerThread
transport.threadFactory.bossThreadSize=1
transport.threadFactory.workerThreadSize=default
#-------------修改这个区域的映射--------------    
transport.shutdown.wait=3
service.vgroupMapping.mygroup=default # 事务分组
service.default.grouplist=127.0.0.1:8091
service.enableDegrade=false
service.disableGlobalTransaction=false
#------------------------------------------    
client.rm.asyncCommitBufferLimit=10000
client.rm.lock.retryInterval=10
client.rm.lock.retryTimes=30
client.rm.lock.retryPolicyBranchRollbackOnConflict=true
client.rm.reportRetryCount=5
client.rm.tableMetaCheckEnable=false
client.rm.tableMetaCheckerInterval=60000
client.rm.sqlParserType=druid
client.rm.reportSuccessEnable=false
client.rm.sagaBranchRegisterEnable=false
client.rm.sagaJsonParser=fastjson
client.rm.tccActionInterceptorOrder=-2147482648
client.tm.commitRetryCount=5
client.tm.rollbackRetryCount=5
client.tm.defaultGlobalTransactionTimeout=60000
client.tm.degradeCheck=false
client.tm.degradeCheckAllowTimes=10
client.tm.degradeCheckPeriod=2000
client.tm.interceptorOrder=-2147482648
store.mode=db # 修改
store.lock.mode=file
store.session.mode=file
store.publicKey=
store.file.dir=file_store/data
store.file.maxBranchSessionSize=16384
store.file.maxGlobalSessionSize=512
store.file.fileWriteBufferCacheSize=16384
store.file.flushDiskMode=async
store.file.sessionReloadReadSize=100
#-----------修改这个区域的JDBC连接-----------    
store.db.datasource=druid
store.db.dbType=mysql
store.db.driverClassName=com.mysql.jdbc.Driver
store.db.url=jdbc:mysql://127.0.0.1:3306/seata?useUnicode=true&rewriteBatchedStatements=true
store.db.user=root
store.db.password=root
store.db.minConn=5
store.db.maxConn=30
#------------------------------------------    
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.distributedLockTable=distributed_lock
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000
store.redis.mode=single
store.redis.single.host=127.0.0.1
store.redis.single.port=6379
store.redis.sentinel.masterName=
store.redis.sentinel.sentinelHosts=
store.redis.maxConn=10
store.redis.minConn=1
store.redis.maxTotal=100
store.redis.database=0
store.redis.password=
store.redis.queryLimit=100
server.recovery.committingRetryPeriod=1000
server.recovery.asynCommittingRetryPeriod=1000
server.recovery.rollbackingRetryPeriod=1000
server.recovery.timeoutRetryPeriod=1000
server.maxCommitRetryTimeout=-1
server.maxRollbackRetryTimeout=-1
server.rollbackRetryTimeoutUnlockEnable=false
server.distributedLockExpireTime=10000
client.undo.dataValidation=true
client.undo.logSerialization=jackson
client.undo.onlyCareUpdateColumns=true
server.undo.logSaveDays=7
server.undo.logDeletePeriod=86400000
client.undo.logTable=undo_log
client.undo.compress.enable=true
client.undo.compress.type=zip
client.undo.compress.threshold=64k
log.exceptionRate=100
transport.serialization=seata
transport.compressor=none
metrics.enabled=false
metrics.registryType=compact
metrics.exporterList=prometheus
metrics.exporterPrometheusPort=9898
tcc.fence.logTableName=tcc_fence_log
tcc.fence.cleanPeriod=1h
```

1\. 修改好这个文件以后，我们就需要把这个文件放到seata目录下

![image-20220118195227875](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/546551b1-21d5-59cc-a8d4-f911fa5349d3.png)

1\. 此时我们需要把这些配置一个个的加入到Nacos配置中，所以我们需要一个脚本来进行执行，官方已经提供好了，地址为：https://github.com/seata/seata/blob/develop/script/config-center/nacos/nacos-config.sh

2\. 我们需要在seata-server-1.4.2文件夹中新建一个脚本文件nacos-config.sh，然后把脚本内容复制进去

![image-20220118200307770](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/05967d89-f3dd-5fa8-a2ef-986c37dbbda2.png)

1\. 利用git来进行执行命令：

```java
sh nacos-config.sh -h localhost -p 8848 -g SEATA_GROUP -t 命名空间 -u nacos -w nacos
```

参数说明：

-h：host，默认值localhost

-p：port，默认值8848

-g：配置分组，默认为SEATA_GROUP

-t：租户信息，对应Nacos的命名空间ID，默认为空

![image-20220118200423603](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f44ecf1e-9403-5b13-929e-915f2ab41457.png)

在执行naocs-config文件的时候要注意，它默认寻找config.txt的路径和我们的路径不同，所以要打开naocs-config文件进行修改，否则无法执行。

![image-20220119002450133](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4fcfaf7f-41a9-5bf1-ab9a-dcaf1f1c2ce7.png)

**测试启动**

当以上的这些配置完成以后，我们就可以启动nacos和seata-server了，此时我们查看Nacos的配置中心，就会看到我们传入的所有配置信息

![image-20220119003324612](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c3617051-81b3-5514-b7db-9516e09ec1a4.png)



#### 1.3.2 附加

各位我们Seata-Server默认端口是8091，那么如果我们是集群部署方式，我们如何修改端口那？

在 Linux/Mac 下

```bash
$ sh ./bin/seata-server.sh
```

在 Windows 下

```cmd
bin\seata-server.bat
```

**支持的启动参数**

| 参数 | 全写         | 作用                       | 备注                                                         |
| :--- | :----------- | :------------------------- | :----------------------------------------------------------- |
| -h   | --host       | 指定在注册中心注册的 IP    | 不指定时获取当前的 IP，外部访问部署在云环境和容器中的 server 建议指定 |
| -p   | --port       | 指定 server 启动的端口     | 默认为 8091                                                  |
| -m   | --storeMode  | 事务日志存储方式           | 支持`file`,`db`,`redis`，默认为 `file` 注:redis需seata-server 1.3版本及以上 |
| -n   | --serverNode | 用于指定seata-server节点ID | 如 `1`,`2`,`3`..., 默认为 `1`                                |
| -e   | --seataEnv   | 指定 seata-server 运行环境 | 如 `dev`, `test` 等, 服务启动时会使用 `registry-dev.conf` 这样的配置 |

如：

```bash
$ sh ./bin/seata-server.sh -p 8091 -h 127.0.0.1 -m file
```

### 1.4 Seata-AT模式

概念：AT模式（Automatic Transaction）是一种无侵入的分布式事务解决方案，在 AT 模式下，用户只需关注自己的“业务 SQL”，用户的 “业务 SQL” 作为一阶段，Seata 框架会自动生成事务的二阶段提交和回滚操作。



#### 1.4.1 整体机制

两阶段提交协议的演变：

1\. 一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。
2\. 二阶段：
  1\. 提交异步化，非常快速地完成。
  2\. 回滚通过一阶段的回滚日志进行反向补偿。

传统 2PC 有两个阶段：

1. **Prepare 阶段**：协调者询问各参与者能不能提交，参与者锁定资源并返回准备结果。
2. **Commit/Rollback 阶段**：协调者根据所有参与者的结果，通知大家统一提交或回滚。

Seata AT 模式也有类似结构：

1. **一阶段**：本地事务直接提交，同时生成 `undo_log`（Seata的日志，不是mysql的），并向 TC 注册分支事务、申请全局锁。
2. **二阶段**：
   - 如果全局事务成功：异步删除 `undo_log`。
   - 如果全局事务失败：根据 `undo_log` 反向补偿，回滚本地数据。

所以它确实是 2PC 思想的演变：仍然有全局协调者，也仍然追求多个分支事务的最终一致提交或回滚。

Seata AT 模式是 2PC 的变体：

- **像 2PC**：有全局协调者，有一阶段和二阶段，有统一提交/回滚决策。
- **不像传统 2PC**：一阶段就提交本地事务，二阶段回滚靠 `undo_log` 补偿，不长期持有数据库本地锁。

所以它牺牲了一部分传统 2PC 的强隔离特性，换来了更好的性能、更低的业务侵入性和更适合微服务场景的工程可用性。



**一阶段**

在一阶段中，Seata会拦截“业务SQL“，首先解析SQL语义，找到要更新的业务数据，在数据被更新前，保存下来"undo"，然后执行”业务SQL“更新数据，更新之后再次保存数据”redo“，最后生成行锁，这些操作都在本地数据库事务内完成，这样保证了一阶段的原子性。

**二阶段**

相对一阶段，二阶段比较简单，负责整体的回滚和提交，如果之前的一阶段中有本地事务没有通过，那么就执行全局回滚，否在执行全局提交，回滚用到的就是一阶段记录的"undo Log"，通过回滚记录生成反向更新SQL并执行，以完成分支的回滚。当然事务完成后会释放所有资源和删除所有日志。

**具体图解**

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/64c59615-8310-520f-b366-f58d83e65244.png" alt="image-20220118161225488" style="zoom:33%;" />

#### 1.4.2 案例

1\. 首先设计两个服务，一个订单order8801 一个库存stock8802

2\. stock库存表为：

   <img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/73e9bc3c-f9ec-54c7-9f29-53da03b1eab2.png" alt="image-20220117223403744" style="zoom:33%;" />

3\. order订单表为：

   <img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f6fc4010-a883-56c7-99ec-510089466c7a.png" alt="image-20220117223340923" style="zoom: 33%;" />

4\. 订单服务通过OpenFegin远程调用库存服务，然后库存服务减库存，订单服务生成订单，完成基本的调用以后我们给订单服务添加异常

```java
@Service
public class OrderServiceImpl implements OrderService {
    @Resource
    private OrderMapper orderMapper;

    @Resource
    private StockClient stockClient;

    @Override
    public void create() {
        // 减库存
        stockClient.decrement();

        // 添加异常
        int i = 1/0;

        // 创建订单
        orderMapper.create();
    }
}
```

5\. 此时我们会发现访问接口出现异常情况，但是库存减少，订单没有增加此时已经出现了分布式事务的问题

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4cc221c2-a885-5599-9a0d-8b57238ed2a6.png" alt="image-20220117223455391" style="zoom:33%;" />

订单表，没有增加数据

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c8fc42c7-e90b-5f66-9fd4-c11138988d1c.png" alt="image-20220117223513706" style="zoom:33%;" />

**通过Seata的AT模式解决分布式事务**

1\. 首先增加对应的Seata依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
</dependency>
```

2\. 在对应的微服务数据库上加上undo_log表，此表用于数据的回滚

```java
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  `ext` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7e894454-a6a8-50cc-a15b-52145759317b.png" alt="image-20220117225203163" style="zoom:33%;" />

3\. 配置yml（8801和8802 Seata的配置保持一致）

```yaml
server:
  port: 8801
spring:
  application:
    name: seata-order
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    alibaba:
      seata:
        tx-service-group: mygroup   # 事务组, 随便写
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test_at?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
    username: root
    password: root
    type: com.alibaba.druid.pool.DruidDataSource

seata:
  tx-service-group: mygroup # 事务组名称，要和服务端对应
  service:
    vgroup-mapping:
      mygroup: default # key是事务组名称 value要和服务端的机房名称保持一致

```

4\. 需要在order8801(TM)的Controller上添加注解

```java
@RestController
public class OrderController {
    @Autowired
    private OrderService orderService;

    @GetMapping("/order/create")
    @GlobalTransactional// 开启分布式事务
    public String create(){
        orderService.create();
        return "生成订单";
    }
}
```

5\. 把8801和8802都跑起来，当然Nacos和Seata都要进行启动，这个时候我们进行访问Order的REST接口：http://localhost:8801/order/create，我们就会发现此时已经解决了分布式事务问题

   库存没有减少

   订单也没有增加

| 图示                                                         |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220118162757573](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/abf738e1-6c76-50ee-be22-1ff1e22bb7fe.png) | ![image-20220118162832649](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3538162b-577c-5bd9-8b4d-20e2d8fceaf8.png) |

6\. 那么为了验证undo_log表用于存储回滚的数据，我们在OrderServiceImpl上异常位置添加断点，同时以debug方式来启动8801订单服务

```java
@Service
public class OrderServiceImpl implements OrderService {
    @Resource
    private OrderMapper orderMapper;

    @Resource
    private StockClient stockClient;

    @Override
    public void create() {
        // 减库存
        stockClient.decrement();

        // 添加异常
        int i = 1/0;// 此处添加断点

        // 创建订单
        orderMapper.create();
    }
}
```

7\. 然后访问接口：http://localhost:8801/order/create，程序会卡在断点上，此时我们来查看undo_log表和库存表，此时我们会发现，库存确实减少了，但是在undo_log表中出现了快照记录了当前修改前的数据，这个数据就是用于回滚的数据

库存减少

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/35b5e485-ca25-5d0f-bb4c-e54dda6c1c41.png" alt="image-20220118163714002" style="zoom: 50%;" />

undo_log表记录快照

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/3eab996b-5ac0-51ca-8c0c-db1646ffa65d.png" alt="image-20220118163908365" style="zoom:50%;" />

放行以后，库存数量回复，回滚生效

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2410a4c4-d83d-5b5e-a423-4902aec8bd87.png" alt="image-20220118164019307" style="zoom:50%;" />

8\. 此时我们就验证了AT事务的执行过程。



**总结：**

订单服务调用链开始时，向 TC 开启一个全局事务，并生成一个 `XID`。之后订单服务远程调用库存服务时，Seata 会把这个 `XID` 通过 Feign 请求上下文传到库存服务。

所以执行链是：

```
订单服务 @GlobalTransactional
  -> 开启全局事务，绑定 XID
  -> 调用库存服务，XID 随请求传递过去
  -> 库存服务感知到 XID
  -> 库存服务的数据库操作注册为分支事务
  -> 订单服务后续异常
  -> TM 通知 TC 全局回滚
  -> TC 通知库存服务 RM 根据 undo_log 回滚库存
```

关键点是：**库存服务不需要再加 `@GlobalTransactional`，因为它不是全局事务发起者，而是参与者 RM。**

但这不代表库存服务什么都不用配。库存服务仍然必须具备这些条件：

1. 引入 Seata 依赖。
2. 配置 Seata 客户端。
3. 数据源被 Seata 代理。
4. 库存库里有 `undo_log` 表。
5. Feign/HTTP 调用能把 `XID` 传过去。

否则库存服务虽然被调用了，但不会加入订单服务发起的全局事务。



#### 1.4.3 全局事务与本地事务协调

下面用一个**下单**场景说明，比较贴近 Seata AT 模式的真实用法。

业务：创建订单、扣库存、扣余额，三个服务分别有自己的数据库。

```java
// 订单服务：全局事务入口，负责“编排”
@Service
public class OrderBizService {

    @Autowired
    private OrderService orderService;
    @Autowired
    private StockClient stockClient;
    @Autowired
    private AccountClient accountClient;

    @GlobalTransactional(rollbackFor = Exception.class)
    public void placeOrder(Long userId, Long productId, Integer count) {
        orderService.createOrder(userId, productId, count);

        stockClient.deduct(productId, count);

        accountClient.debit(userId, 100);

        // 假设这里异常
        // throw new RuntimeException("下单失败");
    }
}
```

分支服务内部用 Spring 本地事务：

```java
// 订单服务本地逻辑
@Service
public class OrderService {

    @Transactional(rollbackFor = Exception.class)
    public void createOrder(Long userId, Long productId, Integer count) {
        orderMapper.insertOrder(userId, productId, count);
        orderLogMapper.insertLog(userId, productId, count);
    }
}
// 库存服务
@Service
public class StockService {

    @Transactional(rollbackFor = Exception.class)
    public void deduct(Long productId, Integer count) {
        int rows = stockMapper.deduct(productId, count);
        if (rows == 0) {
            throw new RuntimeException("库存不足");
        }

        stockLogMapper.insertDeductLog(productId, count);
    }
}
// 账户服务
@Service
public class AccountService {

    @Transactional(rollbackFor = Exception.class)
    public void debit(Long userId, Integer amount) {
        int rows = accountMapper.debit(userId, amount);
        if (rows == 0) {
            throw new RuntimeException("余额不足");
        }

        accountLogMapper.insertDebitLog(userId, amount);
    }
}
```

核心使用方式就是：

```java
全局入口：@GlobalTransactional
分支服务：@Transactional
```

**正常成功时发生什么**

以 AT 模式为例：

1. `placeOrder()` 开启 Seata 全局事务，生成 `XID`。
2. 调用订单、库存、账户服务时，`XID` 会通过 RPC 传到各服务。
3. 每个分支服务里的 `@Transactional` 开启本地事务。
4. Seata 的数据源代理拦截 SQL，生成前镜像、后镜像，并写入 `undo_log`。
5. 每个分支的一阶段提交时，业务数据和 `undo_log` 在同一个本地事务里提交。
6. 所有分支成功后，Seata 二阶段全局提交。
7. AT 模式下提交阶段很轻，主要是异步清理 `undo_log`。

**分支内部异常时怎么回滚**

比如库存不足：

```java
stockClient.deduct(productId, count);
```

库存服务里抛出：

```java
throw new RuntimeException("库存不足");
```

这时分两层看：

第一层是**库存服务本地事务回滚**：

```java
@Transactional
public void deduct(...) {
    stockMapper.deduct(...);
    stockLogMapper.insertDeductLog(...);
    throw new RuntimeException("库存不足");
}
```

因为异常抛出，Spring 的 `@Transactional` 会回滚库存服务当前本地事务。也就是说，库存表、库存流水表这些本地 SQL 不会提交。

第二层是**Seata 全局事务回滚**：

异常从库存服务返回到 `placeOrder()`，`@GlobalTransactional` 感知到异常，于是通知 TC：全局事务失败，需要回滚。

然后 Seata 会通知已经成功提交过一阶段的分支做回滚。比如订单分支已经创建了订单并提交了本地事务，那 Seata 会根据订单库里的 `undo_log` 执行反向补偿，把订单数据恢复到执行前状态。

所以结果是：

```txt
库存分支：本地事务自己回滚
订单分支：已经一阶段提交了，由 Seata 根据 undo_log 回滚
账户分支：如果还没执行，就不会参与；如果已执行成功，也会被 Seata 回滚
```

**全局入口后面异常时怎么回滚**

比如三个服务都执行成功了，但最后：

```java
throw new RuntimeException("下单失败");
```

这时订单、库存、账户三个分支的一阶段本地事务可能都已经提交了。注意，AT 模式不是一直等到最后才提交本地事务，它是一阶段就提交本地事务，并保存 `undo_log`。

全局异常发生后：

1. `@GlobalTransactional` 触发全局回滚。
2. TC 通知订单、库存、账户三个 RM 回滚。
3. 每个服务根据自己的 `undo_log` 做反向 SQL。
4. 回滚成功后删除或标记相关日志。

也就是：

```txt
订单已插入 -> 根据 undo_log 删除/恢复
库存已扣减 -> 根据 undo_log 加回去
余额已扣减 -> 根据 undo_log 加回去
```

**最容易踩的坑**

不要把异常吞掉：

```java
@GlobalTransactional
public void placeOrder(...) {
    try {
        stockClient.deduct(...);
    } catch (Exception e) {
        // 只打印，不抛出
        log.error("扣库存失败", e);
    }

    accountClient.debit(...);
}
```

这样 Seata 可能认为全局事务正常完成，导致不回滚。正确做法是继续抛出：

```java
@GlobalTransactional(rollbackFor = Exception.class)
public void placeOrder(...) {
    try {
        stockClient.deduct(...);
    } catch (Exception e) {
        throw e;
    }
}
```

或者包装成运行时异常：

```java
throw new RuntimeException("扣库存失败", e);
```

另外建议加上：

```java
rollbackFor = Exception.class
```

因为 Spring 默认只对 `RuntimeException` 和 `Error` 回滚；如果你抛的是受检异常，不加 `rollbackFor` 可能不会触发本地事务回滚。

总结一下：

```java
@GlobalTransactional：管多个服务之间的一致性
@Transactional：管单个服务内部多条 SQL 的原子性

本地异常没提交：Spring 本地事务直接回滚
分支已经提交后全局失败：Seata AT 用 undo_log 补偿回滚
异常必须向外抛：否则全局事务可能不会回滚
```



### 1.5 Seata-XA模式

Seata 1.2.0 版本重磅发布新的事务模式：XA 模式，实现对 XA 协议的支持。

我们从三个方面来深入分析：

1\. XA模式是什么？
2\. 为什么支持XA？
3\. XA模式如何实现的，以及如何使用？

#### 1.5.1 XA模式

首先我们需要先了解一下什么是XA？

XA 规范早在上世纪 90 年代初就被提出，用以解决分布式事务处理这个领域的问题。

注意：不存在某一种分布式事务机制可以完美适应所有场景，满足所有需求。

现在，无论 AT 模式、TCC 模式还是 Saga 模式，这些模式的提出，本质上都源自 XA 规范对某些场景需求的无法满足。

**什么是XA协议**

XA 规范 是 X/Open 组织定义的分布式事务处理（DTP，Distributed Transaction Processing）标准

XA 规范 描述了全局的事务管理器与局部的资源管理器之间的接口。 XA规范 的目的是允许的多个资源（如数据库，应用服务器，消息队列等）在同一事务中访问，这样可以使 ACID 属性跨越应用程序而保持有效。

XA 规范 使用两阶段提交（2PC，Two-Phase Commit）来保证所有资源同时提交或回滚任何特定的事务。

XA 规范 在上世纪 90 年代初就被提出。目前，几乎所有主流的数据库都对 XA 规范 提供了支持。



DTP模型定义如下角色：

1\. AP：即应用程序，可以理解为使用DTP分布式事务的程序
2\. RM：资源管理器，可以理解为事务的参与者，一般情况下是指一个数据库的实例（MySql），通过资源管理器对该数据库进行控制，资源管理器控制着分支事务
3\. TM：事务管理器，负责协调和管理事务，事务管理器控制着全局事务，管理实务生命周期，并协调各个RM。全局事务是指分布式事务处理环境中，需要操作多个数据库共同完成一个工作，这个工作即是一个全局事务。



DTP模式定义TM和RM之间通讯的接口规范叫XA，简单理解为数据库提供的2PC接口协议，基于数据库的XA协议来实现的2PC又称为XA方案。

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a60a3e75-3edc-51ac-b046-f4949e01901c.png)

案例解释：

1\. 应用程序（AP）持有订单库和商品库两个数据源。
2\. 应用程序（AP）通过TM通知订单库（RM）和商品库（RM），来创建订单和减库存，RM此时未提交事务，此时商品和订单资源锁定。
3\. TM收到执行回复，只要有一方失败则分别向其他RM发送回滚事务，回滚完毕，资源锁释放。
4\. TM收到执行回复，全部成功，此时向所有的RM发起提交事务，提交完毕，资源锁释放。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ae806cc7-1d12-5166-b4d7-c530bf57fea2.png" alt="image-20220209173222316" style="zoom: 33%;" />

**XA协议的痛点**

如果一个参与全局事务的资源 “失联” 了（收不到分支事务结束的命令），那么它锁定的数据，将一直被锁定。进而，甚至可能因此产生死锁。

这是 XA 协议的核心痛点，也是 Seata 引入 XA 模式要重点解决的问题。

**Seata的事务模式**

Seata 定义了全局事务的框架。

全局事务 定义为若干 分支事务 的整体协调：

1\. TM 向 TC 请求发起（Begin）、提交（Commit）、回滚（Rollback）全局事务。
2\. TM 把代表全局事务的 XID 绑定到分支事务上。
3\. RM 向 TC 注册，把分支事务关联到 XID 代表的全局事务中。
4\. RM 把分支事务的执行结果上报给 TC。（可选）
5\. TC 发送分支提交（Branch Commit）或分支回滚（Branch Rollback）命令给 RM。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a54a2834-5dc9-55ad-ba8f-c87b05215a1e.png" alt="seata-mod" style="zoom:50%;" />

Seata 的 全局事务 处理过程，分为两个阶段：

1\. 执行阶段 ：执行分支事务，并保证执行结果满足是 *可回滚的（Rollbackable）* 和 *持久化的（Durable）*。
2\. 完成阶段： 根据 执行阶段 结果形成的决议，应用通过 TM 发出的全局提交或回滚的请求给 TC，TC 命令 RM 驱动 分支事务 进行 Commit 或 Rollback。

Seata 的所谓事务模式是指：运行在 Seata 全局事务框架下的 分支事务 的行为模式。准确地讲，应该叫作 分支事务模式。

不同的 事务模式 区别在于 分支事务 使用不同的方式达到全局事务两个阶段的目标。即，回答以下两个问题：

1\. 执行阶段 ：如何执行并 保证 执行结果满足是 *可回滚的（Rollbackable）* 和 *持久化的（Durable）*。
  1\. 完成阶段： 收到 TC 的命令后，如何做到分支的提交或回滚？

我们以AT模式举例：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4a19e2e8-3ab4-5d1b-ac37-7b56dc37eff9.png" alt="at-mod" style="zoom:50%;" />

1\. 执行阶段：
  1\. 可回滚：根据 SQL 解析结果，记录回滚日志
  2\. 持久化：回滚日志和业务 SQL 在同一个本地事务中提交到数据库
2\. 完成阶段：
  1\. 分支提交：异步删除回滚日志记录
  2\. 分支回滚：依据回滚日志进行反向补偿更新

#### 1.5.2 Seata的XA模式

XA模式：

在 Seata 定义的分布式事务框架内，利用事务资源（数据库、消息服务等）对 XA 协议的支持，以 XA 协议的机制来管理分支事务的一种 事务模式。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f8c474dd-22dd-5969-8811-17745ae77794.png" alt="xa-mod" style="zoom:50%;" />

1\. 执行阶段：
  1\. 可回滚：业务 SQL 操作放在 XA 分支中进行，由资源对 XA 协议的支持来保证 可回滚
  2\. 持久化：XA 分支完成后，执行 XA prepare，同样，由资源对 XA 协议的支持来保证 *持久化*（即，之后任何意外都不会造成无法回滚的情况）
2\. 完成阶段：
  1\. 分支提交：执行 XA 分支的 commit
  2\. 分支回滚：执行 XA 分支的 rollback

**为什么要在Seata中支持XA**

为什么要在 Seata 中增加 XA 模式呢？支持 XA 的意义在哪里呢？

本质上，Seata 已经支持的 3 大事务模式：AT、TCC、Saga 都是 补偿型 的。

补偿型 事务处理机制构建在 事务资源 之上（要么在中间件层面，要么在应用层面），事务资源 本身对分布式事务是无感知的。

事务资源 对分布式事务的无感知存在一个根本性的问题：无法做到真正的 全局一致性 。

比如，一条库存记录，处在 补偿型 事务处理过程中，由 100 扣减为 50。此时，仓库管理员连接数据库，查询统计库存，就看到当前的 50。之后，事务因为异外回滚，库存会被补偿回滚为 100。显然，仓库管理员查询统计到的 50 就是 脏 数据。所以补偿型事务是存在中间状态的（中途可能读到脏数据）

**XA的价值**

与 补偿型 不同，XA 协议 要求 事务资源 本身提供对规范和协议的支持。

因为 事务资源 感知并参与分布式事务处理过程，所以 事务资源（如数据库）可以保障从任意视角对数据的访问有效隔离，满足全局数据一致性。

比如，刚才提到的库存更新场景，XA 事务处理过程中，中间状态数据库存 50 由数据库本身保证，是不会仓库管理员的查询统计看到的。

除了 全局一致性 这个根本性的价值外，支持 XA 还有如下几个方面的好处：

1\. 业务无侵入：和 AT 一样，XA 模式将是业务无侵入的，不给应用设计和开发带来额外负担。
2\. 数据库的支持广泛：XA 协议被主流关系型数据库广泛支持，不需要额外的适配即可使用。
3\. 多语言支持容易：因为不涉及 SQL 解析，XA 模式对 Seata 的 RM（资源管理器） 的要求比较少。
4\. 传统基于 XA 应用的迁移：传统的，基于 XA 协议的应用，迁移到 Seata 平台，使用 XA 模式将更平滑。

#### 1.5.3 XA模式的使用

我们从官方案例入手，具体的官方案例下载地址：https://github.com/seata/seata-samples

官方案例演示图：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b019080b-a976-531b-b586-f1a43cadb521.png" alt="image-20220211150003482" style="zoom: 33%;" />

案例解析：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e61d27f6-9dfd-5d4f-adf4-0442d7aebb77.png" alt="image-20220211155757596" style="zoom:33%;" />

整体运行机制：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c943bd07-ce59-5293-b8a5-8de08d385600.png" alt="xa-fw" style="zoom: 50%;" />

#### 1.5.4 总结

在当前的技术发展阶段，不存一个分布式事务处理机制可以完美满足所有场景的需求。

一致性、可靠性、易用性、性能等诸多方面的系统设计约束，需要用不同的事务处理机制去满足。

Seata 项目最核心的价值在于：构建一个全面解决分布式事务问题的 标准化 平台。

基于 Seata，上层应用架构可以根据实际场景的需求，灵活选择合适的分布式事务解决方案。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2632ac6b-4282-5bfd-8082-9625ecc8e9ea.png" alt="img" style="zoom:33%;" />

XA 模式的加入，补齐了 Seata 在 全局一致性 场景下的缺口，形成 AT、TCC、Saga、XA 四大 事务模式 的版图，基本可以满足所有场景的分布式事务处理诉求。

### 1.6 Seata-TCC模式

首先我们先来了解常规的TCC模式。

**什么是TCC**

TCC 是分布式事务中的二阶段提交协议，它的全称为 Try-Confirm-Cancel，即资源预留（Try）、确认操作（Confirm）、取消操作（Cancel），他们的具体含义如下：

1\. Try：对业务资源的检查并预留；
2\. Confirm：对业务处理进行提交，即 commit 操作，只要 Try 成功，那么该步骤一定成功；
3\. Cancel：对业务处理进行取消，即回滚操作，该步骤回对 Try 预留的资源进行释放。

TCC 是一种侵入式的分布式事务解决方案，以上三个操作都需要业务系统自行实现，对业务系统有着非常大的入侵性，设计相对复杂，但优点是 TCC 完全不依赖数据库，能够实现跨数据库、跨应用资源管理，对这些不同数据访问通过侵入式的编码方式实现一个原子操作，更好地解决了在各种复杂业务场景下的分布式事务问题。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d6fe3196-bb39-5480-b112-253b185e081b.png" alt="image-20220215233609040" style="zoom: 67%;" />

**Seata的TCC模式**

Seata TCC 模式跟通用型 TCC 模式原理一致。

**TCC和AT区别**

AT 模式基于 **支持本地 ACID 事务** 的 **关系型数据库**：

1\. 一阶段 prepare 行为：在本地事务中，一并提交业务数据更新和相应回滚日志记录。
2\. 二阶段 commit 行为：马上成功结束，**自动** 异步批量清理回滚日志。
3\. 二阶段 rollback 行为：通过回滚日志，**自动** 生成补偿操作，完成数据回滚。

相应的，TCC 模式，不依赖于底层数据资源的事务支持：

1\. 一阶段 prepare 行为：调用 **自定义** 的 prepare 逻辑。
2\. 二阶段 commit 行为：调用 **自定义** 的 commit 逻辑。
3\. 二阶段 rollback 行为：调用 **自定义** 的 rollback 逻辑。

所谓 TCC 模式，是指支持把 **自定义** 的分支事务纳入到全局事务的管理中。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ec8e2bbd-44b2-5dba-b018-1e9bd4703b17.png" alt="Overview of a global transaction" style="zoom:67%;" />

#### 1.6.1 特点

1\. 侵入性比较强，并且需要自己实现相关事务控制逻辑
2\. 在整个过程基本没有锁，性能较强

#### 1.6.2 详细讲解

![image-20220125210417709](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/adb81e1d-7b1d-5327-b671-4561b69b63af.png)

具体使用案例：https://seata.io/zh-cn/blog/integrate-seata-tcc-mode-with-spring-cloud.html

#### 1.6.3 具体代码案例

下面以“下单时冻结账户余额”为例说明 TCC 的编码方式。业务入口仍然由 `@GlobalTransactional` 开启全局事务，账户服务作为 TCC 分支事务参与者，分别实现 Try、Confirm、Cancel 三个方法。

##### 1.6.3.1 业务表设计

账户表需要同时记录可用余额和冻结余额。Try 阶段扣减可用余额并增加冻结余额，Confirm 阶段真正扣减冻结余额，Cancel 阶段把冻结余额退回可用余额。

```sql
CREATE TABLE account_tbl (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id VARCHAR(64) NOT NULL,
    residue DECIMAL(18, 2) NOT NULL COMMENT '可用余额',
    frozen DECIMAL(18, 2) NOT NULL COMMENT '冻结余额'
);
```

为了处理幂等、空回滚和悬挂问题，实际项目中通常还需要一张事务控制表，记录每个 TCC 分支的执行状态。

```sql
CREATE TABLE account_tcc_log (
    xid VARCHAR(128) NOT NULL,
    branch_id BIGINT NOT NULL,
    action_name VARCHAR(64) NOT NULL,
    status VARCHAR(16) NOT NULL COMMENT 'TRYING, CONFIRMED, CANCELED',
    gmt_create DATETIME NOT NULL,
    gmt_modified DATETIME NOT NULL,
    PRIMARY KEY (xid, branch_id)
);
```

##### 1.6.3.2 TCC接口定义

TCC 接口需要使用 `@LocalTCC` 标识为本地 TCC 服务，Try 方法使用 `@TwoPhaseBusinessAction` 指定 Confirm 和 Cancel 方法名。需要在二阶段使用的参数，可以通过 `@BusinessActionContextParameter` 放入 `BusinessActionContext`。

```java
@LocalTCC
public interface AccountTccAction {

    @TwoPhaseBusinessAction(
            name = "accountTccAction",
            commitMethod = "commit",
            rollbackMethod = "rollback"
    )
    boolean prepare(
            BusinessActionContext actionContext,
            @BusinessActionContextParameter(paramName = "userId") String userId,
            @BusinessActionContextParameter(paramName = "money") BigDecimal money
    );

    boolean commit(BusinessActionContext actionContext);

    boolean rollback(BusinessActionContext actionContext);
}
```

##### 1.6.3.3 Try阶段实现

Try 阶段负责资源检查和资源预留。这里的资源预留就是把用户的可用余额转入冻结余额。

```java
@Service
public class AccountTccActionImpl implements AccountTccAction {

    @Resource
    private AccountMapper accountMapper;

    @Resource
    private AccountTccLogMapper accountTccLogMapper;

    @Override
    @Transactional(rollbackFor = Exception.class)
    public boolean prepare(BusinessActionContext actionContext, String userId, BigDecimal money) {
        String xid = actionContext.getXid();
        Long branchId = actionContext.getBranchId();

        AccountTccLog exists = accountTccLogMapper.selectById(xid, branchId);
        if (exists != null) {
            return true;
        }

        int updated = accountMapper.freeze(userId, money);
        if (updated != 1) {
            throw new RuntimeException("账户余额不足，冻结失败");
        }

        accountTccLogMapper.insert(xid, branchId, "accountTccAction", "TRYING");
        return true;
    }
}
```

对应的冻结 SQL 可以这样写：

```sql
UPDATE account_tbl
SET residue = residue - #{money},
    frozen = frozen + #{money}
WHERE user_id = #{userId}
  AND residue >= #{money};
```

##### 1.6.3.4 Confirm阶段实现

Confirm 阶段只处理已经 Try 成功的分支。它需要保证幂等，如果已经 Confirm 过，直接返回成功。

```java
@Override
@Transactional(rollbackFor = Exception.class)
public boolean commit(BusinessActionContext actionContext) {
    String xid = actionContext.getXid();
    Long branchId = actionContext.getBranchId();

    AccountTccLog log = accountTccLogMapper.selectById(xid, branchId);
    if (log == null) {
        return true;
    }
    if ("CONFIRMED".equals(log.getStatus())) {
        return true;
    }
    if ("CANCELED".equals(log.getStatus())) {
        return true;
    }

    String userId = String.valueOf(actionContext.getActionContext("userId"));
    BigDecimal money = new BigDecimal(String.valueOf(actionContext.getActionContext("money")));

    accountMapper.confirm(userId, money);
    accountTccLogMapper.updateStatus(xid, branchId, "CONFIRMED");
    return true;
}
```

对应的确认 SQL：

```sql
UPDATE account_tbl
SET frozen = frozen - #{money}
WHERE user_id = #{userId}
  AND frozen >= #{money};
```

##### 1.6.3.5 Cancel阶段实现

Cancel 阶段负责释放 Try 阶段预留的资源。它也需要处理幂等和空回滚：如果 Try 没有执行成功，Cancel 可能仍然被 TC 调用，此时直接记录取消状态并返回成功。

```java
@Override
@Transactional(rollbackFor = Exception.class)
public boolean rollback(BusinessActionContext actionContext) {
    String xid = actionContext.getXid();
    Long branchId = actionContext.getBranchId();

    AccountTccLog log = accountTccLogMapper.selectById(xid, branchId);
    if (log == null) {
        accountTccLogMapper.insert(xid, branchId, "accountTccAction", "CANCELED");
        return true;
    }
    if ("CANCELED".equals(log.getStatus())) {
        return true;
    }
    if ("CONFIRMED".equals(log.getStatus())) {
        return true;
    }

    String userId = String.valueOf(actionContext.getActionContext("userId"));
    BigDecimal money = new BigDecimal(String.valueOf(actionContext.getActionContext("money")));

    accountMapper.cancel(userId, money);
    accountTccLogMapper.updateStatus(xid, branchId, "CANCELED");
    return true;
}
```

对应的取消 SQL：

```sql
UPDATE account_tbl
SET residue = residue + #{money},
    frozen = frozen - #{money}
WHERE user_id = #{userId}
  AND frozen >= #{money};
```

##### 1.6.3.6 全局事务入口

订单服务作为全局事务入口，只需要在编排方法上添加 `@GlobalTransactional`。账户服务的 `prepare` 方法被调用成功后，会作为一个 TCC 分支注册到全局事务中。

```java
@Service
public class OrderServiceImpl implements OrderService {

    @Resource
    private AccountTccAction accountTccAction;

    @Resource
    private OrderMapper orderMapper;

    @Override
    @GlobalTransactional(rollbackFor = Exception.class)
    public void createOrder(String userId, BigDecimal money) {
        accountTccAction.prepare(null, userId, money);

        orderMapper.insertOrder(userId, money);

        // 模拟后续业务异常，Seata 会触发 TCC Cancel
        if (money.compareTo(BigDecimal.ZERO) > 0) {
            throw new RuntimeException("模拟下单失败");
        }
    }
}
```

执行结果可以这样理解：

1\. 如果全局事务提交，TC 会调用账户服务的 `commit` 方法，扣减冻结余额。
2\. 如果全局事务回滚，TC 会调用账户服务的 `rollback` 方法，把冻结余额退回可用余额。
3\. Try、Confirm、Cancel 都要保证幂等，尤其要处理空回滚和悬挂，否则网络重试或超时场景下容易出现资金重复扣减或重复释放。

### 1.7 Seata-Saga模式

**基本概念**

Saga模式是SEATA提供的长事务解决方案，在Saga模式中，业务流程中每个参与者都提交本地事务，当出现某一个参与者失败则补偿前面已经成功的参与者，一阶段正向服务和二阶段补偿服务（执行处理时候出错了，给一个修复的机会）都由业务开发实现。

![TB1Y2kuw7T2gK0jSZFkXXcIQFXa-445-444](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2117c8fe-42de-5c3a-8fcc-94e8a6cf25f8.png)

Saga 模式下分布式事务通常是由事件驱动的，各个参与者之间是异步执行的，Saga 模式是一种长事务解决方案。

**为什么需要Saga**

之前我们学习的Seata分布式三种操作模型中所使用的的微服务全部可以根据开发者的需求进行修改，但是在一些特殊环境下，比如老系统，封闭的系统（无法修改，同时没有任何分布式事务引入），那么AT、XA、TCC模型将全部不能使用，为了解决这样的问题，才引用了Saga模型。

比如：事务参与者可能是其他公司的服务或者是遗留系统，无法改造，可以使用Saga模式。

![image-20220215170345079](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e95c0970-37c1-5367-be7e-15983e7d0fdc.png)

Saga模式是Seata提供的长事务解决方案，提供了**异构系统的事务统一处理模型**。在Saga模式中，所有的子业务都不在直接参与整体事务的处理（只负责本地事务的处理），而是全部交由了最终调用端来负责实现，而在进行总业务逻辑处理时，在某一个子业务出现问题时，则自动补偿全面已经成功的其他参与者，这样一阶段的正向服务调用和二阶段的服务补偿处理全部由总业务开发实现。

![Saga执行模型](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7e235405-1d0d-5287-9524-da05c09aa129.png)

**Saga状态机**

目前Seata提供的Saga模式只能通过状态机引擎来实现，需要开发者手工的进行Saga业务流程绘制，并且将其转换为Json配置文件，而后在程序运行时，将依据子配置文件实现业务处理以及服务补偿处理，而要想进行Saga状态图的绘制，一般需要通过Saga状态机来实现。

基本原理：

1\. 通过状态图来定义服务调用的流程并生成json定义文件
2\. 状态图中一个节点可以调用一个服务，节点可以配置它的补偿节点
3\. 状态图 json 由状态机引擎驱动执行，当出现异常时状态引擎反向执行已成功节点对应的补偿节点将事务回滚
4\. 可以实现服务编排需求，支持单项选择、并发、子流程、参数转换、参数映射、服务执行状态判断、异常捕获等功能



![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cd82ab46-bbe8-59e8-975d-15644978f7c2.png)

**Saga状态机的应用**

官方提供了一个状态机设计器

![image-20220215175333918](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c3344711-209d-55d1-a008-077ebc23c7f8.png)

官方文档地址：https://seata.io/zh-cn/docs/user/saga.html

Seata Safa状态机可视化图形设计器使用地址：https://github.com/seata/seata/blob/develop/saga/seata-saga-statemachine-designer/README.zh-CN.md

## 2 Seata源码分析

### 2.1 Seata源码学习引入

我们学习了Seata的应用以后，我们从这节课开始要开始分析Seata的源码相关内容

**源码下载**

官方地址：https://seata.io/zh-cn/blog/download.html

![image-20220217165653477](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ad5679d5-5f75-5b2b-92ae-44b2c2b263b5.png)

通过idea打开seata-1.4.2版本的源码

![image-20220217165735950](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ffa6ae53-91b6-533c-863e-f65a8322e257.png)



#### 2.1.1 回顾AT模式

其实在之前的应用课程中，我们已经用过AT模式，同时也写过一个小的Demo，那么这里其实我们主要要分析的是AT模式官方文档中的一些内容

官方文档：https://seata.io/zh-cn/docs/dev/mode/at-mode.html

**写隔离**

1\. 一阶段本地事务提交前，需要确保先拿到 **全局锁** 。
2\. 拿不到 **全局锁** ，不能提交本地事务。
3\. 拿 **全局锁** 的尝试被限制在一定范围内，超出范围将放弃，并回滚本地事务，释放本地锁。

图解：

![image-20220221173230146](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cdf1708d-b648-59d0-994e-4e1d0315439c.png)

如果 tx1 的二阶段全局回滚，则 tx1 需要重新获取该数据的本地锁，进行反向补偿的更新操作，实现分支的回滚。

此时，如果 tx2 仍在等待该数据的 **全局锁**，同时持有本地锁，则 tx1 的分支回滚会失败。分支的回滚会一直重试，直到 tx2 的 **全局锁** 等锁超时，放弃 **全局锁** 并回滚本地事务释放本地锁，tx1 的分支回滚最终成功。

因为整个过程 **全局锁** 在 tx1 结束前一直是被 tx1 持有的，所以不会发生 **脏写** 的问题。

**读隔离**

在数据库本地事务隔离级别 **读已提交（Read Committed）** 或以上的基础上，Seata（AT 模式）的默认全局隔离级别是 **读未提交（Read Uncommitted）** 。

如果应用在特定场景下，必需要求全局的 **读已提交** ，目前 Seata 的方式是通过 SELECT FOR UPDATE 语句的代理。

图解：

![image-20220221175333911](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/71877bc7-59f3-5ecf-865a-12a7411d1ff1.png)

SELECT FOR UPDATE 语句的执行会申请 **全局锁** ，如果 **全局锁** 被其他事务持有，则释放本地锁（回滚 SELECT FOR UPDATE 语句的本地执行）并重试。这个过程中，查询是被 block 住的，直到 **全局锁** 拿到，即读取的相关数据是 **已提交** 的，才返回。

出于总体性能上的考虑，Seata 目前的方案并没有对所有 SELECT 语句都进行代理，仅针对 FOR UPDATE 的 SELECT 语句。

**AT二阶段**

一阶段：

​	1. 解析 SQL：得到 SQL 的类型（UPDATE），表（product），条件（where name = 'TXC'）等相关的信息。

1\. 查询前镜像（改变之前的数据）：根据解析得到的条件信息，生成查询语句，定位数据。
2\. 执行业务 SQL：更新这条数据。
3\. 查询后镜像（改变后的数据）：根据前镜像的结果，通过 **主键** 定位数据。
4\. 插入回滚日志：把前后镜像数据以及业务 SQL 相关的信息组成一条回滚日志记录，插入到 `UNDO_LOG` 表中。
5\. 提交前，向 TC 注册分支：申请 **全局锁** 。
6\. 本地事务提交：业务数据的更新和前面步骤中生成的 UNDO LOG 一并提交。
7\. 将本地事务提交的结果上报给 TC。

二阶段-回滚：

1\. 收到 TC 的分支回滚请求，开启一个本地事务，执行如下操作。
2\. 通过 XID 和 Branch ID 查找到相应的 UNDO LOG 记录。
3\. 根据 UNDO LOG 中的前镜像和业务 SQL 的相关信息生成并执行回滚的语句：
4\. 提交本地事务。并把本地事务的执行结果（即分支事务回滚的结果）上报给 TC。

二阶段-提交：

1\. 收到 TC 的分支提交请求，把请求放入一个异步任务的队列中，马上返回提交成功的结果给 TC。
2\. 异步任务阶段的分支提交请求将异步和批量地删除相应 UNDO LOG 记录。

### 2.2 Seata源码剖析-源码入口

#### 2.2.1 Seata客户端启动

首先一个Seata的客户端启动一般分为几个流程：

1\. 自动加载各种Bean及配置信息
2\. 初始化TM
3\. 初始化RM（具体服务）
4\. 初始化分布式事务客户端完成，代理数据源
5\. 连接TC（Seata服务端），注册RM，注册TM
6\. 开启全局事务

在这个其中，就会涉及到几个核心的类型，首先我们需要来看配置类型GlobalTransactionAutoConfiguration

所以我们直接通过官方案例引入的Seata包，找到SpringBoot项目在启动的时候自动扫描加载类型的spring.factories，然后找到GlobalTransactionAutoConfiguration（Seata自动配置类）

#### 2.2.2 全局事务扫描类源码

这个类型的核心点，就是加载配置，注入相关的Bean

```java
/**
 * seata自动配置类
 */
@Configuration
@EnableConfigurationProperties(SeataProperties.class)
public class GlobalTransactionAutoConfiguration {

	private final ApplicationContext applicationContext;

	private final SeataProperties seataProperties;

	public GlobalTransactionAutoConfiguration(ApplicationContext applicationContext,
			SeataProperties seataProperties) {
		this.applicationContext = applicationContext;
		this.seataProperties = seataProperties;
	}
	// 注入全局事务扫描器
	@Bean
	public GlobalTransactionScanner globalTransactionScanner() {

		String applicationName = applicationContext.getEnvironment()
				.getProperty("spring.application.name");

		String txServiceGroup = seataProperties.getTxServiceGroup();

		if (StringUtils.isEmpty(txServiceGroup)) {
			txServiceGroup = applicationName + "-fescar-service-group";
			seataProperties.setTxServiceGroup(txServiceGroup);
		}
		// 构建全局扫描器，传入参数：应用名、事务分组名，失败处理器
		return new GlobalTransactionScanner(applicationName, txServiceGroup);
	}
}

```

#### 2.2.3 GlobalTransactionScanner全局事务扫描器

在这其中我们要关心的是GlobalTransactionScanner这个类型，这个类型扫描@GlobalTransactional注解，并对代理方法进行拦截增强事务的功能。

![image-20220221231318290](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/79aea092-8c53-58f7-a0c2-fb4c15e6018b.png)

这里给大家展示了当前GlobalTransactionScanner的类关系图，其中我们现在继承了Aop的AbstractAutoProxyCreator类型，在这其中有一个重点方法，其实这个方法就是判断Bean对象是否需要代理，是否需要增强

```java
protected Object wrapIfNecessary(Object bean, String beanName, Object cacheKey) {
    if (beanName != null && this.targetSourcedBeans.contains(beanName)) {
        return bean;
    }
    if (Boolean.FALSE.equals(this.advisedBeans.get(cacheKey))) {
        return bean;
    }
    if (isInfrastructureClass(bean.getClass()) || shouldSkip(bean.getClass(), beanName)) {
        this.advisedBeans.put(cacheKey, Boolean.FALSE);
        return bean;
    }

    // Create proxy if we have advice.
    Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null);
    if (specificInterceptors != DO_NOT_PROXY) {
        this.advisedBeans.put(cacheKey, Boolean.TRUE);
        Object proxy = createProxy(
            bean.getClass(), beanName, specificInterceptors, new SingletonTargetSource(bean));
        this.proxyTypes.put(cacheKey, proxy.getClass());
        return proxy;
    }

    this.advisedBeans.put(cacheKey, Boolean.FALSE);
    return bean;
}
```

当然这是父类提供的方法，那子类继承之后重写此方法，完成了定制化的效果，定义不同的代理对象

```java
@Override
protected Object wrapIfNecessary（奈色色瑞）(Object bean, String beanName, Object cacheKey) {
    try {
        // 加锁防止并发
        synchronized (PROXYED_SET) {
            if (PROXYED_SET.contains(beanName)) {
                return bean;
            }
            interceptor = null;
            //check TCC proxy
            // 检查是否是TCC模式
            if (TCCBeanParserUtils.isTccAutoProxy(bean, beanName, applicationContext)) {
                //TCC interceptor, proxy bean of sofa:reference/dubbo:reference, and LocalTCC
                // 如果是：添加TCC拦截器
                interceptor = new TccActionInterceptor(TCCBeanParserUtils.getRemotingDesc(beanName));
                ConfigurationCache.addConfigListener(ConfigurationKeys.DISABLE_GLOBAL_TRANSACTION,
                                                     (ConfigurationChangeListener)interceptor);
            } else {
                // 不是TCC模式
                Class<?> serviceInterface = SpringProxyUtils.findTargetClass(bean);
                Class<?>[] interfacesIfJdk = SpringProxyUtils.findInterfaces(bean);

                // 判断是否有相关事务注解，如果没有就不代理
                if (!existsAnnotation(new Class[]{serviceInterface})
                    && !existsAnnotation(interfacesIfJdk)) {
                    return bean;
                }

                // 当发现存在全局事务注解标注的Bean，添加拦截器
                if (globalTransactionalInterceptor == null) {
                    // 添加拦截器
                    globalTransactionalInterceptor = new GlobalTransactionalInterceptor(failureHandlerHook);
                    ConfigurationCache.addConfigListener(
                        ConfigurationKeys.DISABLE_GLOBAL_TRANSACTION,
                        (ConfigurationChangeListener)globalTransactionalInterceptor);
                }
                interceptor = globalTransactionalInterceptor;
            }

            LOGGER.info("Bean[{}] with name [{}] would use interceptor [{}]", bean.getClass().getName(), beanName, interceptor.getClass().getName());
            // 检查是否是代理对象
            if (!AopUtils.isAopProxy(bean)) {
                // 不是调用Spring代理（父级）
                bean = super.wrapIfNecessary(bean, beanName, cacheKey);
            } else {
                // 已经是代理对象，反射获取代理类中的已经存在的拦截器组合，然后添加到该集合当中
                AdvisedSupport advised = SpringProxyUtils.getAdvisedSupport(bean);
                Advisor[] advisor = buildAdvisors(beanName, getAdvicesAndAdvisorsForBean(null, null, null));
                for (Advisor avr : advisor) {
                    advised.addAdvisor(0, avr);
                }
            }
            // 将Bean添加到Set中
            PROXYED_SET.add(beanName);
            return bean;
        }
    } catch (Exception exx) {
        throw new RuntimeException(exx);
    }
}
```

### 2.3 Seata源码分析-2PC核心源码解读

#### 2.3.1 2PC提交源码流程

上节课我们分析到了GlobalTransactionalInterceptor全局事务拦截器，一旦执行拦截器，我们就会进入到其中的invoke方法，在这其中会做一些@GlobalTransactional注解的判断，如果有注解以后，会执行全局事务和全局锁，那么在执行全局事务的时候会调用handleGlobalTransaction全局事务处理器，这里主要是获取事务信息

```java
Object handleGlobalTransaction(final MethodInvocation methodInvocation,
                               final GlobalTransactional globalTrxAnno) throws Throwable {
    boolean succeed = true;
    try {
        return transactionalTemplate.execute(new TransactionalExecutor() {
            @Override
            public Object execute() throws Throwable {
                return methodInvocation.proceed();
            }

            // 获取事务名称，默认获取方法名
            public String name() {
                String name = globalTrxAnno.name();
                if (!StringUtils.isNullOrEmpty(name)) {
                    return name;
                }
                return formatMethod(methodInvocation.getMethod());
            }

            /**
                 * 解析GlobalTransactional注解属性，封装为对象
                 * @return
                 */
            @Override
            public TransactionInfo getTransactionInfo() {
                // reset the value of timeout
                // 获取超时时间，默认60秒
                int timeout = globalTrxAnno.timeoutMills();
                if (timeout <= 0 || timeout == DEFAULT_GLOBAL_TRANSACTION_TIMEOUT) {
                    timeout = defaultGlobalTransactionTimeout;
                }

                // 构建事务信息对象
                TransactionInfo transactionInfo = new TransactionInfo();
                transactionInfo.setTimeOut(timeout);// 超时时间
                transactionInfo.setName(name()); // 事务名称
                transactionInfo.setPropagation(globalTrxAnno.propagation());// 事务传播
                transactionInfo.setLockRetryInternal(globalTrxAnno.lockRetryInternal());// 校验或占用全局锁重试间隔
                transactionInfo.setLockRetryTimes(globalTrxAnno.lockRetryTimes());// 校验或占用全局锁重试次数
                Set<RollbackRule> rollbackRules = new LinkedHashSet<>();
                // 其他构建信息
                for (Class<?> rbRule : globalTrxAnno.rollbackFor()) {
                    rollbackRules.add(new RollbackRule(rbRule));
                }
                for (String rbRule : globalTrxAnno.rollbackForClassName()) {
                    rollbackRules.add(new RollbackRule(rbRule));
                }
                for (Class<?> rbRule : globalTrxAnno.noRollbackFor()) {
                    rollbackRules.add(new NoRollbackRule(rbRule));
                }
                for (String rbRule : globalTrxAnno.noRollbackForClassName()) {
                    rollbackRules.add(new NoRollbackRule(rbRule));
                }
                transactionInfo.setRollbackRules(rollbackRules);
                return transactionInfo;
            }
        });
    } catch (TransactionalExecutor.ExecutionException e) {
        // 执行异常
        TransactionalExecutor.Code code = e.getCode();
        switch (code) {
            case RollbackDone:
                throw e.getOriginalException();
            case BeginFailure:
                succeed = false;
                failureHandler.onBeginFailure(e.getTransaction(), e.getCause());
                throw e.getCause();
            case CommitFailure:
                succeed = false;
                failureHandler.onCommitFailure(e.getTransaction(), e.getCause());
                throw e.getCause();
            case RollbackFailure:
                failureHandler.onRollbackFailure(e.getTransaction(), e.getOriginalException());
                throw e.getOriginalException();
            case RollbackRetrying:
                failureHandler.onRollbackRetrying(e.getTransaction(), e.getOriginalException());
                throw e.getOriginalException();
            default:
                throw new ShouldNeverHappenException(String.format("Unknown TransactionalExecutor.Code: %s", code));
        }
    } finally {
        if (degradeCheck) {
            EVENT_BUS.post(new DegradeCheckEvent(succeed));
        }
    }
}
```

在这其中，我们要关注一个重点方法execute()

其实这个方法主要的作用就是，执行事务的流程，大概一下几点：

1\. 获取事务信息
2\. 开始执行全局事务
3\. 发生异常全局回滚，各个数据通过undo_log表进行事务补偿
4\. 全局事务提交
5\. 清除所有资源

这个位置是非常核心的一个位置，因为我们所有的业务进来以后都会走这个位置。

这其中的第三步和第四步就是在想TC（Seata-Server）发起全局事务的提交/回滚

```java
public Object execute(TransactionalExecutor business) throws Throwable {
    // 1. Get transactionInfo
    // 获取事务信息
    TransactionInfo txInfo = business.getTransactionInfo();
    if (txInfo == null) {
        throw new ShouldNeverHappenException("transactionInfo does not exist");
    }
    // 1.1 Get current transaction, if not null, the tx role is 'GlobalTransactionRole.Participant'.
    // 获取当前事务，主要获取Xid
    GlobalTransaction tx = GlobalTransactionContext.getCurrent();

    // 1.2 Handle the transaction propagation.
    // 根据配置的不同事务传播行为，执行不同的逻辑
    Propagation propagation = txInfo.getPropagation();
    SuspendedResourcesHolder suspendedResourcesHolder = null;
    try {
        switch (propagation) {
            case NOT_SUPPORTED:
                // If transaction is existing, suspend it.
                if (existingTransaction(tx)) {
                    suspendedResourcesHolder = tx.suspend();
                }
                // Execute without transaction and return.
                return business.execute();
            case REQUIRES_NEW:
                // If transaction is existing, suspend it, and then begin new transaction.
                if (existingTransaction(tx)) {
                    suspendedResourcesHolder = tx.suspend();
                    tx = GlobalTransactionContext.createNew();
                }
                // Continue and execute with new transaction
                break;
            case SUPPORTS:
                // If transaction is not existing, execute without transaction.
                if (notExistingTransaction(tx)) {
                    return business.execute();
                }
                // Continue and execute with new transaction
                break;
            case REQUIRED:
                // If current transaction is existing, execute with current transaction,
                // else continue and execute with new transaction.
                break;
            case NEVER:
                // If transaction is existing, throw exception.
                if (existingTransaction(tx)) {
                    throw new TransactionException(
                        String.format("Existing transaction found for transaction marked with propagation 'never', xid = %s"
                                      , tx.getXid()));
                } else {
                    // Execute without transaction and return.
                    return business.execute();
                }
            case MANDATORY:
                // If transaction is not existing, throw exception.
                if (notExistingTransaction(tx)) {
                    throw new TransactionException("No existing transaction found for transaction marked with propagation 'mandatory'");
                }
                // Continue and execute with current transaction.
                break;
            default:
                throw new TransactionException("Not Supported Propagation:" + propagation);
        }

        // 1.3 If null, create new transaction with role 'GlobalTransactionRole.Launcher'.
        // 当前没有事务，则创建一个新的事务
        if (tx == null) {
            tx = GlobalTransactionContext.createNew();
        }

        // set current tx config to holder
        GlobalLockConfig previousConfig = replaceGlobalLockConfig(txInfo);

        try {
            // 2. If the tx role is 'GlobalTransactionRole.Launcher', send the request of beginTransaction to TC,
            //    else do nothing. Of course, the hooks will still be triggered.
            // 开始执行全局事务
            beginTransaction(txInfo, tx);

            Object rs;
            try {
                // Do Your Business
                // 执行当前业务逻辑：
                // 1. 在TC注册当前分支事务，TC会在branch_table中插入一条分支事务数据
                // 2. 执行本地update语句，并在执行前后查询数据状态，并把数据前后镜像存入到undo_log表中
                // 3. 远程调用其他应用，远程应用接收到xid，也会注册分支事务，写入branch_table及本地undo_log表
                // 4. 会在lock_table表中插入全局锁数据（一个分支一条）
                rs = business.execute();
            } catch (Throwable ex) {
                // 3. The needed business exception to rollback.
                // 发生异常全局回滚，各个数据通过undo_log表进行事务补偿
                completeTransactionAfterThrowing(txInfo, tx, ex);
                throw ex;
            }

            // 4. everything is fine, commit.
            // 全局提交事务
            commitTransaction(tx);

            return rs;
        } finally {
            //5. clear
            // 清除所有资源
            resumeGlobalLockConfig(previousConfig);
            triggerAfterCompletion();
            cleanUp();
        }
    } finally {
        // If the transaction is suspended, resume it.
        if (suspendedResourcesHolder != null) {
            tx.resume(suspendedResourcesHolder);
        }
    }
}
```

#### 2.3.2 如何发起全局事务

这个位置我们就看当前这个代码中的 beginTransaction(txInfo, tx);方法

```java
// 想TC发起请求，这里采用了模板模式
private void beginTransaction(TransactionInfo txInfo, GlobalTransaction tx) throws TransactionalExecutor.ExecutionException {
    try {
        triggerBeforeBegin();
        // 对TC发起请求
        tx.begin(txInfo.getTimeOut(), txInfo.getName());
        triggerAfterBegin();
    } catch (TransactionException txe) {
        throw new TransactionalExecutor.ExecutionException(tx, txe,
                                                           TransactionalExecutor.Code.BeginFailure);
    }
}	
```

那我们向下来看begin方法，那要注意，这里调用begin方法的是DefaultGlobalTransaction

```java
@Override
public void begin(int timeout, String name) throws TransactionException {
    //判断调用者是否是TM
    if (role != GlobalTransactionRole.Launcher) {
        assertXIDNotNull();
        if (LOGGER.isDebugEnabled()) {
            LOGGER.debug("Ignore Begin(): just involved in global transaction [{}]", xid);
        }
        return;
    }
    assertXIDNull();
    String currentXid = RootContext.getXID();
    if (currentXid != null) {
        throw new IllegalStateException("Global transaction already exists," +
                                        " can't begin a new global transaction, currentXid = " + currentXid);
    }
    // 获取Xid
    xid = transactionManager.begin(null, null, name, timeout);
    status = GlobalStatus.Begin;
    RootContext.bind(xid);
    if (LOGGER.isInfoEnabled()) {
        LOGGER.info("Begin new global transaction [{}]", xid);
    }
}
```

在向下来看begin方法，这时候使用的是(默认事务管理者)DefaultTransactionManager.begin，来真正的获取xid，其中就是传入事务的相关信息，最终TC端返回对应的全局事务Xid。

```java
@Override
public String begin(String applicationId, String transactionServiceGroup, String name, int timeout)
    throws TransactionException {
    GlobalBeginRequest request = new GlobalBeginRequest();
    request.setTransactionName(name);
    request.setTimeout(timeout);
    // 发送请求得到响应
    GlobalBeginResponse response = (GlobalBeginResponse) syncCall(request);
    if (response.getResultCode() == ResultCode.Failed) {
        throw new TmTransactionException(TransactionExceptionCode.BeginFailed, response.getMsg());
    }
    //返回Xid
    return response.getXid();
}
```

这里采用的是Netty的通讯方式

```java
private AbstractTransactionResponse syncCall(AbstractTransactionRequest request) throws TransactionException {
    try {
        // 通过Netty发送请求
        return (AbstractTransactionResponse) TmNettyRemotingClient.getInstance().sendSyncRequest(request);
    } catch (TimeoutException toe) {
        throw new TmTransactionException(TransactionExceptionCode.IO, "RPC timeout", toe);
    }
}
```

图解地址：https://www.processon.com/view/link/6213d58f1e0853078013c58f

### 2.4 Seata源码分析-数据源代理

上节课我们分析了整体的Seata-AT模式的2PC执行流程，那么这节课我们要分析的就是在AT模式中的关键点，数据源代理



#### 2.4.1 AT模式的核心点：

1\. 获取全局锁、开启全局事务
2\. 解析SQL并写入undolog

那么上节课其实我们已经把第一步分析清楚了，那么这节课我们就要分析的是AT模式如何解析SQL并写入undolog，首先我们要先明确实际上Seata其中采用了数据源代理的模式。

那么这个就需要我们在回顾一下GlobalTransactionScanner这个类型，在这个类型中继承了一些的接口和抽象类，比较关键的几个：

1\. AbstractAutoProxyCreator
2\. InitializingBean
3\. ApplicationContextAware
4\. DisposableBean

这里给大家回顾一下：

1\. 继承ApplicationContextAware类型以后，需要实现对应的方法:

   void setApplicationContext(ApplicationContext applicationContext) throws BeansException

   当spring启动完成后，会自动调用这个类型，把ApplicationContext给bean。也就是说，GlobalTransactionScanner天然能拿到Spring的环境。

2\. 继承了InitializingBean接口，需要实现一个方法：

   void afterPropertiesSet() throws Exception;

   凡是继承该接口的类，在初始化bean的时候，当所有properties都设置完成后，会执行该方法。

3\. 继承DisposableBean，需要实现一个方法：

   void destroy() throws Exception;

   和InitializingBean接口相反，这个是在销毁的时候会调用这个方法。

4\. AbstractAutoProxyCreator就比较复杂了，它Spring实现AOP的一种方式。本质上是一个BeanPostProcessor，他在bean初始化之前，调用内部的createProxy方法，创建一个bean的AOP代理bean并返回，对Bean的增强。

总结一下：总体的逻辑就是，GlobalTransactionScanner扫描有注解的bean，做AOP增强。



#### 2.4.2 数据源代理

关于数据源代理这里我们全局事务拦截成功后最终还是执行了业务方法的，但是由于Seata对数据源做了代理，所以sql解析与undolog入库操作是在数据源代理中执行的，箭头处的代理就是Seata对DataSource，Connection，Statement做的代理封装类

![image-20220226142501746](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ac1dc2fc-fa91-5591-8720-0c066046e821.png)

数据源代理是非常重要的一个环节。我们知道，在分布式事务运行过程中，undo log等的记录、资源的锁定等，都是用户无感知的，因为这些操作都在数据源的代理中完成了。

#### 2.4.3 数据源代理DataSourceProxy

DataSourceProxy的主要功能为，它在构造方法中调用了一个自定义的init方法，主要做了以下能力的增强：

1\. 为每个数据源标识了资源组ID

2\. 如果配置打开，会有一个定时线程池定时更新表的元数据信息并缓存到本地

3\. 生成代理连接ConnectionProxy

那我们先来看init方法

```java
private void init(DataSource dataSource, String resourceGroupId) {
    //资源组ID，默认是“default”这个默认值
    this.resourceGroupId = resourceGroupId;
    try (Connection connection = dataSource.getConnection()) {
        //根据原始数据源得到JDBC连接和数据库类型
        jdbcUrl = connection.getMetaData().getURL();
        dbType = JdbcUtils.getDbType(jdbcUrl);
        if (JdbcConstants.ORACLE.equals(dbType)) {
            userName = connection.getMetaData().getUserName();
        }
    } catch (SQLException e) {
        throw new IllegalStateException("can not init dataSource", e);
    }
    DefaultResourceManager.get().registerResource(this);
    if (ENABLE_TABLE_META_CHECKER_ENABLE) {
        //如果配置开关打开，会定时线程池不断更新表的元数据信息
        /**
        *每分钟查询一次数据源的表结构信息并缓存，在需要查询数据库结构时会用到，不然每次去数据库查询结构效率会很低。
        */
        tableMetaExcutor.scheduleAtFixedRate(() -> {
            try (Connection connection = dataSource.getConnection()) {
                TableMetaCacheFactory.getTableMetaCache(DataSourceProxy.this.getDbType())
                    .refresh(connection, DataSourceProxy.this.getResourceId());
            } catch (Exception ignore) {
            }
        }, 0, TABLE_META_CHECKER_INTERVAL, TimeUnit.MILLISECONDS);
    }

    //Set the default branch type to 'AT' in the RootContext.
    RootContext.setDefaultBranchType(this.getBranchType());
}
```

这3个增强里面，前两个都比较容易理解，第三是最重要的。我们知道在AT模式里面，会自动记录[undo](https://so.csdn.net/so/search?q=undo&spm=1001.2101.3001.7020) log、资源锁定等等，都是通过ConnectionProxy完成的。

另外，DataSourceProxy重写了几个方法。

重点是getConnection，此时会返回一个ConnectionProxy，而不是原生的Connection

```java
@Override
public ConnectionProxy getConnection() throws SQLException {
    Connection targetConnection = targetDataSource.getConnection();
    return new ConnectionProxy(this, targetConnection);
}

@Override
public ConnectionProxy getConnection(String username, String password) throws SQLException {
    Connection targetConnection = targetDataSource.getConnection(username, password);
    return new ConnectionProxy(this, targetConnection);
}
```

#### 2.4.4 ConnectionProxy分析

ConnectionProxy继承了AbstractConnectionProxy。一看到Abstract，就知道它的父类封装了很多通用工作。它的父类里面还使用了PreparedStatementProxy、StatementProxy、DataSourceProxy。

![image-20220226172114629](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6958ceaa-7de8-5e2e-8f86-2f462083e2e7.png)

我们先来分析AbstractConnectionProxy

**AbstractConnectionProxy**

在这个抽象连接对象中，定义了很多通用的逻辑，所以在这其中我们要关注的主要在于PreparedStatementProxy和StatementProxy，其实这里的通用逻辑就是数据源连接的步骤，获取连接，创建执行对象等等这些

```java
@Override
public Statement createStatement() throws SQLException {
    //调用真实连接对象获得Statement对象
    Statement targetStatement = getTargetConnection().createStatement();
    //创建Statement的代理
    return new StatementProxy(this, targetStatement);
}

@Override
public PreparedStatement prepareStatement(String sql) throws SQLException {
    //数据库类型，比如mysql、oracle等
    String dbType = getDbType();
    // support oracle 10.2+
    PreparedStatement targetPreparedStatement = null;
    //如果是AT模式且开启全局事务，那么就会进入if分支
    if (BranchType.AT == RootContext.getBranchType()) {
        List<SQLRecognizer> sqlRecognizers = SQLVisitorFactory.get(sql, dbType);
        if (sqlRecognizers != null && sqlRecognizers.size() == 1) {
            SQLRecognizer sqlRecognizer = sqlRecognizers.get(0);
            if (sqlRecognizer != null && sqlRecognizer.getSQLType() == SQLType.INSERT) {
                //得到表的元数据
                TableMeta tableMeta = TableMetaCacheFactory.getTableMetaCache(dbType).getTableMeta(getTargetConnection(),
                                                                                                   sqlRecognizer.getTableName(), getDataSourceProxy().getResourceId());
                //得到表的主键列名
                String[] pkNameArray = new String[tableMeta.getPrimaryKeyOnlyName().size()];
                tableMeta.getPrimaryKeyOnlyName().toArray(pkNameArray);
                targetPreparedStatement = getTargetConnection().prepareStatement(sql,pkNameArray);
            }
        }
    }
    if (targetPreparedStatement == null) {
        targetPreparedStatement = getTargetConnection().prepareStatement(sql);
    }
    // 创建PreparedStatementProxy代理
    return new PreparedStatementProxy(this, targetPreparedStatement, sql);
}
```

**分布式事务SQL执行**

在这两个代理对象中，执行SQL语句的关键方法如下：

```java
@Override
public ResultSet executeQuery(String sql) throws SQLException {
    this.targetSQL = sql;
    return ExecuteTemplate.execute(this, (statement, args) -> statement.executeQuery((String) args[0]), sql);
}

@Override
public int executeUpdate(String sql) throws SQLException {
    this.targetSQL = sql;
    return ExecuteTemplate.execute(this, (statement, args) -> statement.executeUpdate((String) args[0]), sql);
}

@Override
public boolean execute(String sql) throws SQLException {
    this.targetSQL = sql;
    return ExecuteTemplate.execute(this, (statement, args) -> statement.execute((String) args[0]), sql);
}
```

其他执行SQL语句的方法与上面三个方法都是类似的，都是调用ExecuteTemplate.execute方法，下面来看一下ExecuteTemplate类：

```java
/**
 * The type Execute template.
 *
 * @author sharajava
 */
public class ExecuteTemplate {

    /**
     * Execute t.
     *
     * @param <T>               the type parameter
     * @param <S>               the type parameter
     * @param statementProxy    the statement proxy
     * @param statementCallback the statement callback
     * @param args              the args
     * @return the t
     * @throws SQLException the sql exception
     */
    public static <T, S extends Statement> T execute(StatementProxy<S> statementProxy,
                                                     StatementCallback<T, S> statementCallback,
                                                     Object... args) throws SQLException {
        return execute(null, statementProxy, statementCallback, args);
    }

    /**
     * Execute t.
     *
     * @param <T>               the type parameter
     * @param <S>               the type parameter
     * @param sqlRecognizers    the sql recognizer list
     * @param statementProxy    the statement proxy
     * @param statementCallback the statement callback
     * @param args              the args
     * @return the t
     * @throws SQLException the sql exception
     */
    public static <T, S extends Statement> T execute(List<SQLRecognizer> sqlRecognizers,
                                                     StatementProxy<S> statementProxy,
                                                     StatementCallback<T, S> statementCallback,
                                                     Object... args) throws SQLException {
        // 如果没有全局锁，并且不是AT模式，直接执行SQL
        if (!RootContext.requireGlobalLock() && BranchType.AT != RootContext.getBranchType()) {
            // Just work as original statement
            return statementCallback.execute(statementProxy.getTargetStatement(), args);
        }

        // 得到数据库类型 ->MySQL
        String dbType = statementProxy.getConnectionProxy().getDbType();
        if (CollectionUtils.isEmpty(sqlRecognizers)) {
            //sqlRecognizers为SQL语句的解析器，获取执行的SQL，通过它可以获得SQL语句表名、相关的列名、类型的等信息，最后解析出对应的SQL表达式
            sqlRecognizers = SQLVisitorFactory.get(
                    statementProxy.getTargetSQL(),
                    dbType);
        }
        Executor<T> executor;
        if (CollectionUtils.isEmpty(sqlRecognizers)) {
            //如果seata没有找到合适的SQL语句解析器，那么便创建简单执行器PlainExecutor，
            //PlainExecutor直接使用原生的Statement对象执行SQL
            executor = new PlainExecutor<>(statementProxy, statementCallback);
        } else {
            if (sqlRecognizers.size() == 1) {
                SQLRecognizer sqlRecognizer = sqlRecognizers.get(0);
                switch (sqlRecognizer.getSQLType()) {
                    //下面根据是增、删、改、加锁查询、普通查询分别创建对应的处理器
                    case INSERT:
                        executor = EnhancedServiceLoader.load(InsertExecutor.class, dbType,
                                new Class[]{StatementProxy.class, StatementCallback.class, SQLRecognizer.class},
                                new Object[]{statementProxy, statementCallback, sqlRecognizer});
                        break;
                    case UPDATE:
                        executor = new UpdateExecutor<>(statementProxy, statementCallback, sqlRecognizer);
                        break;
                    case DELETE:
                        executor = new DeleteExecutor<>(statementProxy, statementCallback, sqlRecognizer);
                        break;
                    case SELECT_FOR_UPDATE:
                        executor = new SelectForUpdateExecutor<>(statementProxy, statementCallback, sqlRecognizer);
                        break;
                    default:
                        executor = new PlainExecutor<>(statementProxy, statementCallback);
                        break;
                }
            } else {
                // 此执行器可以处理一条SQL语句包含多个Delete、Update语句
                executor = new MultiExecutor<>(statementProxy, statementCallback, sqlRecognizers);
            }
        }
        T rs;
        try {
            // 执行器执行
            rs = executor.execute(args);
        } catch (Throwable ex) {
            if (!(ex instanceof SQLException)) {
                // Turn other exception into SQLException
                ex = new SQLException(ex);
            }
            throw (SQLException) ex;
        }
        return rs;
    }

}

```

从ExecuteTemplate中可以看到，seata将SQL语句的执行委托给了不同的执行器。seata提供了6个执行器（模板模式），所有执行器的父类型为AbstractDMLBaseExecutor。

1\. UpdateExecutor	执行update语句
2\. InsertExecutor	执行insert语句
3\. DeleteExecutor	执行delete语句
4\. SelectForUpdateExecutor	执行select for update语句
5\. PlainExecutor	执行普通查询语句
6\. MultiExecutor	复合执行器，在一条SQL语句中执行多条语句

关系结构图

![image-20220314203616110](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a45534dc-88de-5b56-a44f-e93eb16be683.png)

那我们继续向下看，这里我们要分析的就是executor.execute(args);方法，自然这里调用的就是父类的方法

```java
@Override
public T execute(Object... args) throws Throwable {
    String xid = RootContext.getXID();
    if (xid != null) {
        // 获取xid
        statementProxy.getConnectionProxy().bind(xid);
    }
    // 设置全局锁
    statementProxy.getConnectionProxy().setGlobalLockRequire(RootContext.requireGlobalLock());
    return doExecute(args);
}
```

向下来看doExecute()方法，AbstractDMLBaseExecutor重写的方法

```java
@Override
public T doExecute(Object... args) throws Throwable {
    AbstractConnectionProxy connectionProxy = statementProxy.getConnectionProxy();
    if (connectionProxy.getAutoCommit()) {
        return executeAutoCommitTrue(args);
    } else {
        return executeAutoCommitFalse(args);
    }
}
```

首先我们都清楚，数据库本身都是自动提交

```java
@Override
public T doExecute(Object... args) throws Throwable {
    AbstractConnectionProxy connectionProxy = statementProxy.getConnectionProxy();
    if (connectionProxy.getAutoCommit()) {
        return executeAutoCommitTrue(args);
    } else {
        return executeAutoCommitFalse(args);
    }
}
```

进入executeAutoCommitTrue()方法中

```java
protected T executeAutoCommitTrue(Object[] args) throws Throwable {
    ConnectionProxy connectionProxy = statementProxy.getConnectionProxy();
    try {
        // 更改为手动提交
        connectionProxy.changeAutoCommit();
        return new LockRetryPolicy(connectionProxy).execute(() -> {
            // 调用手动提交方法 得到分支业务最终结果
            T result = executeAutoCommitFalse(args);
            // 执行提交
            connectionProxy.commit();
            return result;
        });
    } catch (Exception e) {
        // when exception occur in finally,this exception will lost, so just print it here
        LOGGER.error("execute executeAutoCommitTrue error:{}", e.getMessage(), e);
        if (!LockRetryPolicy.isLockRetryPolicyBranchRollbackOnConflict()) {
            connectionProxy.getTargetConnection().rollback();
        }
        throw e;
    } finally {
        connectionProxy.getContext().reset();
        connectionProxy.setAutoCommit(true);
    }
}
```

然后我们查看connectionProxy.changeAutoCommit();更改为手动提交

```java
protected T executeAutoCommitFalse(Object[] args) throws Exception {
    if (!JdbcConstants.MYSQL.equalsIgnoreCase(getDbType()) && isMultiPk()) {
        throw new NotSupportYetException("multi pk only support mysql!");
    }
    // 前镜像
    TableRecords beforeImage = beforeImage();
    // 执行具体业余
    T result = statementCallback.execute(statementProxy.getTargetStatement(), args);
    // 后镜像
    TableRecords afterImage = afterImage(beforeImage);
    // 暂存UndoLog，为了在Commit的时候保存到数据库
    prepareUndoLog(beforeImage, afterImage);
    return result;
}
```

然后我们再回到executeAutoCommitTrue这个方法中向下看connectionProxy.commit();

```java
@Override
public void commit() throws SQLException {
    try {
        LOCK_RETRY_POLICY.execute(() -> {
            // 具体执行
            doCommit();
            return null;
        });
    } catch (SQLException e) {
        if (targetConnection != null && !getAutoCommit() && !getContext().isAutoCommitChanged()) {
            rollback();
        }
        throw e;
    } catch (Exception e) {
        throw new SQLException(e);
    }
}
```

进入到doCommit方法中

```java
private void doCommit() throws SQLException {
    //判断是否存在全局事务
    if (context.inGlobalTransaction()) {
        processGlobalTransactionCommit();
    } else if (context.isGlobalLockRequire()) {
        processLocalCommitWithGlobalLocks();
    } else {
        targetConnection.commit();
    }
}
```

此时很明显我们存在全局事务，所以进入到processGlobalTransactionCommit方法中

```java
private void processGlobalTransactionCommit() throws SQLException {
    try {
        // 注册分支
        register();
    } catch (TransactionException e) {
        recognizeLockKeyConflictException(e, context.buildLockKeys());
    }
    try {
        //写入数据库undolog
        UndoLogManagerFactory.getUndoLogManager(this.getDbType()).flushUndoLogs(this);
        //执行原生提交
        targetConnection.commit();
    } catch (Throwable ex) {
        LOGGER.error("process connectionProxy commit error: {}", ex.getMessage(), ex);
        report(false);
        throw new SQLException(ex);
    }
    if (IS_REPORT_SUCCESS_ENABLE) {
        report(true);
    }
    context.reset();
}
```

其中的register方法就是注册分支事务的方法，同时还有把undolog写入数据库和执行提交的操作

```java
// 注册分支事务，生成分支事务id
private void register() throws TransactionException {
    if (!context.hasUndoLog() || !context.hasLockKey()) {
        return;
    }
    // 注册分支事务
    Long branchId = DefaultResourceManager.get().branchRegister(BranchType.AT, getDataSourceProxy().getResourceId(),
                                                                null, context.getXid(), null, context.buildLockKeys());
    context.setBranchId(branchId);
}
```

接下来我们就具体看看写入数据库的方法flushUndoLogs

```java
@Override
public void flushUndoLogs(ConnectionProxy cp) throws SQLException {
    ConnectionContext connectionContext = cp.getContext();
    if (!connectionContext.hasUndoLog()) {
        return;
    }

    String xid = connectionContext.getXid();
    long branchId = connectionContext.getBranchId();

    BranchUndoLog branchUndoLog = new BranchUndoLog();
    branchUndoLog.setXid(xid);
    branchUndoLog.setBranchId(branchId);
    branchUndoLog.setSqlUndoLogs(connectionContext.getUndoItems());

    UndoLogParser parser = UndoLogParserFactory.getInstance();
    byte[] undoLogContent = parser.encode(branchUndoLog);

    CompressorType compressorType = CompressorType.NONE;
    if (needCompress(undoLogContent)) {
        compressorType = ROLLBACK_INFO_COMPRESS_TYPE;
        undoLogContent = CompressorFactory.getCompressor(compressorType.getCode()).compress(undoLogContent);
    }

    if (LOGGER.isDebugEnabled()) {
        LOGGER.debug("Flushing UNDO LOG: {}", new String(undoLogContent, Constants.DEFAULT_CHARSET));
    }
    // 写入数据库具体位置
    insertUndoLogWithNormal(xid, branchId, buildContext(parser.getName(), compressorType), undoLogContent, cp.getTargetConnection());
}
```

具体写入方法，此时我们使用的是MySql，所以执行的是MySql实现类

```java
@Override
protected void insertUndoLogWithNormal(String xid, long branchId, String rollbackCtx, byte[] undoLogContent,
                                       Connection conn) throws SQLException {
    insertUndoLog(xid, branchId, rollbackCtx, undoLogContent, State.Normal, conn);
}

@Override
protected void insertUndoLogWithGlobalFinished(String xid, long branchId, UndoLogParser parser, Connection conn) throws SQLException {
    insertUndoLog(xid, branchId, buildContext(parser.getName(), CompressorType.NONE), parser.getDefaultContent(), State.GlobalFinished, conn);
}

// 具体写入
private void insertUndoLog(String xid, long branchId, String rollbackCtx, byte[] undoLogContent,
                           State state, Connection conn) throws SQLException {
    try (PreparedStatement pst = conn.prepareStatement(INSERT_UNDO_LOG_SQL)) {
        pst.setLong(1, branchId);
        pst.setString(2, xid);
        pst.setString(3, rollbackCtx);
        pst.setBytes(4, undoLogContent);
        pst.setInt(5, state.getValue());
        pst.executeUpdate();
    } catch (Exception e) {
        if (!(e instanceof SQLException)) {
            e = new SQLException(e);
        }
        throw (SQLException) e;
    }
}
```

### 2.5 Seata源码分析- Seata服务端（TC）源码解读

上节课我们已经分析到了SQL语句最终的执行器，但是再往下分析之前，我们需要先来分析一下TM客户端与TC端通讯以后，TC端的具体操作

#### 2.5.1 服务端表解释

我们的Seata服务端在应用的时候需要准备三张表，那么这三张表分别代表的意思就是

1\. branch_table 分支事务表
2\. global_table 全局事务表
3\. lock_table 全局锁表

客户端请求服务端以后，我们就需要把对应的全局事务包括分支事务和全局锁全部存放到这里。

![image-20220311185545970](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f2118d25-5b4c-5b4c-b138-11ca8763ef89.png)

#### 2.5.2 TC服务端启动入口

那么我们任何的Java工程启动都需要主函数main，所以我们就从这里入手，首先在seata源码工程中搜索这个入口

![image-20220311175232256](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ed7d06f1-2fce-59f0-b041-b9879a8505e0.png)

这里我们看Server.java这里就是启动入口，在这个入口中找到协调者，因为TC整体的操作就是协调整体的全局事务

```java
// 协调协调者
DefaultCoordinator coordinator = new DefaultCoordinator(nettyRemotingServer);
```

#### 2.5.3 全局事务开始方法分析

然后进入到其中我们可以看到很多的全局事务处理的方法

```java
// 处理全局事务开始
@Override
protected void doGlobalBegin(GlobalBeginRequest request, GlobalBeginResponse response, RpcContext rpcContext)
    throws TransactionException {
    // 响应客户端XID
    response.setXid(core.begin(rpcContext.getApplicationId(), rpcContext.getTransactionServiceGroup(),
                               request.getTransactionName(), request.getTimeout()));
    if (LOGGER.isInfoEnabled()) {
        LOGGER.info("Begin new global transaction applicationId: {},transactionServiceGroup: {}, transactionName: {},timeout:{},xid:{}",
                    rpcContext.getApplicationId(), rpcContext.getTransactionServiceGroup(), request.getTransactionName(), request.getTimeout(), response.getXid());
    }
}

// 处理全局提交
@Override
protected void doGlobalCommit(GlobalCommitRequest request, GlobalCommitResponse response, RpcContext rpcContext)
    throws TransactionException {
    MDC.put(RootContext.MDC_KEY_XID, request.getXid());
    response.setGlobalStatus(core.commit(request.getXid()));
}

// 处理全局回滚
@Override
protected void doGlobalRollback(GlobalRollbackRequest request, GlobalRollbackResponse response,
                                RpcContext rpcContext) throws TransactionException {
    MDC.put(RootContext.MDC_KEY_XID, request.getXid());
    response.setGlobalStatus(core.rollback(request.getXid()));
}
.....
```

在这其中我们首先关注doGlobalBegin方法中的core.begin()方法，来看一下具体操作

```java
@Override
public String begin(String applicationId, String transactionServiceGroup, String name, int timeout)
    throws TransactionException {
    // 创建全局事务Session
    GlobalSession session = GlobalSession.createGlobalSession(applicationId, transactionServiceGroup, name,
                                                              timeout);
    MDC.put(RootContext.MDC_KEY_XID, session.getXid());

    // 为Session中添加回调监听 SessionHolder.getRootSessionManager()去获取一个全局Session管理器DataBaseSessionManager
    // 观察者设计模式
    session.addSessionLifecycleListener(SessionHolder.getRootSessionManager());

    // 全局事务开启
    session.begin();

    // transaction start event
    eventBus.post(new GlobalTransactionEvent(session.getTransactionId(), GlobalTransactionEvent.ROLE_TC,
                                             session.getTransactionName(), applicationId, transactionServiceGroup, session.getBeginTime(), null, session.getStatus()));

    return session.getXid();
}
```

在向下我们要关注一下全局Session管理器DataBaseSessionManager，进入到getRootSessionManager()方法中

```java
/**
* Gets root session manager.
* 获取一个全局Session管理器
* @return the root session manager
*/
public static SessionManager getRootSessionManager() {
    if (ROOT_SESSION_MANAGER == null) {
        throw new ShouldNeverHappenException("SessionManager is NOT init!");
    }
    return ROOT_SESSION_MANAGER;
}
```

这个管理器如何生成的那，我们可以看一下init初始化方法

```java
public static void init(String mode) {
    if (StringUtils.isBlank(mode)) {
        mode = CONFIG.getConfig(ConfigurationKeys.STORE_MODE);
    }
    // 判断Seata模式，当前为DB
    StoreMode storeMode = StoreMode.get(mode);
    if (StoreMode.DB.equals(storeMode)) {
        // 通过SPI机制读取SessionManager接口实现类，读取的是META-INF.service目录，在通过反射机制创建对象DataBaseSessionManager
        ROOT_SESSION_MANAGER = EnhancedServiceLoader.load(SessionManager.class, StoreMode.DB.getName());
        ASYNC_COMMITTING_SESSION_MANAGER = EnhancedServiceLoader.load(SessionManager.class, StoreMode.DB.getName(),
....
                                                                      
}
```

读取的文件

![image-20220311182615967](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c8c03733-5283-514c-84bc-11cce56a5122.png)

再回到begin方法中，我们就知道DataBaseSessionManager是如何创建的，包括下面这一步就是创建DataBaseSessionManager

```java
// 观察者设计模式，创建DataBaseSessionManager
 session.addSessionLifecycleListener(SessionHolder.getRootSessionManager());
```

但是此时有一个问题，就是我们的init方法在哪里调用的拿，其实我们回到Server中，我们发现在构建默认协调者之前就调用了init方法，说明在执行处理全局事务开始之前，就已经创建好了这个SessionManager了

```java
SessionHolder.init(parameterParser.getStoreMode());

// 默认协调者
DefaultCoordinator coordinator = new DefaultCoordinator(nettyRemotingServer);
```

好了此时分析清楚如何得到这个SessionManager以后，我们在回过头来看代码session.begin()位置

```java
@Override
public String begin(String applicationId, String transactionServiceGroup, String name, int timeout)
    throws TransactionException {
    // 创建全局事务Session
    GlobalSession session = GlobalSession.createGlobalSession(applicationId, transactionServiceGroup, name,
                                                              timeout);
    MDC.put(RootContext.MDC_KEY_XID, session.getXid());

    // 为Session中添加回调监听 SessionHolder.getRootSessionManager()去获取一个全局Session管理器DataBaseSessionManager
    // 观察者设计模式，创建DataBaseSessionManager
    session.addSessionLifecycleListener(SessionHolder.getRootSessionManager());

    // 全局事务开始
    session.begin();

    // transaction start event
    eventBus.post(new GlobalTransactionEvent(session.getTransactionId(), GlobalTransactionEvent.ROLE_TC,
                                             session.getTransactionName(), applicationId, transactionServiceGroup, session.getBeginTime(), null, session.getStatus()));

    return session.getXid();
}
```

session.begin()

```java
@Override
public void begin() throws TransactionException {
    // 声明全局事务开始
    this.status = GlobalStatus.Begin;
    // 开始时间
    this.beginTime = System.currentTimeMillis();
    // 激活全局事务
    this.active = true;
    // 将SessionManager放入到集合中，调用onBegin方法
    for (SessionLifecycleListener lifecycleListener : lifecycleListeners) {
        lifecycleListener.onBegin(this);
    }
}
```

这里我们来看一下 onBegin方法，调用的是父级的方法，在这其中我们要关注addGlobalSession方法，但是要注意，这里我们用的是db模式所以调用的是db模式的DateBaseSessionManager

```java
@Override
public void onBegin(GlobalSession globalSession) throws TransactionException {
    addGlobalSession(globalSession);
}
```

![image-20220311184728236](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5146098d-4181-57e6-92f4-823c1362fffb.png)

```java
@Override
public void addGlobalSession(GlobalSession session) throws TransactionException {
    if (StringUtils.isBlank(taskName)) {
        // 写入session
        boolean ret = transactionStoreManager.writeSession(LogOperation.GLOBAL_ADD, session);
        if (!ret) {
            throw new StoreException("addGlobalSession failed.");
        }
    } else {
        boolean ret = transactionStoreManager.writeSession(LogOperation.GLOBAL_UPDATE, session);
        if (!ret) {
            throw new StoreException("addGlobalSession failed.");
        }
    }
}
```

然后我们来看写入这里

```java
@Override
public boolean writeSession(LogOperation logOperation, SessionStorable session) {
    // 第一次进入一定是写入
    if (LogOperation.GLOBAL_ADD.equals(logOperation)) {
        return logStore.insertGlobalTransactionDO(SessionConverter.convertGlobalTransactionDO(session));
    } else if (LogOperation.GLOBAL_UPDATE.equals(logOperation)) {
        return logStore.updateGlobalTransactionDO(SessionConverter.convertGlobalTransactionDO(session));
    } else if (LogOperation.GLOBAL_REMOVE.equals(logOperation)) {
        return logStore.deleteGlobalTransactionDO(SessionConverter.convertGlobalTransactionDO(session));
    } else if (LogOperation.BRANCH_ADD.equals(logOperation)) {
        return logStore.insertBranchTransactionDO(SessionConverter.convertBranchTransactionDO(session));
    } else if (LogOperation.BRANCH_UPDATE.equals(logOperation)) {
        return logStore.updateBranchTransactionDO(SessionConverter.convertBranchTransactionDO(session));
    } else if (LogOperation.BRANCH_REMOVE.equals(logOperation)) {
        return logStore.deleteBranchTransactionDO(SessionConverter.convertBranchTransactionDO(session));
    } else {
        throw new StoreException("Unknown LogOperation:" + logOperation.name());
    }
}
```

因为我们第一次调用一定是写入，所以此时我们应该查看insertGlobalTransactionDO，此方法的作用就是写入全局事务表中global_table

```java
@Override
public boolean insertGlobalTransactionDO(GlobalTransactionDO globalTransactionDO) {
    String sql = LogStoreSqlsFactory.getLogStoreSqls(dbType).getInsertGlobalTransactionSQL(globalTable);
    Connection conn = null;
    PreparedStatement ps = null;
    try {
        conn = logStoreDataSource.getConnection();
        conn.setAutoCommit(true);
        ps = conn.prepareStatement(sql);
        ps.setString(1, globalTransactionDO.getXid());
        ps.setLong(2, globalTransactionDO.getTransactionId());
        ps.setInt(3, globalTransactionDO.getStatus());
        ps.setString(4, globalTransactionDO.getApplicationId());
        ps.setString(5, globalTransactionDO.getTransactionServiceGroup());
        String transactionName = globalTransactionDO.getTransactionName();
        transactionName = transactionName.length() > transactionNameColumnSize ? transactionName.substring(0,
                                                                                                           transactionNameColumnSize) : transactionName;
        ps.setString(6, transactionName);
        ps.setInt(7, globalTransactionDO.getTimeout());
        ps.setLong(8, globalTransactionDO.getBeginTime());
        ps.setString(9, globalTransactionDO.getApplicationData());
        return ps.executeUpdate() > 0;
    } catch (SQLException e) {
        throw new StoreException(e);
    } finally {
        IOUtil.close(ps, conn);
    }
}
```

我们可以查看GlobalTransactionDO实体类的属性，和global_table 的字段进行比对，就能看出其中道理。
