# JVM调优

JVM 调优的核心不是背参数，而是理解 JVM 的运行模型，并用监控、日志和压测数据验证每一次调整。调优要从业务目标开始：系统到底追求吞吐量、响应时间、稳定性、资源成本，还是几者之间的平衡。

一套可靠的调优方法通常遵循四个原则：

1. 先建立基线，再修改参数。
2. 先定位瓶颈，再选择收集器或参数。
3. 一次只调整少量变量，并保留回滚方案。
4. 参数调优不能替代代码、架构和容量治理。

本文按课程大纲的知识主线展开：基础入门、Class 文件、类加载、JMM、运行时数据区与指令集、对象布局、GC 基础与调优、跨代引用记录、主流垃圾收集器、线上排查和生产配置。正文会吸收辅助笔记中的细节，但最终以一篇独立文档呈现。

## 一、JVM 基础入门

### 1. JVM 是什么

JVM 是 Java Virtual Machine 的缩写。它屏蔽了操作系统和硬件差异，让 Java 字节码可以在不同平台运行。Java 程序编译后得到 `.class` 字节码，JVM 负责加载、验证、解释、编译、执行这些字节码，并管理运行时内存。

典型执行链路：

```text
Java 源码 -> javac 编译 -> class 字节码 -> 类加载 -> 解释执行 / JIT 编译 -> 机器码执行
```

JVM 不只运行 Java，也可以运行 Kotlin、Scala、Groovy 等编译到 JVM 字节码的语言。

### 2. 常见 JVM 实现

常见 JVM 实现包括：

- HotSpot：最主流的 JVM，实现了成熟的 JIT、GC 和诊断工具。
- OpenJ9：内存占用和启动速度有一定优势。
- GraalVM：支持 Graal JIT、Native Image 和多语言能力。

日常讨论 JVM 调优时，默认大多指 HotSpot。

### 3. 解释执行、编译执行与热点代码

JVM 早期通过解释器逐条执行字节码。为了提高性能，HotSpot 会统计热点方法和热点循环，将频繁执行的代码通过 JIT 编译成本地机器码。

常见概念：

- Interpreter：解释器，启动快，但长期执行效率低。
- C1 Compiler：偏向快速编译，适合启动阶段和客户端场景。
- C2 Compiler：偏向高质量优化，适合长期运行的服务端程序。
- Tiered Compilation：分层编译，综合解释器、C1、C2 的优势。

热点检测相关参数：

```shell
-XX:CompileThreshold=<n>
-XX:+PrintCompilation
```

一般不建议生产随意调整编译阈值。只有在启动耗时、预热、低延迟抖动等场景中，才需要结合 JIT 日志分析。

### 4. JVM 调优到底调什么

调优不是“把堆调大”这么简单。真正要调的是系统行为：

- 对象分配速率。
- 对象存活时间。
- 新生代与老年代压力。
- GC 触发时机。
- GC 暂停时间。
- GC 线程与业务线程竞争。
- 类加载与元空间增长。
- 线程数、栈空间和堆外内存。
- 锁竞争、上下文切换和 safepoint 暂停。

如果业务代码持续制造大量长生命周期对象，扩大堆只会推迟 Full GC；如果线程池无限堆积任务，换垃圾收集器也救不了系统。

## 二、Class 文件格式

### 1. Class 文件的定位

`.class` 文件是 JVM 的输入格式。Java 源码、Kotlin 源码、Scala 源码最终都可以编译成 JVM 能识别的 class 文件。

Class 文件不是文本，而是一种严格定义的二进制格式。它保存了类、字段、方法、接口、常量池、字节码指令、调试信息和各种属性。

### 2. Class 文件整体结构

Class 文件整体结构可以概括为：

```text
Magic
Minor Version
Major Version
Constant Pool
Access Flags
This Class
Super Class
Interfaces
Fields
Methods
Attributes
```

关键部分说明：

- Magic：魔数，固定为 `0xCAFEBABE`，用于识别 class 文件。
- Version：标识 class 文件版本，不同 JDK 支持的版本不同。
- Constant Pool：常量池，是 class 文件中非常核心的数据结构。
- Access Flags：类访问标志，例如 public、final、interface、abstract。
- Fields：字段表。
- Methods：方法表，方法的字节码就存在这里的 Code 属性中。
- Attributes：属性表，保存 Code、LineNumberTable、SourceFile、InnerClasses 等信息。

### 3. 常量池

常量池保存 class 文件中用到的字面量和符号引用，例如：

- 类和接口的全限定名。
- 字段名和字段描述符。
- 方法名和方法描述符。
- 字符串常量。
- 数字常量。
- 方法句柄和动态调用信息。

类加载的链接阶段会把部分符号引用解析为直接引用。理解常量池有助于理解类加载、方法调用、反射、动态代理和 `invokedynamic`。

### 4. 字段和方法描述符

JVM 使用描述符表达类型：

| Java 类型 | 描述符 |
| --- | --- |
| `int` | `I` |
| `long` | `J` |
| `float` | `F` |
| `double` | `D` |
| `boolean` | `Z` |
| `void` | `V` |
| 对象 | `Ljava/lang/String;` |
| 数组 | `[I`、`[Ljava/lang/String;` |

方法描述符示例：

```text
(int, String) -> void
对应 JVM 描述符：
(ILjava/lang/String;)V
```

### 5. 字节码与调优的关系

Class 文件格式本身不是 GC 调优参数，但它解释了很多运行时现象：

- 类越多，类元数据和常量池占用越多。
- 动态生成 class 过多，可能导致 Metaspace 增长。
- 方法体过大、调用层次复杂，会影响 JIT 编译和内联。
- `synchronized`、`volatile`、lambda、反射等语言特性都会在字节码层面留下痕迹。

常用观察命令：

```shell
javap -v YourClass
javap -c YourClass
```

### 6. Class 版本与兼容性

Class 文件中的 major version 决定了最低运行 JDK 版本。常见版本：

| JDK | Class major version |
| --- | --- |
| Java 8 | 52 |
| Java 11 | 55 |
| Java 17 | 61 |
| Java 21 | 65 |

生产排查时，如果出现 `UnsupportedClassVersionError`，通常不是 JVM 调优问题，而是编译版本与运行版本不匹配。调优文档中仍然需要理解它，因为类加载失败、动态生成类失败、依赖冲突都会表现为启动失败或运行期异常。

## 三、类加载、链接与初始化

### 1. 类生命周期

类从 class 文件进入 JVM，到卸载，大致经历：

