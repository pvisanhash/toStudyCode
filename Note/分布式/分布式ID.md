# 分布式ID

## 1. 分布式ID基础

### 1.1 什么是分布式ID

在单体应用中，ID通常可以依赖单库自增主键来生成，因为所有数据都写入同一个数据库，同一个表中的自增ID天然不会重复。但在分布式系统中，数据可能会被拆分到不同的库、不同的表，业务服务也可能部署在多台机器上。如果仍然只依赖某一个节点或某一张表来生成ID，就容易出现性能瓶颈、单点故障或者ID冲突的问题。

**分布式ID就是在分布式环境下生成的全局唯一标识**。它常用于数据库主键、订单号、用户ID、消息ID、日志链路追踪ID等场景。一个好的分布式ID方案，不仅要保证不同节点生成的ID不重复，还要兼顾性能、可用性、存储成本以及业务可读性。

### 1.2 分布式ID的核心要求

分布式ID一般需要满足以下要求：

1\. 全局唯一：这是最基本的要求，在不同服务、不同机器、不同数据库节点中生成的ID不能重复。
2\. 趋势递增：ID最好能够大体按照时间递增，这样对MySQL等关系型数据库的索引更加友好，可以减少B+树频繁调整带来的性能损耗。
3\. 高性能：ID生成操作通常出现在核心业务链路中，比如下单、支付、写日志等，所以生成速度要足够快，不能成为系统瓶颈。
4\. 高可用：ID生成服务不能因为某个节点宕机就整体不可用，需要具备一定的容错能力。
5\. 长度适中：ID不能太长，否则会增加数据库存储、索引维护和网络传输成本。
6\. 信息安全：如果ID会暴露给外部用户，需要避免通过ID推测出业务规模、数据量、机器信息等敏感内容。
7\. 便于扩展：随着业务规模扩大，ID生成方案要能够支持更多机器、更多业务线以及更高并发。

## 2. 通用唯一ID方案

### 2.1 UUID

**UUID** (Universally Unique Identifier)，通用唯一识别码。UUID是基于当前时间、计数器（counter）和硬件标识（通常为无线网卡的MAC地址）等数据计算生成的。

#### 2.1.1 UUID的组成

UUID由以下几部分的组合：

1\. 当前日期和时间，UUID的第一个部分与时间有关，如果你在生成一个UUID之后，过几秒又生成一个UUID，则第一个部分不同，其余相同。
2\. 时钟序列。
3\. 全局唯一的IEEE机器识别号，如果有网卡，从网卡MAC地址获得，没有网卡以其他方式获得。

UUID 是由一组32位数的16进制数字所构成，以连字号分隔的五组来显示，形式为 8-4-4-4-12，总共有 36个字符（即三十二个英数字母和四个连字号）。例如：

```txt
aefbbd3a-9cc5-4655-8363-a2a43e6e6c80
xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx
```

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841424.png" alt="image.png" style="zoom: 50%;" />

#### 2.1.2 UUID作为分布式ID的问题

如果需求是只保证唯一性，那么UUID也是可以使用的，但是按照上面的分布式id的要求， UUID其实是不能做成分布式id的，原因如下：

1\. 首先分布式id一般都会作为主键，但是安装mysql官方推荐主键要尽量越短越好，UUID每一个都很长，所以不是很推荐
2\. 既然分布式id是主键，然后主键是包含索引的，然后mysql的索引是通过b+树来实现的，每一次新的UUID数据的插入，为了查询的优化，都会对索引底层的b+树进行修改，因为UUID数据是无序的，所以每一次UUID数据的插入都会对主键生成的b+树进行很大的修改，这一点很不好
3\. 信息不安全：基于MAC地址生成UUID的算法可能会造成MAC地址泄露，这个漏洞曾被用于寻找梅丽莎病毒的制作者位置。

#### 2.1.3 UUID是否无序

不完全是，**要看 UUID 的版本**。

