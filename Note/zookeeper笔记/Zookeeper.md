# Zookeeper

## 1、初识Zookeeper

- Zookeeper 是 Apache Hadoop 项目下的一个子项目，是一个树形目录服务。
- Zookeeper 翻译过来就是 动物园管理员，他是用来管 Hadoop（大象）、Hive(蜜蜂)、Pig(小 猪)的管理员。简称zk
- Zookeeper 是一个分布式的、开源的分布式应用程序的协调服务。
- Zookeeper 为分布式项目提供的主要功能包括：
  - 配置管理
  - 分布式锁
  - 集群管理

## 2、Zookeeper安装与配置
### 2.1 下载安装

**2.1.1、环境准备**

ZooKeeper服务器是用Java创建的，它运行在JVM之上。需要安装JDK 7或更高版本。

**2.1.2、上传**

将下载的ZooKeeper放到/opt/ZooKeeper目录下

```shell
#上传zookeeper alt+p
put f:/setup/apache-zookeeper-3.5.6-bin.tar.gz
#打开 opt目录
cd /opt
#创建zooKeeper目录
mkdir  zooKeeper
#将zookeeper安装包移动到 /opt/zooKeeper
mv apache-zookeeper-3.5.6-bin.tar.gz /opt/zookeeper/
```

**2.1.3、解压**

将tar包解压到/opt/zookeeper目录下

```shell
tar -zxvf apache-ZooKeeper-3.5.6-bin.tar.gz 
```

### 2.2 配置启动

**2.2.1、配置zoo.cfg**

进入到conf目录拷贝一个zoo_sample.cfg并完成配置

```shell
#进入到conf目录
cd /opt/zooKeeper/apache-zooKeeper-3.5.6-bin/conf/
#拷贝
cp  zoo_sample.cfg  zoo.cfg
```

修改zoo.cfg:

```shell
#打开目录
cd /opt/zooKeeper/
#创建zooKeeper存储目录
mkdir  zkdata
#修改zoo.cfg
vim /opt/zooKeeper/apache-zooKeeper-3.5.6-bin/conf/zoo.cfg
```

![1577548250377](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259663.png)

修改存储目录：dataDir=/opt/zookeeper/zkdata

**2.2.2、启动ZooKeeper**

```shell
cd /opt/zooKeeper/apache-zooKeeper-3.5.6-bin/bin/
#启动
./zkServer.sh  start
```

![1577548052037](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259664.png)

看到上图表示ZooKeeper成功启动

**2.2.3、查看ZooKeeper状态**

```shell
./zkServer.sh status
```

zookeeper启动成功。standalone代表zk没有搭建集群，现在是单节点

![1577548175232](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259665.png)

zookeeper没有启动

![1577548112773](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259666.png)

如果没有启动到logs文件夹下，cat下是否有报错日志

## 3、ZooKeeper命令操作

### 3.1、Zookeeper的数据模型

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259667.png)

ZooKeeper 是一个树形目录服务,其数据模型和Unix的文件系统目录树很类似，拥有一个层次化结构。

这里面的每一个节点都被称为： ZNode，每个节点上都会保存自己的数据和节点信息。 

节点可以拥有子节点，同时也允许少量（1MB）数据存储在该节点之下。

**节点可以分为四大类**：

- PERSISTENT 持久化节点 
- EPHEMERAL 临时节点 ：-e
- PERSISTENT_SEQUENTIAL 持久化顺序节点 ：-s
- EPHEMERAL_SEQUENTIAL 临时顺序节点  ：-es

### 3.2、Zookeeper服务端常用命令

启动Zookeeper服务： ./zkServer.sh start

查看Zookeeper服务状态： ./zkServer.sh status

停止Zookeeper服务： ./zkServer.sh stop

重启Zookeeper服务： ./zkServer.sh restart

### 3.3、Zookeeper客户端常用命令

连接Zookepr服务端：	./zkCli.sh –server  ip:port,如果是本机操作则可省略ip:port ，默认端口号为2181

断开连接：	quit

查看帮助：	help

