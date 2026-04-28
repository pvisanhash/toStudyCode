# JDK各个版本特性讲解-JDK19特性

# 一、JAVA19概述

&emsp;&emsp;JDK 19 2022 年 9 月 20 日正式发布以供生产使用，非长期支持版本。不过，JDK 19 中有一些比较重要的新特性值得关注。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231711298.png)

JDK 19 只有 7 个新特性：

* **JEP 405: Record Patterns（记录模式）[1]** （预览）
* **JEP 422: Linux/RISC-V Port[2]**
* **JEP 424: Foreign Function & Memory API（外部函数和内存 API）[3]** （预览）
* **JEP 425: Virtual Threads（虚拟线程）[4]** （预览）
* **JEP 426: Vector（向量）API[5]** （第四次孵化）
* **JEP 427: Pattern Matching for switch（switch 模式匹配）[6]**
* **JEP 428: Structured Concurrency（结构化并发）[7]** （孵化）

# 二、新特性介绍

## 1. 记录模式(预览版本)

&emsp;&emsp;使用*记录模式*增强 Java 编程语言以解构记录值，可以嵌套记录模式和类型模式，实现强大的、声明性的和可组合的数据导航和处理形式。

这是一个预览语言功能。

## 2.Linux/RISC-V 移植

将 JDK 移植到 Linux/RISC-V，目前仅支持 RISC-V 的 RV64GV 配置（包含向量指令的通用 64 位 ISA）。将来可能会考虑支持其他 RISC-V 配置，例如通用 32 位配置 (RV32G)。

## 3.外部函数和内存 API （预览版）

引入一个 API，Java 程序可以通过该 API 与 Java 运行时之外的代码和数据进行互操作。通过该 API
可有效地调用外部函数（ JVM 之外的代码）和安全地访问外部内存（不受 JVM 管理的内存），使得 Java
程序能够调用本机库并处理本机数据，而不会出现 JNI 的脆弱性和危险。

这是个预览版 API 。

## 4.虚拟线程(预览版)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231711299.png)

&emsp;&emsp;JVM线程跟操作系统线程是一一对应的关系，线程真正的执行需要等到OS分配资源进行调度。因此操作系统对线程数量的限制也影响JVM线程数量。Centos7 修改操作系统线程数量大小方法.

```
# vim /etc/security/limits.d/20-nproc.conf
* soft nproc 65535
* hard nproc 65535
```

虚拟线程：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231711300.png)

&emsp;&emsp;针对上述JVM线程限制的问题，JDK19提供虚拟线程的方式，可以在一定程度上突破线程数量大小的限制(借鉴了golang goroutine的设计思想)。在JDK19中线程分为平台线程(Platform Thread)、虚拟线程(Virtual Thread).

&emsp;&emsp;如上图所示，虚拟线程完全归JVM管理，不受操作系统限制，因此JVM可以生成大量的虚拟线程执行业务逻辑，从而提供系统的吞吐量。但是、但是、但是（重要的事情说三遍）,该特性目前并不成熟，不建议在生产环境中使用。

案例代码：

```java
    public static void main(String[] args) throws Exception {
        for (int i = 0; i < 100; i++) {
            // 启动虚拟线程
            Thread.startVirtualThread(()->{
                System.out.println(Thread.currentThread() );
            });
        }
        Thread.sleep(10000);
        System.out.println("Hello ...");
    }
```

相关API说明：

* Thread.ofVirtual() 创建虚拟线程
* Thread.ofPlatform() 创建平台线程
* Thread.startVirtualThread(Runnable) 创建并运行虚拟线程
* Executors.newVirtualThreadPerTaskExecutor() 线程池

测试代码

```java
public class Test03 {

    public static void main(String[] args) throws Exception {
        //ExecutorService executorService = Executors.newFixedThreadPool(1);
        ExecutorService executorService = Executors.newVirtualThreadPerTaskExecutor();
        executorService.execute(new MyTask());
        executorService.execute(new MyTask());
        executorService.execute(new MyTask());
        executorService.execute(new MyTask());
        executorService.execute(new MyTask());

        Thread.sleep(2000);
    }

    static class MyTask implements Runnable{
        @Override
        public void run() {
            System.out.println(Thread.currentThread());
            try {
                Thread.sleep(1000);
            }catch (Exception e){
                e.printStackTrace();
            }
            System.out.println(LocalDateTime.now());
        }
    }

}

```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231711301.png)

修改JVM参数

```txt
-Djdk.virtualThreadScheduler.parallelism=1 -Djdk.virtualThreadScheduler.maxPoolSize=1
```

设置ForkJoinPool的核心线程数和最大线程数

* -Djdk.virtualThreadScheduler.parallelism=1
* -Djdk.virtualThreadScheduler.maxPoolSize=1

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231711302.png)

## 5.Vector API （第四次孵化）

引入一个 API 来表达在运行时能够可靠编译的向量计算，在支持的 CPU 架构上优化向量指令，从而实现优于标量计算的性能。

## 6.Switch 模式匹配（第三预览版）

用 `switch` 表达式和语句的模式匹配，以及对模式语言的扩展来增强 Java 编程语言。将模式匹配扩展到 `switch` 中，允许针对一些模式测试表达式，这样就可以简明而安全地表达复杂的面向数据的查询。

该特性最早在 Java 17 中作为预览版出现， Java 19 为第三次预览。

## 7.结构化并发（孵化阶段）

引入用于结构化并发的 API 来简化多线程编程，结构化并发将不同线程中运行的多个任务视为单个工作单元，从而简化错误处理、提高可靠性并增强可观察性。
