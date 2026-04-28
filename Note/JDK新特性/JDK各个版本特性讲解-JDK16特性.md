# JDK各个版本特性讲解-JDK16特性

# 一、JAVA16概述

2021年3月16日正式发布,一共更新了17JEP

[https://openjdk.java.net/projects/jdk/16/](https://openjdk.java.net/projects/jdk/16/)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231628500.png)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425231628501.png)

# 二、语法层面变化

## 1.JEP 397：密封类（第二次预览）

> sealed class 第二次预览

通过密封的类和接口来增强Java编程语言,这是新的预览特性,用于限制超类的使用密封的类和接口限制其他可继承或者实现他们的其他类或接口.

> 目标

允许类或接口的开发者来控制那些代码负责实现,提供了比限制使用超类的访问修饰符声明方式更多选择,并通过支持对模式的详尽分析而支持模式匹配的未来发展

在java中,类层次构造通过集成实现代码的重用,父类的方法可以被许多子类继承.但是,类层次接口的目的并不总是重用代码.有时是对域中存在的各种可能性进行建模,例如图形库支持函的形状类型.当以这种方式使用类层次结构是,我们可能需要限制子类集从而简化建模.

虽然我们可以通过final来限定子类继承,但是这是绝对杜绝类子类,而类的密封是允许子类,但是限定是那个或者哪些.

## 2.JEP 394：instanceof 的模式匹配

> 概括

增强Java编程语言与*模式匹配*的 `instanceof`运算符。 [模式匹配](https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html) 允许更简洁、更安全地表达程序中的常见逻辑，即从对象中有条件地提取组件。

> 历史

模式匹配 `instanceof`由[JEP 305](https://openjdk.java.net/jeps/305)提出 并在 [JDK 14 中](https://openjdk.java.net/projects/jdk/14)作为 [预览功能提供](https://openjdk.java.net/jeps/12)。它由[JEP 375](https://openjdk.java.net/jeps/375)重新提出， 并在 [JDK 15 中](https://openjdk.java.net/projects/jdk/15)进行第二轮预览。

该 JEP 建议在 JDK 16 中完成该功能，并进行以下改进：

- 取消模式变量是隐式 final 的限制，以减少局部变量和模式变量之间的不对称性。
- 将 `instanceof`类型*S*的表达式与类型*T*的模式进行比较，使模式表达式成为编译时错误，其中*S*是*T*的子类型。（这个 `instanceof`表达式总是成功，然后毫无意义。相反的情况，模式匹配总是失败，已经是一个编译时错误。）

可以根据进一步的反馈合并其他改进。

> 原因

几乎每个程序都包含某种逻辑，这些逻辑结合了测试表达式是否具有特定类型或结构，然后有条件地提取其状态的组件以进行进一步处理。例如，所有 Java 程序员都熟悉 `instanceof`-and-cast 习语：

```java
if (obj instanceof String) {
    String s = (String) obj;    // grr...
    ...
}
```

有三件事情会在这里：测试（是 `obj`一 `String`？），转换（铸造 `obj`到 `String`），和一个新的局部变量的声明（`s`），这样我们就可以使用字符串值。这种模式很简单，所有 Java 程序员都可以理解，但由于几个原因，它并不是最理想的。它很乏味；应该不需要同时进行类型测试和强制转换（`instanceof`测试后你还会做什么 ？）。这个样板——特别是该类型的三个出现 `String`——混淆了后面更重要的逻辑。但最重要的是，重复提供了错误潜入程序中的机会。

我们相信 Java 是时候拥抱*模式匹配了*，而不是寻求临时解决方案。模式匹配允许简洁地表达对象的所需“形状”（*模式*），并允许各种语句和表达式根据其输入（*匹配*）测试该“形状” 。许多语言，从 Haskell 到 C#，都因为其简洁和安全而采用了模式匹配

这允许我们将上面繁琐的代码重构为以下内容：

```java
if (obj instanceof String s) {
    // Let pattern matching do the work!
    ...
}
```

## 3.JEP 395：记录

> 概述

使用[记录](https://cr.openjdk.java.net/~briangoetz/amber/datum.html)增强 Java 编程语言，[记录](https://cr.openjdk.java.net/~briangoetz/amber/datum.html)是充当不可变数据的透明载体的类。记录可以被认为是*名义元组*。

> 历史

记录由[JEP 359](https://openjdk.java.net/jeps/359)提出 并在[JDK 14 中](https://openjdk.java.net/projects/jdk/14)作为 [预览功能提供](https://openjdk.java.net/jeps/12)。

作为对反馈的回应，[JEP 384](https://openjdk.java.net/jeps/384)对该设计进行了改进， 并在[JDK 15 中](https://openjdk.java.net/projects/jdk/15)作为第二次预览功能交付 。第二次预览的改进如下：

- 在第一个预览版中，规范构造函数必须是 `public`. 在第二个预览中，如果隐式声明了规范构造函数，则其访问修饰符与记录类相同；如果显式声明了规范构造函数，则其访问修饰符必须提供至少与记录类一样多的访问权限。
- `@Override`注释的含义被扩展为包括注释方法是记录组件的显式声明的访问器方法的情况。
- 为了强制使用紧凑构造函数，分配给构造函数主体中的任何实例字段会导致编译时错误。
- 引入了声明本地记录类、本地枚举类和本地接口的能力。

该 JEP 建议在 JDK 16 中完成该功能，并进行以下改进：

- 放宽长期存在的限制，即内部类不能声明显式或隐式静态成员。这将变得合法，特别是将允许内部类声明作为记录类的成员。

可以根据进一步的反馈合并其他改进。

> 目标

- 设计一个面向对象的构造来表达简单的值聚合。
- 帮助开发人员专注于建模不可变数据而不是可扩展行为。
- 自动实现数据驱动的方法，例如 `equals`和访问器。
- 保留长期存在的 Java 原则，例如名义类型和迁移兼容性。

> 细节实现

人们普遍抱怨“Java 太冗长”或“仪式太多”。一些最严重的违规者是那些只不过是少数值的不可变 *数据载体*的类。正确编写这样的数据载体类涉及许多低价值、重复、容易出错的代码：构造函数、访问器 `equals`、`hashCode`、`toString`、 等。 例如，携带 x 和 y 坐标的类不可避免地以这样的方式结束：

```
class Point {
    private final int x;
    private final int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    int x() { return x; }
    int y() { return y; }

    public boolean equals(Object o) {
        if (!(o instanceof Point)) return false;
        Point other = (Point) o;
        return other.x == x && other.y == y;
    }

    public int hashCode() {
        return Objects.hash(x, y);
    }

    public String toString() {
        return String.format("Point[x=%d, y=%d]", x, y);
    }
}
```

开发人员有时试图通过省略诸如 的方法来偷工减料 `equals`，导致令人惊讶的行为或可调试性差，或者将替代但不完全合适的类压入服务中，因为它具有“正确的形状”并且他们还不想声明另一个班级。

集成开发环境帮助我们*写*的大部分代码的数据载体类，但没有做任何事情来帮助*读者*提炼出设计意图的“我是一个数据载体 `x`和 `y`”从几十个样板线。编写对少数值建模的 Java 代码应该更容易编写、阅读和验证是否正确。

虽然表面上将记录视为主要与样板减少有关，但我们选择了一个更具语义的目标：*将数据建模为数据*。（如果语义是正确的，样板将自行处理。）声明数据载体类应该简单而简洁*，默认情况下，* 这些类使它们的数据不可变，并提供生成和使用数据的方法的惯用实现。

*记录类*是 Java 语言中的一种新类。记录类有助于用比普通类更少的仪式对普通数据聚合进行建模。

记录类的声明主要由其*状态*的声明组成 ；然后记录类提交到与该状态匹配的 API。这意味着记录类放弃了类通常享有的自由——将类的 API 与其内部表示分离的能力——但作为回报，记录类声明变得更加简洁。

更准确地说，记录类声明由名称、可选类型参数、标题和正文组成。标题列出了记录类的*组件*，它们是构成其状态的变量。（此组件列表有时称为*状态描述*。）例如：

```
record Point(int x, int y) { }
```

因为记录类在语义上声称是其数据的透明载体，所以记录类会自动获取许多标准成员：

- 对于头部中的每个组件，两个成员：一个 `public`与组件同名和返回类型的访问器方法，以及一个 `private` `final`与组件类型相同的字段；
- 一个规范构造函数，其签名与标头相同，并将每个私有字段分配给 `new` 实例化记录的表达式中的相应参数；
- `equals`以及 `hashCode`确保两个记录值相同的方法，如果它们是相同的类型并且包含相同的组件值；和
- 一种 `toString`返回所有记录组件的字符串表示形式及其名称的方法。

换句话说，记录类的头部描述了它的状态，即它的组件的类型和名称，而 API 是从该状态描述中机械地和完全地派生出来的。API 包括用于构建、成员访问、平等和显示的协议。（我们希望未来的版本支持解构模式以实现强大的模式匹配。）

## 4_JEP 390：基于值的类的警告

> 概括

将原始包装类指定为基于值的，并弃用它们的构造函数以进行删除，提示新的弃用警告。提供有关对 Java 平台中任何基于值的类的实例进行同步的不当尝试的警告。

> 原因

在[瓦尔哈拉项目](https://openjdk.java.net/projects/valhalla/)正在推行显著增强了Java编程模型的形式*原始类*。这些类声明它们的实例是无身份的并且能够进行内联或扁平化表示，其中实例可以在内存位置之间自由复制并仅使用实例字段的值进行编码。

原始类的设计和实现已经足够成熟，我们可以自信地预期在未来的版本中将 Java 平台的某些类迁移为原始类。

迁移的候选者 在 API 规范中被非正式地指定为 [*基于值的类*](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/doc-files/ValueBased.html)。从广义上讲，这意味着它们对标识对类的行为不重要的不可变对象进行编码，并且它们不提供实例创建机制，例如公共构造函数，保证每次调用具有唯一标识。

原始包装类（`java.lang.Integer`、`java.lang.Double`等）也旨在成为原始类。这些类满足大多数被指定为基于值的要求，但它们公开了不推荐使用的（自 Java 9 起）`public`构造函数。通过对定义进行一些调整，它们也可以被视为基于值的类。

基于值的类的客户端通常不受原始类迁移的影响，除非它们违反了使用这些类的建议。特别是，在发生迁移的未来 Java 版本上运行时：

1. 这些相等 (per `equals`) 的类的实例也可能被认为是相同的 (per `==`)，这可能会破坏依赖于 `!=`正确行为的结果的程序。
2. 尝试使用 `new Integer`,`new Double`等创建包装类实例，而不是隐式装箱或对 `valueOf`工厂方法的调用，将产生 `LinkageError`。
3. 尝试在这些类的实例上进行同步将产生异常。

这些更改对某些人来说可能不方便，但解决方法很简单：如果您需要标识，请使用不同的类——通常是您自己定义的类，但 `Object`也 `AtomicReference`可能是合适的。迁移到原始类的好处——更好的性能、可靠的相等语义、统一原始类和类——将非常值得带来的不便。

(1) 已经因为避免承诺基于值的类的工厂方法中的唯一身份而受到劝阻。没有一种实用的方法可以自动检测忽略这些规范并依赖当前实现行为的程序，但我们预计这种情况很少见。

我们可以通过弃用包装类构造函数来阻止（2）移除，这将放大编译对这些构造函数的调用时发生的警告。现有 Java 项目的很大一部分（可能占其中的 1%-10%）调用包装类构造函数，但在许多情况下，它们仅打算在 Java 9 之前的版本上运行。许多流行的开源项目已经通过从源代码中删除包装构造函数调用来响应 Java 9 的弃用警告，鉴于“弃用以删除”警告的紧迫性，我们可以期待更多这样做。用于缓解此问题的其他功能在*依赖项*部分中进行了描述。

我们可以通过在编译时和运行时实施警告来阻止 (3)，以通知程序员他们的同步操作在未来版本中将不起作用。

# 三、API层面变化

## 1.JEP 338：Vector API（孵化器）

> 概述

提供的初始迭代[培养箱模块](https://openjdk.java.net/jeps/11)， `jdk.incubator.vector`来表达向量计算在运行时可靠地编译到最佳矢量的硬件指令上支持的CPU架构，从而实现优异的性能等效标量计算。

> 目标

- *清晰简洁的 API：* API 应能够清晰简洁地表达广泛的矢量计算，这些矢量计算由一系列矢量操作组成，这些矢量操作通常在循环内组成，可能还有控制流。应该可以表达对向量大小（或每个向量的车道数）通用的计算，从而使此类计算能够在支持不同向量大小的硬件之间移植（如下一个目标中详述）。
- *平台不可知：* API 应与体系结构无关，支持在支持向量硬件指令的多个 CPU 体系结构上的运行时实现。与平台优化和可移植性冲突的 Java API 中的常见情况一样，偏向于使 Vector API 具有可移植性，即使某些特定于平台的习语不能直接用可移植代码表达。x64 和 AArch64 性能的下一个目标代表了支持 Java 的所有平台上的适当性能目标。在[ARM可缩放矢量扩展](https://arxiv.org/pdf/1803.06185.pdf)（SVE）在这方面特别关注，以确保API能支持这种架构，即使是写没有已知的生产硬件实现的。
- *在 x64 和 AArch64 架构上可靠的运行时编译和性能：* Java 运行时，特别是 HotSpot C2 编译器，应在有能力的 x64 架构上将向量操作序列编译为相应的向量硬件指令序列，例如[Streaming SIMD](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions)支持的那些 [扩展](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions)(SSE) 和[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) (AVX) 扩展，从而生成高效和高性能的代码。程序员应该相信他们表达的向量操作将可靠地映射到相关的硬件向量指令。这同样适用于编译为[Neon](https://en.wikipedia.org/wiki/ARM_architecture#Advanced_SIMD_(Neon))支持的向量硬件指令序列的有能力的 ARM AArch64 架构。
- *优雅降级：* 如果向量计算无法在运行时完全表示为硬件向量指令序列，要么是因为架构不支持某些所需指令，要么是因为不支持另一种 CPU 架构，那么 Vector API 实现应优雅降级并且仍然起作用。这可能包括如果矢量计算无法充分编译为矢量硬件指令，则向开发人员发出警告。在没有向量的平台上，优雅降级将产生与手动展开循环竞争的代码，其中展开因子是所选向量中的通道数。

> 动机

Vector API 旨在通过提供一种在 Java 中编写复杂矢量算法的机制来解决这些问题，使用 HotSpot 中预先存在的矢量化支持，但使用用户模型使矢量化更加可预测和健壮。手工编码的向量循环可以表达 `hashCode`自动向量化器可能永远不会优化的高性能算法（例如向量化或专门的数组比较）。这种显式矢量化 API 可能适用于许多领域，例如机器学习、线性代数、密码学、金融和 JDK 本身的用法。

# 四、其他变化

## 1.JEP 347：启用 C++14 语言功能

> 概括

允许在 JDK C++ 源代码中使用 C++14 语言特性，并给出关于哪些特性可以在 HotSpot 代码中使用的具体指导。

> 目标

通过 JDK 15，JDK 中 C++ 代码使用的语言特性已经被限制在 C++98/03 语言标准。在 JDK 11 中，代码已更新以支持使用较新版本的 C++ 标准进行构建，尽管它还没有使用任何新功能。这包括能够使用支持 C++11/14 语言功能的各种编译器的最新版本进行构建。

此 JEP 的目的是正式允许 JDK 中的 C++ 源代码更改以利用 C++14 语言功能，并提供有关哪些功能可以在 HotSpot 代码中使用的具体指导。

> 描述

要利用 C++14 语言功能，需要在构建时进行一些更改，具体取决于平台编译器。还需要指定各种平台编译器的最低可接受版本。应明确指定所需的语言标准；较新的编译器版本可能会默认使用较新的且可能不兼容的语言标准。

- Windows：JDK 11 需要 Visual Studio 2017。（早期版本会生成配置时警告，可能会也可能不起作用。）对于 Visual Studio 2017，默认的 C++ 标准是 C++14。`/std:c++14` 应添加该选项。将完全放弃对旧版本的支持。
- Linux：将 `-std=gnu++98`编译器选项替换为 `-std=c++14`. gcc 的最低支持版本是 5.0。
- macOS：将 `-std=gnu++98`编译器选项替换为 `-std=c++14`. clang 的最低支持版本是 3.5。
- AIX/PowerPC：将 `-std=gnu++98`编译器选项 替换为 `-std=c++14`并要求使用 xlclang++ 作为编译器。xlclang++ 的最低支持版本是 16.1。

## 2.JEP 357：从 Mercurial 迁移到 Git

> 概括

将 OpenJDK 社区的源代码存储库从 Mercurial (hg) 迁移到 Git。

> 目标

- 将所有单存储库 OpenJDK 项目从 Mercurial 迁移到 Git
- 保留所有版本控制历史，包括标签
- 根据 Git 最佳实践重新格式化提交消息
- 将[jcheck](https://openjdk.java.net/projects/code-tools/jcheck/)、 [webrev](https://openjdk.java.net/projects/code-tools/webrev/)和 [defpath](https://openjdk.java.net/projects/code-tools/defpath/)工具[移植](https://openjdk.java.net/projects/code-tools/defpath/)到 Git
- 创建一个工具来在 Mercurial 和 Git 哈希之间进行转换

> 动机

迁移到 Git 的三个主要原因：

1. 版本控制系统元数据的大小
2. 可用工具
3. 可用主机

转换后的存储库的初始原型显示版本控制元数据的大小显着减少。例如，存储库的 `.git`目录对于 `jdk/jdk`Git 大约为 300 MB，`.hg`对于 Mercurial，该目录大约为 1.2 GB，具体取决于所使用的 Mercurial 版本。元数据的减少保留了本地磁盘空间并减少了克隆时间，因为需要通过线路的位更少。Git 还具有 仅克隆部分历史记录的*浅层克隆*，从而为不需要整个历史记录的用户提供更少的元数据。

与 Mercurial 相比，与 Git 交互的工具还有很多：

- 所有文本编辑器都具有 Git 集成，无论是本机还是插件形式，包括[Emacs](https://www.gnu.org/software/emacs/) （[magit](https://magit.vc/)插件）、[Vim](https://www.vim.org/) （[fugitive.git](https://github.com/tpope/vim-fugitive)插件）、 [VS Code](https://code.visualstudio.com/)（内置）和 [Atom](https://atom.io/)（内置）。
- 几乎所有集成开发环境 (IDE) 还附带开箱即用的 Git 集成，包括 [IntelliJ](https://www.jetbrains.com/idea/)（内置）、 [Eclipse](https://eclipse.org/)（内置）、 [NetBeans](https://netbeans.org/)（内置）和 [Visual Studio](https://visualstudio.microsoft.com/)（内置）。
- 有多个桌面客户端可用于与本地 Git 存储库进行交互。

最后，有许多选项可用于托管 Git 存储库，无论是自托管还是作为服务托管。

## 3.JEP 376：ZGC：并发线程堆栈处理

> 概述

将 ZGC 线程堆栈处理从安全点移动到并发阶段。

> 目标

- 从 ZGC 安全点中删除线程堆栈处理。
- 使堆栈处理变得懒惰、协作、并发和增量。
- 从 ZGC 安全点中删除所有其他每线程根处理。
- 提供一种机制，其他 HotSpot 子系统可以通过该机制延迟处理堆栈。

> 原因

ZGC 垃圾收集器 (GC) 旨在使 HotSpot 中的 GC 暂停和可扩展性问题成为过去。到目前为止，我们已经将所有随堆大小和元空间大小扩展的 GC 操作从安全点操作移到并发阶段。这些包括标记、重定位、引用处理、类卸载和大多数根处理。

仍然在 GC 安全点中完成的唯一活动是根处理的子集和有时间限制的标记终止操作。根包括 Java 线程堆栈和各种其他线程根。这些根是有问题的，因为它们会随着线程的数量而扩展。由于大型机器上有许多线程，根处理成为一个问题。

为了超越我们今天所拥有的，并满足在 GC 安全点内花费的时间不超过一毫秒的期望，即使在大型机器上，我们也必须将这种每线程处理，包括堆栈扫描，移出并发阶段。

在这项工作之后，在 ZGC 安全点操作中基本上不会做任何重要的事情。

作为该项目的一部分构建的基础设施最终可能会被其他项目使用，例如 Loom 和 JFR，以统一延迟堆栈处理。

## 4.JEP 380：Unix 域套接字通道

> 概述

将 Unix 域 ( `AF_UNIX`) 套接字支持添加到包中的[套接字通道](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/nio/channels/SocketChannel.html)和[服务器套接字通道](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/nio/channels/ServerSocketChannel.html)API `java.nio.channels`。扩展[继承的通道机制](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/nio/channels/spi/SelectorProvider.html#inheritedChannel())以支持 Unix 域套接字通道和服务器套接字通道。

> 目标

Unix 域套接字用于同一主机上的进程间通信 (IPC)。它们在大多数方面类似于 TCP/IP 套接字，不同之处在于它们由文件系统路径名而不是 Internet 协议 (IP) 地址和端口号寻址。此 JEP 的目标是支持在主要 Unix 平台和 Windows 中通用的 Unix 域套接字的所有功能。Unix 域套接字通道在读/写行为、连接设置、服务器对传入连接的接受、与选择器中的其他非阻塞可选通道的多路复用以及相关套接字的支持方面的行为与现有的 TCP/IP 通道相同选项。

> 原因

对于本地、进程间通信，Unix 域套接字比 TCP/IP 环回连接更安全、更高效。

- Unix 域套接字严格用于同一系统上的进程之间的通信。不打算接受远程连接的应用程序可以通过使用 Unix 域套接字来提高安全性。
- Unix 域套接字受到操作系统强制的、基于文件系统的访问控制的进一步保护。
- Unix 域套接字比 TCP/IP 环回连接具有更快的设置时间和更高的数据吞吐量。
- 对于需要在同一系统上的容器之间进行通信的容器环境，Unix 域套接字可能是比 TCP/IP 套接字更好的解决方案。这可以使用位于共享卷中的套接字来实现。

Unix 域套接字长期以来一直是大多数 Unix 平台的一个功能，现在 Windows 10 和 Windows Server 2019 都支持。

> 具体操作

为了支持 Unix 域套接字通道，我们将添加以下 API 元素：

- 一个新的套接字地址类，`java.net.UnixDomainSocketAddress`；
- 甲 `UNIX`在现有的恒定值 `java.net.StandardProtocolFamily`枚举;
- 新 `open`的工厂方法 `SocketChannel`，并 `ServerSocketChannel`指定协议族
- 更新 `SocketChannel`和 `ServerSocketChannel`规范以指定 Unix 域套接字的通道的行为方式。

## 5.JEP 386：Alpine Linux 端口

> 概括

将 JDK 移植到 Alpine Linux，以及在 x64 和 AArch64 架构上使用 musl 作为其主要 C 库的其他 Linux 发行版，

> 原因

[Musl](https://musl.libc.org/)是针对基于 Linux 的系统的 ISO C 和 POSIX 标准中描述的标准库功能的实现。包括[Alpine Linux](https://alpinelinux.org/)和 [OpenWrt](https://openwrt.org/)在内的几个 Linux 发行[版](https://alpinelinux.org/)都基于 musl，而其他一些发行版提供了可选的 musl 包（例如[Arch Linux](https://www.archlinux.org/)）。

Alpine Linux 发行版由于其较小的镜像大小，被广泛用于云部署、微服务和容器环境。例如，[用于 Alpine Linux 的](https://hub.docker.com/_/alpine)Docker[基础映像](https://hub.docker.com/_/alpine)小于 6 MB。使 Java 在此类设置中开箱即用将允许 Tomcat、Jetty、Spring 和其他流行框架在此类环境中本地工作。

通过使用 `jlink` [(JEP 282)](https://openjdk.java.net/jeps/282)来减少 Java 运行时的大小，用户将能够创建一个更小的图像来运行特定的应用程序。应用程序所需的模块集可以通过 `jdeps`命令确定。例如，如果目标应用程序仅依赖于 `java.base`模块，则带有 Alpine Linux 的 Docker 映像和仅带有该模块的 Java 运行时和服务器 VM 大小为 38 MB。

同样的动机也适用于嵌入式部署，它们也有大小限制。

> 具体操作

该 JEP 旨在整合上游的[Portola 项目](https://openjdk.java.net/projects/portola/)。

此端口将不支持 HotSpot Serviceability Agent 的附加机制。

要在 Alpine Linux 上构建 JDK 的 musl 变体，需要以下软件包：

alpine-sdk alsa-lib alsa-lib-dev autoconf bash cups-dev cups-libs fontconfig fontconfig-dev freetype freetype-dev grep libx11 libx11-dev libxext libxext-dev libxrandr libxrandr-dev libxrender libxrender-dev libxt libxt-dev libxtst -dev linux-headers zip

安装这些软件包后，JDK 构建过程将照常工作。

如果有需求，可以在后续增强中实现其他架构的 Musl 端口。

## 6.JEP 387：弹性元空间

> 概括

更及时地将未使用的 HotSpot 类元数据（即元*空间*）内存*返还*给操作系统，减少元空间占用空间，并简化元空间代码以降低维护成本。

> 原因

自从在[JEP 122 中出现](https://openjdk.java.net/jeps/122)以来，元空间就因高堆外内存使用而臭名昭著。大多数普通应用程序没有问题，但很容易以错误的方式刺激元空间分配器，从而导致过多的内存浪费。不幸的是，这些类型的病例情况并不少见。

元空间内存在每类加载器管理[领域](https://en.wikipedia.org/wiki/Region-based_memory_management)。一个 arena 包含一个或多个 *chunks*，它的加载器通过廉价的指针碰撞从中分配。元空间块是粗粒度的，以保持分配操作的效率。然而，这会导致使用许多小类加载器的应用程序遭受不合理的高元空间使用。

当类加载器被回收时，其元空间领域中的块被放置在空闲列表中以供以后重用。然而，这种重用可能不会在很长一段时间内发生，或者可能永远不会发生。因此，具有大量类加载和卸载活动的应用程序可能会在元空间空闲列表中累积大量未使用的空间。如果没有碎片化，该空间可以返回给操作系统以用于其他目的，但通常情况并非如此。

> 具体操作

我们建议用基于[好友的分配方案](https://en.wikipedia.org/wiki/Buddy_memory_allocation)替换现有的元空间内存分配器。这是一种古老且经过验证的算法，已成功用于例如 Linux 内核。这种方案将使以更小的块分配元空间内存变得可行，这将减少类加载器的开销。它还将减少碎片，这将使我们能够通过将未使用的元空间内存返回给操作系统来提高弹性。

我们还将根据需要将操作系统中的内存延迟提交到 arenas。这将减少从大型竞技场开始但不立即使用它们或可能永远不会使用它们的全部范围的加载器的占用空间，例如引导类加载器。

最后，为了充分利用伙伴分配提供的弹性，我们将元空间内存安排成大小均匀的*颗粒*，这些*颗粒*可以相互独立地提交和取消提交。这些颗粒的大小可以通过一个新的命令行选项来控制，它提供了一种控制虚拟内存碎片的简单方法。

可以在[此处](https://cr.openjdk.java.net/~stuefe/JEP-Improve-Metaspace-Allocator/review-guide/review-guide-1.0.html)找到详细描述新算法的文档。工作原型作为[JDK 沙箱存储库中的一个分支](https://hg.openjdk.java.net/jdk/sandbox/shortlog/38a706be96d4)存在。

## 7.JEP 388：Windows/AArch64 端口

> 概括

将 JDK 移植到 Windows/AArch64

> 动机

随着新的消费级和服务器级 AArch64 (ARM64) 硬件的发布，由于最终用户的需求，Windows/AArch64 已成为一个重要的平台。

> 具体操作

通过扩展之前为 Linux/AArch64 移植[(JEP 237](https://openjdk.java.net/jeps/237) )所做的工作，我们已将 JDK 移植到 Windows/AArch64 。此端口包括模板解释器、C1 和 C2 JIT 编译器以及垃圾收集器（串行、并行、G1、Z 和 Shenandoah）。它支持 Windows 10 和 Windows Server 2016 操作系统。

这个 JEP 的重点不是移植工作本身，它大部分是完整的，而是将移植集成到 JDK 主线存储库中。

目前，我们有十几个变更集。我们将对共享代码的更改保持在最低限度。我们的更改将 AArch64 内存模型的支持扩展到 Windows，解决了一些 MSVC 问题，将 LLP64 支持添加到 AArch64 端口，并在 Windows 上执行 CPU 功能检测。我们还修改了构建脚本以更好地支持交叉编译和 Windows 工具链。

新平台代码本身仅限于 15 (+4) 个文件和 1222 行 (+322)。

可[在此处](https://github.com/microsoft/openjdk-aarch64)获得抢先体验的二进制文件。

## 8.JEP 389：外部链接器 API（孵化器）

> 概括

介绍一个 API，它提供对本机代码的静态类型、纯 Java 访问。此 API 与外部内存 API ( [JEP 393](https://openjdk.java.net/jeps/393) ) 一起，将大大简化绑定到本机库的其他容易出错的过程。

为该 JEP 提供基础的 Foreign-Memory Access API 最初由[JEP 370](https://openjdk.java.net/jeps/370)提出，并于 2019 年底作为[孵化 API](https://openjdk.java.net/jeps/11)面向 Java 14，随后由面向 Java 的[JEP 383](https://openjdk.java.net/jeps/383)和[JEP 393](https://openjdk.java.net/jeps/393)更新分别为 15 和 16。外部内存访问 API 和外部链接器 API 共同构成了[巴拿马项目的](https://openjdk.java.net/projects/panama/)关键可交付成果。

> 目标

- 易用性：用卓越的纯 Java 开发模型替换 JNI。
- *C 支持：* 这项工作的初始范围旨在在 x64 和 AArch64 平台上提供与 C 库的高质量、完全优化的互操作性。
- 通用性：外部链接器 API 和实现应该足够灵活，随着时间的推移，可以适应其他平台（例如，32 位 x86）和用非 C 语言编写的外部函数（例如 C++、Fortran）。
- 性能：外部链接器 API 应提供与 JNI 相当或优于 JNI 的性能。

> 原因

从 Java 1.1 开始，Java 就支持通过[Java 本地接口 (JNI)](https://docs.oracle.com/en/java/javase/14/docs/specs/jni/index.html)调用本地方法，但这条路径一直是艰难而脆弱的。使用 JNI 包装本机函数需要开发多个工件：Java API、C 头文件和 C 实现。即使有工具帮助，Java 开发人员也必须跨多个工具链工作，以保持多个依赖于平台的工件同步。这对于稳定的 API 来说已经够难了，但是当试图跟踪正在进行的 API 时，每次 API 发展时更新所有这些工件是一个重大的维护负担。最后，JNI 主要是关于代码的，但代码总是交换数据，而 JNI 在访问本机数据方面提供的帮助很小。出于这个原因，开发人员经常求助于解决方法（例如直接缓冲区或 `sun.misc.Unsafe`) 这使得应用程序代码更难维护，甚至更不安全。

多年来，出现了许多框架来填补 JNI 留下的空白，包括[JNA](https://github.com/java-native-access/jna)、[JNR](https://github.com/jnr/jnr-ffi)和[JavaCPP](https://github.com/bytedeco/javacpp)。JNA 和 JNR 从用户定义的接口声明动态生成包装器；JavaCPP 生成由 JNI 方法声明上的注释静态驱动的包装器。虽然这些框架通常比 JNI 体验有显着改进，但情况仍然不太理想，尤其是与提供一流的本地互操作的语言相比时。例如，Python 的[ctypes](https://docs.python.org/3/library/ctypes.html)包可以在没有任何胶水代码的情况下动态包装本机函数。其他语言，例如[Rust](https://rust-lang.github.io/rust-bindgen/)，提供了从 C/C++ 头文件机械地派生本机包装器的工具。

最终，Java 开发人员应该能够（大部分）*只使用*任何被认为对特定任务有用的本地库——我们已经看到现状如何阻碍实现这一目标。此 JEP 通过引入高效且受支持的 API — 外部链接器 API — 来纠正这种不平衡，该 API 提供外部函数支持，而无需任何干预 JNI 胶水代码。它通过将外部函数公开为可以在纯 Java 代码中声明和调用的方法句柄来实现这一点。这大大简化了编写、构建和分发依赖于外部库的 Java 库和应用程序的任务。此外，Foreign Linker API 与 Foreign-Memory Access API 一起，为第三方本机互操作框架（无论是现在还是未来）都可以可靠地构建提供了坚实而高效的基础。

## 9.JEP 392：打包工具

> 概括

提供 `jpackage`用于打包自包含 Java 应用程序的工具。

> 历史

该 `jpackage`工具是由[JEP 343](https://openjdk.java.net/jeps/343)在 JDK 14 中作为孵化工具引入的。它仍然是 JDK 15 中的一个孵化工具，以便有时间提供额外的反馈。现在可以将其从孵化提升为生产就绪功能。作为这种转换的结果，`jpackage`模块的名称将从 更改 `jdk.incubator.jpackage`为 `jdk.jpackage`。

与 JEP 343 相关的唯一实质性变化是我们用 `--bind-services`更通用的 `--jlink-options`选项替换了该选项，[如下所述](https://openjdk.java.net/jeps/392#Runtime-images)。

> 目标

创建一个基于遗留 JavaFX[`javapackager`](https://docs.oracle.com/javase/9/tools/javapackager.htm#JSWOR719)工具的打包工具：

- 支持原生打包格式，为最终用户提供自然的安装体验。这些格式包括 `msi`与 `exe`在Windows，`pkg`并 `dmg`在MacOS，以及 `deb`和 `rpm`在Linux上。
- 允许在打包时指定启动时间参数。
- 可以直接从命令行调用，也可以通过 `ToolProvider`API 以编程方式调用。

> 原因

许多 Java 应用程序需要以一流的方式安装在本机平台上，而不是简单地放置在类路径或模块路径上。应用程序开发人员提供一个简单的 JAR 文件是不够的；他们必须提供适合本机平台的可安装包。这允许以用户熟悉的方式分发、安装和卸载 Java 应用程序。例如，在 Windows 上，用户希望能够双击一个软件包来安装他们的软件，然后使用控制面板来删除软件；在 macOS 上，用户希望能够双击 DMG 文件并将他们的应用程序拖到应用程序文件夹中。

jpackage 工具还可以帮助填补过去技术留下的空白，例如从 Oracle 的 JDK 11 中删除的 Java Web Start，以及 `pack200`在 JDK 14 ( [JEP 367](https://openjdk.java.net/jeps/367) ) 中删除的。开发人员可以 `jlink`将 JDK 拆分为所需的最小模块集，然后使用打包工具生成可部署到目标机器的压缩、可安装映像。

以前，为了满足这些要求，`javapackager`Oracle 的 JDK 8 随附了一个名为的打包工具。但是，作为 JavaFX 删除的一部分，它已从 Oracle 的 JDK 11 中删除。

> 具体操作

该 `jpackage`工具将 Java 应用程序打包到特定于平台的包中，其中包含所有必需的依赖项。应用程序可以作为普通 JAR 文件的集合或作为模块的集合提供。支持的特定于平台的包格式是：

- Linux：`deb`和 `rpm`
- macOS：`pkg`和 `dmg`
- 窗户：`msi`和 `exe`

默认情况下，`jpackage`以最适合运行它的系统的格式生成包。

> 非模块化应用打包

假设您有一个由 JAR 文件组成的应用程序，所有这些文件都在一个名为 的目录中 `lib`，并且 `lib/main.jar`包含主类。然后命令

```
$ jpackage --name myapp --input lib --main-jar main.jar
```

将以本地系统的默认格式打包应用程序，将生成的包文件保留在当前目录中。如果 `MANIFEST.MF`文件中 `main.jar`没有 `Main-Class`属性，则必须明确指定主类：

```
$ jpackage --name myapp --input lib --main-jar main.jar \
  --main-class myapp.Main
```

包的名称将是 `myapp`，但包文件本身的名称会更长，并以包类型结尾（例如，`myapp.exe`）。该软件包将包含应用程序的启动器，也称为 `myapp`. 为了启动应用程序，启动程序将从输入目录复制的每个 JAR 文件放置在 JVM 的类路径上。

如果您希望以默认格式以外的格式生成包，请使用该 `--type`选项。例如，要在 macOS 上生成 `pkg`文件而不是 `dmg`文件：

```
$ jpackage --name myapp --input lib --main-jar main.jar --type pkg
```

> 模块化应用打包

如果您有一个模块化应用程序，由目录中的模块化 JAR 文件和/或 JMOD 文件组成，并且 `lib`模块中的主类 `myapp`，则命令

```
$ jpackage --name myapp --module-path lib -m myapp
```

会打包。如果 `myapp`模块未标识其主类，那么您必须再次明确指定：

```
$ jpackage --name myapp --module-path lib -m myapp/myapp.Main
```

（创建模块化 JAR 或 JMOD 文件时，您可以使用和工具 `--main-class`选项指定主类。）`jar``jmod`

## 10.JEP 393：外部内存访问 API（第三个孵化器）

> 概述

引入 API 以允许 Java 程序安全有效地访问 Java 堆之外的外部内存。

> 历史

Foreign-Memory Access API 最初由[JEP 370](https://openjdk.java.net/jeps/370)提出，并于 2019 年底作为[孵化 API](https://openjdk.java.net/jeps/11)面向 Java 14 ，后来由[JEP 383](https://openjdk.java.net/jeps/383)重新孵化，后者在 2020 年中期面向 Java 15。该 JEP 建议结合基于反馈，并在 Java 16 中重新孵化 API。此 API 更新中包含以下更改：

- `MemorySegment`和 `MemoryAddress`接口之间的角色分离更清晰；
- 一个新的接口，`MemoryAccess`它提供了通用的静态内存访问器，以便 `VarHandle`在简单的情况下最大限度地减少对API的需求；
- 支持*共享*段；和
- 使用 `Cleaner`.

> 目标

- *通用性：*单个 API 应该能够对各种外部内存（例如，本机内存、持久内存、托管堆内存等）进行操作。
- *安全性：*无论操作何种内存，API 都不应该破坏 JVM 的安全性。
- *控制：*客户端应该可以选择如何释放内存段：显式（通过方法调用）或隐式（当该段不再使用时）。
- *可用性：*对于需要访问外部内存的程序，API 应该是传统 Java API（如[`sun.misc.Unsafe`](https://hg.openjdk.java.net/jdk/jdk/file/tip/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java).

> 原因

许多 Java 程序访问外部内存，例如[Ignite](https://apacheignite.readme.io/v1.0/docs/off-heap-memory)、[mapDB](http://www.mapdb.org/)、[memcached](https://github.com/dustin/java-memcached-client)、[Lucene](https://lucene.apache.org/)和 Netty 的[ByteBuf](https://netty.io/wiki/using-as-a-generic-library.html) API。通过这样做，他们可以：

- 避免与垃圾收集相关的成本和不可预测性（尤其是在维护大型缓存时）；
- 跨多个进程共享内存；和
- 通过将文件映射到内存（例如，通过[`mmap`](https://en.wikipedia.org/wiki/Mmap)）来序列化和反序列化内存内容。

不幸的是，Java API 并没有为访问外部内存提供令人满意的解决方案：

- Java 1.4 中引入的[`ByteBuffer`API](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/nio/ByteBuffer.html)允许创建*直接*字节缓冲区，这些缓冲区在堆外分配，因此允许用户直接从 Java 操作堆外内存。但是，直接缓冲区是有限的。例如，无法创建大于 2 GB 的直接缓冲区，因为 `ByteBuffer`API 使用 `int`基于索引的方案。此外，使用直接缓冲区可能很麻烦，因为与它们相关联的内存的释放留给垃圾收集器；也就是说，只有在垃圾收集器认为直接缓冲区不可访问后，才能释放相关内存。多年来，为了克服这些和其他限制（例如[4496703](https://bugs.openjdk.java.net/browse/JDK-4496703)、[6558368](https://bugs.openjdk.java.net/browse/JDK-6558368)，[4837564](https://bugs.openjdk.java.net/browse/JDK-4837564)和[5029431](https://bugs.openjdk.java.net/browse/JDK-5029431)）。许多这些限制源于这样一个事实，即 `ByteBuffer`API 不仅设计用于堆外内存访问，而且还用于生产者/消费者在字符集编码/解码和部分 I/O 操作等领域交换批量数据。
- 开发人员可以从 Java 代码访问外部内存的另一个常见途径是[`Unsafe`API](https://hg.openjdk.java.net/jdk/jdk/file/tip/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java)。由于相对通用的寻址模型 `Unsafe`，公开了许多适用于堆上和堆外访问的内存访问操作（例如，`Unsafe::getInt`和 `putInt`）。使用 `Unsafe`访问内存是非常有效的：所有的内存访问操作被定义为热点JVM内部函数，所以存储器存取操作是由热点JIT编译器优化常规。然而，`Unsafe`根据定义，API 是*不安全的*——它允许访问*任何*内存位置（例如，`Unsafe::getInt`获取 `long`地址）。这意味着 Java 程序可以通过访问一些已经释放的内存位置来使 JVM 崩溃。最重要的是，`Unsafe`API 不是受支持的 Java API，一直[强烈不鼓励](https://web.archive.org/web/19980215011039/http://java.sun.com/products/jdk/faq/faq-sun-packages.html)使用它。
- 使用 JNI 访问外部内存是一种可能，但与此解决方案相关的固有成本使其在实践中很少适用。整个开发流程很复杂，因为 JNI 要求开发人员编写和维护 C 代码片段。JNI 本身也很慢，因为每次访问都需要从 Java 到本机的转换。

总之，在访问外部内存时，开发人员面临着两难选择：他们应该选择安全但有限（并且可能效率较低）的路径，例如 `ByteBuffer`API，还是应该放弃安全保证并接受危险且不受支持的路径？`Unsafe`API？

此 JEP 为外部内存访问引入了安全、受支持且高效的 API。通过为访问外部内存的问题提供有针对性的解决方案，开发人员将摆脱现有 API 的限制和危险。他们还将享受更高的性能，因为新的 API 将在设计时考虑到 JIT 优化。

> 具体操作

Foreign-Memory Access API 作为一个名为的[孵化器模块](https://openjdk.java.net/jeps/11)提供 `jdk.incubator.foreign`，位于同名的包中。它引入了三个主要抽象：`MemorySegment`，`MemoryAddress`和 `MemoryLayout`：

- `MemorySegment`对具有给定空间和时间界限的连续内存区域进行建模，
- `MemoryAddress对一个地址建模，该地址可以驻留在堆上或堆外，并且
- `MemoryLayout`是对内存段内容的编程描述。

可以从各种来源创建内存段，例如本机内存缓冲区、内存映射文件、Java 数组和字节缓冲区（直接或基于堆）。例如，可以按如下方式创建本机内存段：

```
try (MemorySegment segment = MemorySegment.allocateNative(100)) {
   ...
}
```

这将创建一个与大小为 100 字节的本机内存缓冲区相关联的内存段。

内存段在*空间上是有界的*，这意味着它们有下限和上限。任何尝试使用该段访问这些边界之外的内存都将导致异常。

正如 `try`上面使用-with-resource 构造所证明的那样，内存段也是有*时间限制的*，这意味着它们必须被创建、使用，然后在不再使用时关闭。关闭一个段会导致额外的副作用，例如与该段相关联的内存的释放。任何访问已关闭内存段的尝试都会导致异常。空间和时间边界共同保证了外部内存访问 API 的安全性，从而保证它的使用不会使 JVM 崩溃。

## 11.JEP 396：默认情况下强封装 JDK 内部

> 概述

默认情况下，强封装 JDK 的所有内部元素，除了[关键的内部 API](https://openjdk.java.net/jeps/260#Description)，如 `sun.misc.Unsafe`. 允许最终用户选择自 JDK 9 以来一直默认的宽松强封装。

> 目标

- 继续提高 JDK 的安全性和可维护性，这是[Project Jigsaw](https://openjdk.java.net/projects/jigsaw)的主要目标之一。
- 鼓励开发人员从使用内部元素迁移到使用标准 API，以便他们和他们的用户可以轻松升级到未来的 Java 版本。

> 动机

多年来，各种库、框架、工具和应用程序的开发人员以损害安全性和可维护性的方式使用 JDK 的内部元素。特别是：

- 包的一些非 `public`类、方法和字段 `java.*`定义了特权操作，例如[在特定类加载器中定义新类的能力](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/ClassLoader.html#defineClass(java.lang.String,byte[],int,int))，而其他则传送敏感数据，例如[加密密钥](https://youtu.be/Vxfd3ehdAZc?t=1248)。尽管在 `java.*`包中，但这些元素是 JDK 的内部元素。外部代码通过反射使用这些内部元素会使平台的安全性处于危险之中。
- 包的所有类、方法和字段 `sun.*`都是 JDK 的内部 API。一些类，方法和属性 `com.sun.*`， `jdk.*`以及 `org.*`包也是内部API。这些 API 从来都不是标准的，从来没有得到支持，也[从来没有打算供外部使用](http://web.archive.org/web/19980215011039/http://java.sun.com/products/jdk/faq/faq-sun-packages.html)。外部代码使用这些内部元素是一种持续的维护负担。为了不破坏现有代码，保留这些 API 所花费的时间和精力可以更好地用于推动平台向前发展。

在 Java 9 中，我们通过利用模块来[限制对其内部元素的访问](https://openjdk.java.net/jeps/260)，提高了 JDK 的安全性和可维护性。模块提供*强封装*，这意味着

- 模块外的代码只能访问该 模块导出的包的 `public`和 `protected`元素，并且
- `protected` 此外，元素只能从定义它们的类的子类中访问。

强封装适用于编译时和运行时，包括编译代码尝试在运行时通过反射访问元素时。`public`导出包的非元素和未导出包的所有元素都被称为*强封装*。

在 JDK 9 及更高版本中，我们强烈封装了所有新的内部元素，从而限制了对它们的访问。然而，为了帮助迁移，我们故意选择不在运行时强封装 JDK 8 中存在的包的内容。因此类路径上的库和应用程序代码可以继续使用反射来访问非 `public`元素的 `java.*`包，以及所有元素 `sun.*`和其他内部包，用于封装在JDK 8存在这种安排被称为[*放松强大的封装性*](https://openjdk.java.net/jeps/261#Relaxed-strong-encapsulation)。

我们于 2017 年 9 月发布了 JDK 9。 JDK 的大多数常用内部元素现在都有[标准替代品](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool#JavaDependencyAnalysisTool-ReplaceusesoftheJDK'sinternalAPIs)。开发人员必须在三年中，从标准的API，如JDK的内部元素迁移走 `java.lang.invoke.MethodHandles.Lookup::defineClass`，`java.util.Base64`和 `java.lang.ref.Cleaner`。许多库、框架和工具维护者已经完成了迁移并发布了其组件的更新版本。我们现在准备迈出下一步，按照 `sun.misc.Unsafe`Project Jigsaw 最初计划的那样，对 JDK 的所有内部元素进行强封装——除了关键的内部 API，例如。

> 具体操作

松弛强封装由启动器选项控制 `--illegal-access`。这个选项由[JEP 261](https://openjdk.java.net/jeps/261#Relaxed-strong-encapsulation)引入，被挑衅地命名以阻止它的使用。它目前的工作原理如下：

- `--illegal-access=permit`安排 JDK 8 中存在的每个包都对未命名模块中的代码[开放](https://docs.oracle.com/javase/specs/jls/se15/html/jls-7.html#jls-7.7.2)。因此，类路径上的代码可以继续使用反射来访问包的非公共元素 `java.*`，以及 `sun.*` JDK 8 中存在的包和其他内部包的所有元素。对任何此类元素的第一次反射访问操作会导致发出警告，但在那之后不会发出警告。

  自 JDK 9 以来，此模式一直是默认模式。
- `--illegal-access=warn``permit`除了针对每个非法反射访问操作发出警告消息之外，其他都相同。
- `--illegal-access=debug``warn`除了为每个非法反射访问操作发出警告消息和堆栈跟踪之外，其他都相同。
- `--illegal-access=deny`禁用所有非法访问操作，但由其他命令行选项启用的操作除外，*例如*， `--add-opens`。

作为对 JDK 的所有内部元素进行强封装的下一步，我们建议将 `--illegal-access` 选项的默认模式从 `permit`更改为 `deny`。通过此更改，默认情况下将不再打开JDK 8 中存在且不包含[关键内部 API 的包](https://openjdk.java.net/jeps/260#Description)；[此处](https://cr.openjdk.java.net/~mr/jigsaw/jdk8-packages-denied-by-default)提供完整列表 。 **该 `sun.misc`包仍将由 `jdk.unsupported`模块导出，并且仍可通过反射访问。**

我们还将修改[Java 平台规范中](https://cr.openjdk.java.net/~iris/se/9/java-se-9-fr-spec-01/#Relaxing-strong-encapsulation)的相关文本，以禁止在任何 Java 平台实现中默认打开任何包，除非该包明确声明 `open`在其包含模块的声明中。

的 `permit`，`warn`以及 `debug`该模式 `--illegal-access`选项将继续工作。这些模式允许最终用户根据需要选择宽松的强封装。

我们预计未来的 JEP 会 `--illegal-access`完全取消该选项。那时将无法通过单个命令行选项打开所有 JDK 8 包。仍然可以使用[`--add-opens`](https://openjdk.java.net/jeps/261#Breaking-encapsulation)命令行选项或 [`Add-Opens`](https://openjdk.java.net/jeps/261#Packaging:-Modular-JAR-files)JAR-file 属性来打开特定的包。

为了准备最终删除该 `--illegal-access`选项，我们将弃用它作为本 JEP 的一部分进行删除。因此，为 `java`启动器指定该选项将导致发出弃用警告。