```text
Loading -> Linking -> Initializing -> Using -> Unloading
```

其中 Linking 又包括：

```text
Verification -> Preparation -> Resolution
```

### 2. Loading：加载

加载阶段主要做三件事：

1. 通过类的全限定名获取 class 字节流。
2. 将字节流表示的静态结构转化为方法区中的运行时数据结构。
3. 在堆中生成一个 `java.lang.Class` 对象，作为访问该类元数据的入口。

类加载器默认遵循双亲委派模型：

```text
Bootstrap ClassLoader
  -> Extension / Platform ClassLoader
    -> Application ClassLoader
      -> Custom ClassLoader
```

双亲委派的主要价值：

- 避免核心类被篡改。
- 避免类重复加载。
- 保证基础类库类型一致。

### 3. 打破双亲委派

某些场景需要打破双亲委派：

- 插件化架构。
- 应用服务器隔离不同 Web 应用。
- 热部署。
- OSGi 模块隔离。
- SPI 机制中基础类需要反向加载实现类。

打破双亲委派通常通过重写 `loadClass()` 或使用线程上下文类加载器实现。

调优关联：

- 热部署后旧 ClassLoader 无法释放，会导致 Metaspace 泄漏。
- 后台线程、静态变量、ThreadLocal 可能持有旧 ClassLoader。
- 大量动态代理、表达式引擎、脚本引擎可能持续生成 class。

### 4. Linking：链接

Verification：

- 验证文件格式、元数据、字节码和符号引用是否符合 JVM 规范。
- 防止非法字节码破坏 JVM 安全。

Preparation：

- 为类变量分配内存并设置默认值。
- 注意这里是默认值，不是程序中的显式初始值。

示例：

```java
static int value = 10;
```

Preparation 阶段 `value` 是 0，Initializing 阶段才变为 10。

Resolution：

- 将常量池中的符号引用解析为直接引用。
- 涉及类、接口、字段、类方法、接口方法、方法类型、方法句柄等。

### 5. Initializing：初始化

初始化阶段执行类构造器 `<clinit>`，主要包括：

- 静态变量显式赋值。
- 静态代码块。

触发类初始化的常见场景：

- `new` 创建对象。
- 读取或设置非 final 静态字段。
- 调用静态方法。
- 反射调用。
- 初始化子类时先初始化父类。
- JVM 启动时初始化主类。
- MethodHandle 解析到静态字段或静态方法。

### 6. 对象创建过程

对象创建大致经历：

```text
类加载检查 -> 分配内存 -> 设置默认值 -> 设置对象头 -> 执行构造方法
```

和类初始化对应，可以记住：

```text
类变量：准备阶段默认值 -> 初始化阶段显式值
对象：分配内存默认值 -> 构造方法显式值
```

## 四、JMM 与并发基础

### 1. 为什么需要 JMM

现代 CPU 为了性能，会使用多级缓存、乱序执行、写缓冲和指令重排。单线程内这些优化通常不影响结果，但多线程环境下可能产生可见性和有序性问题。

JMM 规定了 Java 线程之间如何通过内存交互，核心目标是定义：

- 原子性。
- 可见性。
- 有序性。

### 2. 缓存一致性与伪共享

CPU 访问缓存通常以 cache line 为单位，常见 cache line 大小为 64 字节。多个线程修改位于同一缓存行的不同变量时，可能导致缓存行反复失效，这就是伪共享。

伪共享常见于：

- 高并发计数器。
- RingBuffer。
- 多线程统计数组。

优化方向：

- 使用填充字段。
- 使用 `@Contended`。
- 避免多个高频写变量紧挨着放在同一对象中。

### 3. 乱序执行与内存屏障

CPU 和编译器可能对指令进行重排，只要单线程语义不变即可。JMM 通过内存屏障约束重排。

常见屏障类型：

- LoadLoad：保证前面的读先于后面的读。
- StoreStore：保证前面的写先于后面的写。
- LoadStore：保证前面的读先于后面的写。
- StoreLoad：保证前面的写先于后面的读，是成本较高的屏障。

### 4. volatile

`volatile` 保证变量的可见性，并提供一定的有序性。

字节码层面：

- 字段带有 `ACC_VOLATILE` 标志。

JVM 层面：

- volatile 写前后插入写相关屏障。
- volatile 读前后插入读相关屏障。

适合场景：

- 状态标志。
- 单例双重检查中的实例引用。
- 一个线程写、多个线程读的轻量同步。

不适合场景：

- 复合操作，例如 `count++`。
- 多变量不变式维护。

### 5. synchronized

`synchronized` 保证原子性、可见性和有序性。

字节码表现：

- 同步代码块：`monitorenter`、`monitorexit`。
- 同步方法：方法访问标志中带 `ACC_SYNCHRONIZED`。

锁状态和对象头 Mark Word 有关。对象头中会保存锁标志、GC 年龄、hash 等信息。调用过 `System.identityHashCode()` 的对象可能影响偏向锁状态。

### 6. 并发问题与调优的关系

JVM 调优不能只看 GC。很多“卡顿”实际来自：

- 锁竞争。
- 线程池耗尽。
- 连接池耗尽。
- 队列积压。
- 上下文切换。
- 伪共享。
- 下游 IO 阻塞。

排查时要同时看 GC 日志、线程栈、CPU、load、业务耗时和下游指标。

## 五、运行时数据区与 JVM 指令集

### 1. 运行时数据区

JVM 运行时主要内存区域：

| 区域 | 线程共享 | 说明 |
| --- | --- | --- |
| Program Counter | 否 | 当前线程执行到哪条字节码 |
| JVM Stack | 否 | Java 方法调用栈 |
| Native Method Stack | 否 | 本地方法调用栈 |
| Heap | 是 | 对象主要分配区域 |
| Method Area | 是 | 类元数据、方法信息等规范概念 |
| Runtime Constant Pool | 是 | 运行时常量池 |
| Direct Memory | 是 | JVM 外的直接内存 |

### 2. Program Counter

程序计数器记录当前线程正在执行的字节码位置。每个线程都有自己的 PC，线程切换后可以恢复到正确的执行位置。

### 3. JVM Stack

每个 Java 方法调用都会创建一个栈帧。栈帧包含：

- Local Variable Table：局部变量表。
- Operand Stack：操作数栈。
- Dynamic Linking：动态链接。
- Return Address：返回地址。

`-Xss` 控制单线程栈大小。栈太小容易 `StackOverflowError`，栈太大则会减少可创建线程数，增加 native thread OOM 风险。

### 4. Heap

