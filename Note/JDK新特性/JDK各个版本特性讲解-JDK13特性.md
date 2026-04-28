# JDK各个版本特性讲解-JDK13特性

# 一、JAVA13概述

&emsp;&emsp;2019年9月17日，国际知名的OpenJDK开源社区发布了Java编程语言环境的最新版本OpenJDK13。

> Features：总共有5个新的JEP(JDK Enhancement Proposals):
> http://openjdk.java.net/projects/jdk/13/

Features:

350:Dynamic CDS Archives：动态CDS档案
351:ZGC: Uncommit Unused Memory：ZGC:取消使用未使用的内存
353:Reimplement the Legacy Socket API：重新实现旧版套接字API
354:Switch Expressions (Preview)：switch表达式（预览）
355:Text Blocks (Preview)：文本块

# 二、语法层面特性

## 1.switch表达式(预览)

&emsp;&emsp;在JDK 12中引入了switch表达式作为预览特性。JDK 13提出了第二个switch表达式预览。JEP 354修改了这个特性，它引入了*yield语句，用于返回值。这意味着，switch表达式(返回值)应该使用yield, switch语句(不返回值)应该使用*break。
&emsp;&emsp;在 JDK 12中有一个，但是要进行一个更改：要从 switch 表达式中生成一个值 break，要删除with value语句以支持ayield 声明。目的是扩展，switch 以便它可以用作语句或表达式，因此两个表单既可以使用 case ... : 带有连贯符号的传统标签，也可以使用新 case … -> 标签，而不需要通过，还有一个新的语句用于从 switch 表达式中产生值。这些更改将简化编码并为模式匹配做好准备。

&emsp;&emsp;在以前，我们想要在switch中返回内容，还是比较麻烦的，一般语法如下：

```java
String x = "3";
int i;
switch (x) {
	case "1":
		i=1;
		break;
	case "2":
		i=2;
		break;
	default:
		i = x.length();
		break;
}
System.out.println(i);
```

在JDK13中使用以下语法：

```java
String x = "3";
int i = switch (x) {
	case "1" -> 1;
	case "2" -> 2;
	default -> {
		yield 3;
	}
};
System.out.println(i);
```

或者：

```java
String x = "3";
int i = switch (x) {
	case "1":
		yield 1;
	case "2":
		yield 2;
	default:
		yield 3;
};
System.out.println(i);
```

&emsp;&emsp;在这之后，switch中就多了一个关键字用于跳出switch块了，那就是yield，他用于返回一个值。和return的区别在于：return会直接跳出当前循环或者方法，而yield只会跳出当前switch块。

## 2.文本块(预览)

### 2.1 概念

&emsp;&emsp;在JDK 12中引入了Raw String Literals特性，但在发布之前就放弃了。这个JEP与引入多行字符串文字（text block）在意义上是类似的。

&emsp;&emsp;这条新特性跟 Kotlin 里的文本块是类似的。

### 2.2 问题

&emsp;&emsp;在Java中，通常需要使用String类型表达HTML，XML，SQL或JSON等格式的字符串，在进行字符串赋值时需要进行转义和连接操作，然后才能编译该代码，这种表达方式难以阅读并且难以维护。****文本块就是指多行字符串，例如一段格式化后的xml、json等。而有了文本块以后，用户不需要转义，Java能自动搞定。因此，文本块将提高Java程序的可读性和可写性。

### 2.3 目标

&emsp;&emsp;简化跨越多行的字符串，避免对换行等特殊字符进行转义，简化编写Java程序。
增强Java程序中字符串的可读性。

定义一段HTML代码

```html
<html>
	<body>
		<a href="http://www.mashibing.com">波波烤鸭</a>
	</body>
</html>
```

将这段代码放入java的String中,会出现如下效果

```java
String words =
    "<html>\n" +
	"\t<body>\n" +
	"\t\t<a href=\"http://www.mashibing.com\">波波烤鸭</a>\n" +
	"\t</body>\n" +
	"</html>";
```

自动将空格换行缩进和特殊符号进行了转义,但是在JDK13中可以使用这样的语法了:

