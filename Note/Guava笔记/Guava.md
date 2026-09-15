# Guava 开发常用模块详解

> 面向 Java 中高级开发者的 Guava 实战指南。本文以服务端 `jre` 版本为主，示例按 Guava `33.6.0-jre` 编写。实际项目应由依赖管理平台统一版本，并结合所用 Java 版本评估是否仍需 Guava。

## 1. Guava 是什么

Guava 是 Google 开源的一组 Java 核心工具库，主要包名为 `com.google.common.*`。它补充了 Java 标准库在集合、缓存、字符串、并发、I/O（Input/Output，输入/输出）、哈希、数学和反射等方面的能力。

Guava 最有价值的地方不是“少写几行代码”，而是提供：

- 语义明确、经过充分测试的基础组件；
- 不可变集合、集合扩展等优秀的数据建模能力；
- 统一的参数校验、字符串解析和资源抽象；
- 本地缓存、限流、布隆过滤器等工程化组件。

需要注意，Java 8～21 持续增强了标准库。`Optional`、Stream、`CompletableFuture`、`List.of`、`Files` 等能力已经覆盖部分 Guava 场景。因此，开发时应优先判断：Guava 是否能显著提升表达力，还是仅仅重复 JDK（Java Development Kit，Java 开发工具包）已有能力。

## 2. 引入依赖

### 2.1 Maven

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>33.6.0-jre</version>
</dependency>
```

### 2.2 Gradle

```groovy
implementation("com.google.guava:guava:33.6.0-jre")
```

Guava 通常提供 `-jre` 与 `-android` 两种变体：

- 服务端、桌面 Java 项目使用 `-jre`；
- Android 项目使用 `-android`，不要混用；
- 多模块项目应通过 Maven `dependencyManagement` 或 Gradle Version Catalog 统一版本；
- 对外发布的基础库应避免在公共 API 中暴露 Guava 类型，否则调用方会被绑定到相同依赖。

## 3. 常用模块总览

| 模块 | 核心包 | 典型能力 | 推荐程度 |
| --- | --- | --- | --- |
| 基础工具 | `com.google.common.base` | 参数校验、字符串处理、对象辅助 | 高频 |
| 集合 | `com.google.common.collect` | 不可变集合、Multimap、Multiset、Table、集合运算 | 最高频 |
| 缓存 | `com.google.common.cache` | 本地缓存、过期、容量回收、自动加载 | 高频 |
| 并发 | `com.google.common.util.concurrent` | 限流、ListenableFuture、服务生命周期 | 按需，部分能力已有 JDK 替代 |
| I/O | `com.google.common.io` | ByteSource、CharSource、文件读写、BaseEncoding | 按需 |
| 哈希 | `com.google.common.hash` | 哈希函数、一致性哈希、布隆过滤器 | 特定场景高价值 |
| 区间 | `com.google.common.collect` | Range、RangeSet、RangeMap | 规则与区间业务非常实用 |
| 原生类型 | `com.google.common.primitives` | 基本类型数组、无符号类型辅助 | 按需 |
| 数学 | `com.google.common.math` | 安全运算、取整、组合数学 | 按需 |
| 图 | `com.google.common.graph` | 图、值图、网络 | 图关系建模 |
| 反射 | `com.google.common.reflect` | TypeToken、ClassPath | 框架开发按需 |
| 事件总线 | `com.google.common.eventbus` | 进程内发布订阅 | 谨慎使用 |

## 4. 基础工具：`com.google.common.base`

### 4.1 Preconditions：前置条件校验

`Preconditions` 适合校验调用方传入的参数、对象状态和下标范围，让程序尽早失败。

```java
import static com.google.common.base.Preconditions.*;