堆是对象主要分配区域，也是 GC 调优的核心。常见参数：

```shell
-Xms<size>
-Xmx<size>
```

生产中常把 `-Xms` 和 `-Xmx` 设置相同，避免运行期堆扩缩容造成抖动。

### 5. Method Area、PermGen 与 Metaspace

方法区是 JVM 规范概念。HotSpot 中：

- JDK 7 及以前主要用永久代实现部分方法区能力。
- JDK 8 起移除永久代，改用 Metaspace。

关键差异：

- JDK 7 字符串常量池曾位于永久代相关区域。
- JDK 8 字符串常量池位于堆中。
- Metaspace 使用本地内存，默认上限受机器内存限制。

常用参数：

```shell
-XX:MetaspaceSize=<size>
-XX:MaxMetaspaceSize=<size>
```

如果动态类很多或类加载器泄漏，可能出现：

```text
java.lang.OutOfMemoryError: Metaspace
java.lang.OutOfMemoryError: Compressed class space
```

### 6. Direct Memory

直接内存不属于 Java Heap，但属于进程内存。NIO、Netty、mmap、堆外缓存都可能使用直接内存。

常用参数：

```shell
-XX:MaxDirectMemorySize=<size>
```

如果堆不高但进程 RSS 很高，应重点检查直接内存、线程栈、mmap 和 native library。

### 7. 基于栈的指令集

JVM 指令集是基于操作数栈的，而不是直接基于寄存器。执行字节码时，数据通常在局部变量表和操作数栈之间流转。

常见指令类别：

- load：从局部变量表加载到操作数栈。
- store：从操作数栈存回局部变量表。
- arithmetic：加减乘除等运算。
- invoke：方法调用。
- return：方法返回。
- new：创建对象。
- getfield / putfield：访问实例字段。
- getstatic / putstatic：访问静态字段。

### 8. 方法调用指令

常见方法调用指令：

- `invokestatic`：调用静态方法。
- `invokevirtual`：调用普通虚方法，支持动态分派。
- `invokeinterface`：调用接口方法。
- `invokespecial`：调用构造方法、私有方法、父类方法。
- `invokedynamic`：动态调用，常见于 lambda、动态语言、运行时生成调用点。

理解方法调用有助于理解多态、反射、lambda、动态代理和 JIT 内联。

## 六、对象布局与内存占用

### 1. 普通对象布局

64 位 HotSpot 中，普通对象通常包含：

- Mark Word：通常 8 字节。
- Class Pointer：类型指针，开启压缩类指针时通常 4 字节。
- Instance Data：实例字段。
- Padding：按 8 字节对齐。

数组对象还包含数组长度。

### 2. 压缩指针

常见参数：

```shell
-XX:+UseCompressedOops
-XX:+UseCompressedClassPointers
```

压缩指针可以把对象引用从 8 字节压缩为 4 字节，显著降低大对象图的内存占用。堆非常大时压缩指针可能失效，因此“堆越大越好”并不总成立。

### 3. 对象定位

JVM 访问对象常见方式：

- 句柄访问：引用指向句柄，句柄再指向对象实例和类型数据。
- 直接指针：引用直接指向对象，对象头中保存类型指针。

HotSpot 主要使用直接指针，访问速度快。

### 4. 对象大小与调优

对象越多、引用越多，GC 需要追踪的对象图越复杂。代码层面的对象设计常常比参数更重要：

- 避免热点路径创建大量临时对象。
- 合理设置集合初始容量。
- 控制缓存大小。
- 避免一次性加载全量数据。
- 大对象使用流式处理或分批处理。
- 谨慎使用对象池，避免生命周期复杂化。

### 5. 使用 Instrumentation 观察对象大小

对象大小可以通过 `java.lang.instrument.Instrumentation#getObjectSize()` 近似观察。典型 Agent：

```java
import java.lang.instrument.Instrumentation;

public class ObjectSizeAgent {
    private static Instrumentation inst;

    public static void premain(String agentArgs, Instrumentation instrumentation) {
        inst = instrumentation;
    }

    public static long sizeOf(Object object) {
        return inst.getObjectSize(object);
    }
}
```

Manifest 中指定：

```text
Premain-Class: ObjectSizeAgent
```

运行时添加：

```shell
-javaagent:/path/to/object-size-agent.jar
```

注意：

- `getObjectSize()` 返回的是对象浅大小，不包含被引用对象的深层对象图。
- 对象最终大小受对象头、压缩指针、字段排列和 8 字节对齐影响。
- 数组对象比普通对象多数组长度字段。
- 一个空 `Object` 在常见 64 位开启压缩指针场景下通常是 16 字节。

### 6. 压缩指针的容量边界

64 位 JVM 中引用天然是 8 字节。压缩指针通过对象对齐和地址压缩，让引用在很多场景下以 4 字节保存。

粗略理解：

- 小堆场景下，压缩指针收益明显。
- 4G 到 32G 左右的堆通常仍能使用压缩 Oops。
- 堆继续增大后，压缩指针可能失效或需要更大对象对齐，实际收益变复杂。

因此内存不是越大越好。扩大堆前要观察 GC 暂停、压缩指针状态、对象大小和进程总内存。

## 七、GC 基础知识

### 1. 什么是垃圾

没有任何 GC Roots 可达的对象就是垃圾。即使多个对象互相引用，只要它们整体不可达，也可以被回收。

Java 自动内存管理减少了手动释放内存的风险，但也带来了 GC 暂停、内存泄漏定位和参数调优问题。

### 2. 如何定位垃圾

常见算法：

- 引用计数：实现简单，但无法解决循环引用。
- 可达性分析：从 GC Roots 出发查找可达对象，HotSpot 主要采用此方式。

常见 GC Roots：

- 虚拟机栈中的局部变量引用。
- 静态字段引用。
- 常量引用。
- JNI 引用。
- 活跃线程。
- 被同步锁持有的对象。

### 3. 常见回收算法

标记清除：

- 优点：不移动对象。
- 缺点：产生内存碎片。

复制算法：

- 优点：没有碎片，适合新生代。
- 缺点：需要额外空间。

标记整理：

- 优点：没有碎片。
- 缺点：移动对象成本较高。

### 4. 分代模型

大多数对象朝生夕死，因此 JVM 常采用分代模型：

```text
新生代 = Eden + Survivor0 + Survivor1
老年代 = 长寿对象、大对象、晋升对象
```

典型过程：

