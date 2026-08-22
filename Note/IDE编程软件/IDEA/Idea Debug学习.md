# IntelliJ IDEA Debug 技巧学习笔记

IntelliJ IDEA 是 JetBrains 提供的集成开发环境。开发者常把它简称为“IDEA”。Debug（调试）解决的是“程序已经运行，但结果、状态或执行顺序不符合预期”这一类问题：在不靠猜测的前提下暂停程序，观察运行时数据，验证故障假设，并逐步缩小问题范围。

本文以 Java 和 IntelliJ IDEA 2026.2 界面为主。不同版本、操作系统、主题和 Keymap（键位映射）的按钮位置或快捷键可能略有差异，动作名称和调试思路保持一致。文中的真实界面截图取自 JetBrains 官方产品文档，已保存到本地资源目录以便离线阅读；图片仅用于教学说明，相关权利归 JetBrains 或相应权利人。

## 1 从一个可观察的错误开始

### 1.1 业务现象：会员订单没有打折

订单有两件商品，价格分别为 120 元和 80 元。会员折扣为 10%，因此应付金额应为 180 元，程序却输出 200 元。这个问题有三个已经明确的事实：

1\. 输入是 `[120, 80]`，会员标记是 `true`。

2\. 期望输出是 `180.00`。

3\. 实际输出是 `200.00`，程序没有抛出异常。

“没有异常但结果错误”通常属于逻辑错误。异常栈无法直接指出答案，需要在数据发生变化的位置观察中间状态。

### 1.2 学习阶段与成功判据

| 阶段 | 阅读范围 | 要解决的问题 | 成功判据 |
| --- | --- | --- | --- |
| 第一次闭环 | 第 1～2 章 | 会不会启动 Debug 并找到错误变量 | 能解释为什么折扣率是 `0.0` |
| 日常调试 | 第 3～6 章 | 如何少走代码、精确停住、读取状态 | 能为一个故障选择断点、栈帧和求值方式 |
| Java 专项 | 第 7～8 章 | Stream、并发和异步链路怎么追 | 能还原数据变换或线程交错过程 |
| 工程调试 | 第 9～12 章 | 远程 JVM、热更新和框架代码怎么查 | 能在控制风险的前提下拿到运行时证据 |
| 复盘与面试 | 第 13～16 章 | 如何形成可复用的排障方法 | 能从现象构造假设、验证并输出证据链 |

第一次阅读可以在完成第 2 章后立即动手，再继续阅读第 3～6 章。远程调试涉及网络与运行环境，实践前应先掌握普通断点和栈帧。

## 2 完成第一次 Debug 闭环

### 2.1 准备最小 Java 程序

前置条件是 IntelliJ IDEA 已配置 JDK（Java Development Kit，Java 开发工具包）17 或更高版本。创建 `OrderPricingDemo.java`，写入以下代码：

~~~java
import java.math.BigDecimal;
import java.util.List;

public class OrderPricingDemo {

    public static void main(String[] args) {
        List<BigDecimal> itemPrices = List.of(
                new BigDecimal("120.00"),
                new BigDecimal("80.00")
        );

        BigDecimal actual = calculatePayable(itemPrices, true);

        System.out.println("expected = 180.00");
        System.out.println("actual   = " + actual);
    }

    static BigDecimal calculatePayable(
            List<BigDecimal> itemPrices,
            boolean member
    ) {
        BigDecimal subtotal = itemPrices.stream()
                .reduce(BigDecimal.ZERO, BigDecimal::add);

        double discountRate = member ? 10 / 100 : 0; // 故意保留的错误
        BigDecimal discount = subtotal.multiply(
                BigDecimal.valueOf(discountRate)
        );

        return subtotal.subtract(discount);
    }
}
~~~

直接运行后应看到：

~~~text
expected = 180.00
actual   = 200.000
~~~

此时先不要修改代码。Debug 的目标是拿到一条可验证的因果链：`member=true`，但 `discountRate=0.0`，所以 `discount=0`，最终没有减掉折扣。

### 2.2 在数据首次偏离预期的位置下断点

断点应靠近“正确状态可能第一次变成错误状态”的位置。这里最值得观察的是 `discountRate` 的赋值语句，而不是最终的 `println`。单击编辑器左侧行号槽，也称 gutter（装订线），会出现红色断点图标。

![在 IntelliJ IDEA 中设置行断点](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/62d8ba3f-3c29-586f-8eb9-3a8f34735a6c.png)