常见的 **UUID v4** 是随机生成的，所以从数据库索引角度看基本是**无序的**。如果把它作为 MySQL 主键，新插入的数据会随机落到 B+ 树不同位置，容易导致页分裂、索引维护成本变高。

但 **UUID v1** 不是完全无序的，它包含时间戳、时钟序列和机器标识，所以理论上带有时间信息。不过标准 UUID v1 的字符串/字节排列方式并不总是对数据库索引最友好，而且还可能暴露 MAC（Media Access Control，媒体访问控制）地址信息。

另外，现在还有一些更适合有序写入的方案，比如：

- **UUID v6**：对 UUID v1 做了时间排序优化
- **UUID v7**：基于 Unix 时间戳，更适合按时间递增
- **ULID**（Universally Unique Lexicographically Sortable Identifier，通用唯一字典序可排序标识符）：天然支持按字典序大体递增

所以：

常用的 UUID v4 是随机无序的；部分 UUID 版本带有时间信息，但传统 UUID 作为数据库主键时通常不如 Snowflake、号段模式等方案索引友好。



## 3. 数据库生成方案

### 3.1 数据库自增ID与主键维护表

#### 3.1.1 单库自增ID的问题

针对表结构的主键，我们常规的操作是在创建表结构的时候给对应的ID设置 `auto_increment`.也就是勾选自增选项。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841425.png" alt="image.png" style="zoom: 50%;" />

但是这种方式我们清楚在单个数据库的场景中我们是可以这样做的，但如果是在分库分表的环境下。直接利用单个数据库的自增肯定会出现问题。因为ID要唯一，但是分表分库后只能保证一个表中的ID的唯一，而不能保证整体的ID唯一。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841426.png" alt="image.png" style="zoom:50%;" />

#### 3.1.2 主键维护表的使用方式

上面的情况我们可以通过单独创建**主键维护表**来处理。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841427.png" alt="image.png" style="zoom: 50%;" />

举个例子来看看：

创建一个表结构

```sql
CREATE TABLE `test_order_id`  (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `title` char(1) NOT NULL,
  PRIMARY KEY (`id`),
	UNIQUE KEY `title` (`title`)
) ENGINE = InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET =utf8;
```

然后我们通过更新ID操作来获取ID信息

```sql
BEGIN;

REPLACE INTO test_order_id (title) values ('p') ;
SELECT LAST_INSERT_ID();

COMMIT;
```

### 3.2 数据库多主模式

#### 3.2.1 多主模式的实现思路

单点数据库方式存在明显的性能问题，可以对数据库进行高可用优化，担心一个主节点挂掉没法使用，可以选择做双主模式集群，也就是两个MySQL实例都能单独生产自增的ID。

查看主键自增的属性