public Order createOrder(Long userId, int quantity, OrderStatus status) {
    checkNotNull(userId, "userId 不能为空");
    checkArgument(quantity > 0, "quantity 必须大于 0，实际值：%s", quantity);
    checkState(status == OrderStatus.PENDING,
            "订单状态必须为 PENDING，实际值：%s", status);
    return new Order(userId, quantity, status);
}
```

常用方法：

- `checkNotNull`：失败时抛出 `NullPointerException`；
- `checkArgument`：校验方法参数，失败时抛出 `IllegalArgumentException`；
- `checkState`：校验对象或系统状态，失败时抛出 `IllegalStateException`；
- `checkElementIndex`：校验元素下标，合法范围为 `[0, size)`；
- `checkPositionIndex`：校验插入位置，合法范围为 `[0, size]`。

工程建议：

- 它适合程序员错误，不适合直接承担 Web 请求的完整业务校验；
- 面向用户的校验通常需要错误码、字段路径和国际化，应使用 Bean Validation 或项目校验体系；
- 错误消息不要携带密码、令牌、完整身份证号等敏感数据；
- 不要在消息参数中执行昂贵操作，因为参数会在方法调用前求值。

### 4.2 Strings：空串、补齐与重复

```java
String value = Strings.nullToEmpty(input);       // null -> ""
String value2 = Strings.emptyToNull(input);      // "" -> null
boolean blank = Strings.isNullOrEmpty(input);    // 只判断 null 或空串，不判断空白字符
String id = Strings.padStart("42", 6, '0');      // "000042"
String suffix = Strings.padEnd("A", 3, '-');    // "A--"
String line = Strings.repeat("-", 20);
```

`isNullOrEmpty("   ")` 返回 `false`。判断空白字符串可以使用 Java 11 的 `String.isBlank()`，或先判空再调用。

### 4.3 Splitter 与 Joiner：可靠拆分和拼接

`String.split` 使用正则表达式，容易出现转义、尾部空值和空白处理问题。`Splitter` 的行为更直观。

```java
List<String> tags = Splitter.on(',')
        .trimResults()
        .omitEmptyStrings()
        .splitToList("java, guava, ,spring");
// [java, guava, spring]

Map<String, String> config = Splitter.on(';')
        .trimResults()
        .withKeyValueSeparator('=')
        .split("host=localhost; port=8080");

String result = Joiner.on(',')
        .skipNulls()
        .join("java", null, "guava");
// java,guava
```

注意：

- `skipNulls()` 与 `useForNull()` 不能同时使用；
- `MapSplitter` 遇到重复键或格式不正确会抛异常，适合严格配置解析；
- 复杂 CSV（Comma-Separated Values，逗号分隔值）包含引号、换行和转义规则，不要用 `Splitter`，应使用专门的 CSV 库。

### 4.4 CharMatcher：字符匹配与清洗

```java
String digits = CharMatcher.digit().retainFrom("订单 A-1024"); // 1024
String compact = CharMatcher.whitespace()
        .trimAndCollapseFrom("  hello   guava \n", ' ');       // hello guava
String safe = CharMatcher.javaLetterOrDigit()
        .or(CharMatcher.anyOf("-_"))
        .retainFrom("user@name-01");                           // username-01
```

字符清洗不等于安全过滤。SQL、HTML、Shell 等上下文必须使用参数化或对应的编码机制，不能依靠 `CharMatcher` 防注入。

### 4.5 MoreObjects：`toString` 与首个非空值

```java
@Override
public String toString() {
    return MoreObjects.toStringHelper(this)
            .add("id", id)
            .add("name", name)
            .omitNullValues()
            .toString();
}

String displayName = MoreObjects.firstNonNull(nickname, username);
```

`firstNonNull` 的两个参数都为 `null` 时会抛出 `NullPointerException`。

## 5. 集合工具：`com.google.common.collect`

集合是 Guava 最成熟、最常用的模块。

### 5.1 Immutable Collections：不可变集合

```java
ImmutableList<String> roles = ImmutableList.of("ADMIN", "USER");

ImmutableMap<Long, String> userNames = ImmutableMap.of(
        1L, "Alice",
        2L, "Bob"
);

ImmutableSet<String> permissions = permissionStream
        .filter(Objects::nonNull)
        .collect(ImmutableSet.toImmutableSet());
```

不可变集合的优势：

- 构造完成后不能增删，降低共享状态产生的缺陷；
- 可以安全共享，无需防御性复制；
- 不接受 `null` 元素或 `null` 键值，错误能更早暴露；
- 适合配置快照、值对象、常量表和 API 返回值。

“不可变”是浅层的：集合结构不可变，不代表元素对象自身不可变。

```java
List<User> source = new ArrayList<>();
ImmutableList<User> snapshot = ImmutableList.copyOf(source);
source.clear();                    // 不影响 snapshot
snapshot.get(0).setName("new");   // 如果 User 可变，元素内容仍可改变
```

Java 9 的 `List.of`、`Set.of`、`Map.of` 已覆盖简单构造场景；Guava 在 Builder、Collector、丰富不可变集合类型和跨旧版本一致性方面仍有优势。

### 5.2 Multimap：一个键对应多个值

传统写法 `Map<K, List<V>>` 需要手工初始化集合、处理空集合和删除空键。`Multimap` 将这种关系作为一等模型。

```java
Multimap<String, String> usersByRole = ArrayListMultimap.create();
usersByRole.put("ADMIN", "Alice");
usersByRole.put("ADMIN", "Bob");
usersByRole.put("USER", "Carol");

