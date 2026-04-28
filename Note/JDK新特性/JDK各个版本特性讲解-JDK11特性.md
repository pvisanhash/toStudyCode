# JDK各个版本特性讲解-JDK11特性

> lecture：波哥


# 一、JAVA11 概述

&emsp;&emsp;2018年9月26日,Oracle官方发布JAVA11.这是JAVA大版本周期变化后的第一个长期支持版本,非常值得关注.最新发布的JAVA11将带来ZGC HttpClient等重要特性,一共17个需要我们关注的JEP,参考文档[http://openjdk.java.net/projects/jdk/11/](http://openjdk.java.net/projects/jdk/11/)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523273.png)

具体的特性介绍

* 181:基于嵌套的访问控制
* 309:动态类文件常量
* 315:改进Aarch64 Intrinsics
* 318:Epsilon:一个无操作的垃圾收集器
* 320:移除Java EE和CORBA模块
* 321:HTTP客户端(标准)
* 323:本地变量语法Lambda参数
* 324:与Curve25519和Curve448的密钥一致
* 327: Unicode 10
* 328:飞行记录器
* 329: ChaCha20和Poly1305密码算法
* 330:启动单文件源代码程序
* 331:低开销堆分析
* 332: TLS (Transport Layer Security) 1.3
* 333:ZGC:一个可伸缩的低延迟垃圾收集器  (实验)
* 335:已弃用Nashorn JavaScript引擎
* 336:已弃用Pack200工具和API

# 二、语法层次的变化

## 1. 局部变量类型推断升级

&emsp;&emsp;局部变量类型推断是java10开始新增的新特性,java11中对局部变量推断进行了升级,在var支持添加注解的语法格式,JAVA10中是无法实现的,在JAVA11中加入了这样的语.

lambda表达式中,注解修饰变量的时候,变量的数据类型必须要写,不能省略,像下面这种写法就是错误的

```java
Consumer<String> con =(@Deprecated  t) -> System.out.println(t.toLowerCase());
```

这个时候就必须要为小括号中的参数添加数据类型,应该这样写

```java
Consumer<String> con =(@Deprecated String t) -> System.out.println(t.toLowerCase());
```

java11中,lambda表达式中的参数数据类型可以使用var,但是不能不写

```java
Consumer<String> con =(@Deprecated var t) -> System.out.println(t.toLowerCase());
```

# 三、API层次的提升

## 1. String新增的方法

&emsp;&emsp;在JDK11中对String处理新增的很多方法。简化对字符串的操作

| **描述**       | **举例**                                      |
| -------------------- | --------------------------------------------------- |
| 判断字符串是否为空白 | "  ".isBlank(); // true                             |
| 去除字符串首尾空白   | " www.boge.com ".strip(); // "www.boge.com"         |
| 去除字符串尾部空格   | " www.boge.com ".stripTrailin(); // " www.boge.com" |
| 去除字符串首部空格   | " www.boge.com ".stripLeading(); // "www.boge.com " |
| 复制字符串           | "boge".repeat(2);// "bogeboge"                      |
| 行数统计             | "A\nB\nC\nD".lines().count(); // 4                  |

```java
    public static void main(String[] args) {
        // 判断字符串是否为空白
        boolean b = " ".isBlank();// true
        // 去除字符串首尾空白
        String s1 = " www.boge.com ".strip();// "www.boge.com"
        // 去除字符串尾部空格
        String s2 = " www.boge.com ".stripTrailing();// " \n \twww.boge.com"
        // 去除字符串首部空格
        String s3 = " www.boge.com ".stripLeading();// "www.boge.com\n \t "
        // 复制字符串
        String r = "boge".repeat(2);// "bogeboge"
        // 行数统计
        long c = "A\nB\nC\nD".lines().count();// 4
        System.out.println(s1);
        System.out.println(s2);
        System.out.println(s3);
        System.out.println(r);
        System.out.println(c);
    }
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523274.png)

## 2. Optional新增方法

&emsp;&emsp;Optional也增加了几个非常好用的方法,现在可以很方便的把一个Optional转换成一个Stream,或者当一个空Optional时,给它一个替代的. 我们发现从JDK8开始出现Stream以后,每个版本都有相关的更新.

| 新增方法                                                                        | 描述                                                           | 新增版本       |
| ------------------------------------------------------------------------------- | -------------------------------------------------------------- | -------------- |
| boolean isEmpty()                                                               | 判断value是否为空                                              | JDK11          |
| T orElseThrow()                                                                 | value非空,返回value,否则抛出NoSuchElementExpception            | JDK10          |
| ifPresentOrElse(Consumer&#x3c;? super T> action,Runnable emptyAction)           | value非空,执行参数1功能,如果value为空,执行参数2功能            | JDK9           |
| Optional&#x3c;T>or(Supplier&#x3c;? extends Optional&#x3c;? extends T> supplier) | value非空,返回对应的Optional,value为空,返回形参封装的Optional  | JDK9           |
| Stream&#x3c;T>stream();                                                         | value非空,返回一个仅包含此value的Steam,否则,返回一个空的Stream | JDK**9** |

```java
    public static void main(String[] args) {
        Optional<String> optional =Optional.empty();
        //JDK8 判断value是否存在
        System.out.println(optional.isPresent());
        //JDK11 判断value是否为空
        System.out.println(optional.isEmpty());

        //JDK10 返回value,如果为null则直接抛出 NoSuchElementExpception
        Optional<String> optional2 = Optional.of("element1");
        String value = optional2.orElseThrow();
        System.out.println(value);

        //JDK9  value非空,执行参数1功能,如果value为空,执行参数2功能
        Optional<String> optional3 =Optional.empty();// Optional.of("element1");
        optional.ifPresentOrElse((v)-> System.out.println("value为"+v),()-> System.out.println("value为null"));

        // JDK9 value非空,返回对应的Optional,value为空,返回形参封装的Optional
        Optional<String> optional4 =Optional.empty();// Optional.of("element1");
        Optional<String> optional5 = optional4.or(() -> Optional.of("element2"));
        System.out.println(optional5);

        // JDK9 value非空,返回一个仅包含此value的Steam,否则,返回一个空的Stream
        Optional<String> optional6 =Optional.of("element3");//Optional.empty();
        Stream<String> stream = optional6.stream();
        stream.forEach(System.out::println);
    }
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523275.png)