```sql
show variables like '%increment%'
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841428.png)

我们可以设置主键自增的步长从2开始。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841429.png" alt="image.png" style="zoom: 50%;" />

#### 3.2.2 多主模式的问题

但是这种在并发量比较高的情况下，如何保证扩展性其实会是一个问题。在高并发情况下无能为力。

### 3.3 号段模式

#### 3.3.1 号段模式的实现思路

号段模式是当下分布式ID生成器的主流实现方式之一，号段模式可以理解为从数据库批量的获取自增ID，每次从数据库取出一个号段范围，例如 (1,1000] 代表1000个ID，具体的业务服务将本号段，生成1~1000的自增ID并加载到内存。表结构如下：

```sql
CREATE TABLE id_generator (
  id int(10) NOT NULL,
  max_id bigint(20) NOT NULL COMMENT '当前最大id',
  step int(20) NOT NULL COMMENT '号段的步长',
  biz_type    int(20) NOT NULL COMMENT '业务类型',
  version int(20) NOT NULL COMMENT '版本号',
  PRIMARY KEY (`id`)
)
```

字段说明：

> biz_type ：代表不同业务类型
>
> max_id ：当前最大的可用id
>
> step ：代表号段的长度
>
> version ：是一个乐观锁，每次都更新version，保证并发时数据的正确性

等这批号段ID用完，再次向数据库申请新号段，对max_id字段做一次update操作，update max_id= max_id + step，update成功则说明新号段获取成功，新的号段范围是(max_id ,max_id +step]

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841430.png" alt="image.png" style="zoom: 50%;" />

#### 3.3.2 号段模式的优缺点

由于多业务端可能同时操作，所以采用版本号version乐观锁方式更新，这种分布式ID生成方式不强依赖于数据库，不会频繁的访问数据库，对数据库的压力小很多。但同样也会存在一些缺点比如：服务器重启，单点故障会造成ID不连续。

## 4. Redis生成方案

### 4.1 Redis INCR生成ID

基于全局唯一ID的特性，我们可以通过Redis的INCR命令来生成全局唯一ID。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841431.png" alt="image.png" style="zoom:50%;" />

同样使用Redis也有对应的缺点：

1\. ID 生成的持久化问题，如果Redis宕机了怎么进行恢复
2\. 单个节点宕机问题

### 4.2 Redis集群步长方案

当然针对故障问题我们可以通过Redis集群来处理，比如我们有三个Redis的Master节点。可以初始化每台Redis的值分别是1,2,3，然后分别把分布式ID的KEY用Hash Tags固定每一个master节点，步长就是master节点的个数。各个Redis生成的ID为：

A:1,4,7

B:2,5,8

C:3,6,9

### 4.3 Redis方案的优缺点

优点：

1\. 不依赖于数据库，灵活方便，且性能优于数据库
2\. 数字ID有序，对分页处理和排序都很友好
3\. 防止了Redis的单机故障

缺点：

1\. 如果没有Redis数据库，需要安装配置，增加复杂度
2\. 集群节点确定是3个后，后面调整不是很友好

### 4.4 RedisDistributedId代码案例

Redis分布式ID的简单案例

```java
/**
 *  Redis 分布式ID生成器
 */
@Component
public class RedisDistributedId {

    @Autowired
    private StringRedisTemplate redisTemplate;

    private static final long BEGIN_TIMESTAMP = 1659312000l;

    /**
     * 生成分布式ID
     * 符号位    时间戳[31位]  自增序号【32位】
     * @param item
     * @return
     */
    public long nextId(String item){
        // 1.生成时间戳
        LocalDateTime now = LocalDateTime.now();
        // 格林威治时间差
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
        // 我们需要获取的 时间戳 信息
        long timestamp = nowSecond - BEGIN_TIMESTAMP;
        // 2.生成序号 --》 从Redis中获取
        // 当前当前的日期
        String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        // 获取对应的自增的序号，如果没有key会新增数据，初始值为0然后+1，一般的还要加后生失效时间
        Long increment = redisTemplate.opsForValue().increment("id:" + item + ":" + date);
      	// 把时间戳塞进高 32 位，把 Redis 自增序号塞进低 32 位。
        return timestamp << 32 | increment;
    }

}
```

## 5. Snowflake雪花算法

### 5.1 Snowflake算法结构

Snowflake，雪花算法是由Twitter开源的分布式ID生成算法，以划分命名空间的方式将64bit位分割成了多个部分，每个部分都有具体的不同含义，在Java中64Bit位的整数是Long类型，所以在Java中Snowflake算法生成的ID就是long来存储的。具体如下：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841432.png" alt="image.png" style="zoom:50%;" />

第一部分：占用1bit,第一位为符号位，不适用

第二部分：41位的时间戳，41bit位可以表示2^41^个数，每个数代表的是毫秒，雪花算法的时间年限是(2^41^)/(1000×60×60×24×365)=69年

第三部分:10bit表示是机器数，即 2^10^ = 1024台机器，通常不会部署这么多机器

第四部分:12bit位是自增序列，可以表示2^12^=4096个数，一毫秒内可以生成4096个ID

### 5.2 Snowflake代码实现

案例代码：

```java
package com.boge.vip.utils;