1. 对象优先分配在 Eden。
2. Eden 不足触发 Young GC。
3. 存活对象进入 Survivor，年龄加 1。
4. 达到晋升年龄后进入老年代。
5. Survivor 放不下时，对象提前进入老年代。
6. 老年代空间不足时，触发 Old GC、Mixed GC 或 Full GC。

常见概念：

- Young GC / Minor GC：新生代回收。
- Old GC / Major GC：老年代回收。
- Full GC：全堆或大范围回收，暂停风险高。
- TLAB：线程本地分配缓冲，减少对象分配时的同步。

### 5. 调优目标

GC 调优一般围绕两个目标：

- 吞吐量：用户代码执行时间占总时间的比例。
- 响应时间：Stop-The-World 暂停越短越好。

吞吐量公式：

```text
吞吐量 = 用户代码执行时间 / (用户代码执行时间 + GC 时间)
```

吞吐量优先适合批处理、离线计算。响应时间优先适合 Web 服务、交易链路、接口服务和 GUI。

### 6. 常见垃圾收集器脉络

HotSpot 收集器大致可以按目标理解：

| 收集器 | 主要目标 | 常见场景 |
| --- | --- | --- |
| Serial | 简单、小堆 | 客户端、小工具、单核环境 |
| Parallel | 吞吐量 | 批处理、离线任务 |
| ParNew | 配合 CMS | JDK 8 历史低停顿方案 |
| CMS | 降低老年代停顿 | 存量低延迟系统 |
| G1 | 平衡吞吐和停顿 | 通用服务端应用 |
| ZGC | 极低暂停 | 大堆、低延迟 |
| Shenandoah | 极低暂停 | 中大堆、低延迟 |
| Epsilon | 不回收 | 压测、实验、短生命周期任务 |

不同 JDK 的默认收集器不同：

- JDK 8 常见默认组合是 Parallel Scavenge + Parallel Old。
- JDK 9 起，G1 成为 server 配置默认收集器。
- CMS 在 JDK 9 被废弃，JDK 14 被移除。
- ZGC、Shenandoah 更适合在较新 JDK 中评估。

### 7. 调优从规划开始

调优不能脱离业务场景。规划时先明确：

1. 系统是吞吐量优先还是响应时间优先。
2. 目标 QPS / TPS、P95、P99 和最大暂停。
3. 峰值流量持续多久。
4. 单次请求大约分配多少对象。
5. 长生命周期对象主要来自哪里，例如缓存、会话、队列、上下文。
6. 机器或容器的 CPU、内存和副本数。

一个粗略估算模型：

```text
峰值 TPS * 单请求临时对象大小 * 对象存活时间 = 新生代压力
峰值并发数 * 单上下文持有对象大小 = 老年代压力
缓存条目数 * 单条目大小 = 缓存占用
线程数 * Xss = 线程栈占用
```

估算只决定参数起点，最终仍要靠压测和日志修正。

## 八、Card Table 与 Remembered Set

### 1. 为什么需要跨代引用记录

新生代 GC 时，如果每次都扫描整个老年代来找“老年代对象引用新生代对象”的情况，成本会非常高。

因此 JVM 使用 Card Table、Remembered Set 等结构记录跨区域引用，让 Young GC 只扫描可能存在跨代引用的区域。

### 2. Card Table

Card Table 可以理解为堆内存的一张粗粒度位图。堆被划分成很多 card，当某个老年代对象字段写入了新生代对象引用，对应 card 会被标记为 dirty。

Young GC 时只需要扫描 dirty card，而不是整个老年代。

### 3. 写屏障

Card Table 的维护依赖写屏障。每次引用字段写入时，JVM 会额外执行一段逻辑，把对应 card 标脏。

这说明 GC 优化不是免费的：减少扫描范围的同时，也会给写引用操作带来额外开销。

### 4. Remembered Set

G1 中每个 Region 都有 Remembered Set，用于记录其他 Region 指向当前 Region 的引用信息。这样回收某些 Region 时，不必扫描整个堆。

RSet 的价值：

- 支持 Region 粒度回收。
- 支持可预测停顿。
- 降低全堆扫描成本。

RSet 的成本：

- 占用额外内存。
- 写屏障维护有 CPU 开销。
- 跨 Region 引用过多时，RSet 维护成本上升。

### 5. 对调优的启发

- 大量跨代引用会增加 Young GC 成本。
- 大量跨 Region 引用会增加 G1 的 RSet 成本。
- 缓存、全局 Map、长生命周期对象引用短生命周期对象时要格外小心。
- 对象关系越复杂，GC 追踪成本越高。

### 6. 跨代引用的代码形态

容易制造跨代引用的代码：

- 老年代中的全局缓存持有新创建对象。
- 单例对象中的集合持续接收请求对象。
- 长生命周期队列积压短生命周期任务。
- ThreadLocal 持有请求上下文。
- 回调、监听器注册后没有注销。

这些对象未必一定泄漏，但会让年轻代回收需要扫描更多 remembered 信息。对高频请求链路来说，降低对象关系复杂度往往比单纯调大年轻代更有效。

## 九、Parallel、ParNew 与 CMS

### 1. 常见收集器组合

JDK 8 中常见组合：

```shell
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+UseParallelOldGC
-XX:+UseConcMarkSweepGC
-XX:+UseG1GC
```

说明：

- Serial：小堆、单核、客户端场景。
- Parallel Scavenge + Parallel Old：吞吐量优先。
- ParNew + CMS：历史低停顿方案。
- G1：JDK 9+ 的默认通用方案。

### 2. Parallel GC

Parallel GC 以吞吐量为目标，年轻代和老年代都可以并行回收。适合：

- 批处理。
- 离线计算。
- 后台任务。
- 可接受较长单次暂停的系统。

常用参数：

```shell
-XX:+UseParallelGC
-XX:ParallelGCThreads=<n>
-XX:MaxGCPauseMillis=<ms>
-XX:GCTimeRatio=<n>
-XX:+UseAdaptiveSizePolicy
```

注意：`MaxGCPauseMillis` 是目标建议，不是硬保证。设置过低可能导致 GC 更频繁，吞吐下降。

Parallel GC 的常见调优方向：

- 增大堆和年轻代，减少 GC 频率。
- 允许较长暂停，换取更高吞吐。
- 让 `ParallelGCThreads` 与 CPU 核数匹配。
- 保持 `UseAdaptiveSizePolicy`，先观察自适应效果。
- 如果 Full GC 频繁，先看对象是否晋升过快和老年代是否不足。

### 3. ParNew

