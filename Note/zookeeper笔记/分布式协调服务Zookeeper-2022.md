# Zookeeper



​         Java 是第一大编程语言和开发平台。它有助于企业降低成本、缩短开发周期、推动创新以及改善应用服务。如今全球有数百万开发人员运行着超过 51 亿个 Java 虚拟机，Java 仍是企业和开发人员的首选开发平台 



# 课程内容的介绍

1. Zookeeper的介绍和安装
2. Zookeeper客户端使用
3. ZookeeperJavaAPI使用



# 一、Zookeeper的介绍和安装

## 1. 为什么要使用Zookeeper

​       我们为了学习Dubbo，而在dubbo中需要一个注册中心，而Zookeeper是我们在使用Dubbo是官方推荐的注册中心，所以我们先来介绍Zookeeper

![image-20210226191457919](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730883.png)



![image-20210226191530528](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730884.png)



## 2. Zookeeper介绍

![image-20210226192111730](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730885.png)





### 2.1 Zookeeper概述
  ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。
  Zookeeper是一个分布式协调服务；就是为用户的分布式应用程序提供协调服务



| 序号 | 功能                                                         |
| ---- | ------------------------------------------------------------ |
| 1    | 为别的分布式程序服务的                                       |
| 2    | 本身就是一个分布式程序                                       |
| 3    | 主从协调 服务器节点动态上下线 统一配置管理 分布式共享锁 统一名称服务 |
| 4    | 管理(存储，读取)用户程序提交的数据 并为用户程序提供数据节点监听服务 |



### 2.2 Zookeeper的集群机制
  Zookeeper是为其他分布式程序提供服务的，所以本身自己不能随便就挂了，所以zookeeper自身的集群机制就很重要。zookeeper的集群机制采用的是**半数存活机制**，也就是整个集群节点中有半数以上的节点存活，那么整个集群环境可用。这也就是说们的集群节点最好是奇数个节点。



**Zookeeper集群节点的角色**

**Leader**



Leader服务器是Zookeeper集群工作的核心，其主要工作如下

1. 事务请求的唯一调度和处理者，保证集群事务处理的顺序性。
2. 集群内部各服务器的调度者



**Follower**

  Follower是Zookeeper集群的跟随者，其主要工作如下

1. 处理客户端非事务性请求（读取数据），转发事务请求给Leader服务器。
2. 参与事务请求Proposal的投票。
3. 参与Leader选举投票。

**Observer**
  Observer充当观察者角色，观察Zookeeper集群的最新状态变化并将这些状态同步过来，其对于非事务请求可以进行独立处理，对于事务请求，则会转发给Leader服务器进行处理。Observer不会参与任何形式的投票，包括事务请求Proposal的投票和Leader选举投票



## 3. 集群环境准备

​       通过上面的介绍我们了解到zookeeper的集群环境应该配置奇数个节点，所以我们在本文中搭建的zookeeper环境准备在3个节点上搭建。接下来我们介绍下需要准备的环境。

### 3.1 准备3个节点