/**
 * Twitter_Snowflake
 * SnowFlake的结构如下(每部分用-分开):
 * 0 - 0000000000 0000000000 0000000000 0000000000 0 - 00000 - 00000 - 000000000000
 * 1位标识，由于long基本类型在Java中是带符号的，最高位是符号位，正数是0，负数是1，所以id一般是正数，最高位是0
 * 41位时间截(毫秒级)，注意，41位时间截不是存储当前时间的时间截，而是存储时间截的差值（当前时间截 - 开始时间截)
 * 得到的值），这里的的开始时间截，一般是我们的id生成器开始使用的时间，由我们程序来指定的（如下下面程序IdWorker类的startTime属性）。41位的时间截，可以使用69年，年T = (1L << 41) / (1000L * 60 * 60 * 24 * 365) = 69
 * 10位的数据机器位，可以部署在1024个节点，包括5位datacenterId和5位workerId
 * 12位序列，毫秒内的计数，12位的计数顺序号支持每个节点每毫秒(同一机器，同一时间截)产生4096个ID序号
 * 加起来刚好64位，为一个Long型。
 * SnowFlake的优点是，整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞(由数据中心ID和机器ID作区分)，并且效率较高，经测试，SnowFlake每秒能够产生26万ID左右。
 */
public class SnowflakeIdWorker {

    // ==============================Fields===========================================
    /**
     * 开始时间截 (2020-11-03，一旦确定不可更改，否则时间被回调，或者改变，可能会造成id重复或冲突)
     */
    private final long twepoch = 1604374294980L;

    /**
     * 机器id所占的位数
     */
    private final long workerIdBits = 5L;

    /**
     * 数据标识id所占的位数
     */
    private final long datacenterIdBits = 5L;

    /**
     * 支持的最大机器id，结果是31 (这个移位算法可以很快的计算出几位二进制数所能表示的最大十进制数)
     */
    private final long maxWorkerId = -1L ^ (-1L << workerIdBits);

    /**
     * 支持的最大数据标识id，结果是31
     */
    private final long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);

    /**
     * 序列在id中占的位数
     */
    private final long sequenceBits = 12L;

    /**
     * 机器ID向左移12位
     */
    private final long workerIdShift = sequenceBits;

    /**
     * 数据标识id向左移17位(12+5)
     */
    private final long datacenterIdShift = sequenceBits + workerIdBits;

    /**
     * 时间截向左移22位(5+5+12)
     */
    private final long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;

    /**
     * 生成序列的掩码，这里为4095 (0b111111111111=0xfff=4095)
     */
    private final long sequenceMask = -1L ^ (-1L << sequenceBits);

    /**
     * 工作机器ID(0~31)
     */
    private long workerId;

    /**
     * 数据中心ID(0~31)
     */
    private long datacenterId;

    /**
     * 毫秒内序列(0~4095)
     */
    private long sequence = 0L;

    /**
     * 上次生成ID的时间截
     */
    private long lastTimestamp = -1L;

    //==============================Constructors=====================================

    /**
     * 构造函数
     *
     */
    public SnowflakeIdWorker() {
        this.workerId = 0L;
        this.datacenterId = 0L;
    }

    /**
     * 构造函数
     *
     * @param workerId     工作ID (0~31)
     * @param datacenterId 数据中心ID (0~31)
     */
    public SnowflakeIdWorker(long workerId, long datacenterId) {
        if (workerId > maxWorkerId || workerId < 0) {
            throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0", maxWorkerId));
        }
        if (datacenterId > maxDatacenterId || datacenterId < 0) {
            throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0", maxDatacenterId));
        }
        this.workerId = workerId;
        this.datacenterId = datacenterId;
    }

    // ==============================Methods==========================================

    /**
     * 获得下一个ID (该方法是线程安全的)
     *
     * @return SnowflakeId
     */
    public synchronized long nextId() {
        long timestamp = timeGen();

        //如果当前时间小于上一次ID生成的时间戳，说明系统时钟回退过这个时候应当抛出异常
        if (timestamp < lastTimestamp) {
            throw new RuntimeException(
                    String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds", lastTimestamp - timestamp));
        }

        //如果是同一时间生成的，则进行毫秒内序列
        if (lastTimestamp == timestamp) {
            sequence = (sequence + 1) & sequenceMask;
            //毫秒内序列溢出
            if (sequence == 0) {
                //阻塞到下一个毫秒,获得新的时间戳
                timestamp = tilNextMillis(lastTimestamp);
            }
        }
        //时间戳改变，毫秒内序列重置
        else {
            sequence = 0L;
        }

        //上次生成ID的时间截
        lastTimestamp = timestamp;

        //移位并通过或运算拼到一起组成64位的ID
        return ((timestamp - twepoch) << timestampLeftShift) //
                | (datacenterId << datacenterIdShift) //
                | (workerId << workerIdShift) //
                | sequence;
    }

    /**
     * 阻塞到下一个毫秒，直到获得新的时间戳
     *
     * @param lastTimestamp 上次生成ID的时间截
     * @return 当前时间戳
     */
    protected long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    /**
     * 返回以毫秒为单位的当前时间
     *
     * @return 当前时间(毫秒)
     */
    protected long timeGen() {
        return System.currentTimeMillis();
    }

    /**
     * 随机id生成，使用雪花算法
     *
     * @return
     */
    public static String getSnowId() {
        SnowflakeIdWorker sf = new SnowflakeIdWorker();
        String id = String.valueOf(sf.nextId());
        return id;
    }

    //=========================================Test=========================================

    /**
     * 测试
     */
    public static void main(String[] args) {
        SnowflakeIdWorker idWorker = new SnowflakeIdWorker(0, 0);
        for (int i = 0; i < 1000; i++) {
            long id = idWorker.nextId();
            System.out.println(id);
        }
    }
}