ParNew 是年轻代并行收集器，常用于配合 CMS。它和 Parallel Scavenge 都能并行回收年轻代，但目标不同：

- ParNew 主要为配合 CMS。
- Parallel Scavenge 更关注吞吐量和自适应调节。

### 4. CMS

CMS 目标是降低老年代回收停顿。它的典型阶段：

```text
Initial Mark -> Concurrent Mark -> Concurrent Preclean -> Final Remark -> Concurrent Sweep -> Concurrent Reset
```

其中 Initial Mark 和 Final Remark 是 STW，并发标记、并发清理阶段可以和业务线程同时运行。

### 5. CMS 的问题

CMS 主要问题：

- 基于标记清除，会产生内存碎片。
- 并发阶段业务线程仍在分配对象，会产生浮动垃圾。
- 老年代空间不足时可能 Concurrent Mode Failure。
- 晋升失败时可能 Promotion Failed。
- 失败后可能退化为 Serial Old，出现很长 STW。
- CMS 已在新版本 JDK 中移除，不适合作为新系统选型。

常用参数：

```shell
-XX:+UseConcMarkSweepGC
-XX:CMSInitiatingOccupancyFraction=<percent>
-XX:+UseCMSInitiatingOccupancyOnly
-XX:+UseCMSCompactAtFullCollection
-XX:CMSFullGCsBeforeCompaction=<n>
-XX:+CMSClassUnloadingEnabled
```

调优思路：

- 降低触发阈值，让 CMS 更早开始。
- 保持老年代余量，避免并发回收来不及。
- 控制对象晋升速度。
- 减少大对象和碎片。
- 关注 Full GC 日志和失败原因。

CMS 日志中的关键阶段：

```text
CMS Initial Mark：初始标记，STW
CMS Concurrent Mark：并发标记
CMS Concurrent Preclean：并发预清理
CMS Final Remark：重新标记，STW
CMS Concurrent Sweep：并发清理
CMS Concurrent Reset：重置内部结构
```

CMS 最怕启动太晚。老年代快满时才开始 CMS，业务线程还在分配对象，容易导致 concurrent mode failure。对响应时间敏感的 JDK 8 存量系统，通常要让 CMS 提前启动，并保留老年代余量。

## 十、G1

### 1. G1 的设计目标

G1 面向服务端中大堆，目标是在可控停顿下获得较好吞吐。JDK 9 起，G1 成为 server 配置下的默认垃圾收集器。

启用方式：

```shell
-XX:+UseG1GC
```

### 2. Region

G1 将堆划分为多个大小相等的 Region。Region 可以扮演 Eden、Survivor、Old、Humongous 等角色。

G1 是逻辑分代、物理分区：

- 逻辑上仍有年轻代和老年代。
- 物理上不要求连续。
- 回收时可以选择收益最高的一组 Region。

### 3. G1 回收过程

常见过程：

- Young GC：回收年轻代 Region。
- Initial Mark：并发标记起点，通常搭在 Young GC 上。
- Concurrent Mark：并发标记存活对象。
- Remark：最终标记。
- Cleanup：清理统计。
- Mixed GC：混合回收年轻代和部分老年代 Region。

### 4. 停顿预测模型

G1 会根据历史数据估算回收每个 Region 的成本和收益，在目标停顿时间内选择 Collection Set。

常用参数：

```shell
-XX:MaxGCPauseMillis=<ms>
-XX:InitiatingHeapOccupancyPercent=<percent>
-XX:G1HeapRegionSize=<size>
-XX:ConcGCThreads=<n>
-XX:ParallelGCThreads=<n>
```

`MaxGCPauseMillis` 是目标，不是保证。设置过低可能让 GC 过于频繁，降低吞吐。

### 5. Humongous 对象

超过 Region 一半大小的对象会被视为 Humongous 对象，通常直接分配到连续 Region 中。大量大对象可能导致：

- Region 难以复用。
- Mixed GC 效果下降。
- 更容易触发 Full GC。

优化方向：

- 控制大数组、大字符串、大 JSON、大 byte buffer。
- 分批处理。
- 流式解析。
- 调整 Region 大小需谨慎，必须结合日志验证。

### 6. G1 Full GC 排查

G1 出现 Full GC 通常是退化信号。常见原因：

- 堆太小。
- 分配速率太高。
- 并发标记启动太晚。
- Humongous 对象太多。
- Mixed GC 回收不及时。

处理方向：

- 增大堆或减少分配。
- 降低 `InitiatingHeapOccupancyPercent`。
- 优化大对象。
- 增加 CPU 或调整 GC 线程。
- 检查是否存在内存泄漏。

### 7. G1 日志关注点

G1 日志中常见关键词：

- `G1 Evacuation Pause`：疏散暂停，将存活对象复制到新 Region。
- `young`：年轻代回收。
- `initial-mark`：并发标记起点。
- `concurrent-root-region-scan`：并发根 Region 扫描。
- `concurrent-mark`：并发标记。
- `remark`：最终标记。
- `cleanup`：清理统计。
- `mixed`：混合回收。
- `to-space exhausted` 或 evacuation failure：疏散空间不足，风险较高。

调优时不要只看单次暂停，还要看 Mixed GC 是否能让老年代占用下降。如果 Mixed GC 后老年代基线仍持续抬升，说明回收速度低于分配或晋升速度。

### 8. G1 参数取舍

常见取舍：

- `MaxGCPauseMillis` 越低，G1 越倾向于选择更小的回收集合，GC 更频繁。
- `InitiatingHeapOccupancyPercent` 越低，并发标记越早启动，CPU 成本可能上升，但 Full GC 风险下降。
- Region 太小，Region 数量和 RSet 成本增加；Region 太大，回收粒度变粗，Humongous 判断边界变化。

优先级建议：

1. 先给合理堆大小。
2. 再设置暂停目标。
3. 观察 GC 日志。
4. 只有日志证明需要时，再调整 IHOP、Region、线程数等参数。

## 十一、ZGC

### 1. ZGC 的目标

ZGC 面向低延迟和大堆场景，目标是把 GC 暂停控制在非常低的范围内。它通过并发标记、并发转移、读屏障和染色指针等机制，把大部分 GC 工作与应用线程并发执行。

启用方式：

```shell
-XX:+UseZGC
```

### 2. 核心思想

ZGC 的关键机制包括：

- Colored Pointers：在指针中编码对象状态。
- Load Barrier：读屏障，在读取引用时处理对象状态。
- Concurrent Relocation：并发移动对象。
- Region-based Memory：分区管理内存。

### 3. 适用场景

适合：