> 图 1：红色圆点表示行断点。蓝色高亮在调试期间表示当前执行点，即下一条将执行的源码位置。界面来源：[JetBrains 第一个 Java 调试教程](https://www.jetbrains.com/help/idea/debugging-your-first-java-application.html)。

执行步骤如下：

1\. 在 `double discountRate = ...` 所在行单击左侧行号槽。

2\. 在 `main` 方法旁单击运行图标，选择 `Debug 'OrderPricingDemo.main()'`。

3\. 程序运行到断点后暂停。确认编辑器出现当前执行点，Debug 工具窗口显示当前线程、栈帧和变量。

4\. 若程序直接结束，先检查断点是否落在可执行行、启动的是不是当前类的 Debug 配置，以及红色断点是否被全局静音。

### 2.3 认识第一次暂停后的界面

![IntelliJ IDEA Debug 工具窗口全貌](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/37ccd44b-1712-5e1b-808b-103948e719fb.png)

> 图 2：上方编辑器给出执行点和行内变量，下方 Debug 工具窗口显示 Frames（栈帧）、Threads（线程）、Variables（变量）与 Console（控制台）。界面来源：[JetBrains 第一个 Java 调试教程](https://www.jetbrains.com/help/idea/debugging-your-first-java-application.html)。

首次暂停时先读四处：

1\. 当前执行行：判断这行是“尚未执行”还是“刚执行完”。通常高亮表示下一条将执行的语句，因此想看赋值后的值，需要执行一次 Step Over。

2\. Variables：确认参数和局部变量是否符合输入预期。

3\. Frames：确认当前方法是谁调用的，调用链是否走到了预期实现。

4\. Console：确认程序在暂停前已经产生了哪些输出或异常。

行内调试值把局部变量直接显示在源码旁，适合快速对照循环每一步的变化。

![源码旁的行内变量值](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fcb2f4b2-e4ce-54d4-b1dc-84cf7fdf16cf.png)

> 图 3：变量值出现在对应源码右侧，减少编辑器与 Variables 面板之间的视线切换。界面来源：[JetBrains 第一个 Java 调试教程](https://www.jetbrains.com/help/idea/debugging-your-first-java-application.html)。

### 2.4 用一次 Step Over 找到根因

断在 `discountRate` 赋值行时，按 Step Over（步过）执行当前语句。随后观察：

~~~text
member = true
subtotal = 200.00
discountRate = 0.0
~~~

Java 中 `10 / 100` 的两个操作数都是 `int`，因此先执行整数除法，结果为 `0`；随后才把 `0` 转换为 `double` 的 `0.0`。变量声明为 `double` 并不会反向改变除法的运算类型。

将代码修改为：

~~~java
double discountRate = member ? 10.0 / 100 : 0;
~~~

重新运行，成功判据是：

~~~text
expected = 180.00
actual   = 180.000
~~~

这次调试形成了完整证据链：业务输入正确，折扣率计算错误，错误来自整数除法，修改后输出与期望一致。一个有效的 Debug 结论应能回答“错误数据从哪一行开始出现”和“修复后用什么结果证明问题消失”。

## 3 用栈帧、线程和变量还原运行现场

### 3.1 调试器与 JVM 如何协作

IntelliJ IDEA 的 Java 调试器通过 JDI（Java Debug Interface，Java 调试接口）与目标 JVM 交互，底层通信通常使用 JDWP（Java Debug Wire Protocol，Java 调试线协议）。断点命中后，JVM 按断点的挂起策略暂停一个或全部线程，调试器再读取线程、栈帧、局部变量和对象字段。

~~~mermaid
flowchart LR
    A["开发者在源码上设置断点"] --> B["IntelliJ IDEA 调试器"]
    B -->|"JDI 请求"| C["JDWP 通信层"]
    C --> D["目标 JVM"]
    D --> E["线程执行到可映射的字节码位置"]
    E --> F{"挂起策略"}
    F -->|"All"| G["暂停全部线程"]
    F -->|"Thread"| H["只暂停命中线程"]
    G --> I["读取栈帧、局部变量、对象字段"]
    H --> I
    I --> B
~~~

图中的断点最终绑定到已加载类的字节码位置。源码行没有可执行字节码、源码与运行中的 class 文件不一致、类尚未加载或符号信息缺失时，断点可能显示为空心、警告或无效状态。

### 3.2 栈帧记录一次方法调用的局部现场

每次方法调用都会向当前线程的调用栈压入一个 frame（栈帧）。栈帧保存该次调用的参数、局部变量、当前执行位置和求值上下文。方法返回时，对应栈帧弹出。

![线程、栈帧与变量面板](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/b09cfcf2-c918-569d-88ac-95cc73ba1cc5.png)

> 图 4：左侧选择线程与栈帧，右侧变量随当前栈帧变化。点击调用者栈帧，可以查看调用发生时调用者的局部状态。界面来源：[JetBrains 第一个 Java 调试教程](https://www.jetbrains.com/help/idea/debugging-your-first-java-application.html)。

调试 `calculatePayable` 时，栈顶是当前方法，下一帧通常是 `main`。选择 `main` 后，Variables 中显示的是 `main` 的 `itemPrices` 与 `actual`，而不是 `calculatePayable` 的 `subtotal`。这说明变量面板始终属于“当前选中的栈帧”，并非整个进程的变量总表。

排查参数为什么错误时，可以沿栈向下寻找第一个错误参数的调用者。若当前方法收到错误值，而调用者传参时已经错误，继续向上追；若调用者传入正确而当前方法内部变错，故障范围就落在当前方法。

### 3.3 线程决定“是谁正在执行”

每个 Java 线程都有独立调用栈。Web 应用中同一段 Controller 或 Service 代码可能同时被多个请求线程执行；线程池任务可能在 `pool-1-thread-3` 中运行；定时任务、消息消费者和异步回调也各自拥有线程上下文。

选择线程时优先核对：

1\. 线程名是否符合入口，例如 HTTP 请求线程、定时任务线程或业务线程池。

2\. 栈顶是否停在目标断点，调用链中是否出现预期 Controller、Service、Repository。

3\. 同名请求是否有多个并发副本，当前停住的是否是携带目标请求标识的那一个。

4\. 线程状态是 RUNNING、WAIT、SLEEPING 还是等待 monitor（监视器锁）；状态与业务现象是否相符。

### 3.4 读取变量时区分“值、引用和展示”

基本类型变量直接保存值；对象变量保存引用。Variables 展开的字段属于该引用所指向的对象。两个变量显示相同内容只说明当前展示结果相似，不能据此判断它们是否引用同一实例。对于引用类型，可以结合对象 ID（Identifier，标识符）、对象标记和 `==` 完成判断。

#### 3.4.1 开启对象 ID

对象 ID 是调试器为当前 JVM 中的具体实例显示的标识，例如 `User@823`。在同一次调试会话中，相同对象 ID 表示变量指向同一实例；不同对象 ID 表示它们是不同实例，即使字段内容和 `toString()` 结果完全相同。

开启步骤如下：

1\. 让程序命中断点，在 Debug 工具窗口中打开 Variables 区域。

2\. 在 Variables 区域右键，确认 `Show Types` 已启用。对象 ID 依赖类型显示，关闭 `Show Types` 后可能看不到 ID。

3\. 再次右键，选择 `Customize Data Views`，进入 Java 数据视图设置。

4\. 在 `Show` 区域勾选 `Object id`。也可以通过 `Settings/Preferences | Build, Execution, Deployment | Debugger | Data Views | Java` 进入相同设置。

假设程序中存在以下引用：

~~~java
User user1 = new User("张三");
User user2 = new User("张三");
User user3 = user1;
~~~

Variables 可能显示：

~~~text
user1 = User@823 {name = "张三"}
user2 = User@824 {name = "张三"}
user3 = User@823 {name = "张三"}
~~~

`user1` 和 `user3` 的对象 ID 都是 `823`，因此它们指向同一实例；`user2` 的字段内容相同，但对象 ID 是 `824`，因此它是另一个实例。对象 ID 用于当前调试会话内识别对象，不是稳定的业务标识，也不应理解为可长期保存的真实内存地址。具体设置可参考 [JetBrains 自定义调试数据视图](https://www.jetbrains.com/help/idea/customizing-views.html)。

#### 3.4.2 使用对象标记持续追踪实例

对象 ID 适合同时比较多个变量，对象标记适合跨栈帧、线程和变量名称持续追踪某一个实例。标记附着在对象实例上；后续即使另一个变量引用该对象，调试器仍会显示相同标签。

操作步骤如下：

1\. 程序命中断点后，在 Variables 或 Watches 中选中目标对象。基本类型没有独立对象实例，不能直接标记；可以标记包含该字段的对象。

2\. 右键选择 `Mark Object`，常见快捷键为 `F11`。macOS 若 `F11` 被系统功能占用，可以直接使用右键菜单。

3\. 输入便于识别的标签，例如 `targetUser`，并按需设置颜色。

4\. 切换栈帧或线程，观察其他变量。凡是指向该实例的变量，都会伴随相同标签。

5\. 若希望目标对象始终出现在 Watches 中，可以为 IDE 生成的标签表达式添加 Watch。表达式名称以界面和自动补全结果为准，常见形式类似 `targetUser_DebugLabel`。

6\. 排查完成后，右键该标签并选择 `Unmark Object`；`F11` 也可用于取消标记。

对象仍存活且调试会话未结束时，标签可以在 Evaluate Expression、Watch 或断点条件中辅助定位。对象标记的官方操作说明见 [JetBrains Labels](https://www.jetbrains.com/help/idea/examining-suspended-program.html#labels)。

#### 3.4.3 用 == 和 equals() 验证判断

对象 ID 和标签提供直观展示，Evaluate Expression 中的 `==` 可以直接验证两个引用是否指向同一对象：

~~~java
user1 == user2
user1 == user3
user1.equals(user2)
~~~

以上示例的典型结果是：

| 表达式 | 结果 | 含义 |
| --- | --- | --- |
| `user1 == user2` | `false` | 两个变量指向不同实例 |
| `user1 == user3` | `true` | 两个变量指向同一实例 |
| `user1.equals(user2)` | 取决于 `User.equals()` 的实现 | 比较业务内容或类定义的相等规则 |

对于引用类型，`==` 比较引用是否相同，`equals()` 比较类所定义的逻辑相等性。若类没有重写 `equals()`，它会继承 `Object.equals()` 的引用比较行为。两个变量都是 `null` 时，`user1 == user2` 也会得到 `true`，但此时不存在可供比较的对象实例。

对象的展示文本可能来自 `toString()` 或自定义 renderer（渲染器）。若 `toString()` 有副作用、访问数据库、触发懒加载或计算量很大，展开变量可能改变程序行为或拖慢调试。重要结论应同时核对原始字段，避免只相信格式化后的单行文本。

## 4 高效查看和试验运行时状态

### 4.1 快速求值适合回答一个小问题

选中源码表达式后使用 Quick Evaluate（快速求值），可以立即查看当前栈帧中的结果。它适合验证 `order.getId()`、`map.get("memberLevel")` 或布尔条件，不必永久添加 Watch。

![快速求值显示表达式结果](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/825213d8-5461-5e5e-8535-00f72d535a9f.png)

> 图 5：快速求值在编辑器附近显示结果，适合一次性确认。界面来源：[JetBrains 查看暂停程序状态](https://www.jetbrains.com/help/idea/examining-suspended-program.html)。

求值依赖当前栈帧和变量作用域。在调用者帧中存在的变量，切换到被调用方法帧后未必可见；已经离开生命周期的局部变量也无法求值。

### 4.2 Evaluate Expression 用于组合验证

Evaluate Expression（表达式求值）可以执行比悬停更完整的表达式，例如过滤集合、计算中间值或验证一个假设。

![Evaluate Expression 对话框](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5c9840f0-3c9b-57d5-b700-e13d2b980261.png)

> 图 6：表达式在当前暂停点的上下文中执行，结果显示在下方。界面来源：[JetBrains 查看暂停程序状态](https://www.jetbrains.com/help/idea/examining-suspended-program.html)。

在订单示例中可以求值：

~~~java
subtotal.multiply(new BigDecimal("0.10"))
~~~

若结果为 `20.0000`，说明商品小计和预期折扣公式成立，问题集中在 `discountRate` 的构造过程。

表达式求值会在目标 JVM 内执行真实代码。以下表达式可能改变现场：

~~~java
queue.poll()
order.setStatus("PAID")
repository.save(order)
iterator.next()
cache.computeIfAbsent(key, this::loadFromDatabase)
~~~

它们可能消费队列元素、修改对象、写数据库、推进迭代器或触发外部调用。排查时通常优先使用读取字段、纯计算和无副作用方法。无法确认方法是否纯净时，先看实现或改为直接展开字段。

### 4.3 Watch 适合持续观察同一表达式

Watch（监视表达式）会在每次暂停后重新计算，适合观察：

~~~java
subtotal.compareTo(new BigDecimal("100"))
itemPrices.size()
requestContext.getTraceId()
cache.containsKey(orderId)
~~~

Watch 的价值在于跨多次 Step 或断点命中保持同一观察维度。频繁命中的断点若配置了复杂 Watch，会增加调试开销。观察目标完成后应删除不再需要的 Watch。

### 4.4 Set Value 用于验证“如果状态不同会怎样”

变量或字段选中后使用 Set Value，可以在暂停期间修改运行时值。它适合快速验证分支，例如把 `discountRate` 从 `0.0` 改为 `0.1`，再继续执行，观察最终金额是否变为 180。

![修改暂停程序中的变量值](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/27358a47-ae5c-5d19-9981-572d4a6f10d0.png)

> 图 7：变量值被修改后，后续指令读取到新值。界面来源：[JetBrains 查看暂停程序状态](https://www.jetbrains.com/help/idea/examining-suspended-program.html)。

Set Value 证明的是“该状态是否足以改变后续结果”，不能替代源码修复。它不会自动修改源码、数据库或下一次运行的初始条件。若修改的是对象字段，其他线程可能立即观察到该值；若修改触发了业务副作用，当前进程已经不再代表原始故障现场。

### 4.5 查看集合时先缩小数据

大型 List、Map 或数组逐层展开效率很低。可以使用集合视图的排序、过滤和分页功能，或用无副作用表达式定位目标元素。对生产规模对象应先按业务键过滤，避免一次展开几十万个对象造成长时间求值。

可执行的观察顺序是：

1\. 先看集合 `size`，判断数量级是否合理。

2\. 再看首尾元素、目标业务键或异常索引。

3\. 比较排序前后、去重前后或过滤前后的数量。

4\. 如果集合来自数据库或远程接口，回到数据产生位置验证边界，不把内存快照当成完整源数据。

## 5 用正确的步进动作控制执行路径

### 5.1 Step Over、Step Into 与 Step Out 的边界

| 动作 | 行为 | 适用场景 | 常见误区 |
| --- | --- | --- | --- |
| Step Over（步过） | 执行当前行，在当前方法的下一可执行行暂停 | 已信任被调用方法，只关心输入输出 | 被调用方法内部断点仍可能命中 |
| Step Into（步入） | 进入当前行上的目标方法 | 怀疑方法内部计算错误 | 一行多个调用时可能进入非目标方法 |
| Smart Step Into（智能步入） | 在一行多个调用中选择具体目标 | 链式调用、参数嵌套调用 | 方法引用受 JVM 信息限制，未必可选 |
| Force Step Into（强制步入） | 忽略跳过规则进入库或 getter | 确实需要看 JDK 或第三方实现 | 容易陷入框架和基础库细节 |
| Step Out（步出） | 执行完当前方法并回到调用者 | 已确认当前方法无关，快速返回 | 方法剩余部分仍会真实执行 |
| Resume（继续） | 运行到下一个断点或结束 | 当前观察完成 | 断点太多时会在无关位置反复停下 |

智能步入会把一行中的候选调用标出来，让开发者选择真正要进入的目标。

![Smart Step Into 选择具体方法](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ddbc9bac-c4c3-540b-8d59-cd027d5d9167.png)

> 图 8：一行包含多个方法调用时，Smart Step Into 可以精确选择目标。界面来源：[JetBrains 单步执行程序](https://www.jetbrains.com/help/idea/stepping-through-the-program.html)。

实际调试通常采用“先 Step Over 看结果，结果异常再回到调用点并 Step Into”的策略。这样可以避免一开始深入大量正确实现。

### 5.2 Run to Cursor 快速跨过无关区间

Run to Cursor（运行到光标）会继续执行，直到到达光标所在行。它像一个一次性目标点，适合跨过已确认无关的循环或初始化代码。

![在编辑器中运行到光标](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bed4bbd7-ba8a-55aa-96fe-d029cebd63e1.png)

> 图 9：把光标放到目标行后执行 Run to Cursor，可快速抵达目标位置。普通模式下，途中已有断点仍可能命中。界面来源：[JetBrains 单步执行程序](https://www.jetbrains.com/help/idea/stepping-through-the-program.html)。

Force Run to Cursor 会忽略途中断点。使用前应确认那些断点不承担安全检查或关键证据采集，否则可能错过真正异常发生的位置。

### 5.3 Reset Frame 不是时间倒流

Reset Frame（重置栈帧，也常被称为 Drop Frame）会丢弃当前方法调用帧，把执行点退回调用者，从而允许重新进入该方法。

![在 Frames 中重置栈帧](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7946b859-236c-559f-acb8-82cfecbbddb9.png)

> 图 10：在栈帧旁执行 Reset Frame，可以重新进入刚才的方法。界面来源：[JetBrains 单步执行程序](https://www.jetbrains.com/help/idea/stepping-through-the-program.html)。

它只重建局部执行位置，不会撤销已经发生的全局状态和外部副作用。例如已写入的数据库记录、已发送的消息、已修改的静态字段、已输出的日志和已扣减的库存仍然存在。重置后再次执行可能产生重复写入。因此它适合纯计算或可控测试环境，不适合对具有不可逆副作用的生产路径随意使用。

## 6 用断点表达调试假设

### 6.1 断点类型与选择依据

| 断点类型 | 暂停条件 | 典型用途 | 成本与边界 |
| --- | --- | --- | --- |
| Line Breakpoint（行断点） | 执行到指定源码行 | 最常见的局部逻辑排查 | 依赖源码行与字节码映射 |
| Method Breakpoint（方法断点） | 进入或退出方法及实现 | 不清楚具体实现类时捕获调用 | 可能显著降低性能 |
| Field Watchpoint（字段观察点） | 字段被读取或写入 | 找出“谁改坏了这个字段” | 高频字段成本高；反射修改可能捕获不到 |
| Exception Breakpoint（异常断点） | 指定异常被抛出 | 在异常上下文仍完整时暂停 | 范围过宽会频繁停在框架内部 |
| Conditional Breakpoint（条件断点） | 到达行且条件为真 | 只捕获目标订单、用户或迭代次数 | 条件每次命中都需计算 |
| Logpoint（日志断点） | 到达位置时记录表达式 | 不暂停地观察时序与路径 | 表达式仍有执行成本与副作用风险 |

### 6.2 字段观察点追查“谁修改了值”

当某对象最终出现错误字段，而写入路径很多时，在字段声明处设置 Field Watchpoint 比在每个 setter 或调用方下断点更直接。

![字段声明处的 Field Watchpoint](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/de9da624-8344-5a9b-bf1b-e3e20899c5fd.png)

> 图 11：字段观察点位于字段声明行，可配置在读取、写入或两者发生时暂停。反射完成的字段修改可能无法捕获。界面来源：[JetBrains 断点文档](https://www.jetbrains.com/help/idea/using-breakpoints.html)。

例如 `Order.status` 意外从 `PAID` 变回 `CREATED`，可以先配置只在修改时暂停。命中后读取调用栈，就能找到写入入口。若字段被高频读取，避免同时开启读取观察，否则调试会产生大量暂停。

### 6.3 异常断点停在“抛出现场”

普通异常日志往往只保留栈和消息，抛出时的局部变量已经消失。Exception Breakpoint 会在异常对象刚被抛出时暂停，可以检查输入、分支与对象状态。

![从控制台异常栈创建异常断点](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/373b9308-e72e-51df-9ee4-c379688e5c77.png)

> 图 12：控制台异常栈旁可直接创建对应异常断点。建议从具体异常类型开始，再根据需要配置 caught/uncaught（已捕获/未捕获）和类过滤。界面来源：[JetBrains 断点文档](https://www.jetbrains.com/help/idea/using-breakpoints.html)。

排查 `NullPointerException` 时直接开启“Any Exception”常会停在框架内部大量被正常捕获的异常上。更精确的做法是：

1\. 选择具体异常类型，如 `IllegalStateException` 或业务异常。

2\. 根据现象决定只看 uncaught，还是连已捕获异常也看。

3\. 添加类过滤，只保留业务包，例如 `com.example.order.*`。

4\. 命中后先看 throw site（抛出点）的局部变量，再沿调用栈理解为何走到该分支。

### 6.4 条件断点把“偶发”变成“精确命中”

同一行会被调用数万次，而故障只发生在订单 `ORD-20260821-0098` 时，可以添加条件：

~~~java
"ORD-20260821-0098".equals(order.getOrderNo())
~~~

条件表达式应满足三点：计算轻量、空值安全、没有副作用。将常量放在 `equals` 左侧可以避免 `order.getOrderNo()` 为 `null` 时抛异常。循环问题还可以使用 Hit Count（命中次数），例如只在第 1000 次命中时暂停。

复杂条件在热点代码上可能非常慢。若条件断点消耗明显，可以在测试环境临时把条件写成源码中的 `if`，在 `if` 内设置普通断点；编译后的判断通常比调试器反复远程求值更高效。

### 6.5 Logpoint 在不中断时序的前提下采样

Logpoint 是 Suspend 关闭的断点。它可以输出表达式，却不暂停线程，适合锁竞争、超时、动画、消息消费等对时序敏感的场景。

![输入日志断点表达式](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/2cf27d1b-cf2b-52c8-be70-23de71e2c593.png)

> 图 13：日志断点在行号槽旁直接输入表达式；命中后结果进入 Debug Console。界面来源：[JetBrains Logpoints 文档](https://www.jetbrains.com/help/idea/logpoints.html)。

一个有用的日志断点信息应能关联请求、线程和业务对象，例如：

~~~java
"order=" + order.getOrderNo()
        + ", status=" + order.getStatus()
        + ", thread=" + Thread.currentThread().getName()
~~~

Logpoint 仍然在目标 JVM 内执行表达式。频率过高、字符串拼接过多或调用重方法时，会改变性能与时序。它适合短期诊断，排查结束后应禁用或删除。

### 6.6 Suspend All 与 Suspend Thread 的取舍

Suspend All 暂停全部线程，现场稳定，适合普通单线程逻辑；缺点是可能让心跳、锁持有者和后台线程一起停住。Suspend Thread 只暂停命中线程，其他线程继续运行，适合并发问题和不希望冻结整个服务的场景；缺点是共享状态会继续变化，Variables 中看到的对象可能在观察期间被其他线程修改。

选择原则如下：

1\. 单元测试、纯计算和普通请求链路优先使用 All，读取现场更稳定。

2\. 并发交错、锁竞争和时序敏感问题使用 Thread，并明确控制哪个线程先继续。

3\. 生产环境若不得不调试，优先考虑非暂停日志、指标、跟踪和转储；暂停线程仍可能破坏服务时延与锁行为。

## 7 调试 Stream、Lambda 与代理调用

### 7.1 Stream 调试的困难来自“多个阶段写在一条链上”

Java Stream 把输入依次传给 `map`、`filter`、`sorted`、`limit` 等操作。最终结果错误时，只看终止操作很难知道元素在哪一步被转换或丢弃。IntelliJ IDEA 的 Java Stream Debugger 插件可以把各阶段的输入输出可视化；该插件通常随 IDE 捆绑并默认启用。

~~~java
List<Integer> payableOrderIds = orderAmounts.entrySet().stream()
        .filter(entry -> entry.getValue().compareTo(new BigDecimal("100")) >= 0)
        .map(Map.Entry::getKey)
        .sorted()
        .toList();
~~~

把程序暂停在 Stream 链附近，调用 Trace Current Stream Chain，可以查看每个元素如何流过各阶段。

![Java Stream 各阶段的数据映射](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/115c0d69-2ce3-5c32-bd66-5e37971a4efc.png)

> 图 14：Stream Trace 将输入元素与过滤后的输出关联起来，能定位某个值在哪一步被保留、转换或丢弃。该功能仅适用于项目源码，库代码或反编译代码不适用。界面来源：[JetBrains 分析 Java Stream 操作](https://www.jetbrains.com/help/idea/analyze-java-stream-operations.html)。

Stream 调试的检查顺序是：

1\. 先验证源集合的数量和关键元素，排除输入本身错误。

2\. 逐阶段比较输入输出数量，找到第一次出现偏差的操作。

3\. 对目标元素查看映射关系，确认过滤条件、空值、比较方向和排序键。

4\. 如果链条过长或包含副作用，将关键步骤提取为命名方法或局部变量，提升测试和可读性。

### 7.2 Lambda 断点应选择具体表达式

一行中包含多个 Lambda 时，IntelliJ IDEA 可以在行内选择具体 Lambda 断点。方法引用受 JVM 调试信息限制，调用栈往往不如显式 Lambda 有信息量；当可追踪性很重要时，可以临时将 `service::handle` 改成 `item -> service.handle(item)`。

### 7.3 Spring AOP 与代理会改变实际调用路径

AOP（Aspect-Oriented Programming，面向切面编程）、事务、缓存和安全注解常通过代理对象生效。断在接口方法或调用点时，栈中可能先出现 JDK 动态代理、CGLIB（Code Generation Library，代码生成库）代理或框架拦截器，再进入业务实现。

排查 `@Transactional`、`@Cacheable` 或 `@Async` “没有生效”时，依次确认：

1\. 调用是否经过 Spring 容器提供的代理对象。

2\. 是否发生同一类内部的自调用，从而绕开代理入口。

3\. 当前对象运行时类型是否带有代理特征，调用栈中是否出现相应 interceptor（拦截器）。

4\. 事务或安全上下文是否跨线程丢失。

5\. 断点落在接口、代理还是实现类；源码是否与运行中的实际实现一致。

## 8 调试并发与异步代码

### 8.1 用单线程挂起稳定复现竞态

竞态条件依赖线程交错，普通日志可能因为改变时序而让问题消失。可以把断点的 Suspend 设置为 Thread，让两个线程分别停在同一临界位置，再手动决定谁先继续。

~~~java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class AddIfAbsentRace {
    private static final List<Integer> numbers =
            Collections.synchronizedList(new ArrayList<>());

    public static void main(String[] args) throws InterruptedException {
        Thread worker = new Thread(() -> addIfAbsent(17), "worker");
        worker.start();
        addIfAbsent(17);
        worker.join();
        System.out.println(numbers);
    }

    private static void addIfAbsent(int value) {
        if (!numbers.contains(value)) {
            numbers.add(value); // 在这里设置 Suspend: Thread
        }
    }
}
~~~

`synchronizedList` 只能保证单个 `contains` 和 `add` 调用各自同步，无法把“先检查再添加”组合成一个原子操作。可以人为构造以下交错：

~~~mermaid
sequenceDiagram
    participant M as "main 线程"
    participant W as "worker 线程"
    participant L as "共享 List"
    M->>L: "contains(17) = false"
    Note over M: "在 add 前暂停"
    W->>L: "contains(17) = false"
    Note over W: "在 add 前暂停"
    W->>L: "add(17)"
    M->>L: "add(17)"
    Note over L: "结果可能是 [17, 17]"
~~~

![断点配置为仅挂起命中线程](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/591ff496-037e-50cc-a382-081e37a61255.png)

> 图 15：在线断点属性中选择 Thread，两个线程可以分别停在目标行，再按指定顺序恢复。界面来源：[JetBrains 并发问题调试教程](https://www.jetbrains.com/help/idea/detect-concurrency-issues.html)。

修复时需要让“检查与添加”处于同一个同步边界内：

~~~java
private static void addIfAbsent(int value) {
    synchronized (numbers) {
        if (!numbers.contains(value)) {
            numbers.add(value);
        }
    }
}
~~~

验证不应只看一次运行结果。可以重复运行并保留人为交错步骤，同时补充并发测试或使用原子语义更合适的数据结构与 API（Application Programming Interface，应用程序编程接口）。

### 8.2 线程状态帮助判断等待原因

遇到“程序卡住”时，先暂停并查看全部线程。重点辨别：

| 状态或现场 | 可能含义 | 下一步 |
| --- | --- | --- |
| 多个线程停在同一 monitor 入口 | 锁竞争或持锁线程阻塞 | 找到持锁线程的栈顶及其外部调用 |
| 线程处于 `Object.wait` | 等待条件通知 | 检查通知方是否可达、条件是否更新 |
| 线程停在 `Future.get` | 等待异步结果 | 查看任务是否已提交、线程池是否耗尽 |
| 线程停在网络读取 | 下游慢或缺少超时 | 核对连接与读取超时、请求追踪 |
| 线程池队列增长且工作线程都阻塞 | 线程池饥饿 | 找到共同阻塞点，检查嵌套提交与锁 |

调试器暂停本身也会改变锁和超时行为，因此现场应与线程转储、指标和日志交叉验证。

### 8.3 异步栈把“任务提交点”和“执行点”连接起来

异步任务在一个线程提交，在另一个线程执行。普通线程栈只能看到执行线程，调用链往往从线程池内部开始。IntelliJ IDEA 的 Async Stack Traces（异步调用栈）会尝试把任务调度处的栈与执行处的栈关联起来。

~~~mermaid
flowchart LR
    A["HTTP 请求线程"] --> B["executor.submit(task)"]
    B --> C["任务进入线程池队列"]
    C --> D["worker-3 取出任务"]
    D --> E["task.run()"]
    E --> F["业务异常位置"]
    B -. "异步栈捕获调度现场" .-> F
~~~

![异步执行线程与调度线程的关联栈](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9b5c519e-2763-5d03-a2f4-0550e9382eef.png)

> 图 16：Frames 中同时呈现 worker 当前栈和任务最初被调度时的栈，虚线关系跨越线程边界。界面来源：[JetBrains 调试异步代码](https://www.jetbrains.com/help/idea/debug-asynchronous-code.html)。

官方文档说明，异步栈开箱即用地支持 Swing 和 Java Concurrency API（Java 并发应用程序编程接口）。以 `ExecutorService.submit()` 为例，IDEA 已经知道任务在哪里提交、在线程池的哪个入口执行，因此不需要修改业务代码。自定义线程池、任务队列或调度框架没有统一的提交与执行入口，IDEA 无法自动判断 `scheduler.publish(task)` 与 `worker.consume(task)` 属于同一条任务链，此时可以使用 JetBrains 异步注解补充关联信息。

#### 8.3.1 捕获点、插入点和匹配键共同还原异步因果链

Capture point（捕获点）位于任务提交一侧。程序经过捕获点时，IDEA 保存当前线程的调用栈，并把该调用栈与一个 key（匹配键）关联。JetBrains 注解中使用 `@Async.Schedule` 标记捕获点。

Insertion point（插入点）位于任务执行一侧。工作线程经过插入点时，IDEA 使用当前任务携带的匹配键找到此前保存的提交栈，再把它附加到当前工作线程的 Frames（栈帧）视图中。JetBrains 注解中使用 `@Async.Execute` 标记插入点。

~~~mermaid
sequenceDiagram
    participant R as "HTTP 请求线程"
    participant S as "自定义调度器"
    participant Q as "任务队列"
    participant W as "工作线程"
    participant I as "IntelliJ IDEA"
    R->>S: "schedule(task)"
    S->>I: "@Async.Schedule：保存提交栈，key = task"
    S->>Q: "放入同一个 task 对象"
    W->>Q: "取出 task"
    W->>I: "@Async.Execute：使用 task 查找提交栈"
    W->>W: "handle(task)"
    I->>I: "把执行栈与提交栈显示在同一条异步链中"
~~~

这里的“插入”只发生在调试器的栈帧展示中。提交线程可能已经返回，IDEA 不会重新执行提交线程中的方法，也不会在 JVM 中制造一条真实的跨线程调用栈。异步栈保存的是“当前任务从哪里被提交”的因果信息。

没有匹配键时，IDEA 无法在大量并发任务中判断当前执行的是哪一个任务。注解既可以标在方法上，也可以标在参数上：

| 注解位置 | 使用的匹配键 | 适用情况 |
| --- | --- | --- |
| 实例方法 | 当前接收对象 `this` | 调度和执行围绕同一个任务对象调用方法 |
| 方法参数 | 被注解的参数对象 | 任务对象通过队列或调度器从提交端传到执行端 |

参数方式更容易明确数据流。为了避免匹配歧义，通常让同一个任务对象引用经过队列到达执行端。执行端重新创建一个字段内容相同的新对象，不应被当作可靠的匹配方式。

#### 8.3.2 使用异步注解描述自定义调度器

下面的调度器在主线程中提交订单任务，在 `order-worker` 线程中执行。使用该示例前，需要把 JetBrains annotations 注解库加入项目；如果项目不能增加该依赖，也可以创建自定义注解，再到 IDEA 的异步栈设置中注册。

~~~java
import org.jetbrains.annotations.Async;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public final class OrderTaskScheduler {
    private final BlockingQueue<OrderTask> queue =
            new LinkedBlockingQueue<>();

    public static void main(String[] args) throws InterruptedException {
        OrderTaskScheduler scheduler = new OrderTaskScheduler();

        Thread worker = new Thread(() -> {
            try {
                scheduler.executeNext();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "order-worker");

        worker.start();
        scheduler.schedule(new OrderTask("ORDER-10086"));
        worker.join();
    }

    public void schedule(@Async.Schedule OrderTask task)
            throws InterruptedException {
        queue.put(task);
    }

    public void executeNext() throws InterruptedException {
        OrderTask task = queue.take();
        execute(task);
    }

    private void execute(@Async.Execute OrderTask task) {
        handle(task);
    }

    private void handle(OrderTask task) {
        System.out.println("处理订单：" + task.orderId()); // 在此处下断点
    }

    record OrderTask(String orderId) {
    }
}
~~~

该示例的关联过程如下：

1\. 主线程调用 `schedule(task)`，`@Async.Schedule` 让 IDEA 捕获主线程当前调用栈，并以参数 `task` 为匹配键。

2\. `BlockingQueue` 保存并交付同一个 `task` 对象，没有在队列两端重新创建任务。

3\. `order-worker` 调用 `execute(task)`，`@Async.Execute` 让 IDEA 使用该参数寻找此前保存的提交栈。

4\. 程序在 `handle()` 断点处暂停后，Frames 中应同时看到工作线程当前栈和主线程的任务提交栈。普通线程栈只会从 `order-worker` 的执行入口向下显示。

#### 8.3.3 启用功能并验证异步栈是否生效

本地调试时，在以下位置检查 Instrumenting agent（插装代理）是否启用：

~~~text
Settings
→ Build, Execution, Deployment
→ Debugger
→ Async Stack Traces
→ Instrumenting agent
~~~

验证自定义注解时可以按以下顺序操作：

1\. 在 `handle()` 中设置普通行断点，以 Debug 模式运行示例。

2\. 断点命中后打开 Debug 工具窗口的 Frames 区域，先确认当前线程是 `order-worker`。

3\. 在当前执行栈之外查找异步任务提交部分，其中应包含 `main()` 调用 `schedule()` 的现场。

4\. 如果只看到工作线程栈，依次检查 annotations 依赖、`@Async.Schedule`、`@Async.Execute`、两端匹配键以及 Instrumenting agent。自定义注解还需要在 `Settings | Build, Execution, Deployment | Debugger | Async Stack Traces | Configure annotations` 中注册为 Async Schedule 和 Async Execute 注解。

远程 JVM（Java Virtual Machine，Java 虚拟机）不会自动加载本地 IDEA 的插装代理。需要把 IDEA 安装目录中的 `debugger-agent.jar` 放到远程机器，并通过 `-javaagent:<debugger-agent.jar 路径>` 加载，之后再连接远程调试器。

#### 8.3.4 开销、节流与跨进程边界

异步栈需要在捕获点保存额外调用信息，并在执行点完成匹配，因此会增加目标 JVM 的时间和内存开销。`CompletableFuture` 回调或协程延续形成很长的异步链时，IDEA 可能进行 throttling（限流），仅在采集开销处于正常范围时继续收集。Frames 中出现 `Could not capture`，表示对应异步栈可能因限流而没有被采集，可以到 Async Stack Traces 设置中检查相关选项。

该机制适合进程内跨线程调度。任务经过消息中间件进入另一个 JVM 后，原任务对象引用和调试器保存的进程内关联通常不再存在，异步注解无法代替跨进程链路追踪。涉及远程服务或消息消费时，应同时保留 trace ID（追踪标识）、结构化日志和分布式追踪数据；它们负责跨进程关联，IDEA 异步栈负责当前调试进程内的线程切换现场。

官方说明与配置入口参见：[JetBrains：调试异步代码](https://www.jetbrains.com/zh-cn/help/idea/debug-asynchronous-code.html)。

## 9 远程 JVM 调试

### 9.1 远程调试的角色与数据流

远程调试不是把远程源码传到本机执行。应用仍在目标主机或容器内运行，本地 IntelliJ IDEA 通过 JDWP 连接目标 JVM，并用本地源码映射远程字节码。

~~~mermaid
flowchart LR
    A["本地 IntelliJ IDEA"] -->|"Attach：localhost:5005"| B["SSH 隧道或 kubectl port-forward"]
    B -->|"加密或受控转发"| C["远程主机/Pod 的 127.0.0.1:5005"]
    C --> D["JDWP Agent"]
    D --> E["目标 JVM"]
    E --> F["已加载 class 与运行时线程"]
    A --- G["本地同版本源码与依赖"]
    G -. "源码行映射" .-> F
~~~

图中安全边界位于转发层。JDWP 具有暂停线程、读取变量和执行表达式的高权限，不应直接暴露在公网或不可信网络。

### 9.2 创建 Remote JVM Debug 配置

在 Run/Debug Configurations 中新增 Remote JVM Debug，设置 Host、Port 和用于源码查找的 module classpath（模块类路径）。

![Remote JVM Debug 配置](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1450c0b2-7d3c-579e-b9d5-84c57c8e8431.png)

> 图 17：配置中可选择 Attach to remote JVM，默认示例端口为 5005，并由 IDE 按 JDK 版本生成启动参数。界面来源：[JetBrains 远程调试教程](https://www.jetbrains.com/help/idea/tutorial-remote-debug.html)。

JDK 9 或更高版本的典型目标 JVM 参数为：

~~~bash
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=127.0.0.1:5005 -jar order-service.jar
~~~

参数含义如下：

| 参数 | 含义 | 选择依据 |
| --- | --- | --- |
| `transport=dt_socket` | 使用 socket 传输调试协议 | 跨进程和远程主机的常用方式 |
| `server=y` | 目标 JVM 监听，等待调试器连接 | IntelliJ IDEA 采用 Attach 模式 |
| `suspend=n` | JVM 启动后立即运行 | 普通运行期问题 |
| `suspend=y` | main 执行前等待调试器 | 启动早期、类初始化或配置装配问题 |
| `address=127.0.0.1:5005` | 仅监听目标机回环地址 | 配合安全隧道，减少暴露面 |

若 IDE 为所选 JDK 生成的参数不同，应优先使用配置面板提供的命令行。特别是旧 JDK 的 `address` 语法与新版本存在差异。

### 9.3 建立受控连接

SSH（Secure Shell，安全外壳协议）隧道示例：

~~~bash
ssh -L 5005:127.0.0.1:5005 ops@app-host
~~~

Kubernetes Pod 的临时端口转发示例：

~~~bash
kubectl port-forward pod/order-service-7c9d8f 5005:5005
~~~

此时 IntelliJ IDEA 的 Host 使用 `localhost`，Port 使用 `5005`。连接成功并命中断点后，远程应用像本地调试一样显示变量和栈帧。

![远程 JVM 命中本地源码断点](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/79c053a1-41e9-5162-a028-3b628db63550.png)

> 图 18：蓝色执行点表示远程 JVM 已在与本地源码映射的行上暂停。界面来源：[JetBrains 远程调试教程](https://www.jetbrains.com/help/idea/tutorial-remote-debug.html)。

### 9.4 远程断点不命中的排查顺序

1\. 连接层：确认目标 JVM 正在监听、端口转发仍存活、IDE 状态为 Connected。

2\. 进程层：确认连接的是目标实例而不是同端口的另一个进程；在集群中核对 Pod 名称和版本。

3\. 类加载层：确认目标类已经加载。启动早期代码需要 `suspend=y` 或更早连接。

4\. 版本层：比对 Git 提交、构建制品、依赖版本和 class 校验信息。本地源码与远程字节码不一致时，行号可能错位。

5\. 路径层：确认请求确实进入该实例、分支和实现类。负载均衡环境可临时定向流量或使用唯一请求标识的条件断点。

6\. 编译层：确认编译时保留调试信息，目标行有可执行字节码，代码没有被编译器优化成其他结构。

### 9.5 生产环境的硬边界

远程 Debug 对服务可用性和数据安全有直接影响。实践时应遵守：

1\. JDWP 端口不得直接暴露公网，也不应通过公共 Service 或宽松安全组开放。

2\. 使用短时、可审计的隧道和最小权限访问，操作结束后关闭端口转发与调试 Agent。

3\. 优先在隔离实例、影子流量、预生产或可回放环境复现。生产实例的暂停会影响请求、锁、心跳和选主。

4\. 不在表达式求值中调用写数据库、发消息、扣库存或变更权限的方法。

5\. 连接前记录实例、版本、负责人和回滚方案；连接后优先使用条件断点、Thread 挂起或非暂停 Logpoint 控制影响。

6\. 敏感变量可能包含令牌、个人数据和密钥。截图、日志和导出信息应脱敏并控制传播范围。

## 10 HotSwap 与运行时试验

### 10.1 HotSwap 解决“修一小段代码但保留现场”

HotSwap（热替换）把重新编译的类定义加载到正在调试的 JVM，尽量保留进程和内存状态。修改方法体后，IDE 会显示 Code changed，并提供应用更新的入口。

![代码修改后的 HotSwap 提示](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/96c61aa9-97fb-58b0-a2b7-51d123556d60.png)

> 图 19：活动调试会话中修改代码后，可通过提示重新编译并加载变更。界面来源：[JetBrains Reload modified classes](https://www.jetbrains.com/help/idea/reloading-classes.html)。

典型流程是：

1\. 在 Debug 会话中修改现有方法体。

2\. 重新编译修改文件或执行 Reload Changed Classes。

3\. 等待 IDE 报告 HotSwap 完成。

4\. 让当前旧栈帧退出，再重新触发业务路径。

5\. 用相同输入验证新结果，同时补回自动化测试和正式源码提交。

### 10.2 标准 JVM HotSwap 的边界

标准 JVM 的类重定义能力通常适合修改现有方法体。新增或删除字段、修改方法签名、改变继承结构等结构性变更往往需要重启，具体能力取决于 JVM 和所用 Agent。

当前方法仍在调用栈中时，修改后的方法体不一定立即作用于该旧栈帧。Frames 可能把它标记为 obsolete（过时）。此时 Step Out 后重新触发调用，比在旧栈帧内判断 HotSwap 是否成功更可靠。

HotSwap 适合缩短本地验证反馈，不等同于发布流程。正式修复仍需编译、测试、制品构建、版本记录和部署验证。

## 11 控制调试器自身的性能与副作用

### 11.1 调试会改变被观察系统

断点挂起、条件表达式、Watch、对象 renderer、`toString()`、异步栈插桩和方法/字段断点都会消耗目标 JVM 的 CPU、内存或停顿时间。并发故障尤其容易因暂停而改变原有线程交错。

IntelliJ IDEA 的 Overhead 视图可以按调试特性统计命中次数和处理器时间，帮助识别高成本条件或观察点。

![Debug 工具窗口中的 Overhead 视图](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/22261dc5-cdfc-5eed-b982-0730a92a4ca7.png)

> 图 20：Overhead 展示各调试特性的命中和耗时，可直接停用高成本项。界面来源：[JetBrains 监控调试器开销](https://www.jetbrains.com/help/idea/monitor-debugger-overhead.html)。

### 11.2 常见高开销来源

| 来源 | 为什么慢 | 改进方式 |
| --- | --- | --- |
| 热循环上的复杂条件断点 | 每次经过都跨调试协议求值 | 缩小入口、增加便宜的前置条件或临时源码判断 |
| Method Breakpoint | 需要追踪大量方法进入/退出 | 找到具体实现后改用行断点 |
| Field Watchpoint | 高频字段访问产生大量事件 | 只监听修改，增加实例或类过滤 |
| 自动调用 `toString()` | 可能遍历大对象或触发懒加载 | 关闭自动展示，直接查看关键字段 |
| 大量 Watch | 每次暂停都重复求值 | 保留当前假设需要的最少表达式 |
| Suspend All | 暂停心跳、锁持有者和后台线程 | 并发场景评估改为 Suspend Thread |
| 异步调用栈插桩 | 需要捕获和关联调度栈 | 只在需要时启用并观察节流提示 |

### 11.3 判断求值是否安全

在执行表达式前用三个问题做检查：

1\. 它是否只读，还是会修改内存、文件、数据库、缓存、队列或远程系统？

2\. 它是否可能阻塞，例如等待锁、网络、Future 或数据库连接？

3\. 它是否可能抛异常、触发懒加载、推进流或消费一次性数据？

任一答案不确定时，优先读取原始字段、复制标识到独立查询工具，或在隔离环境重现。

## 12 常见工程场景的断点布置方法

### 12.1 HTTP 接口返回错误

以 Controller → Service → Repository 的典型链路为例，断点不宜从框架入口一路 Step Into。更高效的顺序是：

1\. 在 Controller 入参完成绑定后确认 DTO（Data Transfer Object，数据传输对象）和请求标识。

2\. 在 Service 的业务决策前后比较领域对象和返回值。

3\. 只有数据读写可疑时，进入 Repository 或数据库驱动层；先核对 SQL 参数和受影响行数。

4\. 在异常映射或响应组装处确认状态码、错误码和最终 DTO。

5\. 使用条件断点锁定唯一订单号或 trace ID，避免其他请求干扰。

若 Controller 参数已经错误，问题可能位于序列化、参数名、默认值或校验层；若 Service 返回正确而 HTTP 响应错误，范围应转向映射、序列化和异常处理。

### 12.2 Spring 事务没有回滚

建议观察四个证据：

1\. 调用是否从代理外部进入带 `@Transactional` 的 public 方法。

2\. 抛出的异常类型是否满足 rollback 规则，异常是否在方法内部被捕获并吞掉。

3\. 调用线程是否发生切换，事务上下文通常绑定在线程上。

4\. 数据库操作是否使用同一事务管理器和连接。

在业务方法入口、异常捕获处和事务拦截器附近分别观察，比只在 `save` 处反复步进更容易建立因果链。

### 12.3 缓存结果与数据库不一致

可以把断点放在缓存查询后的分支点，依次确认 cache key、命中状态、缓存值版本和回源条件。若使用 `@Cacheable`，还要确认调用经过代理、key 表达式的实际结果、缓存名称和序列化后的字段。

通过 Evaluate Expression 直接执行缓存写入会改变现场。通常只读取 key 和当前值，再用独立、可审计的管理工具核对缓存。

### 12.4 消息重复消费

断点应围绕幂等边界布置：消息 ID 读取、幂等记录查询、业务提交、确认或提交 offset（位点）。用 Logpoint 记录消息 ID、线程名、重试次数和事务状态，比 Suspend All 更不容易触发消费者超时与重新均衡。

若暂停时间超过消息系统的心跳或处理期限，调试行为本身可能制造重复消费。因此生产问题更适合回放消息到隔离消费者。

### 12.5 单元测试与集成测试

调试失败测试时优先直接 Debug 单个测试方法，保证输入固定、运行快且没有其他用例噪声。参数化测试使用条件断点锁定失败参数；Mock（模拟对象）返回异常时，查看调用参数、调用次数和实际注入对象。

“测试中能命中，部署后不能命中”通常需要比较构建制品、Profile、依赖注入结果、类加载器和实际流量路径，不能据此直接推断 IDE 有问题。

## 13 从现象到根因的可复用调试流程

### 13.1 第一步：把模糊描述改成可验证现象

“金额不对”信息不足。可验证描述应包含输入、期望、实际、发生频率、环境、版本和最小业务标识。例如：

> 在 release `a13f7c2` 的预生产实例中，会员订单 `ORD-20260821-0098` 输入商品金额 120 与 80，期望应付 180，接口实际返回 200；每次可复现，无异常日志。

这段描述直接决定条件断点、入口和成功判据。

### 13.2 第二步：画出最短数据链

对金额问题可以写成：

~~~text
请求商品价格
    → subtotal
    → discountRate
    → discount
    → payable
    → 响应 DTO
~~~

优先在链条中间布置少量观察点，通过二分思想找到第一次偏差。若 `subtotal` 正确、`discount` 错误，暂时无需进入请求解析和响应序列化。

### 13.3 第三步：每个断点只验证一个假设

有效假设示例：

1\. 会员标记在 Controller 到 Service 之间丢失。

2\. 折扣率因为整数除法变成 0。

3\. 正确结果在 DTO 映射时被旧字段覆盖。

每个假设都应有预期证据。若断点显示 `member=true` 且 `discountRate=0.0`，第二个假设得到支持；若 `discountRate=0.1`，应立即放弃该假设，继续观察下一数据节点。

### 13.4 第四步：区分相关性与因果性

看到变量异常只能证明“这里已经异常”。要找到因果，需要继续回答：

1\. 这个值从哪里生成或传入？

2\. 上一个观察点是否仍正确？

3\. 修改该值后，后续结果是否按预测改变？

4\. 用同样输入修复源码后，错误是否稳定消失？

Set Value 可以支持第三步的快速实验，自动化测试和重复运行负责第四步的正式验证。

### 13.5 第五步：清理诊断手段并固化验证

排查结束后应移除临时代码、Logpoint、宽泛异常断点、远程端口、敏感截图和高开销 Watch。随后增加能够在无调试器条件下发现回归的测试、日志、指标或断言。调试器帮助发现根因，可观测性和测试负责让同类问题更早暴露。

## 14 故障速查表

| 现象 | 最可能的入口 | 第一项检查 | 常见原因 |
| --- | --- | --- | --- |
| 断点灰色或无效 | 断点状态 | 当前行是否有可执行字节码 | 空行、声明行、源码与 class 不匹配 |
| 启动后不进断点 | Run/Debug 配置 | 是否以 Debug 启动正确进程 | 启动了另一个模块、实例或配置 |
| 条件断点不命中 | 条件上下文 | 变量是否在当前作用域且条件无异常 | 空指针、类型不符、请求未走该分支 |
| 每一步都进入 JDK/框架 | Stepping 设置 | 是否使用普通 Step Into | 跳过规则被关闭或使用 Force Step Into |
| Variables 显示与预期方法不符 | Frames | 当前选中了哪个栈帧 | 切换到了调用者或另一个线程 |
| 求值一直等待 | 目标表达式 | 是否包含锁、网络或 Future | 求值方法阻塞或被其他暂停线程持锁 |
| Debug 后程序极慢 | Overhead | 条件断点、方法断点和 Watch 耗时 | 热点命中、高成本 renderer |
| Reset Frame 后出现重复数据 | 副作用 | 方法之前是否已经写外部系统 | 栈帧重置没有回滚数据库或消息 |
| HotSwap 后仍执行旧逻辑 | Frames 与编译结果 | 当前栈帧是否 obsolete | 类未重编译、结构性变更、旧帧未退出 |
| 远程连接成功但不命中 | 版本与实例 | Git 提交、Pod、类是否一致 | 流量到别的实例、源码和字节码错位 |
| 并发问题一 Debug 就消失 | 挂起策略 | 是否 Suspend All | 暂停改变线程时序 |
| 异步异常看不到提交者 | Async Stack Traces | 调试 Agent 和设置是否启用 | 自定义调度器未配置捕获/插入点 |

## 15 动作速查与选择指南

### 15.1 常用调试动作

快捷键受操作系统和 Keymap 影响。以下以 JetBrains 常见默认键位为参考；若不一致，使用 Find Action 搜索动作英文名最可靠。

| 动作 | Windows/Linux 常见键位 | macOS 常见键位 | 作用 |
| --- | --- | --- | --- |
| Toggle Line Breakpoint | `Ctrl+F8` | `⌘F8` | 切换行断点 |
| View Breakpoints | `Ctrl+Shift+F8` | `⌘⇧F8` | 管理全部断点 |
| Resume Program | `F9` | `⌥⌘R` 或当前 Keymap 对应键 | 继续到下个断点 |
| Step Over | `F8` | `F8` | 执行当前行，不进入普通调用 |
| Step Into | `F7` | `F7` | 进入方法 |
| Smart Step Into | `Shift+F7` | `⇧F7` | 选择一行中的具体调用 |
| Step Out | `Shift+F8` | `⇧F8` | 返回调用者 |
| Run to Cursor | `Alt+F9` | `⌥F9` | 运行到光标行 |
| Evaluate Expression | `Alt+F8` | `⌥F8` | 在当前帧求值 |
| Show Execution Point | `Alt+F10` | 以 Find Action 为准 | 回到当前执行点 |

### 15.2 根据问题选择工具

| 你想回答的问题 | 优先工具 |
| --- | --- |
| 这行执行后变量变成什么 | 行断点 + Step Over |
| 一行中到底哪个方法算错 | Smart Step Into |
| 谁把对象字段改坏了 | Field Watchpoint，仅监听修改 |
| 异常刚抛出时参数是什么 | 具体类型的 Exception Breakpoint |
| 只看某个订单或用户 | 空值安全的 Conditional Breakpoint |
| 不能暂停但要知道是否经过 | Logpoint |
| 错误在 Stream 哪一步出现 | Stream Trace |
| 两个线程如何交错 | Suspend Thread + Threads 视图 |
| 异步任务由谁提交 | Async Stack Traces + trace ID |
| 远程实例与本地行为不同 | Remote JVM Debug + 制品版本核对 |
| 改一段方法体后快速重试 | HotSwap，退出旧栈帧后重放 |
| Debug 明显拖慢程序 | Overhead 视图 |

## 16 面试理解、复习与资料入口

### 16.1 能体现理解深度的几个判断

面对“断点有哪些”这类问题，枚举类型只是起点。更有价值的是解释选择与边界：

1\. 条件断点适合把高频路径缩到目标业务对象，但条件会在目标 JVM 中求值，热点路径可能产生明显开销。

2\. Suspend All 提供稳定快照，却会改变并发时序；Suspend Thread 保留其他线程运行，又使共享状态继续变化。

3\. Reset Frame 只重置局部执行帧，不回滚外部副作用，因此不能等同于时间旅行。

4\. Evaluate Expression 能验证假设，也可能执行有副作用的方法；读操作与写操作的边界必须先判断。

5\. 远程 Debug 的完整判断包含 JDWP 安全、源码与制品一致，以及流量确实进入目标实例；连接成功只证明通信层可用。

6\. HotSwap 缩短反馈周期，但标准 JVM 对结构性类变更有限制，正式修复仍需完整构建和测试。

### 16.2 递归追问的分析方向

如果继续追问“为什么断点不命中”，应从连接、进程、类加载、源码映射、执行路径和断点条件逐层排除。如果追问“为什么 Debug 会让并发 Bug 消失”，应说明暂停和求值改变线程调度、锁持有时间与超时。如果追问“如何调试线上问题”，应先说明日志、指标、Trace、线程转储和流量回放的优先级，再讨论受控远程调试的风险边界。

### 16.3 复习自测

1\. 能否用自己的话解释当前执行行是尚未执行还是已经执行，并通过一次 Step Over 验证？

2\. 能否从当前栈帧切换到调用者，并说明 Variables 为什么随之变化？

3\. 能否为“第 1000 次循环时某字段错误”设计低开销断点？

4\. 能否说明 Set Value、Reset Frame 与 HotSwap 分别改变什么、不会改变什么？

5\. 能否用 Suspend Thread 人为构造一次“检查后执行”竞态？

6\. 能否解释异步执行栈为什么缺少提交者，以及 Async Stack Traces 如何补链？

7\. 能否写出受控远程调试拓扑，并说明为什么不能把 JDWP 端口直接暴露公网？

8\. 能否从一个业务现象给出输入、期望、实际、首个观察点和修复后的验证标准？

### 16.4 官方资料

1\. [Debug your first Java application](https://www.jetbrains.com/help/idea/debugging-your-first-java-application.html)：第一次 Java 调试闭环。

2\. [Breakpoints](https://www.jetbrains.com/help/idea/using-breakpoints.html)：断点类型、条件、过滤、挂起策略和状态。

3\. [Examine suspended program](https://www.jetbrains.com/help/idea/examining-suspended-program.html)：栈帧、变量、Watch、求值和修改值。

4\. [Step through the program](https://www.jetbrains.com/help/idea/stepping-through-the-program.html)：步过、步入、智能步入、运行到光标和重置栈帧。

5\. [Logpoints](https://www.jetbrains.com/help/idea/logpoints.html)：非暂停日志断点。

6\. [Analyze Java Stream operations](https://www.jetbrains.com/help/idea/analyze-java-stream-operations.html)：Stream Trace 的使用和边界。

7\. [Debug asynchronous code](https://www.jetbrains.com/help/idea/debug-asynchronous-code.html)：异步调用栈、注解和开销。

8\. [Tutorial: Detect concurrency issues](https://www.jetbrains.com/help/idea/detect-concurrency-issues.html)：用单线程挂起复现竞态。

9\. [Tutorial: Remote debug](https://www.jetbrains.com/help/idea/tutorial-remote-debug.html)：Remote JVM Debug 配置和连接流程。

10\. [Reload modified classes](https://www.jetbrains.com/help/idea/reloading-classes.html)：HotSwap 流程与运行时控制。

11\. [Monitor debugger overhead](https://www.jetbrains.com/help/idea/monitor-debugger-overhead.html)：识别调试器造成的性能开销。