```

### 5.3 生产环境应用方式

实际生产环境中我们应该怎么来应用雪花算法来实现分布式ID。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841433.png)

在实际生产环境中，一般不会让每个业务系统都自己随便写一套雪花算法，而是会把ID生成能力封装成统一的组件或者独立的发号服务。业务服务需要ID时，直接调用这个组件或者服务即可。

常见的使用方式有两种：

1\. 本地组件方式

把雪花算法封装成一个公共jar包，每个业务服务引入这个jar包后，在本地 JVM（Java Virtual Machine，Java虚拟机）中直接生成ID。

这种方式性能最好，因为生成ID不需要远程调用，缺点是每个服务节点都必须拿到不同的 workerId，否则不同机器可能生成重复ID。

2\. 独立发号服务方式

单独部署一个ID生成服务，业务系统通过 HTTP（HyperText Transfer Protocol，超文本传输协议）或者 RPC（Remote Procedure Call，远程过程调用）请求它来获取ID。

这种方式更容易统一管理 workerId、时间回拨、监控告警等问题，缺点是多了一次网络调用，发号服务本身也需要做高可用部署。

生产环境中最关键的是 workerId 的分配。因为雪花算法依赖 `datacenterId + workerId` 来区分不同节点，如果两个节点拿到了相同的机器标识，在同一毫秒内又生成了相同序列号，就可能出现ID重复。

常见的 workerId 分配方式有：

1\. 配置文件指定：每台机器在配置文件中写死不同的 workerId，简单直接，但机器扩容和迁移时容易配置错。

2\. 数据库分配：服务启动时向数据库注册机器信息，由数据库分配 workerId，适合中小规模系统。

3\. ZooKeeper分配：服务启动时在 ZooKeeper 中创建临时顺序节点，根据节点序号生成 workerId，适合动态扩缩容场景。

4\. Redis分配：服务启动时通过 Redis 自增或者注册表获取 workerId，实现相对简单，但要注意 Redis 可用性。

所以生产环境使用雪花算法时，重点不是 `nextId()` 这段代码本身，而是要解决好以下问题：

1\. 每个节点的 workerId 必须唯一。

2\. 服务重启后不能错误复用其他节点的 workerId。

3\. 系统时间要尽量稳定，避免频繁发生时钟回拨。

4\. ID生成服务需要监控，比如生成速度、异常次数、时钟回拨次数等。

5\. 如果作为独立服务部署，需要保证高可用，避免ID服务成为业务链路的单点瓶颈。

### 5.4 时钟回拨问题

时针回拨问题【20：28 --》20：26】 【20：28 --》 20：30】

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841434.png)

雪花算法强依赖系统时间。它生成ID时，会把当前时间戳放到ID的高位，如果系统时间一直向前走，那么生成出来的ID整体就是趋势递增的。

但是如果服务器时间发生了回拨，比如上一次生成ID时系统时间是：

```txt
20:28:00
```

下一次生成ID时，系统时间却变成了：

```txt
20:26:00
```

那么就会出现问题。因为在 `20:26:00 ~ 20:28:00` 这个时间范围内，之前可能已经生成过一批ID。现在时间回到了过去，如果 workerId 和 sequence 又刚好相同，就可能生成重复ID。

雪花算法中的核心判断就是这一段：

```java
if (timestamp < lastTimestamp) {
    throw new RuntimeException(
            String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds", lastTimestamp - timestamp));
}
```

其中：

1\. `timestamp`：当前系统时间。

2\. `lastTimestamp`：上一次生成ID时记录的时间。

3\. 如果 `timestamp < lastTimestamp`，说明当前时间比上一次还小，也就是发生了时钟回拨。

时钟回拨常见原因有：

1\. 服务器手动修改了系统时间。

2\. NTP（Network Time Protocol，网络时间协议）同步时间时发生了较大幅度校准。

3\. 虚拟机、容器迁移后时间发生漂移。

4\. 宿主机时间异常，影响了上面的应用实例。

常见处理方案有：

1\. 直接拒绝生成ID

发现时钟回拨后直接抛异常，停止发号。这种方式最安全，不会生成重复ID，但会影响业务可用性。

2\. 等待时间追上来

如果回拨时间很短，比如只有几毫秒，可以阻塞等待，直到当前时间重新大于等于 `lastTimestamp` 后再继续生成ID。

3\. 使用备用 workerId

如果检测到时钟回拨，可以临时切换到备用 workerId。因为 workerId 变了，即使时间回退，也能降低ID重复风险。

4\. 引入回拨序列号

预留一部分位来表示时钟回拨次数或时钟序列，每次发生回拨时增加该序列，避免和之前的ID冲突。

5\. 使用统一发号服务

把雪花算法集中到统一服务中，统一处理时间回拨、workerId 分配和监控告警，避免每个业务服务各自处理。

实际项目中通常会采用组合方案：小范围回拨就等待，大范围回拨就拒绝发号并告警。如果业务对可用性要求非常高，也可以使用 Leaf、UidGenerator 这类成熟方案来规避部分细节问题。

## 6. 开源框架实现

### 6.1 百度 UidGenerator

#### 6.1.1 UidGenerator实现原理

源码地址：https://github.com/baidu/uid-generator

中文文档地址：https://github.com/baidu/uid-generator/blob/master/README.zh_cn.md

UidGenerator是百度开源的Java语言实现，基于Snowflake算法的唯一ID生成器。它是分布式的，并克服了雪花算法的并发限制。单个实例的QPS能超过6000000。需要的环境：JDK8+，MySQL（用于分配WorkerId）。

百度的Uidgenerator对结构做了部分的调整，具体如下：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841435.png)

#### 6.1.2 UidGenerator特点

UidGenerator的时间部分只有28位，这就意味着UidGenerator默认只能承受8.5年（2^28-1/86400/365）也可以根据你业务的需求，UidGenerator可以适当调整delta seconds、worker node id和sequence占用位数。

### 6.2 美团 Leaf

#### 6.2.1 Leaf支持的模式

由美团开发，开源项目链接：https://github.com/Meituan-Dianping/Leaf

Leaf同时支持号段模式和snowflake算法模式，可以切换使用。ID号码是趋势递增的8byte的64位数字，满足上述数据库存储的主键要求。

#### 6.2.2 Leaf号段模式

Leaf的号段模式是对直接用数据库自增ID充当分布式ID的一种优化，减少对数据库的频率操作。相当于从数据库批量的获取自增ID，每次从数据库取出一个号段范围，例如 (1,1000] 代表1000个ID，业务服务将号段在本地生成1~1000的自增ID并加载到内存.。

#### 6.2.3 Leaf Snowflake模式

Leaf的snowflake模式依赖于ZooKeeper，不同于原始snowflake算法也主要是在workId的生成上，Leaf中workId是基于ZooKeeper的顺序Id来生成的，每个应用在使用Leaf-snowflake时，启动时都会都在Zookeeper中生成一个顺序Id，相当于一台机器对应一个顺序节点，也就是一个workId。

#### 6.2.4 Leaf特性

特性：

1）全局唯一，绝对不会出现重复的ID，且ID整体趋势递增。

2）高可用，服务完全基于分布式架构，即使MySQL宕机，也能容忍一段时间的数据库不可用。

3）高并发低延时，在CentOS 4C8G的虚拟机上，远程调用QPS可达5W+，TP99在1ms内。

4）接入简单，直接通过公司RPC服务或者HTTP调用即可接入。

Leaf采用双buffer的方式，它的服务内部有两个号段缓存区segment。当前号段已消耗10%时，还没能拿到下一个号段，则会另启一个更新线程去更新下一个号段。

简而言之就是Leaf保证了总是会多缓存两个号段，即便哪一时刻数据库挂了，也会保证发号服务可以正常工作一段时间。

#### 6.2.5 Leaf接入案例

案例记录

```xml
        <dependency>
            <artifactId>leaf-boot-starter</artifactId>
            <groupId>com.sankuai.inf.leaf</groupId>
            <version>1.0.1-RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>2.6.0</version>
            <exclusions>
                <exclusion>
                    <artifactId>log4j</artifactId>
                    <groupId>log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

属性文件配置：

```properties
leaf.name=com.boge.vip
leaf.segment.enable=false
#leaf.segment.url=
#leaf.segment.username=
#leaf.segment.password=

leaf.snowflake.enable=true
#leaf.snowflake.address=
#leaf.snowflake.port=
leaf.snowflake.address=192.168.56.100
leaf.snowflake.port=2181
```

放开注解：

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841436.png" alt="image.png" style="zoom:33%;" />

注意你还需要启动一个Zookeeper服务。

测试代码

```java
 @SpringBootTest
class DistributedIdsVipApplicationTests {

    @Autowired
     RedisDistributedId redisDistributedId;

     @Autowired
     private SnowflakeService snowflakeService;

    @Test
    void contextLoads() {
        Result abc = snowflakeService.getId("abc");
        System.out.println(abc.getId());

        System.out.println(Long.toBinaryString(abc.getId()));
    }

}
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513235841437.png)

### 6.3 滴滴 TinyID

#### 6.3.1 TinyID实现原理

由滴滴开发，开源项目链接：https://github.com/didi/tinyid

Tinyid是在美团（Leaf）的leaf-segment算法基础上升级而来，不仅支持了数据库多主节点模式，还提供了tinyid-client客户端的接入方式，使用起来更加方便。但和美团（Leaf）不同的是，Tinyid只支持号段一种模式不支持雪花模式。Tinyid提供了两种调用方式，一种基于Tinyid-server提供的http方式，另一种Tinyid-client客户端方式。每个服务获取一个号段（1000,2000]、（2000,3000]、（3000,4000]

#### 6.3.2 TinyID特性

特性：

1）全局唯一的long型ID

2）趋势递增的id

3）提供 http 和 java-client 方式接入

4）支持批量获取ID

5）支持生成1,3,5,7,9...序列的ID

6）支持多个db的配置

#### 6.3.3 TinyID适用场景

适用场景：只关心ID是数字，趋势递增的系统，可以容忍ID不连续，可以容忍ID的浪费

不适用场景：像类似于订单ID的业务，因生成的ID大部分是连续的，容易被扫库、或者推算出订单量等信息
