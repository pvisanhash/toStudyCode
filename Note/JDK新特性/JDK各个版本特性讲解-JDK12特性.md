# JDK各个版本特性讲解-JDK12特性

# 一、JAVA12概述

&emsp;&emsp;2019年3月19日,java12正式发布了,总共有8个新的JEP(JDK Enhancement Proposals)

> JDK 12 is the open-source reference implementation of version 12 of the Java SE12
> Platform as specified by by JSR 386 in the Java Community Process.
> JDK 12 reached General Availability on 19 March 2019. Production-ready binaries under
> the GPL are available from Oracle; binaries from other vendors will follow shortly.
> The features and schedule of this release were proposed and tracked via the JEP
> Process, as amended by the JEP 2.0 proposal. The release was produced using the JDK
> Release Process(JEP 3).

JAVA12的版本特性地址：http://openjdk.java.net/projects/jdk/12/

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534103.png)

相关特性的介绍

189:Shenandoah:A Low-Pause-Time Garbage Collector(Experimental)
低暂停时间的GC http://openjdk.java.net/jeps/189
230:Microbenchmark Suite
微基准测试套件 http://openjdk.java.net/jeps/230
325:Switch Expressions(Preview)
switch表达式 http://openjdk.java.net/jeps/325
334:JVM Constants API
JVM常量API http://openjdk.java.net/jeps/334
340:One AArch64 Port,Not Two
只保留一个AArch64实现 http://openjdk.java.net/jeps/340
341:Default CDS Archives
默认类数据共享归档文件 http://openjdk.java.net/jeps/341
344:Abortable Mixed Collections for G1
可中止的G1 Mixed GC http://openjdk.java.net/jeps/344
346:Promptly Return Unused Committed Memory from G1
G1及时返回未使用的已分配内存 http://openjdk.java.net/jeps/346

# 二、语法层次的改变

## 1. switch 表达式(预览)

&emsp;&emsp;传统的switch声明语句(switch statement)在使用中有一些问题：

1. 匹配自上而下,若无break, 后面的case语句都会执行；
2. 不同的case语句定义的变量名不能重复；
3. 不能在一个case里写多个执行结果一致的条件；
4. 整个switch不能作为表达式返回值；

Java 12提供增强版的 switch 语句或称为 "switch 表达式"来写出更加简化的代码。

**什么是预览?**

&emsp;&emsp;Switch 表达式也是作为预览语言功能的第一个语言改动被引入新版 Java 中来的，这是一种引入新特性的测试版的方法。通过这种方式，能够根据用户反馈进行升级、更改.如果没有被很好的接纳，则可以完全删除该功能。预览功能的没有被包含在Java SE 规范中。也就时说: 这不是一个正式的语法,是暂时进行测试的一种语法.

**switch详细语法**

&emsp;&emsp;扩展的 switch 语句，不仅可以作为语句（statement），还可以作为表达式（expression），并且两种写法都可以,使用传统的 switch 语法，或者使用简化的“case L ->”模式匹配语法作用于不同范围并控制执行流。这些更改将简化日常编码工作，并为 switch 中的模式匹配（JEP 305）做好准备。

* 使用 Java 12 中 switch 表达式的写法，省去了 break 语句，避免了因少写 break 而出错。
* 同时将多个 case 合并到一行，显得简洁、清晰也更加优雅的表达逻辑分支，其具体写法就是将之前的 case 语句表成了：case L ->，即如果条件匹配 case ，则执行标签右侧的代码 ，同时标签右侧的代码段只能是表达式、代码块或 throw 语句。
* 为了保持兼容性，case 条件语句中依然可以使用字符 : ，这时 fall-through 规则依然有效的，即不能省略原有的 break 语句，但是同一个 switch 结构里不能混用 -> 和 : ，否则会有编译错误。并且简化后的 switch 代码块中定义的局部变量，其作用域就限制在代码块中，而不是蔓延到整个 Switch 结构，也不用根据不同的判断条件来给变量赋值。

JAVA12之前switch语法的使用：

```java
    public static void main(String[] args) {
        Month month=Month.APRIL;
        String season;
        switch (month){
            case DECEMBER:
            case JANUARY:
            case FEBRUARY:
                season="冬";
                break;
            case MARCH:
            case APRIL:
            case MAY:
                season="春";
                break;
            case JUNE:
            case JULY:
            case AUGUST:
                season="夏";
                break;
            case SEPTEMBER:
            case OCTOBER:
            case NOVEMBER:
                season="秋";
                break;
            default:
                throw new RuntimeException("NoSuchMonthException");
        }
        System.out.println(season);
    }
```

JAVA12之后，switch语法的用法:

```java
    public static void main(String[] args) {
        Month month=Month.APRIL;
        String season;
        switch (month){
            case DECEMBER,JANUARY,FEBRUARY ->season="冬";
            case MARCH,APRIL,MAY -> season="春";
            case JUNE,JULY,AUGUST -> season="夏";
            case SEPTEMBER,OCTOBER,NOVEMBER -> season="秋";
            default -> throw new RuntimeException("无效数据");
        }
        System.out.println(season);
    }
```

&emsp;&emsp;似乎可以看出,JAVA开发者或将逐渐的从复杂繁琐的底层抽象代码的编写中解放出来,编写一些更高层次更优雅的代码. 减少出错,提高开发效率. 目前switch表达式支持下面的数据类型, byte char short int Byte, Character,Short,Integer,enum,String,未来是否会支持 float double和long? 目前本版本未对支持的数据类型进行拓展.