显示指定目录下节点：ls 目录    或者   ls2 目录

创建节点：	create    /节点path value

获取节点值：	get   /节点path

设置节点值：	set    /节点path value

删除单个节点: 	delete   /节点path

删除带有子节点的节点：	deleteall   /节点path  --->即递归删除

创建临时节点:	create  -e  /节点path  value

创建顺序节点：	create -s   /节点path  value

查询节点状态： 	ls -s   /节点path  -----> s = tatus

```properties
//详细信息解释
czxid: 节点被创建的事务id  ===  create znode tx id
ctime: 创建时间  === create time
mzxid: 最后一次被更新的事务id  ==== modify znode tx id 
mtime: 修改时间  ==== modify time
pzxid: 子节点(父节点下)列表最后一次被更新的事务id  === parent下 znode tx id
cversion:  子节点的版本号 === children version
dataversion: 数据版本号
aclversion： 权限版本号 === access controll level version 
ephemeralOwner： 用于临时节点，代表冒昧节点的事务id,如果为持名节点则为0
dataLength: 节点储存的数据长度   
numChildren: 当前节点的子节点个数  === number children 
```

## 4、Zookeeper的JavaApi操作

### 4.1、建立连接

#### 4.1.1、Curator介绍

```properties
curator = 管理员
```

常见的ZooKeeper Java API ：

- 原生Java API
- ZkClient
- Curator （推荐）

Curator 最初是 Netfix 研发的,后来捐献了 Apache 基金会,目前是 Apache 的顶级项目。所以Curator 是 Apache ZooKeeper 的Java客户端库Curator 项目的目标是简化 ZooKeeper 客户端的使用。

Curator官网：http://curator.apache.org/

#### 4.1.2、Curator建立连接

> 新建maven工程，导包，写配置

修改pom.xml文件

```xml
   <dependencies>
        <!--junit4单元测试-->
        <!--如果@Test不能用，请删除junit下scope标签-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
            <scope>test</scope>
        </dependency>
        <!--curator，确定版本可在官网找到适配zookeeper版本-->
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>4.0.0</version>
        </dependency>
        <!--日志-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.21</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.21</version>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.20</version>
        </dependency>
    </dependencies>
```

在resouces文件夹下配置简化的`log4j.properties`

​	第一行表示日志输出到文件off，但有标准输出

```properties
log4j.rootLogger=off,stdout

log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%d{yyyy-MM-dd HH/:mm/:ss}]%-5p %c(line/:%L) %x-%m%n
```

> 建立连接

```java
public class CuratorTest {

    @Test
    public void test1() {
        //构建参数
        String connectString = "192.168.58.131:2181"; //多个有逗号间隔
        int sessionTimeoutMs = 60 * 1000; //会话超时，单位毫秒
        int collectionTimeoutMs = 15 * 1000; //连接超时，单位毫秒
        RetryPolicy retryPolicy =
                new ExponentialBackoffRetry(3000, 10);//重试政策
        //创建CuratorFramework对象，即客户端对象
        CuratorFramework curatorFramework =
                CuratorFrameworkFactory.newClient(connectString, sessionTimeoutMs, collectionTimeoutMs, retryPolicy);
        //客户端开启连接，使用完要关闭
        curatorFramework.start(); 
    }
}
```

**也可以用builder来链式编程（推荐）：**

```java
//namespace相当于把myApp当做根目录，myApp不存在则创建，存在就用原来的，且如果如无子节点它会被删除       
		CuratorFramework curatorFramework = CuratorFrameworkFactory
                .builder()
                .namespace("myApp")  
                .connectString("192.168.58.131:2181")
                .connectionTimeoutMs(15 * 1000)
                .sessionTimeoutMs(60 * 1000)
                .retryPolicy(new ExponentialBackoffRetry(3000, 10))
                .build();
		//客户端开启连接，使用完要关闭
		curatorFramework.start();
```

### 4.2、添加节点