Collection<String> admins = usersByRole.get("ADMIN");
usersByRole.remove("ADMIN", "Bob");
```

主要实现：

- `ArrayListMultimap`：值允许重复，保留插入顺序特征；
- `HashMultimap`：值不重复，不保证稳定顺序；
- `LinkedHashMultimap`：值不重复，并维护可预测迭代顺序；
- `TreeMultimap`：键和值按自然顺序或比较器排序；
- `ImmutableListMultimap` / `ImmutableSetMultimap`：不可变版本。

注意 `multimap.size()` 返回键值对总数，不是不同键的数量；不同键数量应使用 `multimap.keySet().size()`。

### 5.3 Multiset：可重复元素计数集合

`Multiset` 类似“元素到出现次数”的映射，又保留集合语义。

```java
Multiset<String> wordCounts = HashMultiset.create();
wordCounts.add("java");
wordCounts.add("java");
wordCounts.add("guava", 3);

int javaCount = wordCounts.count("java"); // 2

for (Multiset.Entry<String> entry : wordCounts.entrySet()) {
    System.out.println(entry.getElement() + ": " + entry.getCount());
}
```

适用于词频、库存数量、投票统计和重复事件计数。并发计数可考虑 `ConcurrentHashMultiset`；超高并发指标统计还应评估 `LongAdder` 或专业指标系统。

### 5.4 Table：双键映射

`Table<R, C, V>` 表示“行键 + 列键 -> 值”，可以替代嵌套的 `Map<R, Map<C, V>>`。

```java
Table<String, String, BigDecimal> prices = HashBasedTable.create();
prices.put("APPLE", "TAIPEI", new BigDecimal("35.00"));
prices.put("APPLE", "TOKYO", new BigDecimal("42.00"));

BigDecimal price = prices.get("APPLE", "TAIPEI");
Map<String, BigDecimal> taipeiPrices = prices.column("TAIPEI");
Map<String, BigDecimal> applePrices = prices.row("APPLE");
```

适用于权限矩阵、地区价格、二维配置。若数据规模大、稀疏度高或需要持久化查询，应使用数据库或专门存储，不要把 `Table` 当数据库。

### 5.5 BiMap：双向唯一映射

```java
BiMap<String, Integer> statusCodes = HashBiMap.create();
statusCodes.put("SUCCESS", 0);
statusCodes.put("NOT_FOUND", 404);

String status = statusCodes.inverse().get(404); // NOT_FOUND
```

值也必须唯一。重复值会抛出 `IllegalArgumentException`；`forcePut` 会移除旧映射，使用时必须明确这正是业务语义。

### 5.6 集合视图与变换

```java
Set<Integer> left = Set.of(1, 2, 3);
Set<Integer> right = Set.of(3, 4, 5);

SetView<Integer> union = Sets.union(left, right);             // 1,2,3,4,5
SetView<Integer> intersection = Sets.intersection(left, right); // 3
SetView<Integer> difference = Sets.difference(left, right);     // 1,2

List<List<Integer>> pages = Lists.partition(List.of(1, 2, 3, 4, 5), 2);
// [[1, 2], [3, 4], [5]]
```

许多 API 返回的是视图，不是独立副本：

- 原集合变化可能反映到视图；
- 视图可能不支持修改；
- `Lists.partition` 的子列表受原列表结构影响；
- 需要稳定快照时，应使用 `ImmutableList.copyOf` 或 `ImmutableSet.copyOf`。

### 5.7 Maps.uniqueIndex 与索引构建

```java
ImmutableMap<Long, User> userById = Maps.uniqueIndex(users, User::getId);
```

如果多个元素产生相同键，`uniqueIndex` 会抛出异常。若重复键是合法的，应使用 `Multimaps.index`：

```java
ImmutableListMultimap<String, User> usersByDept =
        Multimaps.index(users, User::getDepartment);
```

## 6. Range、RangeSet 与 RangeMap：区间模型

### 6.1 Range

```java
Range<Integer> adultAge = Range.closed(18, 65);  // [18, 65]
boolean allowed = adultAge.contains(30);