# 三、API层次的改变

## 1.支持数字压缩格式化

&emsp;&emsp;NumberFormat 添加了对以紧凑形式格式化数字的支持。紧凑数字格式是指以简短或人类可读形式表示的数字。例如，在en_US语言环境中，1000可以格式化为“1K”，1000000可以格式化为“1M”，具体取决于指定的样式NumberFormat.Style。

```java
var cnf = NumberFormat.getCompactNumberInstance(Locale.CHINA,
NumberFormat.Style.SHORT);
System.out.println(cnf.format(1_0000));
System.out.println(cnf.format(1_9200));
System.out.println(cnf.format(1_000_000));
System.out.println(cnf.format(1L << 30));
System.out.println(cnf.format(1L << 40));
System.out.println(cnf.format(1L << 50));
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534104.png)

## 2. String新方法

String#transform(Function) : 它提供的函数作为输入提供给特定的String实例，并返回该函数返回的输出。

```java
    public static void main(String[] args) {
        var result = "波波".transform(input -> input + "烤鸭");
        System.out.println(result); //波波烤鸭

        result = "bobo"
                .transform(input -> input + " kaoya")
                .transform(String::toUpperCase);
        System.out.println(result); 
    }
```

输出结果：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534105.png)

transform源码分析：

```java

    /**
     * This method allows the application of a function to {@code this}
     * string. The function should expect a single String argument
     * and produce an {@code R} result.
     * <p>
     * Any exception thrown by {@code f.apply()} will be propagated to the
     * caller.
     *
     * @param f    a function to apply
     *
     * @param <R>  the type of the result
     *
     * @return     the result of applying the function to this string
     *
     * @see java.util.function.Function
     *
     * @since 12
     */
    public <R> R transform(Function<? super String, ? extends R> f) {
        return f.apply(this);
    }
```

&emsp;&emsp;传入一个函数式接口 Function，接受一个值，返回一个值，连续调用transform方法,对字符串进行连续三次的改变.

```java
    public static void main(String[] args) {
        List<String> list1 = List.of("Java", " Golang", " Python ");
        List<String> list2 = new ArrayList<>();
        list1.forEach(element -> list2.add(element.transform(String::strip)
                .transform(String::toUpperCase)
                .transform((e) -> "Hello," + e))
        );
        list2.forEach(System.out::println);
    }
```

结果为:

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534106.png)

&emsp;&emsp;感觉和StreamAPI中的map功能和语法上有些类似,但是这里毕竟不是Stream,StreamAPI如果对元素进行改变使用map方法完成上述功能

```java
    public static void main(String[] args) {
        List<String> list1 = List.of("Java ", " Golang", " Python ");
        Stream<String> stringStream = list1.stream().map(element ->
                element.strip()).map(String::toUpperCase).map(element -> "yeah!," + element);
        List<String> list2 = stringStream.collect(Collectors.toList());
        list2.forEach(System.out::println);
    }
```

输出结果：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534107.png)

String中的indent方法：该方法允许我们调整String实例的缩进。

```java
    public static void main(String[] args) {
        String result = "Java\nGolang\nPython".indent(3);
        System.out.println(result);
    }
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534108.png)

换行符 \n 后向前缩进 n 个空格，为 0 或负数不缩进。

indent源码:

```java
    public String indent(int n) {
        if (isEmpty()) {
            return "";
        }
        Stream<String> stream = lines();
        if (n > 0) {
            final String spaces = " ".repeat(n);
            stream = stream.map(s -> spaces + s);
        } else if (n == Integer.MIN_VALUE) {
            stream = stream.map(s -> s.stripLeading());
        } else if (n < 0) {
            stream = stream.map(s -> s.substring(Math.min(-n, s.indexOfNonWhitespace())));
        }
        return stream.collect(Collectors.joining("\n", "", "\n"));
    }
```

## 3.Files新增mismatch方法

mismatch方法：对比两个文件的差异,返回从哪个字节开始出现了不一致

```java
FileWriter fileWriter = new FileWriter("d:/a.txt");
fileWriter.write("a");
fileWriter.write("b");
fileWriter.write("c");
fileWriter.close();
FileWriter fileWriterB = new FileWriter("d:/b.txt");
fileWriterB.write("a");
fileWriterB.write("1");
fileWriterB.write("c");
fileWriterB.close();
System.out.println(Files.mismatch(Path.of("d:/a.txt"),Path.of("d:/b.txt")));
```

# 四、关于GC的特性

## 1.Shenandoah GC

&emsp;&emsp;Shenandoah GC：低停顿时间的GC（预览）:Shenandoah 垃圾回收器是 Red Hat 在 2014 年宣布进行的一项垃圾收集器研究项目 Pauseless GC 的实现，旨在针对 JVM 上的内存收回实现低停顿的需求。该设计将与应用程序线程并发，通过交换 CPU 并发周期和空间以改善停顿时间，使得垃圾回收器执行线程能够在 Java 线程运行时进行堆压缩，并且标记和整理能够同时进行，因此避免了在大多数 JVM 垃圾收集器中所遇到的问题。据 Red Hat 研发 Shenandoah 团队对外宣称，Shenandoah 垃圾回收器的暂停时间与堆大小无关，这意味着无论将堆设置为 200 MB 还是 200 GB，都将拥有一致的系统暂停时间，不过实际使用性能将取决于实际工作堆的大小和工作负载。与其他 Pauseless GC 类似，Shenandoah GC 主要目标是 99.9% 的暂停小于 10ms，暂停与堆大小无关等。这是一个实验性功能，不包含在默认（Oracle）的OpenJDK版本中。