```java
public class CuratorTest {

    private CuratorFramework curatorFramework;

    @Before
    public void init() {
        //创建CuratorFramework对象
        curatorFramework = CuratorFrameworkFactory
                .builder()
                .namespace("myApp")  //相当于以myApp是根目录，如果这里加/则报错
                .connectString("192.168.58.131:2181")
                .connectionTimeoutMs(15 * 1000)
                .sessionTimeoutMs(60 * 1000)
                .retryPolicy(new ExponentialBackoffRetry(3000, 10))
                .build();
        curatorFramework.start();
    }

    @Test
    public void test1() throws Exception {
        //返回值为创建的路径名；创建节点未指定数据，默认存储数据为ip，可以linux中get查看
        //相当于create /myApp/app1
        String path = curatorFramework.create().forPath("/app1");
        System.out.println(path);
        //创建时可以携带数据,相当于 create /myApp/app2 "helloWorld"
        curatorFramework.create().forPath("/app2","helloWorld".getBytes());
        //创建里可以指定模式，临时还是序列的等,相当于create -e /myApp/app0 "临时数据"
        curatorFramework.create().withMode(CreateMode.EPHEMERAL).forPath("/app0","临时数据".getBytes());
        //创建时可以多级创建,linux中不能实现多级创建
        curatorFramework.create().creatingParentsIfNeeded().forPath("/app3/row1");
    }

    @After
    public void release(){
        if(Objects.nonNull(curatorFramework)){
            curatorFramework.close();
        }
    }
}
```

### 4.3、查询节点

```java
    @Test
    public void test2() throws Exception {
        //查询节点中保存的数据，相当于 get /myApp/app1
        byte[] bytes = curatorFramework.getData().forPath("/app1");
        System.out.println(new String(bytes));
        //查询子节点，相当于 ls /myApp/app3
        List<String> children = curatorFramework.getChildren().forPath("/app3");
        System.out.println(children);
        List<String> rootChildren = curatorFramework.getChildren().forPath("/");
        System.out.println(rootChildren); //这里/指的是名称空间
        //查询节点状态信息,相当于 ls -s /myApp/app2
        Stat stat = new Stat();
        System.out.println(stat);
        byte[] bytes1 = curatorFramework.getData().storingStatIn(stat).forPath("/app2");
        System.out.println(new String(bytes));
        System.out.println(stat);
    }
```

### 4.4、修改节点

```java
    @Test
    public void test3() throws Exception {
        /**
         * 相当于set /myApp/app1 "app1Hello"
         * 普通的设置数据（不推荐）
         */
        curatorFramework.setData().forPath("/app1", "app1Hello".getBytes());
        /**
         * 根据版本号修改数据（推荐）
         * 先查节点状态的数据版本号，根据当前版本号修改，相当于乐观锁
         * 修改后数据版本号加1
         */
        Stat stat = new Stat();
        curatorFramework.getData().storingStatIn(stat).forPath("/app1");
        int currentVersion = stat.getVersion();
        curatorFramework.setData().withVersion(currentVersion).forPath("/app1","helloWorld".getBytes());
        byte[] bytes = curatorFramework.getData().forPath("/app1");
        System.out.println(new String(bytes));
    }
```

### 4.5、删除节点

```java
    @Test
    public void test4() throws Exception {
        /**
         * 1、删除单个节点 ，相当于delete /myApp/app1
         */
        curatorFramework.delete().forPath("/app1");
        /**
         * 2、删除多级节点 , 相当于deleteAll /myApp/app3/row1
         */
        curatorFramework.delete().deletingChildrenIfNeeded().forPath("/app3");
        /**
         * 3、有保证成功的删除
         */
        curatorFramework.delete().guaranteed().forPath("/app2");
        /**
         * 4、有回调函数的删除,一般要搭配guaranteed()方法
         *
         */
        //先创建个delete节点
        curatorFramework.create().forPath("/delete", "即将被删除".getBytes("utf-8"));
        //执行有回调删除
        curatorFramework.delete().guaranteed().inBackground(new BackgroundCallback() {
            @Override
            public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                System.out.println("我被删除了");
                System.out.println(curatorEvent);
            }
        }).forPath("/delete");
    }
```

### 4.6、Watch事件监听