Range<Integer> leftClosed = Range.closedOpen(0, 10); // [0, 10)
Range<Integer> greaterThan = Range.greaterThan(100); // (100, +∞)
```

常见边界：

- `closed(a, b)`：`[a, b]`；
- `open(a, b)`：`(a, b)`；
- `closedOpen(a, b)`：`[a, b)`；
- `atLeast(a)`：`[a, +∞)`；
- `lessThan(b)`：`(-∞, b)`。

### 6.2 RangeSet：多个区间的并集

```java
RangeSet<Integer> blockedPorts = TreeRangeSet.create();
blockedPorts.add(Range.closed(1, 1023));
blockedPorts.add(Range.closed(8080, 8090));

boolean blocked = blockedPorts.contains(8088);
blockedPorts.remove(Range.closed(8085, 8087));
```

相连区间会被自动合并，非常适合黑白名单、时间段、号码段和规则覆盖范围。

### 6.3 RangeMap：区间映射到值

```java
RangeMap<Integer, String> levels = TreeRangeMap.create();
levels.put(Range.closed(0, 59), "不及格");
levels.put(Range.closed(60, 79), "合格");
levels.put(Range.closed(80, 100), "优秀");

String level = levels.get(88); // 优秀
```

区间重叠时，后写入部分会覆盖已有映射的对应部分。金额和时间边界必须先统一精度、时区及开闭区间规则。

## 7. Cache：进程内本地缓存

### 7.1 LoadingCache 自动加载

```java
LoadingCache<Long, User> userCache = CacheBuilder.newBuilder()
        .maximumSize(10_000)
        .expireAfterWrite(Duration.ofMinutes(10))
        .recordStats()
        .build(new CacheLoader<>() {
            @Override
            public User load(Long userId) {
                return userRepository.findById(userId)
                        .orElseThrow(() -> new UserNotFoundException(userId));
            }
        });

User user = userCache.getUnchecked(1001L);
```

### 7.2 手动 Cache

```java
Cache<String, TokenInfo> tokenCache = CacheBuilder.newBuilder()
        .maximumSize(5_000)
        .expireAfterAccess(Duration.ofMinutes(30))
        .build();

TokenInfo info = tokenCache.getIfPresent(token);
tokenCache.put(token, tokenInfo);
tokenCache.invalidate(token);
```

### 7.3 关键配置的语义

- `maximumSize(n)`：按条目数限制容量；回收是近似并且可能异步发生；
- `maximumWeight(n)` + `weigher`：按条目权重控制容量，例如估算字节数；
- `expireAfterWrite`：写入或更新后经过固定时间过期；
- `expireAfterAccess`：读写后续期，适合会话类数据；
- `refreshAfterWrite`：允许刷新，但必须配合 `LoadingCache`；旧值通常可在刷新时继续读取；
- `weakKeys`、`weakValues`、`softValues`：依赖垃圾回收行为，通常不建议作为稳定容量策略；
- `removalListener`：监听淘汰原因，监听逻辑必须快速，重任务应异步执行；
- `recordStats`：启用命中率、加载耗时和淘汰计数统计，会有少量开销。

### 7.4 缓存并发与异常

```java
User user = userCache.get(userId, () -> loadUser(userId));
```

对于同一个键的并发 `get(key, loader)`，缓存会尽量避免重复加载。但开发者仍需考虑：

- 加载函数可能失败，应设计异常传播和重试策略；
- Guava Cache 不缓存 `null`，空结果可用特殊对象或短期负缓存表达；
- 缓存值应尽量不可变，避免调用者改写共享对象；
- 本地缓存只在当前 JVM（Java Virtual Machine，Java 虚拟机）有效，多实例间不一致；
- 进程重启数据即丢失，不能作为权威存储；
- 缓存穿透、雪崩、热点键和一致性需要单独设计。

现代项目若需要更强的异步刷新、性能或淘汰策略，通常还会评估 Caffeine。Guava Cache 适合已有 Guava 依赖、缓存要求中等且 API 简单的场景。

## 8. 并发工具：`com.google.common.util.concurrent`

### 8.1 RateLimiter：单机限流

```java
RateLimiter limiter = RateLimiter.create(100.0); // 平均每秒 100 个许可

public Result callRemoteService(Request request) {
    if (!limiter.tryAcquire(Duration.ofMillis(50))) {
        throw new TooManyRequestsException();
    }
    return remoteClient.call(request);
}
```

`RateLimiter` 基于许可速率平滑流量，适合单 JVM 的客户端保护、批处理节流和简单 QPS（Queries Per Second，每秒查询数）控制。

它不是分布式限流器：多实例部署时每个实例都有独立额度。若要求全局额度，应使用网关、Redis 脚本或专门的分布式限流方案。

### 8.2 ListenableFuture

```java
ListeningExecutorService executor = MoreExecutors.listeningDecorator(
        Executors.newFixedThreadPool(8));