**STW stop the world**

**Stop-the-World ，简称STW ，指的是GC 事件发生过程中，停止所有的应用程序线程的执行.**

&emsp;&emsp;垃圾回收器的任务是识别和回收垃圾对象进行内存清理。垃圾回收要求系统进入一个停顿的状态。停顿的目的是终止所有应用程序的执行，这样才不会有新的垃圾产生，同时保证了系统状态在某一个瞬间的一致性，并且有益于垃圾回收器更好地标记垃圾对象。**停顿产生时整个应用程序会被暂停，没有任何响应，有点像卡死的感觉，这个停顿称为STW 。**
&emsp;&emsp;如果Stop-the- World 出现在新生代的Minor GC 中时， 由于新生代的内存空间通常都比较小，所以暂停的时间较短,在可接受的范围内，老年代的Full GC 中时，程序的工作线程被暂停的时间将会更久。内存空间越大，执行Full GC 的时间就会越久，工作线程被暂停的时间也就会更长。**到目前为止，哪怕是G1 也不能完全避免Stop-the-world 情况发生，只能说垃圾回收器越来越优秀，尽可能地缩短了暂停时间。**

java垃圾收集器的分类  常见的Serial(串行) ParNEW(并行)  Parallel(吞吐优先并行) CMS(低延迟) G1(区域化分代),不同的垃圾收集器都有自己的特征,简单进行一个分类

按线程数分类:

1、串行垃圾回收器

&emsp;&emsp;串行回收指的是在同一时间段内只允许一件事情发生，简单来说，当多个CPU 可用时，也只能有一个CPU
用于执行垃圾回收操作，井且在执行垃圾回收时，程序中的工作线程将会被暂停，当垃圾收集工作完成后
才会恢复之前被暂停的工作线程，这就是串行回收。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534109.png)

2、并行垃圾回收器

&emsp;&emsp;和串行回收相反，并行收集可以运用多个CPU 同时执行垃圾回收，因此提升了应用的吞吐量，不过并行回
收仍然与串行回收一样，采用独占式，使用了“ Stop-the-world ”机制和复制算法

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534110.png)

按照工作模式分类:

1 并发式垃圾收集器

&emsp;&emsp;并发式垃圾回收器与应用程序线程交替工作，以尽可能减少应用程序的停顿时间。

2 独占式垃圾收集器

&emsp;&emsp;独占式垃圾回收器（ Stop the world)一旦运行，就停止应用程序中的其他所有线程，直到垃圾回收过程完
全结束。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534111.png)

按照碎片处理方式:

> 1、压缩式垃圾回收器;:压缩式垃圾回收器会在回收完成后，对存活对象进行压缩整理，消除回收后的碎片。
>
> 2、非压缩式垃圾回收器

非压缩式的垃圾回收器不进行这步操作。

按照工作的内存区间：

> 1、年轻代垃圾回收器
>
> 2、老年代垃圾回收器

如何判断垃圾回收器的性能:

* 吞吐量：程序的运行时间（程序的运行时间＋内存回收的时间）。
* 垃圾收集开销：吞吐量的补数，垃圾收集器所占时间与总时间的比例。
* 暂停时间：执行垃圾收集时，程序的工作线程被暂停的时间,就是用户功能延迟的时间。
* 收集频率：相对于应用程序的执行，收集操作发生的频率。
* 堆空间： Java 堆区所占的内存大小。
* 快速： 一个对象从诞生到被回收所经历的时间。

&emsp;&emsp;垃圾收集器中吞吐量和低延迟这两个目标本身是相互矛盾的，因为如果选择以吞吐量优先(单位时间希望运行更多的应用程序)，那么必然需要降低内存回收的执行频率，但是这样会导致GC 需要更长的暂停时间来执行内存回收。相反的，如果选择以低延迟优先为原则，那么为了降低每次执行内存回收时的暂停时间，也只能频繁地执行内存回收，但这又引起了年轻代内存的缩减和导致程序吞吐量的下降。

**Shenandoah工作原理：**

从原理的角度，我们可以参考该项目官方的示意图，其内存结构与 G1 非常相似，都是将内存划分为类似棋盘的
region。整体流程与 G1 也是比较相似的，最大的区别在于实现了并发的 疏散(Evacuation) 环节，引入的 Brooks Forwarding Pointer 技术使得 GC 在移动对象时，对象引用仍然可以访问。

G1 内存设计：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534112.png)

Shenandoah GC 工作周期如下所示：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534113.png)

1. Init Mark 启动并发标记阶段
2. 并发标记遍历堆阶段
3. 并发标记完成阶段
4. 并发整理回收无活动区域阶段
5. 并发 Evacuation 整理内存区域阶段
6. Init Update Refs 更新引用初始化 阶段
7. 并发更新引用阶段
8. Final Update Refs 完成引用更新阶段
9. 并发回收无引用区域阶段

&emsp;&emsp;了解 Shenandoah GC 的人比较少，提及比较多的是 Oracle 在 JDK11 中开源出来的 ZGC，或者商业版本的 Azul C4（Continuously Concurrent Compacting Collector）。也有人认为Shenandoah 其实际意义大于后两者,原因有一下几点：