#### 4.6.1、Watch事件监听的简介

ZooKeeper 允许用户在指定节点上注册一些Watcher（监听器），并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是 ZooKeeper 实现分布式协调服务的重要特性。ZooKeeper 中引入了Watcher机制来实现了发布/订阅功能能，能够让多个订阅者同时监听某一个对象，当一个对象自身状态变化时，会通知所有订阅者。

#### 4.6.2、Curator对事件监听的实现

ZooKeeper 原生支持通过注册Watcher来进行事件监听，但是其使用并不是特别方便，需要开发人员自己反复注册Watcher，比较繁琐。Curator引入了 Cache（缓存） 来实现对 ZooKeeper 服务端事件的监听。

**ZooKeeper提供了三种Watcher：**

- NodeCache : 只是监听某一个特定的节点
- PathChildrenCache : 监控当前路径下的所有子节点
- TreeCache : 可以监控整个树上的所有节点，类似于PathChildrenCache和NodeCache的组合

#### 4.6.3、NodeCache的使用

```java
public class CuratorCacheTest {

    private CuratorFramework curatorFramework;

    @Before
    public void init() {
        //创建CuratorFramework对象
        curatorFramework = CuratorFrameworkFactory
                .builder()
                .namespace("myApp")  //相当于以myApp是根目录，如果这里加/则报错
                .connectString("192.168.58.131:2181")
                .connectionTimeoutMs(15 * 1000)
                .sessionTimeoutMs(60 * 1000)
                .retryPolicy(new ExponentialBackoffRetry(3000, 10))
                .build();
        curatorFramework.start();
    }

    /**
     * NOdeCache只监听一个节点
     */
    @Test
    public void testNodeCache() throws Exception {
        //1、新建监听对象
        NodeCache nodeCache = new NodeCache(curatorFramework, "/app1", false);
        //2、添加监听器。    先获得可监听（监听器容器），再加入监听器接口实现类
        nodeCache.getListenable().addListener(new NodeCacheListener() {
            //创建、销毁节点，数据变更都是节点变更
            @Override
            public void nodeChanged() throws Exception {
                System.out.println("节点变化了");
                //可以通过NodeCache获取节点有关的数据
                byte[] datum = nodeCache.getCurrentData().getData();
                System.out.println(new String(datum));
            }
        });
        //3、开启监听。     buildInitial为构建初始化，true则为开启监听时加载缓存数据
        nodeCache.start(true);

        //这个只是让单元测试不停止
        while (true) {

        }

    }

    @After
    public void release() {
        if (Objects.nonNull(curatorFramework)) {
            curatorFramework.close();
        }
    }
}
```

#### 4.6.4、PathChildrenCache的使用

```java
    /**
     * PathChildrenCache是对路径下所有的子节点监听
     */
    @Test
    public void testPathChildrenCache() throws Exception {
        //1、创建监听对象
        PathChildrenCache pathChildrenCache =
                new PathChildrenCache(curatorFramework, "/app2", true);
        //2、添加监听器到容器
        pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
            @Override
            public void childEvent(CuratorFramework curatorFramework, PathChildrenCacheEvent pathChildrenCacheEvent) throws Exception {
                //只对数据变更监听
                PathChildrenCacheEvent.Type type = pathChildrenCacheEvent.getType();
                if (PathChildrenCacheEvent.Type.CHILD_UPDATED.equals(type)) {
                    System.out.println("数据变更");
                    System.out.println(pathChildrenCacheEvent);
                    //获取数据，想知道event结构，可打印出看下,如上
                    System.out.println(pathChildrenCacheEvent.getData().getData());
                }
            }
        });
        //3、监听开启
        pathChildrenCache.start();

        //只是让单位测试不停
        while (true) {
			
        }

    }
```

打印的pathChildrenCacheEvent的数据情况：

```json
PathChildrenCacheEvent{
    type=CHILD_UPDATED, 
    data=ChildData{
    	path='/app2/row1', 	      								      							stat=0,0,0,0,2,0,0,0,10,0,117, 
    	data=[104, 101, 108, 108, 111, 87, 111, 114, 108, 100]
	}
}
```