- 大堆服务。
- 对最大暂停极敏感的业务。
- 希望将 GC 暂停压到毫秒级甚至更低的系统。

不适合盲目使用的情况：

- 堆很小，普通 G1 已经满足目标。
- CPU 资源紧张。
- 分配速率远高于回收能力。
- JDK 版本较旧，ZGC 能力不够成熟。

### 4. 调优重点

ZGC 不是“没有成本”，而是把暂停成本转化为并发成本。

重点关注：

- allocation stall。
- 分配速率。
- CPU 余量。
- 堆余量。
- 容器内存限制。
- JDK 版本。

建议使用较新 JDK 获得更成熟的分代 ZGC 能力。

### 5. ZGC 的风险信号

使用 ZGC 时重点看：

- Allocation Stall：应用分配被迫等待 GC 腾空间。
- GC 周期过密：说明堆余量或 CPU 余量不足。
- Concurrent 阶段耗时持续变长：可能分配速率过高或机器负载过重。
- RSS 接近容器限制：堆外、页缓存和 JVM 内部结构没有余量。

处理方向：

- 增大堆或降低分配速率。
- 提高 CPU 配额。
- 优化大对象和缓存。
- 使用更高版本 JDK。
- 在容器中给进程总内存留出足够空间。

### 6. ZGC 适配建议

ZGC 常用于低延迟系统，但迁移前应做灰度和压测：

- 对比 G1 与 ZGC 的 P99、最大暂停和 CPU 使用率。
- 观察业务吞吐是否下降。
- 观察峰值期间是否有 allocation stall。
- 验证监控系统能正确解析 ZGC 日志。
- 不要只用平均响应时间评估效果。

## 十二、Shenandoah

### 1. Shenandoah 的目标

Shenandoah 也是低暂停收集器，目标是让标记、清理、对象移动尽可能并发执行，从而降低暂停时间与堆大小之间的关联。

启用方式依 JDK 发行版和版本而定，常见形式：

```shell
-XX:+UseShenandoahGC
```

### 2. 核心机制

Shenandoah 常见机制：

- Region-based Heap。
- Concurrent Marking。
- Concurrent Evacuation。
- Brooks Pointer 或相关转发机制。
- Barrier 维护对象访问正确性。

### 3. 适用场景

适合：

- 低延迟服务。
- 中大堆。
- 对最大暂停敏感。
- 希望降低 Full GC 风险。

使用前要确认：

- 当前 JDK 是否支持。
- 发行版是否将其作为生产可用能力。
- 业务压测下吞吐损失是否可接受。

### 4. Shenandoah 与 G1、ZGC 的取舍

Shenandoah 和 ZGC 都追求低暂停，但实现机制不同。选择时主要看：

- 当前 JDK 发行版支持情况。
- 团队运维经验。
- 日志与监控兼容性。
- 实际压测下的最大暂停、CPU 成本和吞吐损失。

如果 G1 已经满足 P99 和最大暂停目标，不必为了“更先进”而切换。低暂停收集器适合解决明确的暂停问题，不是所有服务的默认答案。

## 十三、JVM 常用参数

### 1. 参数分类

HotSpot 参数大致分三类：

- 标准参数：`-` 开头，例如 `-version`。
- 非标准参数：`-X` 开头，例如 `-Xmx`。
- 不稳定参数：`-XX` 开头，不同版本可能变化。

查看参数：

```shell
java -version
java -X
java -XX:+PrintCommandLineFlags -version
java -XX:+PrintFlagsInitial -version
java -XX:+PrintFlagsFinal -version
```

### 2. 内存参数

```shell
-Xms<size>
-Xmx<size>
-Xmn<size>
-Xss<size>
-XX:MetaspaceSize=<size>
-XX:MaxMetaspaceSize=<size>
-XX:MaxDirectMemorySize=<size>
```

进程总内存不是只有 Java Heap：

```text
进程内存 = Java Heap + Metaspace + Direct Memory + Thread Stack + Code Cache + GC 内部结构 + native library + mmap
```

容器或机器内存不能全部给 `-Xmx`，要给堆外和系统留余量。

### 3. 新生代与晋升参数

```shell
-XX:SurvivorRatio=<n>
-XX:MaxTenuringThreshold=<n>
-XX:PretenureSizeThreshold=<size>
-XX:+UseTLAB
-XX:TLABSize=<size>
```

说明：

- 新生代太小，Young GC 频繁。
- 新生代太大，单次 Young GC 可能变长。
- Survivor 太小，对象容易提前晋升。
- 晋升过快会增加老年代压力。

### 4. GC 选择参数

常见收集器参数：

```shell
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+UseParallelOldGC
-XX:+UseConcMarkSweepGC
-XX:+UseG1GC
-XX:+UseZGC
-XX:+UseShenandoahGC
```

注意：

- 不同 JDK 支持情况不同。
- 新版本中 CMS 已不可用。
- `UseParallelGC` 在 JDK 8 常见于吞吐优先场景。
- JDK 9+ 通用服务通常先评估 G1。
- ZGC 和 Shenandoah 要结合 JDK 版本、发行版和压测结果。

### 5. GC 日志参数

JDK 8 常用：

```shell
-Xloggc:/opt/app/logs/gc-%t.log
-XX:+UseGCLogFileRotation
-XX:NumberOfGCLogFiles=5
-XX:GCLogFileSize=20M
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+PrintGCCause
-XX:+PrintGCApplicationStoppedTime
-XX:+PrintGCApplicationConcurrentTime
```

JDK 9+ 统一日志：

```shell
-Xlog:gc*,safepoint:file=/opt/app/logs/gc-%t.log:time,uptime,level,tags:filecount=5,filesize=20m
```

生产建议默认开启 GC 日志，并配置滚动。

### 6. OOM 现场参数

```shell
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/opt/app/dumps
-XX:ErrorFile=/opt/app/logs/hs_err_pid%p.log
```

大堆 dump 会造成明显暂停和磁盘压力。线上 dump 前应评估影响，最好摘流后操作。

### 7. 显式 GC

业务代码或第三方库调用 `System.gc()` 可能触发 Full GC。可以考虑：

```shell
-XX:+DisableExplicitGC
```

但更重要的是定位调用方，参数只是止血。

### 8. 容器参数

容器中可使用百分比参数：

```shell
-XX:InitialRAMPercentage=70
-XX:MaxRAMPercentage=70
```

也可以直接固定堆：

```shell
-Xms2g
-Xmx2g
```