1. 使用 ZGC 的最低门槛是升级到 JDK11，版本的更新不是一件容易的事情,而且 ZGC 实际表现如何也是尚不清楚。
2. C4 成本较高,很多企业甚至斤斤计较几百元的软件成本。
3. Shenandoah GC 可是有稳定的 JDK8u 版本发布的。甚至已经有公司在 HBase 等高实时性产品中有较多的实践。
4. ZGC也是面向low-pause-time的垃圾收集器，不过ZGC是基于colored pointers来实现，而Shenandoah GC是
5. 基于brooks pointers来实现。

&emsp;&emsp;不是唯有 GC 停顿可能导致常规应用程序响应时间比较长。具有较长的 GC 停顿时间会导致系统响应慢的问题，但响应时间慢并非一定是 GC 停顿时间长导致的，队列延迟、网络延迟、其他依赖服务延迟和操作提供调度程序抖动等都可能导致响应变慢。使用 Shenandoah 时需要全面了解系统运行情况，综合分析系统响应时间。下面是jbb15benchmark 中，Shenandoah GC 相对于其他主流 GC 的表现。

各种 GC 工作负载对比：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534114.png)

&emsp;&emsp;GC 暂停相比于 CMS 等选择有数量级程度的提高，对于 GC 暂停非常敏感的场景，价值还是很明显的，能够在 SLA层面有显著提高。当然，这种对于低延迟的保证，也是以消耗 CPU 等计算资源为代价的，实际吞吐量表现也不是非常明朗，需要看企业的实际场景需求，并不是一个一劳永逸的解决方案。

```xml
-XX:+AlwaysPreTouch：使用所有可用的内存分页，减少系统运行停顿，为避免运行时性能损失。
  
-Xmx == -Xmsv：设置初始堆大小与最大值一致，可以减轻伸缩堆大小带来的压力，与 AlwaysPreTouch 参数配
合使用，在启动时提交所有内存，避免在最终使用中出现系统停顿。
  
-XX:+ UseTransparentHugePages：能够大大提高大堆的性能，同时建议在 Linux 上使用时将
/sys/kernel/mm/transparent_hugepage/enabled 和
/sys/kernel/mm/transparent_hugepage/defragv 设置为：madvise，同时与 AlwaysPreTouch 一起使
用时，init 和 shutdownv 速度会更快，因为它将使用更大的页面进行预处理。
  
-XX:+UseNUMA：虽然 Shenandoah 尚未明确支持 NUMA（Non-Uniform Memory Access），但最好启用此功
能以在多插槽主机上启用 NUMA 交错。与 AlwaysPreTouch 相结合，它提供了比默认配置更好的性能。
  
-XX:+DisableExplicitGC：忽略代码中的 System.gc() 调用。当用户在代码中调用 System.gc() 时会强制
Shenandoah 执行 STW Full GC ，应禁用它以防止执行此操作，另外还可以使用 
  
-XX:+ExplicitGCInvokesConcurrent，在 调用 System.gc() 时执行 CMS GC 而不是 Full GC，建议在有
System.gc() 调用的情况下使用。
不过目前 Shenandoah 垃圾回收器还被标记为实验项目，如果要使用Shenandoah GC需要编译时--with-jvmfeatures
选项带有shenandoahgc，然后启动时使用参数
  
-XX:+UnlockExperimentalVMOptions -XX:+UseShenandoahGC
```

## 2.可中断的 G1 Mixed GC

&emsp;&emsp;当 G1 垃圾回收器的回收超过暂停时间的目标，则能中止垃圾回收过程。

&emsp;&emsp;G1是一个垃圾收集器，设计用于具有大量内存的多处理器机器。由于它提高了性能效率，G1垃圾收集器最终将取代
&emsp;&emsp;CMS垃圾收集器。该垃圾收集器设计的主要目标之一是满足用户设置的预期的 JVM 停顿时间。

&emsp;&emsp;G1 采用一个高级分析引擎来选择在收集期间要处理的工作量，此选择过程的结果是一组称为 GC 回收集（collection set( CSet )）的区域。一旦收集器确定了 GC 回收集 并且 GC 回收、整理工作已经开始，这个过程是without stopping的，即 G1 收集器必须完成收集集合的所有区域中的所有活动对象之后才能停止；但是如果收集器选择过大的 GC 回收集，此时的STW时间会过长超出目标pause time。

&emsp;&emsp;这种情况在mixed collections时候比较明显。这个特性启动了一个机制，当选择了一个比较大的collection set，Java 12 中将把 GC 回收集（混合收集集合）拆分为mandatory（必需或强制）及optional两部分( 当完成mandatory的部分，如果还有剩余时间则会去处理optional部分)来将mixed collections从without stopping变为abortable，以更好满足指定pause time的目标。

* 其中必需处理的部分包括 G1 垃圾收集器不能递增处理的 GC 回收集的部分（如：年轻代），同时也可以包含老年代以提高处理效率。
* 将 GC 回收集拆分为必需和可选部分时，垃圾收集过程优先处理必需部分。同时，需要为可选 GC 回收集部分维护一些其他数据，这会产生轻微的 CPU 开销，但小于 1 ％的变化，同时在 G1 回收器处理 GC 回收集期间，本机内存使用率也可能会增加，使用上述情况只适用于包含可选 GC 回收部分的 GC 混合回收集合。
* 在 G1 垃圾回收器完成收集需要必需回收的部分之后，如果还有时间的话，便开始收集可选的部分。但是粗粒度的处理，可选部分的处理粒度取决于剩余的时间，一次只能处理可选部分的一个子集区域。在完成可选收集部分的收集后，G1 垃圾回收器可以根据剩余时间决定是否停止收集。如果在处理完必需处理的部分后，剩余时间不足，总时间花销接近预期时间，G1 垃圾回收器也可以中止可选部分的回收以达到满足预期停顿时间的目标。