#### 4.6.5、TreeCache的使用

```java
    /**
     * TreeCache监听树，即当前节点以及所有子节点
     */
    @Test
    public void testTreeCache() throws Exception {
        //1、新建监听对象
        TreeCache treeCache = new TreeCache(curatorFramework, "/app3");
        //2、添加监听器到容器
        treeCache.getListenable().addListener(new TreeCacheListener() {
            @Override
            public void childEvent(CuratorFramework curatorFramework, TreeCacheEvent treeCacheEvent) throws Exception {
                System.out.println("数据变更了");
                System.out.println(treeCacheEvent);
            }
        });
        //3、监听开启
        treeCache.start();
    }
```

### 4.7、分布式锁实现

#### 4.7.1、分布式锁的引入

在我们进行单机应用开发，涉及并发同步的时候，我们往往采用synchronized或者Lock的方式来解决多线程间的代码同步问题，这时多线程的运行都是在同一个JVM之下，没有任何问题。

但当我们的应用是分布式集群工作的情况下，属于多JVM下的工作环境，跨JVM之间已经无法通过多线程的锁解决同步问题。

那么就需要一种更加高级的锁机制，来处理种跨机器的进程之间的数据同步问题——这就是分布式锁。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259668.png)

#### 4.7.2、Zookeeper分布式锁的原理

核心思想：当客户端要获取锁，则创建节点，使用完锁，则删除该节点。

1.客户端获取锁时，在lock节点下创建`临时顺序节点`。

2.然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。使用完锁后，将该节点删除。

3.如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小一的那个节点，同时对其注册事件监听器，监听删除事件。

4.如果发现比自己小的那个节点被删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点

是否是lock子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点

并注册监听。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259669.png)

#### 4.7.3、Curator简化zookeeper分布式锁

**在Curator中有五种锁方案：**

- InterProcessSemaphoreMutex：分布式排它锁（非可重入锁）
- InterProcessMutex：**分布式可重入排它锁（常用）**
- InterProcessReadWriteLock：分布式读写锁
- InterProcessMultiLock：将多个锁作为单个实体管理的容器
- InterProcessSemaphoreV2：共享信号量

#### 4.7.4、模拟12306卖票

卖票的资源类:

```java
public class Ticket12306 implements Runnable {
    //共有的
    private Integer ticketNumber = 20;

    @Override
    public void run() {
        while (true) {
            if (ticketNumber > 0) {
                System.out.println(Thread.currentThread().getName() + "正在卖"+ticketNumber+"票");
                ticketNumber--;
            }
        }
    }
}
```

测试类：多个进程共享同个资源

```java
public class Ticket12306 implements Runnable {
    //共有的
    private Integer ticketNumber = 20;

    @Override
    public void run() {
        while (true) {
            if (ticketNumber > 0) {
                System.out.println(Thread.currentThread().getName() + "正在卖"+ticketNumber+"票");
                ticketNumber--;
            }
        }
    }
}
```

**分析：上面的代码，因为出现多个进程共享同个资源的问题，所以会有线程安全问题**

改造资源类，加入分布式锁：

初始化锁对象后，只要在要同步的代码块前，获取锁；在同步代码块后，释放锁就行。