```java
String words = """
    <html>
        <body>
            <a href="http://www.mashibing.com">波波烤鸭</a>
        </body>
    </html>""";
```

使用"""作为文本块的开始符和结束符，在其中就可以放置多行的字符串，不需要进行任何转义。看起来就十分清爽了。

如常见的SQL语句：

```
select empno,ename,sal,deptno
from emp
where deptno in (40,50,60)
order by deptno asc
```

原来的方式

```
String query = "select empno,ename,sal,deptno\n" +
"from emp\n" +
"where deptno in (40,50,60)\n" +
"order by deptno asc";
```

现在方式

```
String newQuery = """
select empno,ename,sal,deptno
from emp
where deptno in (40,50,60)
order by deptno asc
""";
```

### 2.4 语法细节1 基本使用

* 文本块是Java语言中的一种新文字。它可以用来表示任何字符串，并且提供更大的表现力和更少的复杂性。
* 文本块由零个或多个字符组成，由开始和结束分隔符括起来。
  * 开始分隔符是由三个双引号字符（"""），后面可以跟零个或多个空格，最终以行终止符结束。文本块内容以开始分隔符的行终止符后的第一个字符开始。
  * 结束分隔符也是由三个双引号字符（"""）表示，文本块内容以结束分隔符的第一个双引号之前的最后一个字符结束。
* 文本块中的内容可以直接使用"，"但不是必需的。
* 文本块中的内容可以直接包括行终止符。允许在文本块中使用 \n，但不是必需的。例如，文本块：

```
"""
line1
line2
line3
"""
```

相当于

```
"line1\nline2\nline3\n"
```

或者相当于一个字符串用+拼接

```
"line1\n" +
"line2\n" +
"line3\n"
```

文本块可以表示空字符串，但不建议这样做，因为它需要两行源代码：

```
String empty = """
""";
```

以下是错误格式的文本块

```
String a = """"""; // 开始分隔符后没有行终止符
String b = """ """; // 开始分隔符后没有行终止符
String c = """
"; // 没有结束分隔符
String d = """
abc \ def
"""; // 含有未转义的反斜线（请参阅下面的转义处理）
```

在运行时，文本块将被实例化为String的实例，就像字符串一样。从文本块派生的String实例与从字符串派生的实例是无法区分的。具有相同内容的两个文本块将引用相同的String实例，就像字符串一样。

### 2.5 语法细节2 编译器在编译时,会删除多余的空格

下面这段代码中，我们用.来表示我们代码中的的空格，而这些位置的空格就是多余的。

```
String html = """
..............<html>
.............. <body>
.............. <p>Hello, world</p>
.............. </body>
..............</html>
..............""";
```

多余的空格还会出现在每一行的结尾，特别是当你从其他地方复制过来时，更容易出现这种情况，比如下面的代码：

```
String html = """
..............<html>...
.............. <body>
.............. <p>Hello, world</p>....
.............. </body>.
..............</html>...
..............""";
```

PS:每行文字后面的空格,编译器会自动帮助我们去掉,但是开头部分的空格和结束的"""; 前面的空格数有关, """;前面有几个空格,就会自动帮助我们去掉每一行前面的几个空格

&emsp;&emsp;这些多余的空格对于程序员来说是看不到的，但是他又是实际存在的，所以如果编译器不做处理，可能会导致程序员看到的两个文本块内容是一样的，但是这两个文本块却因为存在这种多余的空格而导致差异，比如哈希值不相等。

### 2.6 语法细节3 转义字符

&emsp;&emsp;允许开发人员使用 \n，\f 和\r 来进行字符串的垂直格式化，使用 \b和 \t进行水平格式化。比如下面的代码是合法的：

```
String html = """
<html>\n
<body>\n
<p>Hello, world</p>\n
</body>\n
</html>\n
""";
```

请注意，在文本块内自由使用"是合法的。例如:

```
String story = """
"When I use a word," Humpty Dumpty said,
in rather a scornful tone, "it means just what I
choose it to mean - neither more nor less."
"The question is," said Alice, "whether you
can make words mean so many different things."
"The question is," said Humpty Dumpty,
"which is to be master - that's all."
""";
```

但是，三个"字符的序列需要进行转义至少一个"以避免模仿结束分隔符：

```
String code =
"""
String text = \"""
A text block inside a text block
\""";
""";
```

### 2.7 语法细节4 文本块连接

&emsp;&emsp;可以在任何可以使用字符串的地方使用文本块。例如，文本块和字符串可以相互连接：

```
String code = "public void print(Object o) {" +
"""
System.out.println(Objects.toString(o));
}
""";
```

&emsp;&emsp;但是，涉及文本块的连接可能变得相当笨重。以下面文本块为基础：

```
String code = """
public void print(Object o) {
System.out.println(Objects.toString(o));
}
""";
```

&emsp;&emsp;假设我们想把上面的Object改为来自某一变量，我们可能会这么写：

```
String code = """
public void print(""" + type + """
o) {
System.out.println(Objects.toString(o));
}
""";
```

&emsp;&emsp;可以发现这种写法可读性是非常差的，更简洁的替代方法是使用String :: replace或String :: format，比如：

```
String code = """
public void print($type o) {
System.out.println(Objects.toString(o));
}
""".replace("$type", type);
```

```
String code = String.format("""
public void print(%s o) {
System.out.println(Objects.toString(o));
}
""", type);
```

另一个方法是使用String :: formatted，这是一个新方法，比如：

```
String source = """
public void print(%s object) {
System.out.println(Objects.toString(object));
}
""".formatted(type);
```

# 三、API层次特性

## 1.重新实现旧版套接字API

> 目前的问题

重新实现了古老的 Socket 接口。现在已有的 java.net.Socket 和 java.net.ServerSocket 以及它们的实现类，都可以回溯到 JDK 1.0 时代了。

* 它们的实现是混合了 Java 和 C 的代码的，维护和调试都很痛苦。
* 实现类还使用了线程栈作为 I/O 的缓冲，导致在某些情况下还需要增加线程栈的大小。
* 支持异步关闭，此操作是通过使用一个本地的数据结构来实现的，这种方式这些年也带来了潜在的不稳定性和跨平台移植问题。该实现还存在几个并发问题，需要彻底解决。

在未来的网络世界，要快速响应，不能阻塞本地方法线程，当前的实现不适合使用了。

> 新的实现类

&emsp;&emsp;全新实现的 NioSocketImpl 来替换JDK1.0的PlainSocketImpl。此实现与NIO实现共享相同的内部基础结构,并且与现有的缓冲区高速缓存机制集成在一起,因此不需要使用线程堆栈.除此之外,他还有一些其他更改,例如使用java.lang.ref.Cleaner机制关闭套接字,实现在尚未关闭的套接字上进行了垃圾收集,以及在轮训时套接字出于非阻塞模式时处理超时操作等方法

* 它便于维护和调试，与 NewI/O (NIO) 使用相同的 JDK 内部结构，因此不需要使用系统本地代码。
* 它与现有的缓冲区缓存机制集成在一起，这样就不需要为 I/O 使用线程栈。
* 它使用 java.util.concurrent 锁，而不是 synchronized 同步方法，增强了并发能力。
* 新的实现是Java 13中的默认实现，但是旧的实现还没有删除，可以通过设置系统属性jdk.net.usePlainSocketImpl来切换到旧版本。

> 代码说明

运行一个实例化Socket和ServerSocket的类将显示这个调试输出。这是默认的(新的)。

```java
/**
 * The abstract class {@code SocketImpl} is a common superclass
 * of all classes that actually implement sockets. It is used to
 * create both client and server sockets.
 *
 * @implNote Client and server sockets created with the {@code Socket} and
 * {@code SocketServer} public constructors create a system-default
 * {@code SocketImpl}. The JDK historically used a {@code SocketImpl}
 * implementation type named "PlainSocketImpl" that has since been replaced by a
 * newer implementation. The JDK continues to ship with the older implementation
 * to allow code to run that depends on unspecified behavior that differs between
 * the old and new implementations. The old implementation will be used if the
 * Java virtual machine is started with the system property {@systemProperty
 * jdk.net.usePlainSocketImpl} set to use the old implementation. It may also be
 * set in the JDK's network configuration file, located in {@code
 * ${java.home}/conf/net.properties}. The value of the property is the string
 * representation of a boolean. If set without a value then it defaults to {@code
 * true}, hence running with {@code -Djdk.net.usePlainSocketImpl} or {@code
 * -Djdk.net.usePlainSocketImpl=true} will configure the Java virtual machine
 * to use the old implementation. The property and old implementation will be
 * removed in a future version.
 *
 * @since   1.0
 */