## 3.增强G1

### G1概述

&emsp;&emsp;上面介绍了 Java 12 中增强了 G1 垃圾收集器关于混合收集集合的处理策略，这节主要介绍在 Java 12 中同时也对 G1垃圾回收器进行了改进，**使其能够在空闲时自动将 Java 堆内存返还给操作系统**，这也是 Java 12 中的另外一项重大改进。

&emsp;&emsp;目前 Java 11 版本中包含的 G1 垃圾收集器暂时无法及时将已提交的 Java 堆内存返回给操作系统。为什么呢？ G1目前只有在full GC或者concurrent cycle（并发处理周期）的时候才会归还内存，由于这两个场景都是G1极力避免的，
&emsp;&emsp;因此在大多数场景下可能不会及时归还committed Java heap memory给操作系统。除非有外部强制执行。

&emsp;&emsp;在使用云平台的容器环境中，这种不利之处特别明显。即使在虚拟机不活动，但如果仍然使用其分配的内存资源，哪怕是其中的一小部分，G1 回收器也仍将保留所有已分配的 Java 堆内存。而这将导致用户需要始终为所有资源付费，
&emsp;&emsp;哪怕是实际并未用到，而云提供商也无法充分利用其硬件。如果在此期间虚拟机能够检测到 Java 堆内存的实际使用情况，并在利用空闲时间自动将 Java 堆内存返还，则两者都将受益。

### 具体操作

&emsp;&emsp;为了尽可能的向操作系统返回空闲内存，G1 垃圾收集器将在应用程序不活动期间定期生成或持续循环检查整体 Java堆使用情况，以便 G1 垃圾收集器能够更及时的将 Java 堆中不使用内存部分返还给操作系统。**对于长时间处于空闲状态的应用程序，此项改进将使 JVM 的内存利用率更加高效。而在用户控制下，可以可选地执行Full GC，以使返回的内存量最大化。

&emsp;&emsp;JDK12的这个特性新增了两个参数分别是G1 PeriodicGCInterval及G1 PeriodicGCSystemLoadThreshold，设置为0的话，表示禁用。如果应用程序为非活动状态，在下面两种情况任何一个描述下，G1 回收器会触发定期垃圾收集：

* 自上次垃圾回收完成以来已超过 G1PeriodicGCInterval ( milliseconds )， 并且此时没有正在进行的垃圾回收
  任务。如果 G1PeriodicGCInterval 值为零表示禁用快速回收内存的定期垃圾收集。
* 应用所在主机系统上执行方法 getloadavg()，默认一分钟内系统返回的平均负载值低于
  G1PeriodicGCSystemLoadThreshold指定的阈值，则触发full GC或者concurrent GC( 如果开启
  G1PeriodicGCInvokesConcurrent )，GC之后Java heap size会被重写调整，然后多余的内存将会归还给操作
  系统。如果 G1PeriodicGCSystemLoadThreshold 值为零，则此条件不生效。

&emsp;&emsp;如果不满足上述条件中的任何一个，则取消当期的定期垃圾回收。等一个 G1PeriodicGCInterval 时间周期后，将重新考虑是否执行定期垃圾回收。

&emsp;&emsp;G1 定期垃圾收集的类型根据 G1PeriodicGCInvokesConcurrent 参数的值确定：如果设置值了，G1 垃圾回收器将继续上一个或者启动一个新并发周期；如果没有设置值，则 G1 回收器将执行一个Full GC。在每次一次 GC 回收末尾，
&emsp;&emsp;G1 回收器将调整当前的 Java 堆大小，此时便有可能会将未使用内存返还给操作系统。新的 Java 堆内存大小根据现有配置确定，具体包括下列配置：- XX:MinHeapFreeRatio、-XX:MaxHeapFreeRatio、-Xms、-Xmx。

&emsp;&emsp;默认情况下，G1 回收器在定期垃圾回收期间新启动或继续上一轮并发周期，将最大限度地减少应用程序的中断。如果定期垃圾收集严重影响程序执行，则需要考虑整个系统 CPU 负载，或让用户禁用定期垃圾收集。

# 五、其他方面的特性

## 1.JVM常量API

&emsp;&emsp;Java 12 中引入 JVM 常量 API，用来更容易地对关键类文件 (key class-file) 和运行时构件（artefact）的名义描述(nominal description) 进行建模，特别是对那些从常量池加载的常量，这是一项非常技术性的变化，能够以更简单、标准的方式处理可加载常量。

&emsp;&emsp;具体来说就是java.base模块新增了java.lang.constant包。包中定义了一系列基于值的符号引用（JVMS 5.1）类型，它们能够描述每种可加载常量。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534115.png)

```java
官方api链接地址：
http://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/constant/package-summary.html

Java SE > Java SE Specifications > Java Virtual Machine Specification下的第5章：
Chapter 5. Loading, Linking, and Initializing
https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-5.html
```

&emsp;&emsp;引入了ConstantDesc接口( ClassDesc、MethodTypeDesc、MethodHandleDesc这几个接口直接继承了ConstantDesc接口)以及Constable接口；ConstantDesc接口定义了resolveConstantDesc方法，Constable接口定义了describeConstable方法；String、Integer、Long、Float、Double均实现了这两个接口，而EnumDesc实现了ConstantDesc接口

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231534116.png)