```java
public class Ticket12306 implements Runnable {
    //共有的
    private Integer ticketNumber = 20;
    //1、间程间互斥锁
    private InterProcessMutex interProcessMutex;

    public Ticket12306() {

        CuratorFramework client = CuratorFrameworkFactory
                .builder()
                .connectString("192.168.58.131:2181")
                .connectionTimeoutMs(15 * 1000)
                .sessionTimeoutMs(60 * 1000)
                .retryPolicy(new ExponentialBackoffRetry(3 * 1000, 10))
                .build();
        //开启连接
        client.start();
        /**
         * 参数2是指定路径存放各临时顺序节点
         */
        //2、锁初始化
        interProcessMutex = new InterProcessMutex(client, "/lock");
    }

    @Override
    public void run() {
        while (true) {
            try {
                //3、获取分布式锁,参数是获取锁等待的时间
                interProcessMutex.acquire(5, TimeUnit.SECONDS);

                if (ticketNumber > 0) {
                    System.out.println(Thread.currentThread().getName() + "正在卖" + ticketNumber + "票");
                    //获取票后让线程睡一会
                    TimeUnit.SECONDS.sleep(3);
                    ticketNumber--;
                }

            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                //4、释放分布式锁
                try {
                    interProcessMutex.release();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

## 5、Zookeeper集群搭建

### 5.1、Zookeeper集群的介绍

**Leader选举主要看两个参数：**

​	Serverid：服务器ID（主要看这个）

​		比如有三台服务器，编号分别是1,2,3。

​		编号越大在选择算法中的权重越大。

​	Zxid：数据ID，节点的事物id（znode transaction id）

​		服务器中存放的最大数据ID.值越大说明数据越新，在选举算法中数据越新权重越大。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259670.png)

在Leader选举的过程中，如果某台ZooKeeper获得了超过半数的选票，则此ZooKeeper就可以成为Leader了。

**Leader选举的过程：**

假设有5台zookeeper服务器，首先Server1先启动，自己给自己投1票，但没有过半数，没有成为leader。Server2启动，Server1因为Server2的服务器id大，所以给Server2投了一票，同理Server2也给自己投了一票，

但没有过半数，没有成为leader。Server3启动，都给Server3投票，过了半数，Server3成为Leader。其他服务器都启动成功，都是Server3的Follower。当然如果配置了Observer，则其他服务器可能是Observer,但Observer不具有投票权。

### 5.2、集群的搭建

#### **5.2.1 搭建要求**

真实的集群是需要部署在不同的服务器上的，但是在我们测试时同时启动很多个虚拟机内存会吃不消，所以我们通常会搭建**伪集群**，也就是把所有的服务都搭建在一台虚拟机上，用端口进行区分。

我们这里要求搭建一个三个节点的Zookeeper集群（伪集群）。

#### **5.2.2 准备工作**

重新部署一台虚拟机作为我们搭建集群的测试服务器。

（1）安装JDK  【此步骤省略】。

（2）Zookeeper压缩包上传到服务器
（3）将Zookeeper解压 ，建立/usr/local/zookeeper-cluster目录，将解压后的Zookeeper复制到以下三个目录

/usr/local/zookeeper-cluster/zookeeper-1

/usr/local/zookeeper-cluster/zookeeper-2

/usr/local/zookeeper-cluster/zookeeper-3

```shell
[root@localhost ~]# mkdir /usr/local/zookeeper-cluster
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-1
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-2
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-3
```

（4）创建data目录 ，并且将 conf下zoo_sample.cfg 文件改名为 zoo.cfg

```shell
mkdir /usr/local/zookeeper-cluster/zookeeper-1/data
mkdir /usr/local/zookeeper-cluster/zookeeper-2/data
mkdir /usr/local/zookeeper-cluster/zookeeper-3/data

mv  /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg
mv  /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg
mv  /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg
```


（5） 配置每一个Zookeeper 的dataDir 和 clientPort 分别为2181  2182  2183

修改/usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg

clientPort=2181
dataDir=/usr/local/zookeeper-cluster/zookeeper-1/data
```

修改/usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg

clientPort=2182
dataDir=/usr/local/zookeeper-cluster/zookeeper-2/data
```

修改/usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

clientPort=2183
dataDir=/usr/local/zookeeper-cluster/zookeeper-3/data
```


#### **5.2.3 配置集群**

（1）在每个zookeeper的 data 目录下创建一个 myid 文件，内容分别是1、2、3 。这个文件就是记录每个服务器的ID

```shell
echo "1" >/usr/local/zookeeper-cluster/zookeeper-1/data/myid
echo "2" >/usr/local/zookeeper-cluster/zookeeper-2/data/myid
echo "3" >/usr/local/zookeeper-cluster/zookeeper-3/data/myid
```

