# JDK各个版本特性讲解-JDK18特性

# 一、JAVA18概述

&emsp;&emsp;Java 18 在 2022 年 3 月 22 日正式发布，Java 18 不是一个长期支持版本，这次更新共带来 9 个新功能。

https://openjdk.org/projects/jdk/18/

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642032.png)

# 二、具体新特性

## 1. 默认UTF-8字符编码

&emsp;&emsp;JDK 一直都是支持 UTF-8 字符编码，这次是把 UTF-8 设置为了默认编码，也就是在不加任何指定的情况下，默认所有需要用到编码的 JDK API 都使用 UTF-8 编码，这样就可以避免因为不同系统，不同地区，不同环境之间产生的编码问题。

## 2. 简单的Web服务器

&emsp;&emsp;在JDK18中提供一个简单的Web服务。也就是在bin目录下提供了一个 `jwebserver`的命令。运行这个命令可以启动一个 `简单的、最小的`静态web服务器。它不支持 CGI 和 Servlet，所以最好的使用场景是用来测试、教育、演示等需求。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642033.png)

然后启动 `jwebserver`:

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642034.png)

启动成功：http://127.0.0.1:8000

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642035.png)

cmd窗口出现乱码的情况。因为JAVA18默认编码为 `UTF-8`,但是在cmd中的默认编码为GBK。这时我们需要调整下。在cmd窗口输入 `chcp`

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642036.png)

655001：代表UTF-8编码，
936 : 代表GBK编码。

我们临时修改就可以了。输入chcp 65001可以将当前窗口的编码形式修改为UTF-8

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642037.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642038.png)

服务启动访问的是当前bin目录。我们可以在自己的项目中创建对应的index.html文件

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642039.png)

然后我们可以看看帮助命令。怎么指定到特定的目录：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642040.png)

jwebserver -d E:\workspace\VipWorkSpace\JDKDemo03\src

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642041.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642042.png)

有请求的时候控制台会有相关的输出信息

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642043.png)

## 3.JavaDoc的增强

&emsp;&emsp;在Java18中新增了对JavaDoc的增强，在之前的版本虽然已经提供了在JavaDoc中添加代码片段，但是对高亮处理没有提供支持。在Java18中提供了功能

```java
/**
 * JavaDoc特性讲解
 *  {@snippet :
 *      if(v.isPresent()){
 *          System.out.println("Hello ... ")
 *      }
 *  }
 */
public class Test01 {

    /**
     * 正则高亮：
     * {@snippet :
     *   public static void main(String... args) {
     *       for (var arg : args) {                 // @highlight region regex = "\barg\b"
     *           if (!arg.isBlank()) {
     *               System.out.println(arg);
     *           }
     *       }                                      // @end
     *      }
     *   }
     */
    public static void main(String[] args) {
        System.out.println(Charset.defaultCharset());
        System.out.println("波哥....");
    }

    /**
     * 两数求和：
     * {@snippet :
     *   public Integer add(int a,int b){
     *      System.out.println("add ..."); // @replace regex='".*"' replacement="..."
     *      return a + b;
     *   }
     * }
     */
    public Integer add(int a,int b){
        System.out.println("add ...");
        return a + b ;
    }
}

```

生成doc文档

```
D:\software\openjdk-19.0.1_windows-x64_bin\jdk-19.0.1\bin\javadoc.exe -public -sourcepath ./src -subpackages com -encoding utf-8 -charset utf-8 -d ./javadocout
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642044.png)

然后结合 `jwebserver`来查看发布的doc文档

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642045.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642046.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642047.png)

## 4. 反射功能的新特性

&emsp;&emsp;Java 18 改进了 java.lang.reflect.Method、Constructor 的实现逻辑，使之性能更好，速度更快。这项改动不会改动相关 API ，这意味着开发中不需要改动反射相关代码，就可以体验到性能更好反射。

OpenJDK 官方给出了新老实现的反射性能基准测试结果。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642048.png)

## 5.Vector API(三次孵化)

在 Java 16 中引入一个新的 API 来进行向量计算，它可以在运行时可靠的编译为支持的 CPU 架构，从而实现更优的计算能力。在 Java 17 中改进了 Vector API 性能，增强了例如对字符的操作、字节向量与布尔数组之间的相互转换等功能。现在在 JDK 18 中将继续优化其性能。

## 6. 互联网地址解析SPI

&emsp;&emsp;为主机名和地址解析定义服务提供者接口 (SPI)，以便 `java.net.InetAddress`可以使用平台内置解析器以外的解析器。

```java
    public static void main(String[] args) throws Exception {
        InetAddress inetAddress = InetAddress.getByName("cart.msb.com");
        System.out.println(inetAddress.getHostAddress());
    }
```

## 7. 外部函数和内存API(二次孵化)

新的 API 允许 Java 开发者与 JVM 之外的代码和数据进行交互，通过调用外部函数，可以在不使用 JNI 的情况下调用本地库。

这是一个孵化功能；需要添加 `--add-modules jdk.incubator.foreign` 来编译和运行 Java 代码，Java 18 改进了相关 API ，使之更加简单易用。

## 8.switch 表达式

&emsp;&emsp;从 Java 17 开始，对于 Switch 的改进就已经在进行了，Java 17 的 JEP 406 已经对 Switch 表达式进行了增强，使之可以减少代码量。

注意这块的代码需要通过2022版的IDEA才支持。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231642049.png)

下面是几个例子：

```java
// JDK 17 以前
static String formatter(Object o) {
    String formatted = "unknown";
    if (o instanceof Integer i) {
        formatted = String.format("int %d", i);
    } else if (o instanceof Long l) {
        formatted = String.format("long %d", l);
    } else if (o instanceof Double d) {
        formatted = String.format("double %f", d);
    } else if (o instanceof String s) {
        formatted = String.format("String %s", s);
    }
    return formatted;
}

```

而在 Java 17 之后，可以通过下面的写法进行改进

```java
// JDK 17 之后
static String formatterPatternSwitch(Object o) {
    return switch (o) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> o.toString();
    };
}

```

switch 可以和 `null` 进行结合判断：

```java
static void testFooBar(String s) {
    switch (s) {
        case null         -> System.out.println("Oops");
        case "Foo", "Bar" -> System.out.println("Great");
        default           -> System.out.println("Ok");
    }
}

```

case 时可以加入复杂表达式：

```java
static void testTriangle(Shape s) {
    switch (s) {
        case Triangle t && (t.calculateArea() > 100) ->
            System.out.println("Large triangle");
        default ->
            System.out.println("A shape, possibly a small triangle");
    }
}

```

case 时可以进行类型判断

```java
sealed interface S permits A, B, C {}
final class A implements S {}
final class B implements S {}
record C(int i) implements S {}  // Implicitly final

static int testSealedExhaustive(S s) {
    return switch (s) {
        case A a -> 1;
        case B b -> 2;
        case C c -> 3;
    };
}

```