&emsp;&emsp;符号引用以纯 nominal 形式描述可加载常量，与类加载或可访问性上下文区分开。有些类可以作为自己的符号引用（例如 String）。而对于可链接常量，另外定义了一系列符号引用类型，具体包括： ClassDesc (Class 的可加载常量标称描述符) ，MethodTypeDesc(方法类型常量标称描述符) ，MethodHandleDesc (方法句柄常量标称描述符) 和DynamicConstantDesc (动态常量标称描述符) ，它们包含描述这些常量的 nominal 信息。此 API 对于操作类和方法的工具很有帮助。

ConstantDesc源码

```java
/**A nominal descriptor for a loadable constant value, as defined in JVMS 4.4. Such a descriptor can be resolved via resolveConstantDesc(MethodHandles.Lookup) to yield the constant value itself.
Class names in a nominal descriptor, like class names in the constant pool of a classfile, must be interpreted with respect to a particular class loader, which is not part of the nominal descriptor.
Static constants that are expressible natively in the constant pool (String, Integer, Long, Float, and Double) implement ConstantDesc, and serve as nominal descriptors for themselves. Native linkable constants (Class, MethodType, and MethodHandle) have counterpart ConstantDesc types: ClassDesc, MethodTypeDesc, and MethodHandleDesc. Other constants are represented by subtypes of DynamicConstantDesc.
APIs that perform generation or parsing of bytecode are encouraged to use ConstantDesc to describe the operand of an ldc instruction (including dynamic constants), the static bootstrap arguments of dynamic constants and invokedynamic instructions, and other bytecodes or classfile structures that make use of the constant pool.
Constants describing various common constants (such as ClassDesc instances for platform types) can be found in ConstantDescs.
Implementations of ConstantDesc should be immutable and their behavior should not rely on object identity.
Non-platform classes should not implement ConstantDesc directly. Instead, they should extend DynamicConstantDesc (as Enum.EnumDesc and invoke.VarHandle.VarHandleDesc do.)
Nominal descriptors should be compared using the Object.equals(Object) method. There is no guarantee that any particular entity will always be represented by the same descriptor instance.
API Note:
In the future, if the Java language permits, ConstantDesc may become a sealed interface, which would prohibit subclassing except by explicitly permitted types. Clients can assume that the following set of subtypes is exhaustive: String, Integer, Long, Float, Double, ClassDesc, MethodTypeDesc, MethodHandleDesc, and DynamicConstantDesc; this list may be extended to reflect future changes to the constant pool format as defined in JVMS 4.4.
Since:
12
See Also:
Constable, ConstantDescs
*/
public interface ConstantDesc {
    /**
     * Resolves this descriptor reflectively, emulating the resolution behavior
     * of JVMS 5.4.3 and the access control behavior of JVMS 5.4.4.  The resolution
     * and access control context is provided by the {@link MethodHandles.Lookup}
     * parameter.  No caching of the resulting value is performed.
     *
     * @param lookup The {@link MethodHandles.Lookup} to provide name resolution
     *               and access control context
     * @return the resolved constant value
     * @throws ReflectiveOperationException if a class, method, or field
     * could not be reflectively resolved in the course of resolution
     * @throws LinkageError if a linkage error occurs
     *
     * @apiNote {@linkplain MethodTypeDesc} can represent method type descriptors
     * that are not representable by {@linkplain MethodType}, such as methods with
     * more than 255 parameter slots, so attempts to resolve these may result in errors.
     *
     * @jvms 5.4.3 Resolution
     * @jvms 5.4.4 Access Control
     */
    Object resolveConstantDesc(MethodHandles.Lookup lookup) throws ReflectiveOperationException;
}
```

Constable接口源码

```java
/**
 * Represents a type which is <em>constable</em>.  A constable type is one whose
 * values are constants that can be represented in the constant pool of a Java
 * classfile as described in JVMS 4.4, and whose instances can describe themselves
 * nominally as a {@link ConstantDesc}.
 *
 * <p>Some constable types have a native representation in the constant pool:
 * {@link String}, {@link Integer}, {@link Long}, {@link Float},
 * {@link Double}, {@link Class}, {@link MethodType}, and {@link MethodHandle}.
 * The types {@link String}, {@link Integer}, {@link Long}, {@link Float},
 * and {@link Double} serve as their own nominal descriptors; {@link Class},
 * {@link MethodType}, and {@link MethodHandle} have corresponding nominal
 * descriptors {@link ClassDesc}, {@link MethodTypeDesc}, and {@link MethodHandleDesc}.
 *
 * <p>Other reference types can be constable if their instances can describe
 * themselves in nominal form as a {@link ConstantDesc}. Examples in the Java SE
 * Platform API are types that support Java language features such as {@link Enum},
 * and runtime support classes such as {@link VarHandle}.  These are typically
 * described with a {@link DynamicConstantDesc}, which describes dynamically
 * generated constants (JVMS 4.4.10).
 *
 * <p>The nominal form of an instance of a constable type is obtained via
 * {@link #describeConstable()}. A {@linkplain Constable} need
 * not be able to (or may choose not to) describe all its instances in the form of
 * a {@link ConstantDesc}; this method returns an {@link Optional} that can be
 * empty to indicate that a nominal descriptor could not be created for an instance.
 * (For example, {@link MethodHandle} will produce nominal descriptors for direct
 * method handles, but not necessarily those produced by method handle
 * combinators.)
 * @jvms 4.4 The Constant Pool
 * @jvms 4.4.10 The {@code CONSTANT_Dynamic_info} and {@code CONSTANT_InvokeDynamic_info} Structures
 *
 * @since 12
 */
public interface Constable {
    /**
     * Returns an {@link Optional} containing the nominal descriptor for this
     * instance, if one can be constructed, or an empty {@link Optional}
     * if one cannot be constructed.
     *
     * @return An {@link Optional} containing the resulting nominal descriptor,
     * or an empty {@link Optional} if one cannot be constructed.
     */
    Optional<? extends ConstantDesc> describeConstable();
}

```

