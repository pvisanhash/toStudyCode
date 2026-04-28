# JDK各个版本特性讲解-JDK15特性

# 一、JAVA15概述

2020年9月15日,java15正式发布,(风平浪静的一个版本)共有14个JEP,是时间驱动形式发布的第六个版本.相关文档: [https://openjdk.java.net/projects/jdk/15/](https://openjdk.java.net/projects/jdk/15/)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231622294.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231622295.png)

# 二、语法层面的变化

## 1.密封类(预览)

> JEP360:Sealed Classes(Preview)密封的类和接口预览

通过密封的类和接口来增强Java编程语言,这是新的预览特性,用于限制超类的使用密封的类和接口限制其他可继承或者实现他们的其他类或接口.

> 目标

允许类或接口的开发者来控制那些代码负责实现,提供了比限制使用超类的访问修饰符声明方式更多选择,并通过支持对模式的详尽分析而支持模式匹配的未来发展

在java中,类层次构造通过集成实现代码的重用,父类的方法可以被许多子类继承.但是,类层次接口的目的并不总是重用代码.有时是对域中存在的各种可能性进行建模,例如图形库支持函的形状类型.当以这种方式使用类层次结构是,我们可能需要限制子类集从而简化建模.

虽然我们可以通过final来限定子类继承,但是这是绝对杜绝类子类,而类的密封是允许子类,但是限定是那个或者哪些.

> 具体方式

引入 Seald class或interface,这些class或者interface只允许被指定的类或者interface进行扩展和实现

使用修饰符sealed,我们可以将一个类声明为密封类.密封类使用reserved关键字permits列出可以直接扩展他的类.子类可以是最终的,非密封或者密封的

> 示例代码

```java

public class TestSealedClass {
}
/*sealed 对Person类进行密封
* permits 指明哪些类可以继承
* 子类必须是final修饰的或者也是密封的
* 如果子类不想被密封,可以使用non-sealed修饰
* */
sealed class Person permits Worker,Teacher,Cook,Boss,Employee,Student {}
final class Cook              extends Person{}
final class Boss              extends Person{}
final class Employee          extends Person{}
final class Teacher           extends Person{}
// 密封的子类允许继续有子类
sealed class Student          extends Person permits PrimaryStudent,GraduateStudent{}
final class PrimaryStudent    extends Student{}
final class GraduateStudent   extends Student{}
// 通过non-sealed取消子类密封
non-sealed class Worker       extends Person{}
class CarWorker               extends Worker{}
```

> 密封接口 指定实现类的接口

```java
public class Test2 {
}

/*
* 只有接口可以继承接口
* 一个接口可以同时继承多个接口
* final不能修饰接口,密封接口在被继承时,子接口要么使用 sealed non sealed  修饰
* */
sealed interface  Myinter1 permits Myinter3{}
sealed interface  Myinter2 permits Myinter3 {}
sealed interface  Myinter3 extends Myinter1,Myinter2{}
non-sealed class MyImpl implements Myinter3{}

sealed interface I permits A,B,C  {}
final class A implements I{}
sealed class B implements I{}
non-sealed class C implements I{}

final class D extends B{}
```

密封接口不可以使用匿名内部类进行实现

> 密封列与接口和模式匹配问题

```java
public class TestSealedClass {
    public static void main(String[] args) {
        test(new C());
    }
    public static void test(C c){
        if( c instanceof I){
            System.out.println( "it is an i");
        }else{
            System.out.println("it is not i");
        }
    }

}
interface I{

}
sealed class C implements I permits D,E{}
non-sealed class D extends C{}
final class E extends C {}
// 密封类仅仅是控制类的继承和实现关系,不会影响我们的模式匹配
```

> 密封接口和records

record是隐匿式的final,可以直接实现密封接口

```java
package com.msb.test2;

public class TestRecords {
    public static void main(String[] args) {
        MyInter1 myInter1=new Person(10,"旋涡刘能");
   
    }
}

sealed interface  MyInter1{
    public void eat();
}

/*record 默认继承的 java.lang.Record
* record可以直接实现密封接口,不需要用sealed 修饰 non-sealed 修饰
* record本身是隐式的final修饰
* 
* */

record Person(Integer pid,String pname)  implements MyInter1 {
    @Override
    public void eat() {
  
    }
}
record Student(Integer pid,String pname) implements MyInter1{
    @Override
    public void eat() {
  
    }
}
record Cook(Integer pid,String pname) implements MyInter1{
    @Override
    public void eat() {
  
    }
}
record Worker(Integer pid,String pname) implements MyInter1{
    @Override
    public void eat() {
  
    }
}
```

## 2.隐藏类

> JEP371 :HiddenClass(隐藏类)

该提案通过启用标准API来定义无法发现且有有限生命周期的隐藏类,从而提高JVM上所有语言的效率. JDK内部和外部的框架将能够动态生成类,而这些类可以定义隐藏类.通常来说基于JVM的很多语言都有动态生成类的机制,这样可以提高语言的灵活性和效率.

* 隐藏类天生为框架设计的,在运行时生成内部的class
* 隐藏类只能通过反射访问,不能直接被其他类的字节码访问
* 隐藏类可以独立于其他类加载,卸载,这样可以减少框架的内存占用

> 什么是Hidden Class

就是不能直接被其他class的二进制代码使用的class. 主要被一些框架用来生成运行时类,但是这些类不能被用来直接使用的,是通过反射来调用的

比如JDK8中引入的lambda表达式,编译时不会将lambda表达式转换为专门的类,而是在运行时将相应的字节码动态生成相应的类对象

另外使用动态代理也可以为某些类生成新的动态类

> 特征

我们希望这样的动态类有哪些特征呢?

* 不可发现性.因为我们是为某些静态的类动态生成的动态类,所以我们希望这个动态生成的类看作是静态类的一部分,所以我们不希望除了该静态类以外的其他机制发现
* 访问控制. 我们希望在访问控制静态类的同时,也能控制到动态生成的类
* 生命周期.动态生成类的声明周期一般都比较短. 我们不需要将其保存和静态类的生命周期一致

> API支持

因此,我们需要一些API来定义无法发现的且具有有限声明周期的隐藏类,这将有助于提高基于JVM的语言实现效率.比如

java.lang.reflect.Proxy 可以定义隐藏类作为实现代理接口的代理类

java.lang.invoke.StringConcatFactory可以生成隐藏类来保存常量连接方法

java.lang.invoke.LambdaMetaFactory可以生成隐藏的nestmate类,以容纳访问封闭变量的lambda主体

普通类是通过调用ClassLoader::defineClass创建的,而隐藏类是通过调用Lookup::defineHiddenClass创建的,这使JVM提供的字节派生一个隐藏类,链接该隐藏类,并返回提供对隐藏类的反射访问的查找对象,调用程序可以通过返回的查找对象来获取隐藏类的Class对象

## 3.instanceof模式匹配(预览)

> JAVA 14中作为预览语言功能引入instanceof模式匹配,在JAVA15中出于第二次预览,而没有任何更改,回顾JAVA14即可

## 4.Records(预览)

> Records Class 第二次预览

JDK14中引入了Records, 只用一个Records可以很方便的创建一个常量类,就是一个数据的透明持有类,简化专门用于存储数据的类的创建语法

> 当声明一个Record时,该类将自动获取的内容

* 获取成员变量的简单方法, 就是get方法,get方法将简化为成员变量同名方法
* 一个equals的实现
* 一个hashcode的实现
* 一个toString的重现
* 一个全参构造方法
* 对应声明的所有final修饰的成员变量

## 5.文本块(确定)

> JAVA13开始引入文本块,JAVA14 进行二次预览,JAVA15中成为一个正式的标准,参照JAVA14中的文本块回顾即可

# 三 关于虚拟机

## 1.ZGC功能(确定)

JEP377:ZGC :A Scalable Low_Latency Garbage Collector ZGC  功能成为正式标准

ZGC是JAVA11 引入的新的垃圾收集器,经历了多个阶段,自从终于成正式特性自2008年以来,ZGC已经增加了许多改进,并发类卸载,取消未使用的内存,对类数据实现共享的支持到NUMA感知,此外,最大的堆从4T增加到了16T,支持平台包括Linux,Windows和MacOS .ZGC 是一个重新设计的并发垃圾收集器,通过GC停顿时间来提高性能,但是这并不是替换默认的G1垃圾收集器,只不过之前需要-XX:+UnlockExperimentalVMOptions -XX:+UseZGC,现在只需要-XX:+UseZGC就可以,相信不久的将来它必然会成为默认的垃圾回收器

相关的参数有:

```java
ZAllocationSpikeTolerance ZCollectionInterval ZFragmentationLimit ZMarkStackSpaceLimit ZProcative ZUncommit ZunCommitDelay ZGC-specific JFR events(ZAllocationStall ZPageAllocation ZPageCacheFlush ZRelocationSet ZRelocationSetGroup Zuncommit ) 也从experimental变为product
```

## 2.ShenandoahGC垃圾收集算法转正

> Shenandoah垃圾回收算法终于从实验特性转变为产品特性

这是一个JAVA12引入的回收算法,该算法通过正在运行的JAVA线程同时进行疏散工作来减少GC暂停时间.Shenandoah的暂停时间与堆大小无关,无论是200M还是200G ,都具有机会一致的暂停时间.

Shenandoah 和ZGC 对比

* 相同: 性能几乎认为是相同的
* 不同: ZGC是OracleJDK的, 而Shenandoah只存在于OpenJDK中,因此使用时需要注意JDK版本

> 打开方式: 使用-XX:+UseShenandoahGC命令行参数打开

Shenandoah在JDK12作为experimental引入,在JDK15变为Production ,之前需要通过-XX:+UnlockExperimentalVMOptions -XX:+UseShenandoahGC ,现在只需要-XX:+UseShenandoahGC

# 四 其他变化

## 1.edDSA签名算法

> Edwards-Curve Digital Singnature Algorithm 数字曲线签名算法

这是一个新功能,新加基于EdWardS-Curve 数字签名算法,与JDK中现有的签名方案相比,EdDSA具有更高的安全性和性能,因此备受关注.它已经在OpenSSL和BoringSSL等加密库中得到支持,在区块链领域用的比较多.

EdDSA是一种现代椭圆曲线方案,具有JDK中现有签名方案的优点,EdDSA将只在SunECMA提供中实现

## 2.禁用偏向锁定

> jep 374 Disable and Deprecate Biased Locking 禁用偏向锁定

在默认情况下禁用偏向锁定,并弃用所有的相关命令选项.目标是确定是否需要继续支持偏置锁定的高维护成本的遗留同步优化.HotSpot虚拟机使用该优化来减少非竞争锁的开销. 尽管某些JAVA应用程序在禁用偏向锁后可能会出现性能下降,但是偏向锁的性能提高通常不像以前那么明显

该特性默认禁用了 biased locking(-XX:+UseBisaedLocking),并且废弃了所有相关的命令行选型(BiasedLockingStartupDelay,BiasedLockingBulkRebiasThreshold,BiasedLockingBulkRevokeThreshold,BiasedLockingDecayTime,UseOptoBiasInlining,PrintBisasedLockingStatistics and PrintPreciseBiasedLockingStatistics)

## 3.重新实现SocketAPI

> JEP373 Reimplement the legcy DatagramSocketAPI  重新实现DatagramSocketAPI

作为JEP353的后续,该方案重新实现了遗留的套接字API. java.net.datagram.Socket 和java.netMulticastSocket的当前实现可以追溯到JDK1.0,当时IPV6还在开发中. 因此,当前的套接字实现尝试调和IPV4和IPV6难以维护的方式.

> 具体情况

* 通过替换 java.net.datagram 的基础实现,重新实现旧版DatagramSocket API
* 更改java.net.DatagramSocket和java.net.MulticastSocket 为更加简单,现代化的底层实现,提高了JDK的可维护性和稳定性

> 新的实现

1 易于维护和调试

2 Project Loom中正在探索虚拟线程协同

## 4.外部存储API

> JEP383 Foreign-Memory Access API (Second Incubator) 外部存储器访问API

目的是引入一个API.以允许java程序安全.有效地访问JAVA对之外的外部存储器.如本机,持久和托管堆.

有许多JAVA程序访是访问外部内存的,比如 Ignite和MapDB.`该API将有助于避免与垃圾收集相关的成本以及与跨进程共享内存以及通过将文件映射到内存来序列化和返序列化内存内容相关的不可预测性`. 该java API 目前没有为访问外部内存停工令人满意的解决方案.但是在新的提议中,API不应该破坏JVM的安全性

Foreign-Memory Access API在JDK14中作为 incubating API引入,在JDK15中出于 Second Incubator,提供了改进.

## 5.废弃和移除

> 废弃

* Deprecated RMI Activation For Removal

RMI Activation(延迟激活: 延迟激活对象,推迟到客户第一次使用之前)被标记为删除,在未来的版本中将会删除,自JAVA8依赖一直是可选的,而不是必选项目. RMI激活机制增加了持续的维护负担,RMI的其他部分暂时不会被弃用.

对于现在应用程序来说. 分布式系统大部都是基于Web的,web服务器已经解决了穿越防火墙,过滤请求,身份验证和安全性问题,并且也提供了很多延迟加载的技术.所以在现代引用程序中,RMIActivation已经很少用了,并且在各种开源代码库中,也基本上找不见了

在JDK8中, RMI Activation被置为可选,JDK15 中,废弃了

* Deprecated -XX:ForceMUMA Option ,废弃了ForceNUMA选项
* Disable Native SunEC Implementation by Default 默认禁用了Native SunEC Implementation

> 移除

* Obsolete -XX:UseAdaptiveGCBoundary,淘汰了 -XX:UseAdativeGCBoundary
* 移除Solaris和SPCRC端口

近年来,Solaris和SPARC都已被Linux操作系统和英特尔处理器取代.放弃对Solaris和SPARC 端口的支持,将使OpenJDK社区的贡献者们能够加速开发新功能,从而推动平台向前发展

* 移除 Nashorn JS 引擎

Nashorn 是JDK提出的脚本执行引擎,该功能时2014年3月发布的JDK8的新特性,在JDK11就已经把它标记为废弃了,JDK15完全移除了

在JDK中取以代之的是GraalVM . GraalVM 是一个运行时平台,他支持java和其他基于java字节码的语言,但也支持其他语言,如JAVAScript Ruby Python 或者 LLVM. 性能是Nashorn 的两倍以上

JDK15 移除了Nashorn JAVAScript Engine 以及jjs命令工具,具体就是jdk.scripting.nashorn及jdk.scripting.nashorn.shell这两个模块移除了

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231622296.png)

Graal VM在hotSpot VM基础上,增强而形成的跨语言全栈虚拟机,可以作为"任何语言"的运行平台使用.