​     准备3个centos7.0的虚拟机节点，并且安装配置好JDK版本最好是8.不清楚的可参考此地址[Linux之jdk安装](https://blog.csdn.net/qq_38526573/article/details/86777163),并配置好相关的网络配置。



| ip              | 主机名 |
| --------------- | ------ |
| 192.168.100.120 | bobo01 |
| 192.168.100.121 | bobo02 |
| 192.168.100.122 | bobo03 |

通过VMware的克隆或者直接复制文件夹的方式来创建另外两个新的节点



### 3.2 网络配置

修改ifcfg-ens33配置文件

![image-20210228125047032](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730886.png)



重启网络服务

```shell
service network restart
```



ping测试

![image-20210228125219258](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730887.png)



三个节点的网络配置都ok的化我们就可以通过XShell来连接了

### 3.3 节点的映射关系

每个节点设置相应的ip和主机名的映射关系，方便集群环境的部署

修改hosts配置文件中的信息

![image-20210228130055348](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730888.png)



### 3.4 配置免密登录

生成公钥和私钥

```shell
ssh-keygen
```

输入命令后根据提示，四次回车即可

![image-20210228130543258](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730889.png)

发送公钥给需要免密登录的节点

```shell
ssh-copy-id bobo01
ssh-copy-id bobo02
ssh-copy-id bobo03
```



![image-20210228130833067](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730890.png)

节点和节点发送文件通过scp命令实现

```shell 
scp -r b.txt bobo01:/root/
```

![image-20210228131239060](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730891.png)



### 3.5 关闭防火墙

查看防火墙状态

```shell
firewall-cmd --state
```

停止防火墙

```shell
systemctl stop firewall.service
```

禁止开机启动

```shell
systemctl disable firewall.service
```









## 4. Zookeeper集群环境搭建

### 4.1 获取安装文件

下载地址:https://mirrors.bfsu.edu.cn/apache/zookeeper/

通过wget命令将安装文件下载到opt目录下

注意：

apache-zookeeper-3.5.9-bin.tar.gz和apache-zookeeper-3.5.9.tar.gz的区别 -bin是编译后的文件 我们用这个

```shell
wget https://mirrors.bfsu.edu.cn/apache/zookeeper/zookeeper-3.5.9/apache-zookeeper-3.5.9-bin.tar.gz
```

![image-20210228135143823](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730892.png)

解压缩文件

![image-20210228135240561](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730893.png)

进入Zookeeper目录

![image-20210228135417463](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730894.png)



### 4.2 修改配置

修改zoo.cfg文件，系统默认的名称是 zoo_smple.cfg我们需要重命名为zoo.cfg

![image-20210228135552375](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730895.png)

修改cfg的内容

修改了两块：

 1是Zookeeper中存储数据的文件夹，还有就是Zookeeper集群环境节点信息

![image-20210228135948719](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730896.png)



配置myid文件

​     我们需要在Zookeeper的数据存储的目录中创建一个myid文件，文件中的内容只有一行信息，即表示我们集群几点的标识，范围是1-255，每个节点的myid的数字和我们在zoo.cfg中配置的server.数字是对应的

```shell
1
```





### 4.3 分发文件

​     当我们配置好了一个Zookeeper节点后，我们就可以将Zookeeper文件夹分发给其他几个节点了

```shell
scp -r zookeeper bobo02:`pwd`
scp -r zookeeper bobo03:`pwd`
```

分发成功后我们需要修改各个节点中的myid的信息为配置文件中对应的数字





### 4.4 启动测试

整个集群环境都配置好了之后我们就可以测试启动了

启动命令

```shell
./bin/zkServer.sh start
```

当我们仅仅启动一个节点的时候，因为半数存活机制，3个节点只启动一个节点是没有效果的，

![image-20210228151546411](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730897.png)



当我们启动第二个节点后发现集群环境可以使用了

![image-20210228151658522](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730898.png)

然后第一个节点的状态也改变了

![image-20210228151723421](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730899.png)



然后再把第三个节点也启动起来

![image-20210228151751891](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730900.png)



## 5. Zookeeper的选举机制

为什么要进行Leader选举？
Leader 主要作用是保证分布式数据一致性，即每个节点的存储的数据同步。遇到以下两种情况需要进行Leader选举

- 服务器初始化启动
- 服务器运行期间无法和Leader保持连接，Leader节点崩溃，逻辑时钟崩溃。

### 5.1 服务器初始化时Leader选举
  Zookeeper由于其自身的性质，一般建议选取奇数个节点进行搭建分布式服务器集群。以3个节点组成的服务器集群为例，说明服务器初始化时的选举过程。启动第一台安装Zookeeper的节点时，无法单独进行选举，启动第二台时，两节点之间进行通信，开始选举Leader。

1. 每个Server投出一票。他们两都选自己为Leader，投票的内容为（SID，ZXID）。
   SID即Server的id，安装zookeeper时配置文件中所配置的myid；ZXID，事务id，
   为节点的更新程度，ZXID越大，代表Server对Znode的操作越新。由于服务器初始化，
   每个Sever上的Znode为0，所以Server1投的票为（1,0），Server2为（2,0）。
   两Server将各自投票发给集群中其他机器。

2. 每个Server接收来自其他Server的投票。集群中的每个Server先判断投票有效性，
   如检查是不是本轮的投票，是不是来Looking状态的服务器投的票。

3. 对投票结果进行处理。先了解下处理规则

 - 首先对比ZXID。ZXID大的服务器优先作为Leader
 - 若ZXID相同，比如初始化的时候，每个Server的ZXID都为0，
 - 就会比较myid，myid大的选出来做Leader。

   对于Server而言，他接受到的投票为（2,0），因为自身的票为（1,0），所以此时它会选举Server2为Leader，
   将自己的更新为（2,0）。而Server2收到的投票为Server1的（1,0）由于比他自己小，
   Server2的投票不变。Server1和Server2再次将票投出，投出的票都为（2,0）。

4. 统计投票。每次投票之后，服务器都会统计投票信息，如果判定某个Server有过半的票数投它，
   那么该Server将会作为Leader。对于Server1和Server2而言,统计出已经有两台机器接收了（2,0）的投票信息，
   此时认为选出了Leader。

5. 改变服务器状态。当确定了Leader之后，每个Server更新自己的状态，
   Leader将状态更新为Leading，Follower将状态更新为Following。



![image-20210301104123274](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730901.png)



### 5.2 服务器运行期间的Leader选举

  Zookeeper运行期间，如果有新的Server加入，或者非Leader的Server宕机，那么Leader将会同步数据到新Server或者寻找其他备用Server替代宕机的Server。若Leader宕机，此时集群暂停对外服务，开始在内部选举新的Leader。假设当前集群中有Server1、Server2、Server3三台服务器，Server2为当前集群的Leader，由于意外情况，Server2宕机了，便开始进入选举状态。过程如下



1. 变更状态。其他的非Observer服务器将自己的状态改变为Looking，开始进入Leader选举。

2. 每个Server发出一个投票（myid，ZXID），由于此集群已经运行过，所以每个Server上的ZXID可能不同。
   假设Server1的ZXID为145，Server3的为122，第一轮投票中，Server1和Server3都投自己，
   票分别为（1，145）、（3,122）,将自己的票发送给集群中所有机器。

3. 每个Server接收接收来自其他Server的投票，接下来的步骤与初始化时相同。

# 二、Zookeeper客户端使用

## 1. 配置Zookeeper的环境变量

​     为了简化我们每次操作Zookeeper而不用进入到Zookeeper的安装目录，我们可以将Zookeeper的安装信息配置到系统的环境变量中

```shell
vim /etc/profile
```

添加的内容

```shell
export ZOOKEPPER_HOME=/opt/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

执行source命令

```shell
source /etc/profile
```

我们就可以在节点的任意位置操作Zookeeper了

通过scp命令将profile文件发送到其他几个节点上

```shell
scp /etc/profile bobo02:/etc/
```



## 2.客户端连接

通过bin目录下的zkCli.sh 命令连接即可

```shell
zkCli.sh
```



![image-20210301110004402](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730902.png)



zkCli.sh默认连接的是当前节点的Zookeeper节点，如果我们要连接其他节点执行如下命令即可

```shell
zkCli.sh -timeout 5000 -server bobo02:2181
```



![image-20210301110253746](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730903.png)





## 3.数据操作

### 3.1 Zookeeper的数据结构

1. 层次化的目录结构，命名符合常规文件系统规范
2. 每个节点在Zookeeper中叫做znode，并且有一个唯一的路径标识
3. 节点znode可以包含数据和子节点（但是EPHEMERAL类型的节点不能有子节点）
4. 客户端应用可以在节点上设置监听器



![image-20210301113558950](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730904.png)



### 3.2 节点类型

**1).znode有两种类型：**

短暂性（ephemeral）（断开连接自己删除）
持久性（persistent）（断开连接不删除）

**2).znode有四种形式的目录节点（默认是persistent）如下**

| 序号 | 节点类型              | 描述         |
| ---- | --------------------- | ------------ |
| 1    | PERSISTENT            | 持久节点     |
| 2    | PERSISTENT_SEQUENTIAL | 持久有序节点 |
| 3    | EPHEMERAL             | 短暂节点     |
| 4    | EPHEMERAL_SEQUENTIAL  | 短暂有序节点 |



​         创建znode时设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，有父节点维护
在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序



### 3.3 常用命令

​       Zookeeper作为Dubbo的注册中心用来保存我们各个服务的节点信息，显示Zookeeper是可以实现输出的存储操作的，我们来看下Zookeeper中存储操作的基本命令



![image-20210301115013270](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730905.png)



**ls**

​    ls用来查看某个节点下的子节点信息

![image-20210301114751517](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730906.png)

增强的命令，查看节点下的子节点及当前节点的属性信息 ls2或者 ls -s 命令

![image-20210301114914215](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730907.png)



**create**

​     创建节点信息

![image-20210301114945149](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730908.png)



**get**

​    get命令用来查看节点的数据

![image-20210301115127622](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730909.png)

如果要查看节点的属性信息那么我们可以通过get -s 来实现

![image-20210301115623726](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730910.png)



**delete**

​    delete只能删除没有子节点的节点要删除非空节点可以通过 rmr 或者 deleteall 命令实现

![image-20210301115752831](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730911.png)

**set**

​    set命令可以用来修改节点的内容。

![image-20210301115841724](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730912.png)





### 3.4 事件监听

#### 3.4.1 数据改变的监听

​      监听某个节点的数据内容变化，通过get命令 带 -w 参数即可，在3.4版本的Zookeeper中是通过` get path watch` 来说实现监控的

![image-20210301135116352](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730913.png)



然后我们在其他节点上修改app1节点的数据，会触监听事件

![image-20210301135159833](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730914.png)



![image-20210301135216095](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730915.png)

注意监听一次节点只会触发一次，如果要实现多次监听，那么可以在触发事件的处理函数中再次追加对节点的监听操作



#### 3.4.2 子节点的改变

​     监听节点下面的子节点的改变。

```shell
[zk: localhost:2181(CONNECTED) 14] ls -w /app1
[]
```





触发

![image-20210301135538966](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730916.png)



![image-20210301135629103](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730917.png)



# 三、Zookeeper Java API使用

​    介绍如何通过Java代码来操作Zookeeper中的数据

在Zookeeper的安装目录下是提供的有相关的Jar依赖的

![image-20210301141050300](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730918.png)



但是我们对于Maven构建项目已经习惯而且是主流，那么我们可以通过maven坐标来管理

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.5.9</version>
    </dependency>
    <dependency>
        <groupId>com.github.sgroschupf</groupId>
        <artifactId>zkclient</artifactId>
        <version>0.1</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>

</dependencies>
```



## 1. API的使用

### 1.1 连接ZK服务

```java
package com.bobo.test;

import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;
import org.junit.Test;

import java.io.IOException;

public class Test1 {

    private String connectString = "192.168.100.121:2181,192.168.122:2181,192.168.100.122:2181";

    private int sessionTimeOut = 5000;

    /**
     * 连接Zookeeper服务端
     */
    @Test
    public void test1() throws IOException {
        ZooKeeper zooKeeper = new ZooKeeper(connectString, sessionTimeOut, new Watcher() {
            /**
             * 触发监听事件的回调方法
             * @param watchedEvent
             */
            @Override
            public void process(WatchedEvent watchedEvent) {
                System.out.println("触发了.....");
            }
        });
        System.out.println("--->" + zooKeeper);
    }
}

```



![image-20210301141935591](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/20260107212730919.png)





### 1.2 基本操作

```java
package com.bobo.test;

import jdk.nashorn.internal.ir.CallNode;
import org.apache.zookeeper.*;
import org.apache.zookeeper.data.Stat;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.util.List;

public class Test1 {

    private String connectString = "192.168.100.121:2181,192.168.122:2181,192.168.100.122:2181";

    private int sessionTimeOut = 10000;

    ZooKeeper zooKeeper = null;

    /**
     * 连接Zookeeper服务端
     */
    @Before
    public void test1() throws IOException {
        zooKeeper = new ZooKeeper(connectString, sessionTimeOut, new Watcher() {
            /**
             * 触发监听事件的回调方法
             * @param watchedEvent
             */
            @Override
            public void process(WatchedEvent watchedEvent) {
                System.out.println("触发了.....");
            }
        });
        //System.out.println("--->" + zooKeeper);
    }

    /**
     * 创建节点
     */
    @Test
    public void createNode() throws Exception{
        String path = zooKeeper.create("/apptest" // 节点路径
                ,"HelloZookeeper".getBytes() // 节点的数据
                , ZooDefs.Ids.OPEN_ACL_UNSAFE // 权限
        , CreateMode.PERSISTENT // 节点类型
        );
        System.out.println(path);
    }

    /**
     * 判断节点是否存在
     */
    @Test
    public void exist() throws  Exception{
        // true表示的是使用Zookeeper中的watch
        Stat stat = zooKeeper.exists("/apptest", true);
        if(stat != null){
            System.out.println("节点存在"+ stat.getNumChildren());
        }else{
            System.out.println("节点不存在 ....");
        }
    }

    /**
     * 获取某个节点下面的所有的子节点
     */
    @Test
    public void getChildrens() throws Exception{
        List<String> childrens = zooKeeper.getChildren("/app1", true);
        for (String children : childrens) {
            // System.out.println(children);
            // 获取子节点中的数据
            byte[] data = zooKeeper.getData("/app1/" + children, false, null);
            System.out.println(children+":" + new String(data));
        }
    }

    /**
     * 修改节点的内容
     */
    @Test
    public void setData() throws Exception{
        // -1 不指定版本 自动维护
        Stat stat = zooKeeper.setData("/app1/a1", "666666".getBytes(), -1);
        System.out.println(stat);
    }


    /**
     * 删除节点
     */
    @Test
    public void deleteNode() throws Exception{
        zooKeeper.delete("/app1",-1);
        
    }


}

```



### 1.3 事件监听处理

​        Java程序如何监听Zookeeper中的数据的变化？

```java

    /**
     * 监听Node节点下的子节点的变化
     */
    @Test
    public void nodeChildrenChange() throws Exception{
        List<String> list = zooKeeper.getChildren("/app1", new Watcher() {

            /**
             *              None(-1),
             *             NodeCreated(1),
             *             NodeDeleted(2),
             *             NodeDataChanged(3),
             *             NodeChildrenChanged(4),
             *             DataWatchRemoved(5),
             *             ChildWatchRemoved(6);
             * @param watchedEvent
             */
            @Override
            public void process(WatchedEvent watchedEvent) {
                System.out.println("--->"+ watchedEvent.getType());
            }
        });
        for (String s : list) {
            System.out.println(s);
        }

        Thread.sleep(Integer.MAX_VALUE);
    }

    /**
     * 监听节点内容变更
     */
    @Test
    public void nodeDataChanged() throws Exception{
        byte[] data = zooKeeper.getData("/app1/a1", new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                System.out.println("--->" + watchedEvent.getType());
            }
        }, null);
        System.out.println("--->" + new String(data));
        Thread.sleep(Integer.MAX_VALUE);
    }
```





