String源码

```java
/**
* Returns an {@link Optional} containing the nominal descriptor for this
* instance, which is the instance itself.
*
* @return an {@link Optional} describing the {@linkplain String} instance
* @since 12
*/
@Override
public Optional<String> describeConstable() {
return Optional.of(this);
}
/**
* Resolves this instance as a {@link ConstantDesc}, the result of which is
* the instance itself.
*
* @param lookup ignored
* @return the {@linkplain String} instance
* @since 12
*/
@Override
public String resolveConstantDesc(MethodHandles.Lookup lookup) {
return this;
}
```

测试代码

```java
String name = "波波烤鸭";
Optional<String> optional = name.describeConstable();
System.out.println(optional.get());
```

测试结果

```java
波波烤鸭
```

## 2.微基准测试套件

> 什么是JMH(java微基准测试)

&emsp;&emsp;JMH，即Java Microbenchmark Harness，是专门用于代码微基准测试的工具套件。何谓Micro Benchmark呢？简单的来说就是基于方法层面的基准测试，精度可以达到微秒级。当你定位到热点方法，希望进一步优化方法性能的时候，就可以使用JMH对优化的结果进行量化的分析。

> 应用场景

1 想准确的知道某个方法需要执行多长时间，以及执行时间和输入之间的相关性；
2 对比接口不同实现在给定条件下的吞吐量；
3 查看多少百分比的请求在多长时间内完成；

> JMH使用

&emsp;&emsp;要使用JMH，首先需要准备好Maven环境,

&emsp;&emsp;如果要在现有Maven项目中使用JMH，只需要把生成出来的两个依赖以及shade插件拷贝到项目的pom中即可

```xml
<dependency>
	<groupId>org.openjdk.jmh</groupId>
	<artifactId>jmh-core</artifactId>
	<version>0.7.1</version>
</dependency>
<dependency>
	<groupId>org.openjdk.jmh</groupId>
	<artifactId>jmh-generator-annprocess</artifactId>
	<version>0.7.1</version>
	<scope>provided</scope>
</dependency>

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-shade-plugin</artifactId>
	<version>2.0</version>
	<executions>
		<execution>
			<phase>package</phase>
			<goals>
				<goal>shade</goal>
			</goals>
			<configuration>
				<finalName>microbenchmarks</finalName>
				<transformers>
   					 <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
						<mainClass>org.openjdk.jmh.Main</mainClass>
					</transformer>
				</transformers>
			</configuration>
		</execution>
	</executions>
</plugin>

```

> 新特性说明
>
> Java 12 中添加一套新的基本的微基准测试套件（microbenchmarks suite），此功能为JDK源代码添加了一套微基准测试（大约100个），**简化了现有微基准测试的运行和新基准测试的创建过程**.使开发人员可以轻松运行现有的微基准测试并创建新的基准测试，其目标在于提供一个稳定且优化过的基准。 它基于Java Microbenchmark
> Harness（JMH），可以轻松测试JDK性能，支持JMH更新。
>
> **微基准套件与 JDK 源代码位于同一个目录中，并且在构建后将生成单个 jar 文件**。但它是一个单独的项目，在支持构建期间不会执行，以方便开发人员和其他对构建微基准套件不感兴趣的人在构建时花费比较少的构建时间。
>
> 要构建微基准套件，用户需要运行命令：make build-microbenchmark， 类似的命令还有：make test TEST="micro:java.lang.invoke" 将使用默认设置运行java.lang.invoke 相关的微基准测试。

## 3.只保留一个AArch64实现

> 现状

&emsp;&emsp;当前 Java 11 及之前版本JDK中存在两个64位ARM端口。这些文件的主要来源位于src/hotspot/cpu/arm 和
open/src/hotspot/cpu/aarch64 目录中。尽管两个端口都产生了aarch64 实现，我们将前者（由Oracle贡献）称为arm64 ，将后者称为aarch64 。

> 新特征

&emsp;&emsp;Java 12 中将删除由 Oracle 提供的 arm64端口相关的所有源码，即删除目录 open/src/hotspot/cpu/arm 中关于64-bit 的这套实现，只保留其中有关 32-bit ARM端口的实现，余下目录的 open/src/hotspot/cpu/aarch64 代码部分就成了 AArch64 的默认实现。

> 目的

&emsp;&emsp;这将使开发贡献者将他们的精力集中在单个 64 位 ARM 实现上，并消除维护两套实现所需的重复工作。

## 4.默认生成类的数据共享(CDS)归档文件

> 概述