ListenableFuture<User> future = executor.submit(() -> loadUser(userId));

Futures.addCallback(future, new FutureCallback<>() {
    @Override
    public void onSuccess(User user) {
        audit(user);
    }

    @Override
    public void onFailure(Throwable error) {
        log.error("加载用户失败", error);
    }
}, callbackExecutor);
```

新项目通常优先使用 Java 8+ 的 `CompletableFuture`、虚拟线程或响应式框架。已有大量 Guava 异步链路时，`ListenableFuture` 仍有维护价值。

不要随意使用 `MoreExecutors.directExecutor()` 执行未知或耗时回调：它可能直接占用完成任务的线程，放大延迟，甚至造成栈递归或死锁风险。

### 8.3 Service：后台服务生命周期

`Service`、`AbstractIdleService`、`AbstractExecutionThreadService` 用于管理后台组件的启动、运行和停止。

```java
public final class QueueConsumer extends AbstractExecutionThreadService {
    @Override
    protected void run() throws Exception {
        while (isRunning()) {
            consumeOne();
        }
    }

    @Override
    protected void triggerShutdown() {
        consumer.wakeup();
    }
}
```

适合非 Spring 容器或基础框架。Spring 项目通常优先采用容器生命周期接口，避免维护两套生命周期模型。

## 9. I/O：`com.google.common.io`

### 9.1 ByteSource 与 CharSource

这两个抽象把“数据来源”与具体文件、内存或网络实现解耦，并且每次读取都新建流。

```java
CharSource source = Files.asCharSource(path.toFile(), StandardCharsets.UTF_8);
String content = source.read();
ImmutableList<String> lines = source.readLines();

ByteSource bytes = Files.asByteSource(path.toFile());
HashCode sha256 = bytes.hash(Hashing.sha256());
```

```java
CharSink sink = Files.asCharSink(
        output.toFile(),
        StandardCharsets.UTF_8,
        FileWriteMode.APPEND
);
sink.writeLines(List.of("line1", "line2"));
```

小文件整体读取很方便；对不可信或可能很大的文件，应使用流式处理并设置大小上限，避免内存耗尽。

### 9.2 ByteStreams 与 CharStreams

```java
long copied = ByteStreams.copy(inputStream, outputStream);
byte[] data = ByteStreams.toByteArray(inputStream);
String text = CharStreams.toString(reader);
```

这些方法通常不会替调用者关闭传入的流，资源所有权仍属于调用者，应使用 `try-with-resources`。

### 9.3 BaseEncoding

```java
String encoded = BaseEncoding.base64().encode(bytes);
byte[] decoded = BaseEncoding.base64().decode(encoded);

String hex = BaseEncoding.base16().lowerCase().encode(bytes);
```

Java 8 已提供 `java.util.Base64`。如果只需要 Base64，优先考虑 JDK；需要 Base16/Base32、严格解码或统一风格时，Guava 更方便。

## 10. Hashing 与 BloomFilter

### 10.1 Hashing：哈希计算

```java
HashCode hash = Hashing.sha256()
        .hashString(payload, StandardCharsets.UTF_8);
String hex = hash.toString();
```

组合字段时应使用 `Hasher` 明确编码，避免简单字符串拼接产生歧义：

```java
HashCode key = Hashing.sha256().newHasher()
        .putLong(userId)
        .putString(resource, StandardCharsets.UTF_8)
        .putInt(version)
        .hash();
```

不要使用普通哈希保存密码。密码存储需要 Argon2、scrypt、bcrypt 或 PBKDF2（Password-Based Key Derivation Function 2，基于密码的密钥派生函数 2）等专用算法和随机盐。

### 10.2 一致性哈希

```java
int bucket = Hashing.consistentHash(hash.asLong(), nodeCount);
```

它适合节点数量变化时尽量减少映射迁移。这里的参数是桶数量，不直接处理节点身份、权重、故障转移或虚拟节点；复杂分片应采用成熟的一致性哈希实现。

### 10.3 BloomFilter：布隆过滤器

布隆过滤器是一种空间效率很高的概率型集合：

- 判断“不存在”时一定不存在；
- 判断“可能存在”时可能误判；
- 一般不支持删除；
- 适合在访问数据库前过滤大量确定不存在的键。

```java
BloomFilter<Long> userIds = BloomFilter.create(
        Funnels.longFunnel(),
        1_000_000,
        0.01
);

userIds.put(1001L);