固定值稳定，百分比适合多规格模板。无论哪种方式，都要给 Metaspace、Direct Memory、线程栈和系统组件留空间。

### 9. 不建议轻易调整的参数

以下参数或机制不建议在没有证据时随意改：

- 锁自旋次数。
- JIT 编译阈值。
- 逃逸分析开关。
- TLAB 精细尺寸。
- G1 Region 大小。
- GC 线程数。

原因是它们会影响 JVM 自适应策略。除非有日志、压测和明确假设，否则越调越乱。

## 十四、GC 日志分析

### 1. 基本问题

看 GC 日志时，先回答：

1. 什么时候发生 GC？
2. 为什么发生 GC？
3. 回收前后各区域变化如何？
4. 单次暂停多久？
5. GC 是否变频繁？
6. Full GC 后释放了多少内存？

### 2. Young GC

重点看：

- Eden 回收是否有效。
- Survivor 是否放得下。
- 是否大量晋升老年代。
- 单次暂停和频率是否可接受。

Young GC 很频繁，可能是新生代太小或分配速率太高。Young GC 后老年代增长快，可能是 Survivor 太小、晋升年龄太低、对象存活率太高或存在队列积压。

典型观察字段：

```text
回收前年轻代占用 -> 回收后年轻代占用 (年轻代总容量)
回收前堆占用 -> 回收后堆占用 (堆总容量)
user / sys / real 时间
```

如果 `real` 时间远大于预期，要结合 CPU、系统负载和 safepoint 一起看。

### 3. Full GC

判断方法：

```text
Full GC 后堆明显下降 -> 容量或触发时机问题居多
Full GC 后堆几乎不降 -> 长生命周期对象或内存泄漏居多
Full GC 后堆不高但仍频繁 -> 看 System.gc、Metaspace、直接内存、类加载
```

### 4. CMS 日志

重点关注：

- `Concurrent Mode Failure`
- `Promotion Failed`
- 老年代触发阈值是否太高。
- CMS 是否退化到 Serial Old。
- Full GC 是否伴随长暂停。

### 5. G1 日志

重点关注：

- Young Evacuation Pause。
- Mixed GC 是否及时。
- Humongous 对象数量。
- Evacuation Failure。
- Full GC。
- 并发标记是否启动太晚。

### 6. 日志分析顺序

建议顺序：

1. 先看问题时间段。
2. 再看 GC Cause。
3. 看 GC 前后堆、老年代、Metaspace 是否下降。
4. 看 Young GC 后晋升速度。
5. 看 Full GC 后是否释放明显内存。
6. 看是否有 CMS/G1 退化。
7. 结合 QPS、响应时间、CPU 和线程数判断。

不要只截取一行日志下结论。GC 是时间序列问题，需要看趋势。

## 十五、线上问题排查

### 1. 常用工具

| 工具 | 场景 | 示例 |
| --- | --- | --- |
| `jps` | 找 Java 进程 | `jps -l` |
| `jstat` | 观察 GC 统计 | `jstat -gc <pid> 1000` |
| `jcmd` | JVM 诊断 | `jcmd <pid> VM.flags` |
| `jstack` | 线程栈 | `jstack <pid>` |
| `jmap` | 堆信息和 dump | `jmap -histo <pid>` |
| `top -Hp` | 找高 CPU 线程 | `top -Hp <pid>` |
| `arthas` | 在线诊断 | `dashboard`、`thread`、`watch` |
| MAT | 离线 dump 分析 | dominator tree、retained size |

线上优先使用低影响命令。`jmap -dump`、`jmap -histo:live`、强制 Full GC 都可能造成暂停。

### 2. Arthas 常用命令

Arthas 适合线上低侵入排查：

| 命令 | 用途 |
| --- | --- |
| `dashboard` | 总览线程、内存、GC、运行时信息 |
| `thread` | 查看线程状态和热点线程 |
| `jvm` | 查看 JVM 参数和内存区 |
| `sc` | 搜索已加载类 |
| `sm` | 搜索类方法 |
| `jad` | 反编译线上实际加载的类 |
| `watch` | 观察方法入参、返回值、异常 |
| `trace` | 跟踪方法内部耗时 |
| `heapdump` | 导出堆转储 |

Arthas 的价值在于不用重新发版就能观察线上行为，但 `watch`、`trace`、`heapdump` 仍需控制范围和时机。

### 3. JMX 与图形工具

图形工具适合测试、压测和离线分析，不建议作为生产首选排查手段。远程 JMX 常见启动参数：

```shell
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=<port>
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
-Djava.rmi.server.hostname=<ip>
```

生产环境若使用 JMX，应开启认证和网络访问控制。图形工具能帮助观察堆、线程、类加载和 GC 趋势，但线上紧急排查优先命令行和日志。

### 4. CPU 100%

步骤：

```shell
top
top -Hp <pid>
printf "%x\n" <tid>
jstack <pid> > thread.dump
```

在 `jstack` 中搜索十六进制线程 id。

判断：

- GC 线程高：看 GC 日志和堆占用。
- 业务线程 RUNNABLE：看热点代码。
- 大量 BLOCKED：看锁竞争。
- 大量 WAITING：看线程池、队列、连接池和下游。

### 5. 内存持续上涨

步骤：

```shell
jstat -gc <pid> 1000
jmap -histo <pid> | head -30
jcmd <pid> GC.class_histogram
```

必要时 dump：

```shell
jmap -dump:format=b,file=/tmp/heap.hprof <pid>
```

常见持有链：

- 静态集合。
- 无界缓存。
- 无界队列。
- ThreadLocal 未清理。
- 监听器未注销。
- 任务闭包持有大对象。
- 旧 ClassLoader 被线程或静态变量持有。

### 6. OOM 类型

| 类型 | 常见原因 | 优先动作 |
| --- | --- | --- |
| Java heap space | 堆不足、泄漏 | 分析 heap dump |
| GC overhead limit exceeded | GC 无效 | 判断泄漏或堆太小 |
| Metaspace | 动态类、类加载器泄漏 | 查类加载和卸载 |
| Direct buffer memory | NIO、Netty、堆外缓存 | 查直接内存 |
| unable to create new native thread | 线程过多、栈太大 | 查线程池和 `-Xss` |
| StackOverflowError | 递归或调用过深 | 查异常栈 |

Native Memory Tracking 可用于堆外排查，但要启动时开启：

```shell
-XX:NativeMemoryTracking=summary
jcmd <pid> VM.native_memory summary
```

### 7. 频繁 Full GC

可能原因：