## 3.HTTPClient

&emsp;&emsp;HTTP,用于传输网页的协议,在1997年就被采用1.1的版本中,到2015年,HTTP2才成为标准. HTTP1.1和HTTP2的主要区别就是如何在客户端和服务器之间构建和传输数据, HTTP1.1依赖请求/响应周期. HTTP2允许服务器push数据:它可以发送比客户端请求更多的数据.这使得他可以优先处理并发送对于首先加载网页至关重要的数据.

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523276.png)

&emsp;&emsp;JAVA9开始引入一个处理HTTP请求的HTTPClient API,该API支持同步和异步,而在JAVA11中成为正式可用状态,可以在java.net包中找到这个API,它将替代仅适用于bolocking模式的HTTPUrlConnection(创建于Http1.0s时代,并使用了协议无关的方法),并提供对WebSocket和HTTP2的支持

```java
//HttpClient 替换原有的HttpUrlConnection  同步方式
HttpClient client =HttpClient.newHttpClient();
HttpRequest request =HttpRequest.newBuilder(URI.create("http://127.0.0.1:8080/demo")).build();
HttpResponse.BodyHandler<String> respnoseBodyHandler= HttpResponse.BodyHandlers.ofString();
HttpResponse<String> response =client.send(request,respnoseBodyHandler);
String body = response.body();
System.out.println(body);
```

```java
//HttpClient 替换原有的HttpUrlConnection  异步方式
HttpClient client =HttpClient.newHttpClient();
HttpRequest request =HttpRequest.newBuilder(URI.create("http://127.0.0.1:8080/demo")).build();
HttpResponse.BodyHandler<String> respnoseBodyHandler= HttpResponse.BodyHandlers.ofString();
CompletableFuture<HttpResponse<String>> sendAsync = client.sendAsync(request, respnoseBodyHandler);
sendAsync.thenApply(t-> t.body()).thenAccept(System.out::println);
```

# 四、其他变化

## 1. 更简化的编译运行

&emsp;&emsp;JAVA11 提供了更简化的编译运行程序，编译一个java源代码文件语法应该是

```java
javac Test1.java
```

&emsp;&emsp;解释执行一个java字节码的语法应该是

```java
java Test1
```

&emsp;&emsp;在我们目前的知识里面,运行一个java源代码必须经过两个不中,一个是编译,一个是解释执行,而在java11中,通过一个java命令就可以直接搞定了,语法是:

```java
java Test1.java
```

需要注意的是:

1. 源代码文件中如果有多个类,执行源文件中的第一个类中主方法,注意这里的第一个是代码顺序的第一个,和是否由public修饰无关
2. 不可以使用其他源文件中定中自定义的类,当前文件中自定义的类是可以使用的

定义一个源代码文件进行测试

```java
public class HelloJAVA11{
    public static void main(String[] args){
        System.out.println("HelloJAVA11.main");
        // 实例化当前文件中的Person类
        Person p=new Person();
        // 实例化另一个文件中的Student类
        Student stu =new Student();
  
    }
  
}

class Person {
    private String pid;
    private String pname;
  
}
class Test2{
    public static void main(String[] args){
        System.out.println("Test2.main");
    }
}
```

```java
public class Student{
  
}
```

如果当前文件中,没有使用其他文件中的类,可以直接运行成功

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523277.png)

如果当前文件中使用类其他文件中的类,那么会出现异常

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231523278.png)

## 2.ZGC

&emsp;&emsp;GC 是java的主要优势之一(另一个是强大的JVM),永远都是java优化的一个核心点. 然而,当GC的STW(stop the world)太长,就会影响应用的响应时间. 消除或者减少GC的停顿时长,将会使JAVA对更广泛的引用场景成为一个更具有吸引力的平台. 此外,现代系统中可用内存不断增长,用户和程序员希望JVM能够以更高效的方式利用这些内存,并且无需长时间STW. ZGC  A Scalable Low-Latency Garbage Collector(Experimental).作为JDK11最瞩目的特征,但是后面带了Experimental,说明是实验版本,也就不建议在生产环境中使用.ZGC是一个并发,基于 region的压缩性垃圾收集器,只有root扫描阶段会STW,因此GC停顿时间不会随着堆的增长和存活对象的增长而变长.

优势：

* 暂停时间不会超过10ms
* 既能处理几百兆的小堆,也能处理几个T的大堆(OMG)
* 和G1相比,应用吞吐能力不会下降超过15%
* 为未来的GC功能和利用colord指针以及Load Barriers优化奠定基础
* 初始只支持64位系统

设计目标：

1. 支持TB级内存容量,暂停时间低(&#x3c;10ms),对整个程序吞吐量的影响小于15%
2. 将来还可以扩展实现机制,用以支持很多让人兴奋的功能. 如多层堆或者压缩堆

PS: 多层堆即对象置于DRAM和冷对象置于NVMe闪存

## 3.其他了解

* unicode10
* Deprecate The Pack200 Tools and API
* 新的Epsilon垃圾收集器
* 完全支持Linux容器,包括Docker
* 支持G1上的并行完全垃圾收集
* 最新的HTTPS安全协议TLS 1.3
* JAVA Flight Recoder