if (userIds.mightContain(requestUserId)) {
    return userRepository.findById(requestUserId);
}
return Optional.empty();
```

容量与误判率必须按业务估算。实际写入量远超预计容量后，误判率会显著升高。它不能替代权限验证或数据库唯一性校验。

## 11. 原生类型工具：`com.google.common.primitives`

常用类包括 `Ints`、`Longs`、`Doubles`、`Bytes`、`Booleans` 和 `UnsignedInts`。

```java
Integer port = Ints.tryParse("8080"); // 失败返回 null，不抛 NumberFormatException

int saturated = Ints.saturatedCast(Long.MAX_VALUE); // Integer.MAX_VALUE

List<Integer> view = Ints.asList(1, 2, 3); // 基本类型数组的 List 视图

int index = Bytes.indexOf(data, target);
```

注意：

- `tryParse` 返回可空类型，需要显式处理 `null`；
- `asList` 返回数组视图，修改列表会影响原数组；
- 大数据量场景装箱开销明显，不能因为 API 方便而忽略内存成本；
- Java 本身没有真正的无符号整型类型，`UnsignedInts` 只是按无符号语义解释位模式。

## 12. 数学工具：`com.google.common.math`

### 12.1 溢出安全运算

```java
int total = IntMath.checkedAdd(a, b);       // 溢出抛 ArithmeticException
long amount = LongMath.checkedMultiply(x, y);
int capped = IntMath.saturatedAdd(a, b);    // 溢出时钳制到最大/最小值
```

### 12.2 除法与取整模式

```java
int pages = IntMath.divide(total, pageSize, RoundingMode.CEILING);
int log2 = IntMath.log2(value, RoundingMode.FLOOR);
```

它比手写 `(total + pageSize - 1) / pageSize` 更明确，也能避免加法溢出。

### 12.3 BigInteger 与 Double

`BigIntegerMath`、`DoubleMath` 提供阶乘、组合数、对数、整数判断等能力。涉及金额时仍应使用 `BigDecimal`，并明确小数位和 `RoundingMode`，不能使用 `double` 替代精确十进制金额。

## 13. Graph：图关系建模

Guava 提供三类图结构：

- `Graph<N>`：节点与边，不在边上存值；
- `ValueGraph<N, V>`：边携带一个值，如距离或权重；
- `Network<N, E>`：边本身也是对象，支持平行边。

```java
MutableValueGraph<String, Integer> routes = ValueGraphBuilder
        .directed()
        .allowsSelfLoops(false)
        .build();

routes.putEdgeValue("Taipei", "Tokyo", 2100);
routes.putEdgeValue("Tokyo", "Seoul", 1150);

Set<String> nextCities = routes.successors("Taipei");
Optional<Integer> distance = routes.edgeValue("Taipei", "Tokyo");
```

适用于依赖关系、组织关系、路由和工作流拓扑。Guava 主要提供数据结构和基础遍历，不是完整图算法库；最短路、社区发现等复杂算法应评估 JGraphT 等专用库。

## 14. Reflection：泛型与类路径辅助

### 14.1 TypeToken

Java 类型擦除使运行时很难直接表达 `List<String>`。`TypeToken` 可以保留和检查复杂泛型类型信息。

```java
TypeToken<List<String>> type = new TypeToken<>() {};
Type javaType = type.getType();
```

在泛型父类中解析类型变量：

```java
abstract class Repository<T> {
    private final TypeToken<T> entityType = new TypeToken<T>(getClass()) {};
}
```

它常用于序列化、依赖注入和框架开发。普通业务代码大量依赖反射会削弱类型安全和可读性，应谨慎控制边界。

### 14.2 ClassPath

`ClassPath` 可扫描类加载器可见的类和资源，但在模块系统、特殊类加载器、原生镜像、应用服务器与打包后的 fat JAR 中可能受到限制。生产级扫描通常应选用经过目标运行环境验证的框架能力。

## 15. EventBus：进程内事件总线

```java
public final class OrderSubscriber {
    @Subscribe
    public void onOrderCreated(OrderCreatedEvent event) {
        auditService.record(event);
    }
}