&emsp;&emsp;我们知道在同一个物理机／虚拟机上启动多个JVM时，如果每个虚拟机都单独装载自己需要的所有类，启动成本和内存占用是比较高的。所以Java团队引入了类数据共享机制 (Class Data Sharing ，简称 CDS) 的概念，通过把一些核心类在每个JVM间共享，每个JVM只需要装载自己的应用类即可。好处是：启动时间减少了，另外核心类是共享的，所以JVM的内存占用也减少了。

> 历史版本

1 JDK5引入了Class-Data Sharing可以用于多个JVM共享class，提升启动速度，最早只支持system classes及serial GC。
2 JDK9对其进行扩展以支持application classes(自定义class)及其他GC算法。
3 java10的新特性JEP 310: Application Class-Data Sharing扩展了JDK5引入的Class-Data Sharing，支持application的Class-Data Sharing并开源出来(以前是commercial feature)CDS 只能作用于 BootClassLoader 加载的类，不能作用于 AppClassLoader 或者自定义的 ClassLoader加载的类。在 Java 10 中，则将 CDS 扩展为 AppCDS，顾名思义，AppCDS 不止能够作用于BootClassLoader了，AppClassLoader 和自定义的 ClassLoader 也都能够起作用，大大加大了 CDS 的适用范围。也就说开发自定义的类也可以装载给多个JVM共享了。
4 JDK11将-Xshare:off改为默认-Xshare:auto，以更加方便使用CDS特性

> 迭代效果

&emsp;&emsp;可以说，自 Java 8 以来，在基本 CDS 功能上进行了许多增强、改进，启用 CDS 后应用的启动时间和内存占用量显着减少。使用 Java 11 早期版本在 64 位 Linux 平台上运行 HelloWorld 进行测试，测试结果显示启动时间缩短有 32％，同时在其他 64 位平台上，也有类似或更高的启动性能提升。

> JAVA12中的新特性

&emsp;&emsp;JDK 12之前，想要利用CDS的用户，即使仅使用JDK中提供的默认类列表，也必须java -Xshare:dump 作为额外的步骤来运行。

&emsp;&emsp;Java 12 针对 64 位平台下的 JDK 构建过程进行了增强改进，使其默认生成类数据共享（CDS）归档，以进一步达到改进应用程序的启动时间的目的，同时也避免了需要手动运行：java -Xshare:dump 的需要，修改后的 JDK 将在${JAVA_HOME}/lib/server 目录中生成一份名为classes.jsa的默认archive文件(大概有18M)方便大家使用。

&emsp;&emsp;当然如果需要，也可以添加其他 GC 参数，来调整堆大小等，以获得更优的内存分布情况，同时用户也可以像之前一样创建自定义的 CDS 存档文件。

## 5.增加:支持UNICODE11

&emsp;&emsp;JDK 12版本包括对Unicode 11.0.0的支持。在发布支持Unicode 10.0.0的JDK 11之后，Unicode 11.0.0引
入了以下JDK 12中包含的新功能： 684 new characters,   11 new blocks.  7 new scripts.
其中：

684个新字符，包含以下重要内容：

* 66个表情符号字符（66 emoji characters）
* Copyleft符号（Copyleft symbol）
* 评级系统的半星（Half stars for rating systems）
* 额外的占星符号（Additional astrological symbols）
* 象棋中国象棋符号（Xiangqi Chinese chess symbols）

7个新脚本：

* Hanifi Rohingya
* Old Sogdian
* Sogdian
* Dogra
* Gunjala Gondi
* Makasar
* Medefaidrin

11个新块，包括上面列出的新脚本的7个块和以下现有脚本的4个块：

* 格鲁吉亚扩展（Georgian Extended）
* 玛雅数字（Mayan Numerals）
* 印度Siyaq数字（Indic Siyaq Numbers）
* 国际象棋符号（Chess Symbols）

## 6.其他新增

* Collectors新增teeing方法用于聚合两个downstream的结果
* CompletionStage新增exceptionallyAsync、exceptionallyComposeAsync方法，允许方法体在异步线程执行，同时新增了exceptionallyCompose方法支持在exceptionally的时候构建新的CompletionStage。
* ZGC: Concurrent Class Unloading

  * ZGC在JDK11的时候还不支持class unloading，JDK12对ZGC支持了Concurrent Class Unloading，默认是开启，使用-XX:-ClassUnloading可以禁用
* 新增-XX:+ExtensiveErrorReports

  * -XX:+ExtensiveErrorReports可以用于在jvm crash的时候收集更多的报告信息到hs_err.log文件中，product builds中默认是关闭的，要开启的话，需要自己添加-XX:+ExtensiveErrorReports参数
* 新增安全相关的改进

  * 支持java.security.manager系统属性，当设置为disallow的时候，则不使用SecurityManager以提升性能，如果此时调用System.setSecurityManager则会抛出UnsupportedOperationExceptionkeytool新增-groupname选项允许在生成key pair的时候指定一个named group新增PKCS12 KeyStore配置属性用于自定义PKCS12 keystores的生成Java Flight Recorder新增了security-related的event支持ChaCha20 and Poly1305 TLS Cipher Suites

## 7.移除项

移除com.sun.awt.SecurityWarnin；
移除FileInputStream、FileOutputStream、- Java.util.ZipFile/Inflator/Deflator的finalize方法；
移除GTE CyberTrust Global Root；
移除javac的-source, -target对6及1.6的支持，同时移除--release选项；

## 8.废弃项

废弃的API列表见deprecated-list
废弃-XX:+/-MonitorInUseLists选项
废弃Default Keytool的-keyalg值