public abstract class SocketImpl implements SocketOptions {
    private static final boolean USE_PLAINSOCKETIMPL = usePlainSocketImpl();

    private static boolean usePlainSocketImpl() {
        PrivilegedAction<String> pa = () -> NetProperties.get("jdk.net.usePlainSocketImpl");
        String s = AccessController.doPrivileged(pa);
        return (s != null) && !s.equalsIgnoreCase("false");
    }
```

SocketImpl的USE_PLAINSOCKETIMPL取决于usePlainSocketImpl方法，而它会从NetProperties读取
jdk.net.usePlainSocketImpl配置，如果不为null且不为false，则usePlainSocketImpl方法返回true；
createPlatformSocketImpl会根据USE_PLAINSOCKETIMPL来创建PlainSocketImpl或者NioSocketImpl。

# 四、其他变化

## 1. ZGC取消为使用的内存

G1和Shenandoah：

JVM的GC释放的内存会还给操作系统吗？
GC后的内存如何处置，其实是取决于不同的垃圾回收器。因为把内存还给OS，意味着要调整JVM的堆大小，这个过程是比较耗费资源的。

* Java12的346: Promptly Return Unused Committed Memory from G1新增了两个参数分别是
  G1PeriodicGCInterval及G1PeriodicGCSystemLoadThreshold用于GC之后重新调整Java heap size，然后将多余的内存归还给操作系统
* Java12的189: Shenandoah: A Low-Pause-Time Garbage Collector (Experimental)拥有参数
  -XX:ShenandoahUncommitDelay=来指定ZPage的page cache的失效时间，然后归还内存

HotSpot的G1和Shenandoah这两个GC已经提供了这种能力，并且对某些用户来说，非常有用。因此，Java13则给ZGC新增归还unused heap memory给操作系统的特性。

> **ZGC的使用背景**

&emsp;&emsp;在JDK 11中，Java引入了ZGC，这是一款可伸缩的低延迟垃圾收集器，但是当时只是实验性的。号称不管你开了多大的堆内存，它都能保证在 10 毫秒内释放 JVM ，不让它停顿在那。但是，当时的设计是它不能把内存归还给操作系统。对于比较关心内存占用的应用来说，肯定希望进程不要占用过多的内存空间了，所以这次增加了这个特性。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231601827.png)

&emsp;&emsp;在Java 13中，JEP 351再次对ZGC做了增强，将没有使用的堆内存归还给操作系统。ZGC当前不能把内存归还给操作系统，即使是那些很久都没有使用的内存，也只进不出。这种行为并不是对任何应用和环境都是友好的，尤其是那些内存占用敏感的服务，例如：

1. 按需付费使用的容器环境；
2. 应用程序可能长时间闲置，并且和很多其他应用共享和竞争资源的环境；
3. 应用程序在执行期间有非常不同的堆空间需求，例如，可能在启动的时候所需的堆比稳定运行的时候需要更多的堆内存。

使用细节:

&emsp;&emsp;ZGC的堆由若干个Region组成，每个Region被称之为ZPage。每个Zpage与数量可变的已提交内存相关联。当ZGC压缩堆的时候，ZPage就会释放，然后进入page cache，即ZPageCache。这些在page cache中的ZPage集合就表示没有使用部分的堆，这部分内存应该被归还给操作系统。回收内存可以简单的通过从page cache中逐出若干个选好的
&emsp;&emsp;ZPage来实现，由于page cache是以LRU（Least recently used，最近最少使用）顺序保存ZPage的，并且按照尺寸（小，中，大）进行隔离，因此逐出ZPage机制和回收内存相对简单了很多，主要挑战是设计关于何时从page cache中逐出ZPage的策略。

&emsp;&emsp;一个简单的策略就是设定一个超时或者延迟值，表示ZPage被驱逐前，能在page cache中驻留多长时间。这个超时时间会有一个合理的默认值，也可以通过JVM参数覆盖它。Shenandoah GC用了一个类型的策略，默认超时时间是5分钟，可以通过参数-XX:ShenandoahUncommitDelay = milliseconds覆盖默认值。

&emsp;&emsp;像上面这样的策略可能会运作得相当好。但是，用户还可以设想更复杂的策略：不需要添加任何新的命令行选项。例如，基于GC频率或某些其他数据找到合适超时值的启发式算法。JDK13将使用哪种具体策略目前尚未确定。可能最初只提供一个简单的超时策略，使用-XX:ZUncommitDelay = seconds选项，以后的版本会添加更复杂、更智能的策略（如果可以的话）。

&emsp;&emsp;uncommit能力默认是开启的，但是无论指定何种策略，ZGC都不能把堆内存降到低于Xms。这就意味着，如果Xmx和Xms相等的话，这个能力就失效了。-XX:-ZUncommit这个参数也能让这个内存管理能力失效。

## 2.动态CDS档案

> 作用

&emsp;&emsp;在JAVA应用程序在程序执行结束时动态归档类. 归档的类将包括默认基层CDS归档中不存在的所有已加载应用程序类和类库

&emsp;&emsp;CDS，是java 12的特性了，可以让不同 Java 进程之间共享一份类元数据，减少内存占用，它还能加快应用的启动速度。而JDK13的这个特性支持在Java application执行之后进行动态archive。存档类将包括默认的基础层CDS存档中不存在的所有已加载的应用程序和库类。也就是说，在Java 13中再使用AppCDS的时候，就不再需要这么复杂了。
&emsp;&emsp;该提案处于目标阶段，旨在提高AppCDS的可用性，并消除用户进行运行时创建每个应用程序的类列表的需要。

```
# JVM退出时动态创建共享归档文件：导出jsa
java -XX:ArchiveClassesAtExit=hello.jsa -cp hello.jar Hello
# 用动态创建的共享归档文件运行应用:使用jsa
java -XX:SharedArchiveFile=hello.jsa -cp hello.jar Hello
```

> 目的

JAVA13 这次对CDS增强的目的

* 改善APPCDS的可用性,减少用户每次都要创建一个类列表的需要
* 通过开启 -Xshare:dump 选项来开启静态归档,使用类列表仍然行得通,包含内置的类加载信息和用户定义的类加载信息

> 意义

&emsp;&emsp;在JDK13中做的增强,可以只开启命令行选项完成上述过程,在程序运行的时候,动态评估哪些类需要归档,同时支持内置的类加载器和用户定义的类加载器

&emsp;&emsp;在第一次程序执行完成后,会自动的将类进行归档,后续启动项目的时候也无需指定要使用哪些归档,整个过程看起来更加透明

## 3.增加废弃和移除

### 增加项

* 添加FileSystems.newFileSystem(Path, Map&#x3c;String, ?>) Method
* 新的java.nio.ByteBuffer Bulk get/put Methods Transfer Bytes Without Regard to Buffer Position
* 支持Unicode 12.1
* 添加-XX:SoftMaxHeapSize Flag，目前仅仅对ZGC起作用
* ZGC的最大heap大小增大到16TB

### 移除项

* 移除awt.toolkit System Property
* 移除Runtime Trace Methods
* 移除-XX:+AggressiveOpts
* 移除Two Comodo Root CA Certificates、Two DocuSign Root CA Certificates
* 移除内部的com.sun.net.ssl包

### 废弃项

* 废弃-Xverify:none及-noverify
* 废弃rmic Tool并准备移除
* 废弃javax.security.cert并准备移除