EventBus eventBus = new EventBus();
eventBus.register(new OrderSubscriber());
eventBus.post(new OrderCreatedEvent(orderId));
```

`AsyncEventBus` 可以通过执行器异步分发。

不建议在大型业务系统中无节制使用 EventBus，原因包括：

- 发布者与订阅者关系隐式，代码检索和调用链追踪困难；
- 缺少持久化、确认、重试和跨进程投递保证；
- 订阅方法由反射发现，重构与异常处理更隐蔽；
- 同步订阅者的慢操作会阻塞发布线程。

简单桌面程序、同进程插件通知或隔离良好的内部事件可使用。核心业务事件通常更适合显式调用、Spring 事件机制或 Kafka/RabbitMQ 等可靠消息系统。

## 16. Ordering、ComparisonChain 等比较工具

### 16.1 ComparisonChain

```java
@Override
public int compareTo(User other) {
    return ComparisonChain.start()
            .compare(this.department, other.department)
            .compare(this.age, other.age)
            .compare(this.name, other.name, Ordering.natural().nullsLast())
            .result();
}
```

现代 Java 通常更推荐组合 `Comparator`：

```java
Comparator<User> comparator = Comparator
        .comparing(User::getDepartment)
        .thenComparingInt(User::getAge)
        .thenComparing(User::getName, Comparator.nullsLast(naturalOrder()));
```

`Ordering` 在维护旧代码或需要 Guava 特有操作时仍有用，新代码应先评估 JDK `Comparator`。

## 17. 常见工程组合示例

下面以“按用户加载权限并缓存”为例，组合不可变集合、参数校验、缓存和 Multimap：

```java
public final class PermissionService {
    private final PermissionRepository repository;
    private final LoadingCache<Long, ImmutableSet<String>> permissionCache;

    public PermissionService(PermissionRepository repository) {
        this.repository = Preconditions.checkNotNull(repository, "repository");
        this.permissionCache = CacheBuilder.newBuilder()
                .maximumSize(20_000)
                .expireAfterWrite(Duration.ofMinutes(5))
                .recordStats()
                .build(new CacheLoader<>() {
                    @Override
                    public ImmutableSet<String> load(Long userId) {
                        return repository.findPermissionCodes(userId).stream()
                                .filter(Objects::nonNull)
                                .collect(ImmutableSet.toImmutableSet());
                    }
                });
    }

    public boolean hasPermission(long userId, String permission) {
        Preconditions.checkArgument(userId > 0, "userId 必须为正数");
        Preconditions.checkArgument(
                !Strings.isNullOrEmpty(permission),
                "permission 不能为空"
        );
        return permissionCache.getUnchecked(userId).contains(permission);
    }

    public void evict(long userId) {
        permissionCache.invalidate(userId);
    }