- 老年代空间不足。
- 对象晋升过快。
- 大对象直接进入老年代。
- Metaspace 触发回收。
- 显式 `System.gc()`。
- CMS 并发失败。
- G1 并发标记来不及。
- 内存泄漏。

处理方向：

- 看 Full GC 前后堆变化。
- 看 GC cause。
- 看老年代增长曲线。
- 调整新生代、Survivor、晋升年龄。
- 降低 CMS/G1 并发标记触发阈值。
- 排查缓存、队列、集合和 ThreadLocal。

### 8. 典型案例

大堆升级后更卡：

- 现象：32 位小堆系统升级为 64 位大堆后，用户反馈卡顿更严重。
- 原因：小堆时 GC 频繁但单次暂停较短；大堆后 Full GC 次数可能减少，但单次暂停变长。
- 处理：明确响应时间目标，评估 G1、ZGC 或 Shenandoah；同时优化对象生命周期和缓存策略。

线程池任务堆积：

- 现象：内存持续上涨，Full GC 后下降不明显。
- 原因：定时任务或异步任务不断提交，任务闭包持有业务对象，队列或延迟任务长期不释放。
- 处理：检查线程池队列长度、定时任务注册次数、拒绝策略和 lambda 捕获对象。

显式 GC：

- 现象：堆占用不高，但 Full GC 频繁。
- 原因：业务代码、第三方库、RMI 或监控组件调用 `System.gc()`。
- 处理：通过 GC Cause 定位；必要时使用 `DisableExplicitGC` 止血，但根因仍要找调用方。

Metaspace OOM：

- 现象：堆不高，但发生 `Metaspace` 或 `Compressed class space` OOM。
- 原因：动态代理、反射访问器、表达式引擎、热部署类加载器泄漏。
- 处理：观察类加载数量、类卸载情况和 ClassLoader 持有链。

## 十六、调优流程与生产实践

### 1. 明确目标

先确认：

- 目标 QPS / TPS。
- P95、P99、最大响应时间。
- 可接受 GC 暂停。
- 峰值流量持续时间。
- CPU、内存、容器限制。
- 是吞吐优先还是响应时间优先。

没有目标就是盲调。

### 2. 做容量估算

可用粗略模型：

```text
峰值 TPS * 单请求临时对象大小 * 对象存活时间 = 新生代压力
峰值并发数 * 单上下文持有对象大小 = 老年代压力
缓存条目数 * 单条目大小 = 缓存占用
线程数 * Xss = 线程栈占用
```

估算决定起点，压测修正模型。

### 3. 建立基线

至少收集：

- GC 日志。
- CPU、load、IO。
- 堆、非堆、直接内存。
- Young GC、Full GC 次数和耗时。
- 线程数和线程状态。
- QPS、响应时间、错误率。
- 对象分配速率。

### 4. 决策树

```text
响应慢
  -> CPU 高？
      -> GC 时间高：分析 GC 日志、堆占用、分配速率
      -> GC 时间不高：查业务热点、锁竞争、线程状态
  -> CPU 不高？
      -> 查 IO、连接池、下游依赖、线程等待

内存高
  -> Heap 高？
      -> Full GC 后下降：容量或参数问题
      -> Full GC 后不下降：泄漏或长生命周期对象
  -> Heap 不高但 RSS 高？
      -> 查直接内存、线程栈、mmap、JNI

Full GC 频繁
  -> Cause 是 System.gc：定位调用方
  -> Cause 是 Metadata：查类加载
  -> Old 区持续增长：查晋升、缓存、队列、泄漏
  -> G1/CMS 退化：提前并发回收或增加余量
```

### 5. 调优记录模板

每次调整建议记录：

```text
问题现象：
影响范围：
JDK 版本：
当前 JVM 参数：
业务峰值：
修改前指标：
GC 日志结论：
根因判断：
修改项：
预期收益：
风险与回滚：
修改后指标：
结论：
```

### 6. 生产参数模板

JDK 8 吞吐优先：

```shell
-server
-Xms4g
-Xmx4g
-XX:+UseParallelGC
-XX:+UseParallelOldGC
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+PrintGCCause
-Xloggc:/opt/app/logs/gc-%t.log
-XX:+UseGCLogFileRotation
-XX:NumberOfGCLogFiles=5
-XX:GCLogFileSize=20M
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/opt/app/dumps
```

JDK 11+ 通用服务：

```shell
-server
-Xms4g
-Xmx4g
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200
-Xlog:gc*,safepoint:file=/opt/app/logs/gc-%t.log:time,uptime,level,tags:filecount=5,filesize=20m
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/opt/app/dumps
-XX:ErrorFile=/opt/app/logs/hs_err_pid%p.log
```

低延迟大堆：

```shell
-server
-Xms8g
-Xmx8g
-XX:+UseZGC
-Xlog:gc*,safepoint:file=/opt/app/logs/gc-%t.log:time,uptime,level,tags:filecount=5,filesize=20m
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/opt/app/dumps
```

模板只是起点，必须通过压测验证。

### 7. 参数反模式

常见错误：

- 机器 8G 内存，`-Xmx8g`，没有给堆外留空间。
- 只设置 `-Xmx` 不设置 `-Xms`。
- 把 `MaxGCPauseMillis` 设置得过低。
- 生产不开 GC 日志。
- 所有服务套同一套参数。
- 大堆服务随手 dump。
- 内存泄漏时只扩大堆。

### 8. 上线前检查清单

- 明确吞吐量、P95、P99、最大暂停目标。
- 压测模型接近真实业务。
- 记录 JDK 版本、CPU、内存、容器限制。
- 开启 GC 日志并配置滚动。
- 配置 OOM dump 和错误日志路径。
- `-Xms` 与 `-Xmx` 合理。
- Metaspace、Direct Memory、线程栈有规划。
- 线程池有名称、有界队列、监控和拒绝策略。
- 缓存有上限、过期策略和命中率监控。
- 大对象、批处理大小、队列长度有上限。
- 没有无意义的 `System.gc()`。
- 没有依赖 `finalize()` 释放资源。
- 有回滚方案。
- 告警覆盖 Full GC、长暂停、堆使用率、线程数、RSS。

## 十七、参考资料

- OpenJDK JEP 248：<https://openjdk.org/jeps/248>
- OpenJDK JEP 363：<https://openjdk.org/jeps/363>
- OpenJDK JEP 379：<https://openjdk.org/jeps/379>
- OpenJDK JEP 439：<https://openjdk.org/jeps/439>
- OpenJDK JEP 490：<https://openjdk.org/jeps/490>