（2）在每一个zookeeper 的 zoo.cfg配置客户端访问端口（clientPort）和集群服务器IP列表。

集群服务器IP列表如下

```shell
vim /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg
vim /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg
vim /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

server.1=192.168.149.135:2881:3881
server.2=192.168.149.135:2882:3882
server.3=192.168.149.135:2883:3883

#如果是真集群：
server.1=192.168.149.135:2888:3888
server.2=192.168.149.135:2888:3888
server.3=192.168.149.135:2888:3888
```

**解释：server.服务器ID=服务器IP地址：服务器之间通信端口：服务器之间投票选举端口**

**2181是默认客户端访问服务器的端口，2888是默认服务器间通信端口，3888是默认服务器间选举端口**

#### **5.2.4 启动集群**

启动集群就是分别启动每个实例。

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh start
```

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259671.jpg) 

启动后我们查询一下每个实例的运行状态

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status
```

先查询第一个服务

![img](images\wps12.jpg) 

Mode为follower表示是**跟随者**（从）

再查询第二个服务Mod 为leader表示是**领导者**（主）

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259672.jpg) 

查询第三个为跟随者（从）

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259673.jpg) 

#### **5.2.5 模拟集群异常**

（1）首先我们先测试如果是从服务器挂掉，会怎么样

把3号服务器停掉，观察1号和2号，发现状态并没有变化

```shell
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259674.jpg) 

由此得出结论，3个节点的集群，从服务器挂掉，集群正常（2号机主，1号机从）

（2）我们再把1号服务器（从服务器）也停掉，查看2号（主服务器）的状态，发现已经停止运行了。

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259675.jpg) 

**由此得出结论，3个节点的集群，2个从服务器都挂掉，主服务器也无法运行。因为可运行的机器没有超过集群总数量的半数。**

**即：投票要过半，运行也要过半**

（3）我们再次把1号服务器启动起来，发现2号服务器又开始正常工作了。而且依然是领导者。

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh start

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259676.jpg) 

（4）我们把3号服务器也启动起来，把2号服务器停掉,停掉后观察1号和3号的状态。

```shell
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status
```

![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259677.jpg) 

发现新的leader产生了~  

由此我们得出结论，当集群中的主服务器挂了，集群中的其他服务器会自动进行选举状态，然后产生新得leader 

（5）我们再次测试，当我们把2号服务器重新启动起来启动后，会发生什么？2号服务器会再次成为新的领导吗？我们看结果

```shell
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh start

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status
```

![img](images/wps19.jpg)![img](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259678.jpg) 

我们会发现，2号服务器启动后依然是跟随者（从服务器），3号服务器依然是领导者（主服务器），没有撼动3号服务器的领导地位。

由此我们得出结论，当领导者产生后，再次有新服务器加入集群，不会影响到现任领导者。

### 5.3、Zookeepr集群核心理论

在ZooKeeper集群服中务中有三个角色：

Leader 领导者 ：          

1. 处理事务请求（增删改）

2. 集群内部各服务器的调度者

Follower 跟随者 ：

1. 处理客户端非事务请求（查询），转发事务请求给Leader服务器

2. 参与Leader选举投票

Observer 观察者：

1. 处理客户端非事务请求，转发事务请求给Leader服务器
2. **不参与Leader选择投票**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042259679.png)

## 7.常见异常

7.1如果指定CuratorFramework的对象的名称空间时，不能加/，不然会报错，无效的名称空间

```properties
java.lang.IllegalArgumentException: Invalid namespace: /myApp
```

7.2创建CuratorFramework的对象后要开始才能使用，不然报错，实例要被启动

```bm properties
java.lang.IllegalStateException: instance must be started before calling this method
```

7.3使用分布式锁时，出现非法监听器状态异常，你没有获取锁

可能没有连接上zookeeper服务器，需要client.star();开启连接

可能没加等待时间，进程操作共享资源，如果其他资源尝试获取锁，但没有获取到就会报没有获取锁，非法的监视器异常，这时候需要暂停一下协调时间，再释放

```properties
java.lang.IllegalMonitorStateException: You do not own the lock: /lock
```