    public CacheStats stats() {
        return permissionCache.stats();
    }
}
```

生产环境还应补充：

- 权限变更时的主动失效机制；
- 多实例的一致性通知；
- 加载失败的监控与告警；
- `hitRate`、`evictionCount`、`averageLoadPenalty` 等指标；
- 空权限集合的合理短期缓存，防止持续回源；
- 缓存最多占用多少内存的压测结论。

## 18. 常见误区与避坑清单

### 18.1 不要混用多个 Guava 版本

Guava 历史版本跨度大，间接依赖很容易带来运行时冲突，例如 `NoSuchMethodError`。建议：

- 使用依赖树检查版本来源；
- 在统一依赖管理中锁定版本；
- 库项目尽量不在公共 API 暴露 Guava 类型；
- 必须隔离时评估 shading/relocation，但要考虑包重定位后的调试与许可证要求。

常用检查命令：

```bash
mvn dependency:tree -Dincludes=com.google.guava:guava
```

### 18.2 区分视图、快照与可变副本

- `Sets.union` 等通常返回实时视图；
- `ImmutableList.copyOf` 返回不可变快照；
- `new ArrayList<>(source)` 返回独立可变副本；
- 命名和接口文档应明确调用方拿到的是哪一种。

### 18.3 不要用 Guava Optional 设计新 API

现代 Java 项目优先使用 `java.util.Optional`。不要在同一个项目中混用 `com.google.common.base.Optional` 与 JDK Optional，否则会增加转换成本与导包错误。

同样，`Function`、`Predicate`、`Supplier` 等新代码应优先使用 `java.util.function` 包。

### 18.4 不要过度追求链式写法

复杂的集合转换、过滤和视图嵌套可能导致：

- 延迟计算被多次触发；
- 异常堆栈不直观；
- 中间集合或装箱产生额外开销；
- 代码调试困难。

当逻辑包含复杂分支、副作用或性能关键路径时，清晰的普通循环往往更好。

### 18.5 缓存不是 Map，也不是数据库

缓存条目随时可能因过期、容量、引用回收或显式失效而消失。业务正确性不能依赖“缓存里一定有值”，更不能依赖缓存保存唯一副本。

### 18.6 序列化兼容性

不要默认 Guava 集合和内部实现的 Java 原生序列化形式可作为长期协议。跨服务、持久化或长期存档应使用明确的 DTO（Data Transfer Object，数据传输对象）与 JSON、Protobuf 等稳定协议。

## 19. Guava 与现代 JDK 的选择建议

| 需求 | 优先考虑 | 说明 |
| --- | --- | --- |
| 简单不可变集合 | `List.of` / `Set.of` / `Map.of` | JDK 9+ 已足够 |
| 不可变集合 Builder、Collector | Guava Immutable Collections | 构建能力更丰富 |
| 一个键多个值 | Guava `Multimap` | JDK 无直接同类抽象 |
| 元素计数 | Guava `Multiset` | 语义优于 `Map<T, Integer>` |
| 二维键映射 | Guava `Table` | JDK 无直接同类抽象 |
| 集合并、交、差视图 | Guava `Sets` | 表达简洁 |
| 简单字符串拼接 | `String.join` / Collectors | JDK 通常足够 |
| 可配置字符串拆分 | Guava `Splitter` | 空值、裁剪等语义清晰 |
| Optional | `java.util.Optional` | 新代码统一用 JDK |
| 异步任务编排 | `CompletableFuture` / 虚拟线程 | 新项目通常优先 JDK |
| Base64 | `java.util.Base64` | JDK 8+ 通常足够 |
| 文件读写 | `java.nio.file.Files` | JDK 能力已很成熟 |
| 本地限流 | Guava `RateLimiter` | 简单实用，但只限单机 |
| 本地缓存 | Guava Cache / Caffeine | 按性能与刷新要求选型 |
| 区间集合与映射 | Guava `RangeSet` / `RangeMap` | Guava 独特优势 |
| 布隆过滤器 | Guava `BloomFilter` | 快速建立单机概率过滤器 |

## 20. 推荐的团队使用规范

1. 统一 Guava 版本，禁止子模块自行覆盖。
2. 服务端使用 `-jre`，Android 使用 `-android`。
3. 新公共 API 优先暴露 JDK 接口，如 `List`、`Map`、`Optional`，避免绑定 Guava 实现。
4. 默认优先不可变集合；确需修改时再复制为可变集合。
5. 明确集合 API 返回的是视图、快照还是副本。
6. Cache 必须配置容量上限；过期时间不能替代容量限制。
7. 开启缓存统计后接入监控，不能只配不看。
8. `RateLimiter` 只用于单实例额度；全局限流采用分布式方案。
9. 密码、签名和鉴权不要误用普通哈希或 BloomFilter。
10. 新代码避免 Guava Optional、旧 Function 和旧 Predicate。
11. EventBus 仅用于边界清晰的进程内通知，不承载需要可靠投递的核心业务。
12. 升级 Guava 时执行完整测试，并重点检查间接依赖和二进制兼容问题。

## 21. 学习顺序建议

如果希望快速把 Guava 用到实际项目中，可按以下顺序学习：

1. `Preconditions`、`Splitter`、`Joiner`；
2. `ImmutableList`、`ImmutableSet`、`ImmutableMap`；
3. `Multimap`、`Multiset`、`Table`、`BiMap`；
4. `Lists.partition`、`Sets`、`Maps` 等集合辅助类；
5. `Range`、`RangeSet`、`RangeMap`；
6. `CacheBuilder`、缓存统计与失效策略；
7. `RateLimiter`、Hashing、BloomFilter；
8. 最后按项目需要学习 Graph、Reflection、Service 等低频模块。

## 22. 结语

Guava 最值得掌握的是它提供的数据结构和工程语义，而不是工具方法的数量。实际开发中，优先关注以下核心能力：

- 用不可变集合减少共享状态；
- 用 `Multimap`、`Multiset`、`Table` 准确表达业务模型；
- 用 `RangeSet`、`RangeMap` 管理复杂区间规则；
- 用 Cache 和 RateLimiter 解决适度的单机缓存与节流问题；
- 用 Preconditions、Splitter 等基础工具统一代码意图；
- 同时尊重现代 JDK 的进步，避免为了使用 Guava 而使用 Guava。

## 参考资料

- [Guava 官方项目](https://github.com/google/guava)
- [Guava Explained 官方用户指南](https://github.com/google/guava/wiki)
- [Guava API 文档](https://guava.dev/api/)
- [Guava 官方构建配置说明](https://github.com/google/guava/wiki/UseGuavaInYourBuild)

> 版本说明：文中的版本号依据 2026-09-15 查询到的 Guava 官方 README 示例。依赖版本会持续更新，落地项目时应再次核对官方发布信息，并通过依赖扫描评估兼容性与安全风险。
