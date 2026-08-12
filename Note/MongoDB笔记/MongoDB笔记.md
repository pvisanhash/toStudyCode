# MongoDB 笔记

> 定位：面向 Java 零基础学习者与后端开发者，同时服务于系统学习、面试复习和生产落地。
>
> 示例约定：命令默认在 `mongosh` 中执行，业务示例统一使用 `shop` 数据库中的 `orders`、`products`、`users` 集合。生产环境不要直接照抄示例账号、密码和单机配置。

## 1 先建立感觉：MongoDB 到底在保存什么

### 1.1 从一件商品开始，而不是从术语开始

先想象一个很普通的需求：商城要保存一件“机械键盘”，页面需要展示商品编号、名称、价格和标签。在 MongoDB 中，这条记录可以长成下面这样：

```javascript
({
  sku: "KB-001",
  name: "机械键盘",
  price: NumberDecimal("499.00"),
  tags: ["wireless", "hot-swap"]
})
```

MongoDB 最基础的三个层级是 Document（文档）、Collection（集合）和 Database（数据库）。上面这一整块数据是一个文档，即一条完整的业务记录；许多商品文档组成 `products` 集合；多个集合属于同一个数据库。它们大致对应关系型数据库里的“行、表、数据库”，但 MongoDB 文档可以直接包含数组和嵌套对象。

MongoDB 的基础心智模型是：把经常一起读取的数据保存在同一文档中，再按字段查询；是否放在同一文档，应由业务的读写方式决定。

### 1.2 第一阶段的目标只有一个：亲手完成一次读写

第 2 章会带你完成一个最小闭环：启动 MongoDB、用 `mongosh` 连接、写入商品和订单、再把它们查询出来。`mongosh` 是发送命令的交互式客户端，`mongod` 是执行命令并保存数据的数据库服务进程。

完成第 2 章后，应能观察到下面四个结果：

1\. MongoDB 容器处于运行状态，日志没有反复退出。

2\. `ping` 返回 `{ ok: 1 }`，证明客户端真的请求到了服务端。

3\. 商品计数为 `2`、订单计数为 `1`，证明数据确实写入。

4\. 查询能返回机械键盘，而不是只看到命令“没有报错”。

如果这四项还没有全部成功，先不要学习副本集、分片或事务。初学阶段最重要的是把“输入命令—服务端执行—数据变化—查询验证”这条链路走通。

### 1.3 按四个阶段学习，避免一次吃下整本笔记

| 阶段 | 建议阅读 | 这一阶段真正要会什么 | 暂时可以跳过什么 |
| --- | --- | --- | --- |
| 第一次跑通 | 第 1 至 4 章 | 理解文档层级，启动数据库，完成新增、查询、更新和删除 | 建模模式、事务、集群原理 |
| 能写项目代码 | 第 5 至 8 章 | 按访问方式建模，写聚合与索引，用 Java 驱动或 Spring Data MongoDB 接入 | 分片、底层恢复、复杂运维 |
| 理解生产系统 | 第 9 至 13 章 | 理解一致性、副本集、分片、安全、备份、监控和进阶能力 | 不属于当前业务的专项能力 |
| 复习与落地 | 第 14 至 16 章 | 用追问、项目模板和自测标准发现知识缺口 | 不需要机械背诵所有答案 |

如果首要目标是尽快让 Java 程序连上数据库，完成第 1 至 4 章后可以直接跳到第 8 章。先完成 8.1 至 8.9 节，得到“连接—查询—原子更新—Spring Data 查询”的可运行结果；8.10 和 8.11 节涉及事务，建议学完第 9 章的一致性与事务、并完成第 10.7 节的副本集实验后再回来实践。

第 5 章列出了多种数据建模模式。第一次阅读只需掌握 5.1 的访问模式和 5.2 的嵌入与引用；5.3 的具体模式应在遇到相应问题时选择性阅读，不需要一次背完。

### 1.4 一次请求如何到达数据库

学习阶段先使用下面这条简单链路，不必立即展开集群内部细节：

```mermaid
flowchart LR
    A["mongosh 或 Java 程序"] --> B["MongoDB 驱动：把操作编码为 BSON 请求"]
    B --> C["mongod：执行查询和写入"]
    C --> D["Database"]
    D --> E["Collection"]
    E --> F["Document"]
    F --> B
```

驱动是应用与数据库之间的客户端程序库；BSON（Binary JSON，二进制 JSON）是 MongoDB 传输和保存文档时使用的数据格式。初学时只需知道 BSON 比普通 JSON 多了日期、精确小数和 `ObjectId` 等类型。第 3 章会结合刚刚写入的数据逐一解释。

当应用进入生产环境后，`mongod` 可能不再是单个节点，而是副本集或分片集群。部署形态改变的是可靠性与扩展方式，不会推翻“客户端发出操作、服务端处理文档、调用方验证结果”这条主线。

### 1.5 MongoDB 适合解决什么问题

MongoDB 是面向文档的数据库。它适合保存天然具有嵌套结构、经常按一个完整业务对象读取、字段会逐步演进的数据。例如商品目录中，不同商品拥有不同规格，但详情页通常一次读取整件商品。

| 场景特征 | 更适合评估 MongoDB | 更适合优先评估关系型数据库 |
| --- | --- | --- |
| 数据形状 | JSON 风格对象、数组和嵌套结构较自然 | 数据高度规范化、关系稳定且复杂 |
| 读取方式 | 经常整体读取一个业务对象 | 经常进行临时多表关联与复杂关系分析 |
| 一致性边界 | 一条文档能容纳多数共同变化的数据 | 大量业务约束天然跨越多个实体 |
| 结构变化 | 字段会演进，但团队能治理版本和校验 | 强依赖固定列、外键和数据库约束 |

MongoDB 不等于“没有结构”，也不保证“天然比关系型数据库快”。模型不合理、类型混乱或缺少索引时，它同样会慢；跨许多文档的强事务和复杂临时报表很多时，关系型数据库往往更直接。选型要回到真实读写方式，而不是只看 NoSQL（Not Only SQL，不仅仅是 SQL）这个标签。

### 1.6 初学时怎样处理陌生术语

1\. 第一次出现术语时，先问“它解决什么问题”，再记名称；不要先背全称和分类。

2\. 代码执行后必须检查返回值、影响条数或最终数据；“没有抛异常”不等于业务成功。

3\. 当前章节明确说“第一次可跳过”的内容，可以先留作索引页，等遇到对应问题再回来。

4\. 学完一个阶段后，用该阶段的成功判据自测；结果不符合预期时，先沿客户端、连接、数据库、集合和文档逐层排查。

先完成第 2 章的实际操作，再学习 MongoDB 的数据层级和类型；这些概念会直接对应刚刚创建的数据。

## 2 教程：跑通第一个可验证闭环

### 2.1 前置条件

需要 Docker 和 `mongosh`，或者一个可访问的 MongoDB Atlas 集群。以下示例使用 `mongo:8.0` 作为学习基线：它固定了 8.0 主版本系列，但会随该标签更新到新的 8.0 补丁版，不是字节级可复现的镜像。CI（Continuous Integration，持续集成）和长期保留的实验环境应在安全评审后固定完整补丁版或镜像摘要，并通过明确变更升级。

本文在 2026-08-12 Review 时，官方手册将 MongoDB 8.2 标为最新次要版本，8.2 发行说明已列出包含安全和可靠性修复的 8.2.12 补丁版。生产选版应查看支持周期、安全公告、发行说明、驱动兼容矩阵和升降级边界，不使用浮动的 `latest` 标签。参见 [MongoDB 8.2 Release Notes](https://www.mongodb.com/docs/manual/release-notes/8.2/) 与 [MongoDB Versioning](https://www.mongodb.com/docs/v8.2/reference/versioning/)。

### 2.2 启动本地实例

```bash
# 这些凭据仅用于本机学习容器，生产环境必须由密钥系统注入。
export MONGO_DEMO_USER='root'
export MONGO_DEMO_PASSWORD='change-this-demo-password'

docker run --name mongodb-learning \
  -p 127.0.0.1:27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME="$MONGO_DEMO_USER" \
  -e MONGO_INITDB_ROOT_PASSWORD="$MONGO_DEMO_PASSWORD" \
  -v mongodb-learning-data:/data/db \
  -d mongo:8.0
```

只绑定 `127.0.0.1` 可以避免学习实例直接暴露到外网。验证容器状态：

```bash
# 同时查看容器状态和启动日志，避免只凭“命令返回”判断成功。
docker ps --filter name=mongodb-learning
docker logs mongodb-learning
```

预期结果是容器状态为 `Up`，日志出现等待连接的信息。端口占用时使用 `lsof -nP -iTCP:27017 -sTCP:LISTEN` 确认冲突进程；容器反复退出时先看日志，不要直接删除数据卷。

### 2.3 连接并确认服务端

```bash
# 不在 URI 中写明密码，让 mongosh 交互式提示输入。
mongosh "mongodb://root@127.0.0.1:27017/?authSource=admin"
```

命令会提示输入密码。连接后执行：

```javascript
// ping 证明请求到达服务端；hello 用于识别节点和部署形态。
db.runCommand({ ping: 1 });
db.version();
db.hello();
```

`ping` 返回 `{ ok: 1 }` 才表示请求真正到达服务端。`db.hello()` 可观察当前节点是否可写，以及是否属于副本集。

### 2.4 创建最小业务数据

下面的重置命令只用于本地学习数据，保证重复执行教程后仍得到相同结果；不要把这类删除语句直接用于生产集合。示例显式固定 `_id`，是为了让后续查询、聚合和事务章节可以直接复用同一批数据：

```javascript
// 以下删除和固定标识只为保证本地教程可重复执行。
use shop;

var keyboardId = ObjectId("66a72c000000000000000001");
var mouseId = ObjectId("66a72c000000000000000002");
var aliceId = ObjectId("66a72d000000000000000001");
var paidOrderId = ObjectId("66a72e000000000000000001");

db.products.deleteMany({
  sku: { $in: ["KB-001", "MS-001"] }
});
db.users.deleteMany({
  username: { $in: ["alice", "bob"] }
});
db.orders.deleteMany({
  $or: [
    { _id: paidOrderId },
    { requestId: "tutorial-order-001" }
  ]
});

db.products.createIndex(
  { sku: 1 },
  { name: "uk_products_sku", unique: true }
);
db.users.createIndex(
  { username: 1 },
  { name: "uk_users_username", unique: true }
);
db.orders.createIndex(
  { requestId: 1 },
  { name: "uk_orders_request_id", unique: true }
);

db.products.insertMany([
  {
    _id: keyboardId,
    sku: "KB-001",
    name: "机械键盘",
    category: "keyboard",
    price: NumberDecimal("499.00"),
    stock: NumberInt(30),
    isDeleted: false,
    tags: ["wireless", "hot-swap"],
    createdAt: new Date()
  },
  {
    _id: mouseId,
    sku: "MS-001",
    name: "无线鼠标",
    category: "mouse",
    price: NumberDecimal("199.00"),
    stock: NumberInt(50),
    isDeleted: false,
    tags: ["wireless"],
    createdAt: new Date()
  }
]);

db.users.insertOne({
  _id: aliceId,
  username: "alice",
  profileVersion: NumberInt(1),
  profile: {
    displayName: "Alice",
    city: "Singapore"
  },
  roles: ["customer"],
  createdAt: new Date()
});

db.orders.insertOne({
  _id: paidOrderId,
  schemaVersion: NumberInt(1),
  requestId: "tutorial-order-001",
  requestFingerprint: "sha256:demo-payload-hash",
  userId: aliceId,
  status: "PAID",
  items: [
    {
      productId: keyboardId,
      nameSnapshot: "机械键盘",
      unitPrice: NumberDecimal("499.00"),
      quantity: NumberInt(1)
    }
  ],
  totalAmount: NumberDecimal("499.00"),
  createdAt: ISODate("2026-07-28T08:00:00Z")
});
```

成功返回值中的 `acknowledged: true` 表示服务端确认写入，`insertedIds` 给出写入的标识。继续验证：

```javascript
// 用数量与样例查询共同验证初始化结果，而不是只看 insert 返回值。
db.products.countDocuments({
  sku: { $in: ["KB-001", "MS-001"] }
});
db.orders.countDocuments({
  requestId: "tutorial-order-001"
});
db.products.find(
  { category: "keyboard" },
  { _id: 0, sku: 1, name: 1, price: 1 }
);
```

预期商品计数为 `2`、订单计数为 `1`，查询返回一件机械键盘。没有抛异常并不足以证明业务成功，必须检查确认状态、影响条数和最终数据。若 `createIndex()` 报重复键错误，说明集合中已有重复业务键，应先查询并确认数据来源，不要直接删除生产数据。

### 2.5 清理学习环境

停止容器不会删除数据：

```bash
# stop 只停止容器，不会删除容器或持久化数据卷。
docker stop mongodb-learning
```

是否删除容器和数据卷应由学习者显式决定。数据卷包含持久化数据，不应在生产环境执行未经确认的删除操作。

## 3 回看刚才的数据：理解最少必要概念

本章只解释第 2 章已经遇到的对象。第一次阅读先掌握数据库、集合、文档、字段、BSON 和 `_id`；NoSQL、CAP 与底层存储机制属于后续解释，不应阻塞第一次 CRUD。

### 3.1 数据库、集合、文档与字段

| MongoDB 概念 | 作用 | 关系型数据库近似概念 | 不能直接等同之处 |
| --- | --- | --- | --- |
| Database | 数据库级命名空间 | Database | 物理资源边界依部署而定 |
| Collection | 保存一组文档 | Table | 默认不要求文档字段完全一致 |
| Document | 一条 BSON 记录 | Row | 可包含数组和嵌套文档 |
| Field | 文档中的键值 | Column | 同一字段在治理不当时可能出现不同类型 |
| `_id` | 文档唯一标识 | Primary Key | 缺省时驱动通常生成 `ObjectId` |
| Embedded Document | 内嵌子对象 | 被反范式化的关联行 | 与父文档一起原子更新 |
| Reference | 保存另一个文档的标识 | Foreign Key | 默认没有外键约束和级联行为 |

MongoDB 通常在首次写入时隐式创建数据库和集合，因此 `use shop` 只是切换当前数据库上下文，不代表数据已经持久化。验证成功应使用 `show dbs`、`show collections` 和实际查询；空数据库通常不会出现在 `show dbs` 结果中。

### 3.2 BSON、JSON 与常见类型

BSON 是 MongoDB 的存储和传输格式。它在 JSON 基础上增加了 `ObjectId`、日期、二进制、Decimal128、正则表达式和时间戳等类型。业务层若把所有值都当成字符串，会导致排序、范围查询、计算和索引行为错误。

| 类型 | 示例 | 典型用途 | 常见错误 |
| --- | --- | --- | --- |
| String | `"PAID"` | 文本、枚举值 | 用字符串保存数字和日期 |
| Int32 / Int64 | `NumberInt(3)` | 数量、计数 | 计数范围溢出 |
| Decimal128 | `NumberDecimal("19.90")` | 精确十进制金额 | 用 Double 表示金额产生精度误差 |
| Date | `ISODate("2026-07-28T00:00:00Z")` | 时间点 | 把本地时间字符串当作统一时间 |
| ObjectId | `ObjectId("...")` | 默认 `_id` | 用字符串查询 ObjectId 导致匹配失败 |
| Array | `["NEW", "PAID"]` | 有界多值 | 无界数组让文档持续膨胀 |
| Embedded Document | `{city: "Singapore"}` | 聚合对象 | 更新时误覆盖整个子文档 |
| Null / Missing | `null` / 字段不存在 | 不同业务状态 | 查询时误以为两者始终等价 |

官方文档规定标准集合中的 BSON 文档最大为 16 MiB；超大二进制文件可考虑 GridFS，但文件对象存储通常更适合通用文件业务。参见 [MongoDB Documents](https://www.mongodb.com/docs/manual/core/document/)。

### 3.3 `_id` 与 ObjectId

每个标准集合文档都必须有唯一 `_id`。未提供时，驱动通常生成 `ObjectId`。`ObjectId` 包含时间相关部分，但不要把它当成严格连续、不可预测或可替代业务时间的序列。

```javascript
// 生成一个新的 ObjectId，并读取其中的时间相关部分。
var id = ObjectId();
id;
id.getTimestamp();
```

业务查询若已经拿到 `_id` 字符串，需要显式转换：

```javascript
// 字段实际是 ObjectId 时，查询值也必须使用相同的 BSON 类型。
db.orders.findOne({ _id: ObjectId("66a72e000000000000000001") });
```

若字段实际类型是 `ObjectId`，使用字符串 `"66a72e..."` 查询不会命中。下面的命令从 `orders` 集合中抽取最多五条文档，只显示 `_id` 及其 BSON 类型，用来回答“数据库里保存的 `_id` 到底是什么类型”：

```javascript
// aggregate() 接收一个数组；数组中的每个对象都是一个按顺序执行的处理阶段。
db.orders.aggregate([
  {
    // $project 决定每条输出文档保留什么字段，以及要计算什么新字段。
    $project: {
      // 数字 1 表示保留原文档的 _id。
      _id: 1,
      // idType 是自定义的新字段名；$type 返回 _id 的 BSON 类型名称。
      // "$_id" 中的 $ 表示引用当前文档的 _id 字段，不是普通字符串 "_id"。
      idType: { $type: "$_id" }
    }
  },
  // 最多返回五条结果，避免为了抽样检查类型而输出整个集合。
  { $limit: 5 }
]);
```

可以把它理解成两步流水线：第一步 `$project` 把每条订单转换成“订单标识 + 标识类型”，第二步 `$limit` 只留下前五条。假设集合中有下面一条订单：

```javascript
// 这是用于理解输入形状的示意文档，不需要重复插入教程数据库。
var sampleOrder = {
  _id: ObjectId("66a72e000000000000000001"),
  status: "PAID",
  totalAmount: NumberDecimal("499.00")
};
```

经过 `$project` 后，`status` 和 `totalAmount` 没有被保留，输出类似：

```javascript
// objectId 是 $type 返回的 BSON 类型名称。
var projectedOrder = {
  _id: ObjectId("66a72e000000000000000001"),
  idType: "objectId"
};
```

关键符号可以这样记：

| 写法 | 含义 |
| --- | --- |
| `aggregate([...])` | 启动聚合管道，按数组顺序处理文档 |
| `$project` | 选择输出字段，或者计算新字段 |
| `_id: 1` | 在结果中保留 `_id` |
| `idType` | 自己定义的输出字段名，可以换成其他名字 |
| `$type` | 返回指定值的 BSON 类型名称 |
| `"$_id"` | 读取当前文档的 `_id` 字段值 |
| `$limit: 5` | 最多返回五条结果 |

正常教程数据的 `idType` 应为 `"objectId"`。若返回 `"string"`，说明 `_id` 保存的是字符串，查询时应使用相同类型，或者先确认是否需要迁移历史数据；若没有任何输出，通常表示 `orders` 集合为空、当前数据库不是 `shop`，或者连接到了错误的实例。这个命令只读取数据，不会修改订单。

### 3.4 学完基础后再理解 NoSQL 与 CAP

NoSQL 通常解释为 Not Only SQL（不仅仅是 SQL），指不以传统关系模型作为唯一组织方式的一类数据库。键值、文档、列族和图数据库都属于广义 NoSQL，MongoDB 只是其中的文档数据库。

CAP 指 Consistency（一致性）、Availability（可用性）和 Partition Tolerance（分区容错性）。CAP 讨论的是发生网络分区时，一致性和可用性之间的取舍，不是说一个正常运行的系统只能永久选择两个属性。MongoDB 的实际行为还取决于部署结构、写关注、读关注和读偏好，不能用“MongoDB 是 CP 或 AP”一句话替代配置分析。

## 4 CRUD：从语法到正确语义

### 4.1 新增文档

`insertOne()` 写入单个文档，`insertMany()` 批量写入。批量写入默认有序，前面的错误会阻止后续文档继续执行；设置 `ordered: false` 可让互不依赖的写入继续，但调用方必须逐项处理错误。

```javascript
// 唯一索引会在并发下阻止重复 username，应用仍需处理重复键异常。
db.users.insertOne({
  username: "bob",
  profileVersion: NumberInt(1),
  profile: {
    displayName: "Bob",
    city: "Singapore"
  },
  roles: ["customer"],
  createdAt: new Date()
});
```

第 2.4 节已经为用户名创建唯一索引。若没有执行教程准备步骤，可显式补建；对相同定义重复调用 `createIndex()` 不会创建第二个索引：

```javascript
// 独立运行本节时先补建唯一约束；相同定义不会重复创建索引。
db.users.createIndex(
  { username: 1 },
  { name: "uk_users_username", unique: true }
);
```

应用层先查再插存在并发竞态，唯一索引才是可靠约束；应用仍需捕获重复键错误并映射为清晰的业务响应。

### 4.2 查询过滤、投影、排序与分页

```javascript
// 过滤、投影、排序和限制都在服务端执行。
db.products.find(
  {
    category: "keyboard",
    price: { $gte: NumberDecimal("300"), $lt: NumberDecimal("800") },
    tags: "wireless"
  },
  {
    _id: 0,
    sku: 1,
    name: 1,
    price: 1
  }
).sort({ price: 1 }).limit(20);
```

过滤文档是 MQL（MongoDB Query Language，MongoDB 查询语言）的核心。字段条件之间默认是逻辑与；只有需要表达同一字段的多个分支或复杂组合时才显式使用 `$and`、`$or`、`$nor`。

| 条件类别 | 常用操作符 | 示例目的 |
| --- | --- | --- |
| 比较 | `$eq`、`$ne`、`$gt`、`$gte`、`$lt`、`$lte`、`$in`、`$nin` | 价格范围、状态集合 |
| 逻辑 | `$and`、`$or`、`$nor`、`$not` | 组合多个业务条件 |
| 字段 | `$exists`、`$type` | 区分字段缺失、空值和类型 |
| 数组 | `$all`、`$elemMatch`、`$size` | 匹配数组内容或元素组合 |
| 表达式 | `$expr` | 在过滤中比较字段或使用表达式 |

MongoDB 的比较具有 BSON 类型语义。字段保存为字符串 `"499"` 时，不应期待它与 Decimal128 的 `NumberDecimal("499")` 按数值等价。遇到查询结果异常，先用 `$type` 检查真实数据，不要只看界面显示。

`null` 与字段不存在是两个不同的数据状态，但 `{ field: null }` 会同时匹配二者。下面三种过滤不能混用：

```javascript
// 匹配显式 null 或字段不存在。
db.users.find({ "profile.nickname": null });

// 只匹配 BSON Null。
db.users.find({ "profile.nickname": { $type: 10 } });

// 只匹配字段不存在。
db.users.find({ "profile.nickname": { $exists: false } });
```

需要“字段存在且不为 null”时可使用 `{ "profile.nickname": { $ne: null } }`。生产模型必须先定义缺失、显式 `null`、空字符串和默认值各自代表什么，再让校验、查询与 Java 映射保持一致。参见 [Query for Null or Missing Fields](https://www.mongodb.com/docs/manual/tutorial/query-for-null-fields/)。

投影中的 `1` 表示包含，`0` 表示排除。除 `_id` 外，同一个投影不能混合包含与排除；`_id` 默认返回，需要显式写 `_id: 0` 才会省略。投影可以减少网络传输和对象解码，但只有过滤字段与返回字段都能由索引提供时，才可能成为第 7.4 节所述的覆盖查询。

数组字段使用 `{ tags: "wireless" }` 可匹配包含该元素的文档。多个数组元素需由同一个子文档同时满足条件时应使用 `$elemMatch`，否则不同数组元素可能分别满足不同条件。

```javascript
// 包含两个标签，忽略数组中的顺序和其他元素。
db.products.find({
  tags: { $all: ["wireless", "hot-swap"] }
});

// 精确匹配整个数组，元素顺序也必须相同。
db.products.find({
  tags: ["wireless", "hot-swap"]
});
```

```javascript
// $elemMatch 要求数量和单价由同一个订单项同时满足。
db.orders.find({
  items: {
    $elemMatch: {
      quantity: { $gte: 2 },
      unitPrice: { $lt: NumberDecimal("100") }
    }
  }
});
```

深分页使用大 `skip()` 会扫描和丢弃大量结果。稳定排序场景优先使用游标分页：

```javascript
// 使用稳定的 _id 作为游标，避免大 skip() 带来的深分页扫描。
db.orders.find({
  status: "PAID",
  _id: { $gt: ObjectId("66a72e000000000000000001") }
}).sort({ _id: 1 }).limit(20);
```

游标字段必须具备稳定全序；时间字段可能重复，通常用 `{ createdAt: 1, _id: 1 }` 作为复合游标。

### 4.3 更新文档与并发安全

使用 `$set`、`$inc`、`$push` 等更新操作符只修改目标字段：

```javascript
// $set 只修改名称，$inc 原子递减库存；结果仍需检查影响条数。
db.products.updateOne(
  { sku: "KB-001" },
  {
    $set: { name: "三模机械键盘" },
    $inc: { stock: -1 }
  }
);
```

直接传入替换文档会替换除 `_id` 外的整个文档，容易丢字段。更新后检查 `matchedCount` 和 `modifiedCount`：前者为 `0` 表示过滤条件没命中；前者为 `1` 而后者为 `0` 可能表示新旧值相同。

库存扣减应把条件和变化放在同一个原子更新中：

```javascript
// 把“库存足够”的条件与扣减放在同一次单文档原子更新中。
var result = db.products.updateOne(
  { sku: "KB-001", stock: { $gte: 1 } },
  { $inc: { stock: -1 } }
);

if (result.modifiedCount !== 1) {
  throw new Error("库存不足或商品不存在");
}
```

这比“先查询库存，再执行扣减”更安全，因为同一文档的条件判断和更新是原子的。需要返回更新后文档时使用 `findOneAndUpdate()`，并明确返回旧值还是新值。

当更新不是可交换的增减操作，而是“基于刚才读到的资料保存修改”时，还要防止后写入者静默覆盖先写入者。可把读取到的版本放进更新条件，并在成功时同时递增版本：

```javascript
// 把读取到的版本放进更新条件，防止并发写入静默互相覆盖。
var expectedProfileVersion = NumberInt(1);
var profileResult = db.users.updateOne(
  {
    username: "alice",
    profileVersion: expectedProfileVersion
  },
  {
    $set: { "profile.city": "Tokyo" },
    $inc: { profileVersion: 1 }
  }
);
profileResult.matchedCount;
profileResult.modifiedCount;
```

首次执行预期为 `1` 和 `1`；仍携带旧版本的并发请求会得到 `0` 和 `0`，调用方应返回并发冲突或重新读取，不能把它当成成功。所有受保护的写路径都必须维护 `profileVersion`，否则机制会失效。这是 Optimistic Concurrency Control（OCC，乐观并发控制）的常见实现，依据是 MongoDB 单文档更新会再次检查过滤条件。参见 [Atomicity and Transactions](https://www.mongodb.com/docs/manual/core/write-operations-atomicity/)。

### 4.4 替换、查改返与批量写入

`updateOne()` 使用更新操作符修改部分字段；`replaceOne()` 用新文档替换匹配文档除 `_id` 外的全部内容；`findOneAndUpdate()` 原子更新并返回文档；`bulkWrite()` 把多种写操作批量提交。它们的成功语义和适用场景不同。

完整替换适合“外部系统提供一份完整权威快照”的场景。下面以独立的导入任务集合演示，避免破坏教程商品数据：

```javascript
// replaceOne 会移除替换文档中未出现的旧字段，因此只用于完整权威快照。
var replaceResult = db.importJobs.replaceOne(
  { source: "catalog-2026-07-29" },
  {
    source: "catalog-2026-07-29",
    status: "COMPLETED",
    importedCount: NumberInt(2),
    finishedAt: new Date()
  },
  { upsert: true }
);
replaceResult.matchedCount;
replaceResult.upsertedId;
```

首次执行会插入并产生 `upsertedId`，再次执行会匹配并替换。替换文档中没有出现的旧字段会消失，因此普通资料编辑应优先使用 `$set` 等局部更新；更新文档也不能改变已有 `_id`。

需要在一次往返中得到更新后商品时，可以使用：

```javascript
// returnDocument: "after" 要求返回更新后的文档，而不是默认的旧值。
var reviewedProduct = db.products.findOneAndUpdate(
  { sku: "KB-001" },
  { $set: { lastReviewedAt: new Date() } },
  {
    projection: { _id: 0, sku: 1, name: 1, lastReviewedAt: 1 },
    returnDocument: "after"
  }
);
reviewedProduct;
```

默认 `returnDocument: "before"` 返回修改前文档；显式设置 `"after"` 才返回修改后文档。过滤条件未命中且未启用 Upsert 时返回 `null`，调用方必须处理。参见 [`findOneAndUpdate()`](https://www.mongodb.com/docs/manual/reference/method/db.collection.findOneAndUpdate/)。

批量写入减少网络往返，适合目录同步、回填和批处理，但它不是事务。下面的两个操作彼此独立，因此允许无序执行：

```javascript
// 两项更新互不依赖，所以允许无序执行；调用方必须处理部分成功。
var bulkResult = db.products.bulkWrite(
  [
    {
      updateOne: {
        filter: { sku: "KB-001" },
        update: { $set: { catalogChecked: true } }
      }
    },
    {
      updateOne: {
        filter: { sku: "MS-001" },
        update: { $set: { catalogChecked: true } }
      }
    }
  ],
  { ordered: false }
);
bulkResult.matchedCount;
bulkResult.modifiedCount;
```

顺序模式 `ordered: true` 是默认值，某项失败后停止后续操作；无序模式会继续执行可执行项，因此可能部分成功。调用方必须检查 `insertedCount`、`matchedCount`、`modifiedCount`、`deletedCount`、`upsertedCount` 和逐项错误。若多项写入必须共同成功，应重新评估单文档建模或事务，而不是把 `bulkWrite()` 当成原子批次。参见 [`bulkWrite()`](https://www.mongodb.com/docs/manual/reference/method/db.collection.bulkWrite/)。

### 4.5 数组更新

```javascript
// 位置操作符 $ 只更新第一个匹配的订单项。
db.orders.updateOne(
  {
    _id: ObjectId("66a72e000000000000000001"),
    "items.productId": ObjectId("66a72c000000000000000001")
  },
  {
    $set: { "items.$.giftWrapped": true }
  }
);
```

位置操作符 `$` 更新第一个匹配数组元素。这里更新的是不影响订单金额的包装标记；如果修改数量或单价，必须同时维护总金额等业务不变量，已支付订单通常还应保持不可变。需要更新多个符合条件的元素时使用数组过滤器：

```javascript
// $[item] 配合 arrayFilters 更新所有满足价格条件的数组元素。
db.orders.updateOne(
  { _id: ObjectId("66a72e000000000000000001") },
  { $set: { "items.$[item].discounted": true } },
  {
    arrayFilters: [
      { "item.unitPrice": { $gte: NumberDecimal("300") } }
    ]
  }
);
```

### 4.6 Upsert 与幂等

`upsert: true` 表示找不到就插入，适合按唯一业务键同步状态：

```javascript
// $setOnInsert 只在 Upsert 新建文档时设置 createdAt。
db.userPreferences.updateOne(
  { userId: ObjectId("66a72d000000000000000001") },
  {
    $set: { locale: "zh-CN", updatedAt: new Date() },
    $setOnInsert: { createdAt: new Date() }
  },
  { upsert: true }
);
```

要让它在并发下真正幂等，必须给 `userId` 建唯一索引。调用方应区分 `upsertedId`、`matchedCount` 和 `modifiedCount`。

### 4.7 删除与软删除

硬删除示例删除第 4.1 节创建的测试用户，避免影响后续商品示例：

```javascript
// 删除结果必须检查 deletedCount，未抛异常不代表确实删除了一条。
var deleteResult = db.users.deleteOne({ username: "bob" });
deleteResult.deletedCount;
```

预期 `deletedCount` 为 `1`。删除前要确保过滤条件足够精确并检查结果；审计要求高的业务常使用软删除。下面将测试鼠标标记为已删除；`isDeleted` 作为稳定的状态字段，`deletedAt` 记录实际删除时间：

```javascript
// 条件更新使软删除幂等，重复执行时 modifiedCount 为 0。
var softDeleteResult = db.products.updateOne(
  { sku: "MS-001", isDeleted: false },
  {
    $set: {
      isDeleted: true,
      deletedAt: new Date()
    }
  }
);
softDeleteResult.modifiedCount;
```

预期 `modifiedCount` 为 `1`。软删除会影响所有查询和唯一约束：业务查询要带上 `isDeleted: false`，而第 2.4 节创建的全量唯一索引仍会禁止复用已删除商品的 SKU。若业务明确允许复用，可改成只约束活跃数据的部分唯一索引。

下面的索引替换只用于学习环境；生产变更应先确认每条历史商品都有布尔类型的 `isDeleted`，回填缺失状态，检查活跃数据中的重复 SKU，再评估约束切换窗口和回滚方案：

```javascript
// 仅限学习环境：先删除全量唯一索引，再创建只约束活跃数据的部分索引。
db.products.dropIndex("uk_products_sku");
db.products.createIndex(
  { sku: 1 },
  {
    name: "uk_active_products_sku",
    unique: true,
    partialFilterExpression: { isDeleted: false }
  }
);
```

部分索引只覆盖满足过滤条件的文档，因此希望利用它的查询也应包含 `isDeleted: false`。`partialFilterExpression` 支持字段等值条件，但官方列出的存在性条件只有 `$exists: true`；不能用 `deletedAt: { $exists: false }` 定义这个部分索引。显式布尔状态还能让新建、已删除和历史异常数据的语义更容易校验。若业务不允许复用 SKU，则保留原全量唯一索引更简单，不需要执行这次替换。参见 [Partial Indexes](https://www.mongodb.com/docs/manual/core/index-partial/)。

后续章节继续使用“SKU 全局唯一”的学习基线。若执行了上面的部分索引实验，先恢复原索引：

```javascript
// 恢复后续章节使用的“SKU 全局唯一”学习基线。
db.products.dropIndex("uk_active_products_sku");
db.products.createIndex(
  { sku: 1 },
  { name: "uk_products_sku", unique: true }
);
db.products.getIndexes();
```

预期重新看到 `uk_products_sku`。生产环境应通过受控迁移工具完成索引切换，不应把教程命令直接粘贴到在线主库。

## 5 数据建模：让结构服务访问模式

第一次阅读本章时，先完成 5.1 和 5.2，能够解释订单项为什么适合嵌入订单、用户为什么通常独立引用即可。5.3 的十一种模式是遇到具体问题时使用的工具箱，不是入门前置知识；可以先阅读模式选择表，再按业务问题进入对应小节。模式校验和结构演进适合在基础模型稳定后学习。

### 5.1 从访问模式出发

关系型设计常先消除冗余，MongoDB 设计则先列出关键读写路径。目标不是把所有数据塞进一个文档，而是让高频业务操作尽量在少量文档、少量索引和可控更新范围内完成。

建模前至少回答：

1\. 最常见的查询过滤条件、排序字段和返回字段是什么？

2\. 哪些数据总是一起读取，哪些数据独立变化？

3\. 一对多关系中的“多”是否有明确上限？

4\. 哪些字段需要唯一约束、模式校验或精确金额类型？

5\. 文档和索引未来一年如何增长？

6\. 是否存在跨文档强一致写入，能否重新聚合到单文档？

### 5.2 嵌入与引用

```mermaid
flowchart TD
    A["一组数据是否通常一起读取？"] -->|否| R["优先引用"]
    A -->|是| B["子数据是否有明确上限？"]
    B -->|否，无界增长| R
    B -->|是| C["子数据是否独立高频更新？"]
    C -->|是| R
    C -->|否| E["优先嵌入"]
    E --> F["一次读取完成，单文档原子更新"]
    R --> G["独立扩展，但需要额外查询或 $lookup"]
```

嵌入适合“包含”关系和有界一对多，例如订单与下单时的商品快照。引用适合独立生命周期、无界增长或被大量父对象共享的数据，例如用户与其全部行为日志。

嵌入订单项示例：

```javascript
// 订单项随订单整体读取和提交，因此嵌入同一个文档。
var embeddedOrder = {
  _id: ObjectId("66a72e000000000000000001"),
  schemaVersion: NumberInt(1),
  requestId: "tutorial-order-001",
  requestFingerprint: "sha256:demo-payload-hash",
  userId: ObjectId("66a72d000000000000000001"),
  status: "PAID",
  items: [
    {
      productId: ObjectId("66a72c000000000000000001"),
      nameSnapshot: "机械键盘",
      unitPrice: NumberDecimal("499.00"),
      quantity: NumberInt(1)
    }
  ],
  totalAmount: NumberDecimal("499.00"),
  createdAt: ISODate("2026-07-28T08:00:00Z")
};
```

`nameSnapshot` 和 `unitPrice` 有意冗余，因为订单必须保留成交时的事实，不能随商品主数据变化。冗余不是错误，无法解释的一致性维护策略才是错误。

### 5.3 常见模式及边界

| 模式 | 核心思想 | 适用例子 | 主要代价 |
| --- | --- | --- | --- |
| 多态模式 | 同一集合保存相近但字段不同的类型 | 不同商品类目 | 查询和校验更复杂 |
| 属性模式 | 把动态属性转为键值数组 | 商品规格筛选 | 写法更复杂，索引数量可控 |
| 子集模式 | 热数据嵌入，完整数据另存 | 商品保留最近评论 | 需要同步和一致性策略 |
| 扩展引用模式 | 引用同时冗余少量常用字段 | 订单保存用户昵称快照 | 被引用字段变化时需治理 |
| 桶模式 | 多条时序记录聚合为一个桶 | 设备指标、日志摘要 | 桶大小和并发更新需设计 |
| 异常值模式 | 普通结构与极端记录分开处理 | 少数超大粉丝列表 | 查询路径分支增加 |
| 预分配模式 | 提前创建固定结构或容量 | 有界矩阵、固定槽位 | 空间浪费和扩容边界 |
| 近似值模式 | 用可接受误差换吞吐量 | 高频计数器 | 结果非严格实时精确 |
| 计算模式 | 保存可由源数据重建的派生结果 | 销售汇总、评分统计 | 新鲜度和重建策略 |
| 树形模式 | 冗余父级、祖先或路径以加速层级查询 | 商品类目、组织架构 | 移动节点时需要维护冗余路径 |
| 文档版本控制模式 | 当前状态与历史修订分开保存 | 保单、合同、配置历史 | 写放大、历史保留和原子切换 |

下面的示例用于解释模型，不要求初学者现在执行。完成第 2、4 章后，可以在学习数据库中创建示例集合，再用给出的查询验证；不要把示例集合、固定标识或索引直接复制到生产环境。

#### 5.3.1 多态模式：共同业务实体允许不同形状

多态模式适合“业务上属于同一类、经常一起查询，但不同子类型拥有少量专属字段”的数据。商品目录就是典型例子：键盘和显示器都需要 SKU（Stock Keeping Unit，库存量单位）、名称、价格和状态，但键盘关心轴体，显示器关心尺寸和分辨率。

```javascript
// 同一集合保存共同字段，并用 itemType 区分不同商品形状。
db.catalogItems.insertMany([
  {
    _id: "KB-001",
    itemType: "keyboard",
    name: "三模机械键盘",
    status: "ACTIVE",
    price: NumberDecimal("499.00"),
    keyboard: {
      switchType: "red",
      layout: "75%"
    }
  },
  {
    _id: "MON-001",
    itemType: "monitor",
    name: "27 英寸显示器",
    status: "ACTIVE",
    price: NumberDecimal("1999.00"),
    monitor: {
      sizeInches: NumberInt(27),
      resolution: "3840x2160"
    }
  }
]);
```

共同字段放在相同路径，因此列表页可以一次查询所有活跃商品：

```javascript
// 列表查询只投影所有商品类型都具备的共同字段。
db.catalogItems.find(
  { status: "ACTIVE" },
  { itemType: 1, name: 1, price: 1 }
);
```

需要键盘专属筛选时，同时限定类型和专属字段：

```javascript
// 查询专属字段时同时限制 itemType，避免混入其他形状的文档。
db.catalogItems.find({
  itemType: "keyboard",
  "keyboard.switchType": "red"
});
```

预期第一个查询返回两类商品，第二个查询只返回红轴键盘。`itemType` 是类型判别字段，应用反序列化和模式校验都应先读取它，再决定哪些专属字段必须存在。若不同类型几乎没有共同查询、权限、生命周期或索引，它们只是“都能保存成 JSON”，此时拆成不同集合更清晰。参见 [Polymorphic Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/polymorphic-data/)。

#### 5.3.2 属性模式：把大量同类动态字段收敛为可索引属性

如果每个商品类目都把规格直接变成字段，例如 `switchType`、`screenSize`、`batteryCapacity`，字段和候选索引会随类目增长。属性模式把这些“名称不同但角色相同”的规格收敛为数组元素。

```javascript
// 动态规格统一保存为 key/value 数组，减少随类目增长的索引定义。
db.attributeProducts.insertMany([
  {
    _id: "KB-001",
    name: "三模机械键盘",
    attributes: [
      { key: "switchType", value: "red" },
      { key: "layout", value: "75%" },
      { key: "connectivity", value: "wireless" }
    ]
  },
  {
    _id: "MON-001",
    name: "27 英寸显示器",
    attributes: [
      { key: "sizeInches", value: "27" },
      { key: "resolution", value: "3840x2160" }
    ]
  }
]);

db.attributeProducts.createIndex({
  "attributes.key": 1,
  "attributes.value": 1
});
```

查询红轴商品时，`key` 和 `value` 必须由同一个数组元素同时满足，所以使用 `$elemMatch`：

```javascript
// $elemMatch 保证 key 与 value 来自同一个数组元素。
db.attributeProducts.find({
  attributes: {
    $elemMatch: {
      key: "switchType",
      value: "red"
    }
  }
});
```

预期只返回 `KB-001`。如果分别写 `"attributes.key": "switchType"` 和 `"attributes.value": "red"` 而不使用 `$elemMatch`，两个条件可能由不同数组元素分别满足，产生错误匹配。

属性模式减少的是“每种动态字段各建一个索引”的需求，不是让索引键数量消失：每个数组元素仍会产生多键索引项。必须限制属性数量，统一属性名称、值类型和单位；需要数值范围查询时，不要把 `27`、`499` 等全部保存成字符串。核心、稳定且高频使用的字段仍应保留为明确的顶层字段。参见 [Attribute Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/attribute-pattern/)。

#### 5.3.3 子集模式：主文档只携带高频读取的小集合

假设商品详情页只展示最近三条评论，但一个热门商品可能积累数十万条评论。把所有评论嵌入商品会形成无界数组；每次读取商品都携带大量冷数据，扩大工作集并逼近 16 MiB 文档限制。

子集模式把完整评论独立保存，同时在商品中保留页面最常用的最近三条：

```javascript
// recentReviews 是热子集，完整评论仍由 productReviews 集合保存。
db.reviewProducts.insertOne({
  _id: "KB-001",
  name: "三模机械键盘",
  recentReviews: [
    {
      reviewId: "R-103",
      rating: NumberInt(5),
      text: "连接稳定",
      createdAt: ISODate("2026-07-29T08:00:00Z")
    },
    {
      reviewId: "R-102",
      rating: NumberInt(4),
      text: "手感不错",
      createdAt: ISODate("2026-07-28T08:00:00Z")
    }
  ],
  reviewCount: NumberInt(2)
});

db.productReviews.insertMany([
  {
    _id: "R-103",
    productId: "KB-001",
    rating: NumberInt(5),
    text: "连接稳定",
    createdAt: ISODate("2026-07-29T08:00:00Z")
  },
  {
    _id: "R-102",
    productId: "KB-001",
    rating: NumberInt(4),
    text: "手感不错",
    createdAt: ISODate("2026-07-28T08:00:00Z")
  }
]);

db.productReviews.createIndex({
  productId: 1,
  createdAt: -1
});
```

商品首屏只读取 `reviewProducts`；用户点击“全部评论”后，再按 `productId` 查询 `productReviews`。示例为保持简短只有两条评论，生产中可能有数十万条。验证时应确认 `recentReviews` 最多三条、顺序与完整集合一致，并且 `reviewCount` 能通过完整集合重新计算。

这里存在有意重复：最近评论同时出现在两个集合。新增、删除或审核评论时，必须决定由事务、事件处理还是定期修复任务维护子集。若读取几乎总要完整数据，拆分只会增加查询和一致性成本；若“热子集”没有明确大小，也会重新退化成无界数组。参见 [Subset Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/subset-pattern/)。

#### 5.3.4 扩展引用模式：引用旁边复制真正需要的少量字段

普通引用只在订单保存 `userId`，显示订单列表时还要查询用户集合才能得到名称。把完整用户文档复制进每个订单又会产生大量无关冗余。扩展引用模式选择折中方案：保留引用，同时复制该读取路径真正需要的少数字段。

```javascript
// 只复制订单列表真正需要的用户字段，同时保留 userId 引用。
db.orderViews.insertOne({
  _id: "ORDER-001",
  userId: "USER-001",
  customerSnapshot: {
    displayName: "Alice",
    shippingCity: "Singapore"
  },
  status: "PAID",
  totalAmount: NumberDecimal("499.00"),
  createdAt: ISODate("2026-07-29T08:00:00Z")
});
```

订单列表直接读取 `customerSnapshot.displayName`，需要用户邮箱、偏好或当前资料时仍按 `userId` 查询用户集合。这个模式的关键不是“复制得越多越快”，而是只复制高频读取、体积小且变化不频繁的字段。

复制字段必须先定义语义。订单上的收货城市若表示“下单时事实”，就应作为不可变快照，用户改地址后不能回写历史订单；若它表示“当前用户展示名缓存”，则要设计变更传播、版本号和最终一致性修复。验证时不仅比较字段值，还要验证快照字段在源数据变化后是否按预定规则保持或更新。参见 [Extended Reference Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-extended-reference-pattern)。

子集模式与扩展引用模式容易混淆：

| 对比项 | 子集模式 | 扩展引用模式 |
| --- | --- | --- |
| 原始问题 | 一个实体包含过多冷数据 | 读取一个实体时反复查询另一个实体 |
| 数据来源 | 同一完整数据集中的热子集 | 被引用实体中的少量常用字段 |
| 典型例子 | 商品只嵌入最近评论，完整评论另存 | 订单引用用户并复制下单时名称 |
| 一致性问题 | 热子集与完整集合如何同步 | 源实体变化后副本保持历史还是更新 |

#### 5.3.5 桶模式：把有界的一组记录装进一个文档

桶模式按时间窗口、页码、数量上限或业务键，把大量相似小记录分成多个有界文档。下面按商品和评论页分桶；为了让结构易读，学习示例把每桶上限设为两条，真实页面可以根据访问模式设置为十条或其他经过验证的上限：

```javascript
// 每个桶都有明确的数量上限，不能让 reviews 数组无限增长。
db.reviewBuckets.insertMany([
  {
    _id: "KB-001:page:1",
    productId: "KB-001",
    page: NumberInt(1),
    count: NumberInt(2),
    reviews: [
      {
        reviewId: "R-103",
        rating: NumberInt(5),
        text: "连接稳定"
      },
      {
        reviewId: "R-102",
        rating: NumberInt(4),
        text: "手感不错"
      }
    ]
  },
  {
    _id: "KB-001:page:2",
    productId: "KB-001",
    page: NumberInt(2),
    count: NumberInt(1),
    reviews: [
      {
        reviewId: "R-093",
        rating: NumberInt(5),
        text: "续航很好"
      }
    ]
  }
]);

db.reviewBuckets.createIndex(
  { productId: 1, page: 1 },
  { unique: true }
);
```

读取第二页时只定位一个桶：

```javascript
// productId 与 page 共同定位一个确定的评论桶。
db.reviewBuckets.findOne({
  productId: "KB-001",
  page: NumberInt(2)
});
```

预期返回 `count: 1` 和一条评论。`count` 必须与数组长度一起原子维护；本示例达到两条后写入下一桶。生产设计还要解决多个写请求同时发现“当前桶未满”的竞态，常用带 `count < bucketLimit` 条件的原子更新、稳定桶标识和失败重试。

桶不能无限增长，否则只是把无界数组换了名字。桶过小会增加文档数和查询次数，过大会放大文档更新和读取；边界应由页面大小、写入并发、文档大小和查询范围共同决定。时序数据通常优先使用第 13.3 节的原生 Time Series Collection（时序集合），因为 MongoDB 会自动进行内部 Bucketing（分桶）；手工桶更适合自定义分页或非时序分组。参见 [Bucket Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/bucket-pattern/)。

#### 5.3.6 异常值模式：只让极少数超常数据走特殊路径

假设绝大多数图书的购买者不到 50 人，只有极少数畅销书达到数万人。若为了少数畅销书把所有图书都设计成复杂分桶结构，会增加普通路径成本；若全部放进一个数组，畅销书文档又会持续膨胀。为了让下面的结构可以完整展示，学习示例把异常阈值缩小为三个购买者；生产阈值必须根据真实分布确定。

异常值模式给普通文档设定明确阈值，超过阈值的数据移到额外集合：

```javascript
// 普通数据留在主文档，超过阈值的部分写入额外集合。
db.bookSales.insertOne({
  _id: "BOOK-001",
  title: "MongoDB 实战",
  firstPurchaserIds: [
    "USER-001",
    "USER-002",
    "USER-003"
  ],
  hasExtras: true,
  purchaserCount: NumberInt(6)
});

db.extraBookSales.insertOne({
  bookId: "BOOK-001",
  part: NumberInt(1),
  purchaserIds: [
    "USER-051",
    "USER-052",
    "USER-053"
  ]
});

db.extraBookSales.createIndex(
  { bookId: 1, part: 1 },
  { unique: true }
);
```

普通图书只读 `bookSales`。当 `hasExtras: true` 时，应用再查询 `extraBookSales` 并合并结果。验证时要覆盖阈值两侧：在学习示例中，第三个购买者仍走普通路径，第四个购买者必须设置标记并写入额外集合；`purchaserCount` 应能从两处数据重新核对。若生产阈值是 50，则对应验证第 50 与第 51 个购买者。

该模式只适合异常值确实稀少且阈值能由数据分布解释的场景。如果所有数组最终都会超过阈值，问题不是“少数异常”，而是模型包含无界数组，应直接采用引用、桶或其他有界设计。更新路径增加分支也是主要代价。参见 [Outlier Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/outlier-pattern/)。

#### 5.3.7 预分配模式：已知固定结构时先创建空槽位

预分配模式不是“为了性能给所有文档塞空字段”，而是在业务结构固定且槽位可直接寻址时，先创建待填充结构。例如小型影厅的座位布局在开售前已经确定：

```javascript
// 只有业务槽位固定时才预先创建结构；不要把它当通用性能优化。
db.venueMaps.insertOne({
  _id: "ROOM-A",
  rows: [
    {
      row: "A",
      seats: [
        { number: NumberInt(1), status: "AVAILABLE" },
        { number: NumberInt(2), status: "AVAILABLE" },
        { number: NumberInt(3), status: "BLOCKED" }
      ]
    },
    {
      row: "B",
      seats: [
        { number: NumberInt(1), status: "AVAILABLE" },
        { number: NumberInt(2), status: "AVAILABLE" }
      ]
    }
  ]
});
```

应用可以直接按“第 A 排 2 号”定位固定槽位，不必在每次查询时推导不存在的座位。验证重点是布局是否与权威场馆配置一致、每个座位标识是否唯一，以及并发占座是否使用第 4.5 节的条件更新和数组过滤器，而不是先读后写。

历史上 MMAPv1 存储引擎常用预分配降低文档增长后的搬迁成本；当前 WiredTiger 不需要这种通用优化。现在采用它的主要理由应是简化固定结构的应用逻辑。槽位很多、稀疏或会动态增长时，预分配会扩大文档和内存工作集，应改用独立座位文档、区间或其他模型。参见 [Preallocation Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-preallocation-pattern)。

#### 5.3.8 近似值模式：明确允许误差后减少高频更新

商品浏览量每秒可能增长数千次，但页面只显示“约 12.4 万次浏览”，并不要求每次刷新都精确到个位。近似值模式按更粗粒度更新持久值，以更少写入换取可接受误差。

```javascript
// isExact 明确告诉读取方：该计数允许存在受控误差。
db.trafficSummary.insertOne({
  _id: "/products/KB-001",
  approximateViews: NumberLong(124000),
  updateStep: NumberInt(100),
  isExact: false,
  updatedAt: ISODate("2026-07-29T08:00:00Z")
});
```

应用或流处理任务先累积增量，每累计 100 次浏览才执行：

```javascript
// 每累计 100 次才持久化一次，使用更少写入换取最多约 99 的滞后。
db.trafficSummary.updateOne(
  { _id: "/products/KB-001" },
  {
    $inc: { approximateViews: NumberLong(100) },
    $set: {
      isExact: false,
      updatedAt: new Date()
    }
  }
);
```

假设内存中还有 37 次未落库，页面显示值最多落后约 99；这就是选择 `updateStep: 100` 时显式接受的误差边界。验证不能只看写入量下降，还要比较近似值与权威明细，确认绝对误差、相对误差和刷新延迟满足产品目标，并监控增量在进程故障时是否会丢失。

库存、余额、支付金额、唯一约束和配额拦截需要精确结果，不能使用近似值模式。若业务既要快速展示趋势又要最终精确，应保留权威明细或精确计数来源，定期校准近似结果。参见 [Approximation Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/approximation-schema-pattern/)。

#### 5.3.9 计算模式：保存可重建的派生结果，避免每次读取重复计算

计算模式适合“源数据更新相对少，但同一聚合结果被高频读取”的场景。例如商品详情页每秒被读取数千次，却只需每分钟更新一次销量汇总。与其每次请求都扫描订单，不如把汇总结果保存到独立集合。

下面从已支付或已完成订单重新计算每个商品的销售数量和金额，并用 `$merge` 写入物化结果。学习示例同时接受 `PAID` 和 `COMPLETED`，是为了让第 2.4 节创建的订单能够直接产生结果；生产系统必须按自己的履约、退款和取消规则定义哪些状态计入销售：

```javascript
// 从权威订单重建派生汇总；$merge 会对目标集合产生真实写入。
db.orders.aggregate([
  {
    $match: {
      status: {
        $in: ["PAID", "COMPLETED"]
      }
    }
  },
  {
    $unwind: "$items"
  },
  {
    $group: {
      _id: "$items.productId",
      soldQuantity: {
        $sum: "$items.quantity"
      },
      revenue: {
        $sum: {
          $multiply: [
            "$items.unitPrice",
            "$items.quantity"
          ]
        }
      }
    }
  },
  {
    $set: {
      calculatedAt: "$$NOW",
      source: "orders"
    }
  },
  {
    $merge: {
      into: "productSalesSummary",
      on: "_id",
      whenMatched: "replace",
      whenNotMatched: "insert"
    }
  }
]);
```

`orders` 是权威来源，`productSalesSummary` 是可以删除后重建的派生数据。运行后应查询汇总集合，并用同一时间范围的订单重新聚合抽样核对：

```javascript
// 汇总按销售额降序展示，用于和权威订单抽样核对。
db.productSalesSummary.find().sort({
  revenue: -1
});
```

若只完成了第 2.4 节，预期至少得到键盘对应的一条汇总：`soldQuantity` 为 `1`，`revenue` 为 `499.00`。若结果为空，先检查订单状态和 `items.productId` 的 BSON 类型；若金额不符，再核对单价、数量和订单状态是否应纳入统计。

计算结果可以在每次源数据变化时同步维护，也可以由定时任务、Change Streams 或批处理异步刷新。同步维护更及时，但会增加写路径复杂度；异步刷新吞吐更好，却必须定义最大陈旧时间、失败重试、重复计算幂等和全量重建方法。

计算模式与近似值模式不能混淆。计算模式保存的是由源数据推导的结果，重新计算后可以精确核对；近似值模式主动接受误差。周期性计算值在刷新前可能陈旧，但“陈旧”不等于“近似算法”。余额、库存等强一致数据若使用计算结果参与拦截，还必须证明更新与源数据提交处于同一原子边界。参见 [Computed Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/computed-schema-pattern/)。

#### 5.3.10 树形模式：按主要层级查询冗余父级与祖先路径

商品类目、组织架构和菜单都具有树形关系。只保存 `parentId` 容易更新，但查询全部祖先或全部后代可能需要多次往返或 `$graphLookup`；同时保存祖先数组可以用一次多键索引查询后代，但移动节点时必须更新整棵子树。

```javascript
// parentId 支持直接子级查询，ancestors 支持全部后代查询。
db.categories.insertMany([
  {
    _id: "computer-parts",
    name: "电脑配件",
    parentId: null,
    ancestors: []
  },
  {
    _id: "storage",
    name: "存储设备",
    parentId: "computer-parts",
    ancestors: [
      "computer-parts"
    ]
  },
  {
    _id: "ssd",
    name: "固态硬盘",
    parentId: "storage",
    ancestors: [
      "computer-parts",
      "storage"
    ]
  }
]);

db.categories.createIndex({
  parentId: 1
});

db.categories.createIndex({
  ancestors: 1
});
```

查询“存储设备”的直接子类使用 `parentId`，查询它的全部后代使用 `ancestors`：

```javascript
// 两个查询故意对比“直接子级”和“任意深度后代”的差异。
db.categories.find({
  parentId: "storage"
});

db.categories.find({
  ancestors: "storage"
});
```

预期两个查询都返回 `ssd`；若它下面还有更深层级，第一个查询只返回直接子级，第二个查询返回所有后代。数组顺序还可以直接还原从根到当前节点的路径。

这个模型用冗余换取读取效率。把 `storage` 移到另一个父类时，不能只修改自己的 `parentId`，还要更新它及全部后代的 `ancestors`。生产写路径应使用稳定的节点标识、限制树深、记录迁移任务进度，并在失败后通过 `parentId` 或其他权威关系重新构建路径。层级频繁变化、存在多父节点或需要复杂图遍历时，应评估 `$graphLookup`、独立图模型或其他专用方案。参见 [Model Tree Structures](https://www.mongodb.com/docs/manual/applications/data-models-tree-structures/)。

#### 5.3.11 文档版本控制模式：区分当前业务状态与历史修订

文档版本控制模式解决“需要快速读取当前状态，同时保留少量历史版本”的问题。它通常把最新版本放在当前集合，把旧版本放在历史集合；这与第 5.5 节的结构版本不同：

| 字段或模式 | 回答的问题 | 典型取值 |
| --- | --- | --- |
| `schemaVersion` | 这条文档采用哪一种字段结构 | `1`、`2` |
| `revision` | 同一业务对象经历了第几次业务修订 | `1`、`2`、`3` |
| 文档版本控制模式 | 当前状态与历史状态分别保存在哪里 | `currentPolicies`、`policyRevisions` |

先创建当前保单和历史修订的唯一约束：

```javascript
// 当前集合只保存最新版，历史集合保存旧 revision。
db.currentPolicies.insertOne({
  _id: "POLICY-001",
  revision: NumberInt(1),
  customerId: "USER-001",
  insuredItems: [
    "car"
  ],
  updatedAt: ISODate("2026-07-29T08:00:00Z")
});

db.policyRevisions.createIndex(
  {
    policyId: 1,
    revision: 1
  },
  {
    unique: true
  }
);
```

更新时必须把旧版本写入历史集合，再以旧 `revision` 作为并发条件更新当前集合。下面使用事务保证两次写入共同提交，因此需要副本集或分片集群：

```javascript
// 事务保证“保存旧版本”和“更新当前版本”共同提交或共同回滚。
var policySession = db.getMongo().startSession();
var policyDb = policySession.getDatabase("shop");

try {
  policySession.withTransaction(
    function() {
      var currentPolicy = policyDb.currentPolicies.findOne({
        _id: "POLICY-001"
      });

      if (currentPolicy === null) {
        // 不允许在缺少当前版本时凭空创建一条历史修订。
        throw new Error("current policy not found");
      }

      policyDb.policyRevisions.insertOne({
        policyId: currentPolicy._id,
        revision: currentPolicy.revision,
        customerId: currentPolicy.customerId,
        insuredItems: currentPolicy.insuredItems,
        validUntil: new Date()
      });

      var updateResult = policyDb.currentPolicies.updateOne(
        {
          _id: currentPolicy._id,
          revision: currentPolicy.revision
        },
        {
          $set: {
            insuredItems: [
              "car",
              "camera"
            ],
            updatedAt: new Date()
          },
          $inc: {
            revision: NumberInt(1)
          }
        }
      );

      if (updateResult.modifiedCount !== 1) {
        // 旧 revision 未命中，说明已有并发写入抢先完成。
        throw new Error("policy revision conflict");
      }
    },
    {
      readConcern: { level: "snapshot" },
      writeConcern: { w: "majority" }
    }
  );
} finally {
  policySession.endSession();
}
```

`withTransaction()` 会在允许重试的瞬态错误下重新执行回调，所以回调中不能直接发送邮件、调用支付或产生其他不可幂等的外部副作用。验证时应确认当前集合只有一条 `revision: 2`，历史集合保存 `revision: 1`，并测试两个并发更新只能有一个通过旧版本条件。历史文档还需要保留期、访问权限和不可篡改要求；若每个对象更新极频繁、历史数量巨大或需要按任意时间点重建全局状态，应评估审计日志、事件溯源或归档系统，而不是无限扩展修订集合。参见 [Document Versioning Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/document-versioning/) 与 [`Session.withTransaction()`](https://www.mongodb.com/docs/mongodb-shell/reference/method/session-withtransaction/)。

这些模式可以组合，但组合必须能说清数据的权威来源和修复路径。例如商品评论可以用子集模式保存最近三条，同时对完整评论使用桶模式分页；订单可以使用扩展引用保存下单时用户快照。不能因为模式名称听起来专业就叠加使用，先用真实数据分布、核心查询形状、更新频率和失败场景验证收益。

快速选择时可以按下面的主问题判断：

| 当前最主要的问题 | 优先评估 |
| --- | --- |
| 同类实体有共同字段，也有少量类型专属字段 | 多态模式 |
| 动态同类属性导致字段和索引不断增加 | 属性模式 |
| 一个实体包含大量冷数据，但常用部分很小 | 子集模式 |
| 读取时反复关联另一个实体的少量字段 | 扩展引用模式 |
| 大量同类记录天然按页、时间或数量分组 | 桶模式 |
| 只有极少数文档远大于普通文档 | 异常值模式 |
| 业务槽位固定且直接寻址能明显简化逻辑 | 预分配模式 |
| 业务明确允许误差，精确更新成本过高 | 近似值模式 |
| 同一派生结果被高频重复计算 | 计算模式 |
| 高频查询父子、祖先或后代关系 | 树形模式 |
| 高频读取当前状态，同时必须保留历史修订 | 文档版本控制模式 |

### 5.4 模式校验

灵活模式适合演进，但成熟业务应在关键字段上增加校验，阻止类型漂移和非法状态。MongoDB 默认在文档违反验证规则时拒绝写入，也可以在迁移期只记录警告。参见 [Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)。

```javascript
// 第 2.4 节已经创建 orders，因此本节用 collMod 为现有集合增加校验。
var orderValidator = {
  $jsonSchema: {
    bsonType: "object",
    required: [
      "schemaVersion",
      "requestId",
      "requestFingerprint",
      "userId",
      "status",
      "items",
      "totalAmount",
      "createdAt"
    ],
    properties: {
      schemaVersion: { bsonType: "int", minimum: 1 },
      requestId: { bsonType: "string", minLength: 1 },
      requestFingerprint: { bsonType: "string", minLength: 1 },
      userId: { bsonType: "objectId" },
      status: { enum: ["CREATED", "PAID", "CANCELLED", "COMPLETED"] },
      items: {
        bsonType: "array",
        minItems: 1,
        items: {
          bsonType: "object",
          required: ["productId", "unitPrice", "quantity"],
          properties: {
            productId: { bsonType: "objectId" },
            unitPrice: { bsonType: "decimal" },
            quantity: { bsonType: "int", minimum: 1 }
          }
        }
      },
      totalAmount: { bsonType: "decimal", minimum: NumberDecimal("0") },
      createdAt: { bsonType: "date" }
    }
  }
};

// 先证明历史文档都符合新规则，再把违规写入切换为拒绝。
var invalidExistingOrders = db.orders.countDocuments({
  $nor: [orderValidator]
});

if (invalidExistingOrders !== 0) {
  throw new Error(
    "存在不符合新校验器的历史订单: " + invalidExistingOrders
  );
}

var validationResult = db.runCommand({
  collMod: "orders",
  validator: orderValidator,
  validationLevel: "strict",
  validationAction: "error"
});
validationResult.ok;
```

`invalidExistingOrders` 的预期值为 `0`，`validationResult.ok` 的预期值为 `1`。再故意写入一条数量为零的完整订单：

```javascript
// quantity 违反 minimum: 1，预期整次插入被拒绝。
db.orders.insertOne({
  schemaVersion: NumberInt(1),
  requestId: "invalid-validation-order-001",
  requestFingerprint: "sha256:invalid-validation-payload",
  userId: ObjectId("66a72d000000000000000001"),
  status: "CREATED",
  items: [
    {
      productId: ObjectId("66a72c000000000000000001"),
      unitPrice: NumberDecimal("499.00"),
      quantity: NumberInt(0)
    }
  ],
  totalAmount: NumberDecimal("0"),
  createdAt: new Date()
});
```

预期收到文档验证失败，且 `db.orders.countDocuments({ requestId: "invalid-validation-order-001" })` 返回 `0`。这同时验证了拒绝信号和最终数据，比只检查 `collMod` 返回值更完整。`createCollection()` 只适合首次创建集合；迁移老集合时可先使用 `validationAction: "warn"` 观察违规数据，治理完成后再切换为 `error`。参见 [Modify Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/update-schema-validation/)。

### 5.5 模式演进与 `schemaVersion`

灵活结构并不等于应用可以在一次发布中突然停止理解旧文档。生产模式演进通常采用 Expand-Contract（扩展—收缩）过程：先让读取端同时理解新旧结构，再让写入端生成新结构，随后回填历史数据，最后才收紧校验器、索引和旧代码。

`schemaVersion` 表示文档结构版本，不是业务修订号，也不能替代第 4.3 节的乐观并发控制。下面的回填只演示如何标记尚未声明版本的旧订单：

```javascript
// 只回填尚未声明结构版本的历史文档，避免覆盖已有版本。
var versionResult = db.orders.updateMany(
  { schemaVersion: { $exists: false } },
  { $set: { schemaVersion: NumberInt(1) } }
);
versionResult.modifiedCount;
```

结果应等于旧订单数量；没有旧订单时为 `0`。对已有集合执行这类回填时，校验器应先处于兼容旧结构的 `warn` 或等价过渡状态；历史订单无法可靠重建的 `requestFingerprint` 必须单独制定兼容策略，不能伪造为看似有效的摘要。大集合回填还必须限速、分批、可恢复，并持续观察复制延迟、磁盘和锁等待。读取端在迁移窗口内要覆盖新旧路径，索引也必须根据实际查询形状评估，不能只加版本字段就宣布迁移完成。参见 [Schema Versioning Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/schema-versioning/)。

### 5.6 从需求到物理模型的完整建模闭环

MongoDB 建模不是从“建几个集合”开始，而是逐步把业务语言转成可以验证的数据结构。Conceptual Data Model（CDM，概念数据模型）回答系统管理哪些业务对象；Logical Data Model（LDM，逻辑数据模型）回答对象具有什么属性和关系；Physical Data Model（PDM，物理数据模型）才决定集合、嵌入、引用、索引和校验器。

以创建订单为例：

| 阶段 | 要回答的问题 | 订单示例产出 |
| --- | --- | --- |
| 概念模型 | 系统管理什么 | 用户、商品、订单、支付 |
| 逻辑模型 | 对象有什么属性和关系 | 一个订单属于一个用户，包含一个或多个订单项 |
| 访问模式 | 实际怎样读写 | 按用户分页查订单；按请求标识防重复；创建订单时共同保存所有订单项 |
| 物理模型 | MongoDB 中怎样保存 | 订单项嵌入订单；用户和商品使用引用；名称和成交价保存快照 |
| 约束与索引 | 怎样阻止错误并加速核心请求 | 唯一 `requestId`、用户时间复合索引、金额和状态校验 |
| 一致性与恢复 | 写到一半或重复调用怎么办 | 单文档原子写、幂等键、请求指纹、影响条数检查 |
| 容量与演进 | 一年后是否仍然成立 | 估算订单项上限、文档大小、日增量、索引、归档和分片可能性 |

这个过程不是一次性的文档评审。上线前应拿真实或接近真实的数据分布完成以下验证：

1\. 用最大合理订单项数量构造文档，确认没有逼近 16 MiB 限制，读取也不会携带无关大字段。

2\. 用核心过滤、排序和投影执行 `explain("executionStats")`，保存扫描量、返回量和延迟证据。

3\. 并发提交相同 `requestId`，确认唯一索引和请求指纹只产生一个业务结果。

4\. 故意写入非法状态、字符串金额和空订单项，确认模式校验能够拒绝。

5\. 模拟用户改名和商品改价，确认历史订单快照不会被错误回写。

6\. 按日增量、保留期和索引比例估算一年后的数据与索引容量，再判断是否需要归档或分片。

若任何一步只能用“应该可以”回答，模型还没有完成。最终物理结构必须能够追溯到一个明确访问模式或业务约束；无法说明用途的冗余字段、索引和集合都应重新评估。

## 6 聚合框架：把数据逐阶段加工

### 6.1 管道模型

聚合管道由一系列阶段组成，文档依次流过每个阶段。`$match` 负责过滤，`$project` 重塑字段，`$unwind` 展开数组，`$group` 分组计算，`$sort` 排序，`$lookup` 关联其他集合。

旧资料可能把 Map-Reduce 与聚合管道并列为常规选择。MongoDB 5.0 起 Map-Reduce 已弃用，新设计应优先使用聚合管道；自定义逻辑可按需要评估 `$accumulator` 或 `$function`，但服务端 JavaScript 仍有性能、可维护性和安全边界。迁移旧任务时应先把 Map、Reduce 和输出语义映射到 `$project`、`$group`、`$merge` 等阶段，再用相同输入核对数量和结果，而不是只替换 API 名称。参见 [Map-Reduce](https://www.mongodb.com/docs/manual/core/map-reduce/)。

```mermaid
flowchart LR
    A["orders 原始文档"] --> B["$match：筛选已支付订单"]
    B --> C["$unwind：展开 items"]
    C --> D["$group：按商品汇总"]
    D --> E["$sort：按销售额降序"]
    E --> F["$limit：取 Top 10"]
```

### 6.2 销售额汇总示例

```javascript
// 先按状态和时间过滤，再展开订单项并按商品汇总。
db.orders.aggregate([
  {
    $match: {
      status: "PAID",
      createdAt: {
        $gte: ISODate("2026-07-01T00:00:00Z"),
        $lt: ISODate("2026-08-01T00:00:00Z")
      }
    }
  },
  { $unwind: "$items" },
  {
    $group: {
      _id: "$items.productId",
      quantity: { $sum: "$items.quantity" },
      revenue: {
        $sum: {
          $multiply: ["$items.unitPrice", "$items.quantity"]
        }
      }
    }
  },
  { $sort: { revenue: -1 } },
  { $limit: 10 }
]);
```

输入是已支付订单，`$unwind` 把每个订单项变为一条管道记录，`$group` 按商品聚合数量和销售额，最终返回销售额最高的十个商品。金额使用 Decimal128，可避免二进制浮点误差。

### 6.3 `$lookup` 的用途与代价

```javascript
// $lookup 用 userId 关联 users._id，随后只投影需要的用户字段。
db.orders.aggregate([
  { $match: { status: "PAID" } },
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user"
    }
  },
  { $unwind: "$user" },
  {
    $project: {
      totalAmount: 1,
      createdAt: 1,
      "user.username": 1
    }
  }
]);
```

`$lookup` 不是禁止使用，但高频请求若每次都依赖大型关联，应重新审视嵌入、扩展引用或预计算。被关联字段通常需要索引；否则数据规模增长后会出现显著扫描成本。

### 6.4 阶段、表达式与累加器不能混淆

阶段决定文档流如何变化，例如 `$match` 筛选、`$unwind` 展开、`$group` 汇总；表达式在阶段内部计算一个值，例如 `$multiply`、`$cond`、`$map`；累加器把多条输入归并为结果，例如 `$sum`、`$avg`、`$first`。同名操作符在不同上下文中的输入语义可能不同，不能只凭名字复制。

下面的管道不分组，而是逐个订单重新计算订单项金额，用于审计保存的 `totalAmount`：

```javascript
// $map 逐项计算金额，$sum 再把同一订单内的金额相加。
db.orders.aggregate([
  { $match: { status: "PAID" } },
  {
    $set: {
      calculatedTotal: {
        $sum: {
          $map: {
            input: "$items",
            as: "item",
            in: {
              $multiply: ["$$item.unitPrice", "$$item.quantity"]
            }
          }
        }
      }
    }
  },
  {
    $project: {
      _id: 1,
      totalAmount: 1,
      calculatedTotal: 1,
      amountMatches: { $eq: ["$totalAmount", "$calculatedTotal"] }
    }
  }
]);
```

`$map` 逐个处理 `items`，`$$item` 是当前数组元素，`$sum` 再对映射结果求和。教程订单的 `amountMatches` 预期为 `true`。若为 `false`，说明历史写入没有共同维护订单项和总金额，需要先定位写路径，不能直接用聚合结果覆盖生产数据。

### 6.5 内存、磁盘与结果写回边界

`$sort`、`$group`、`$bucket` 和窗口计算等阻塞阶段可能需要等待大量输入并保存中间状态。单个返回文档仍受 16 MiB 限制；单条管道最多包含 1000 个阶段。MongoDB 6.0 起，`allowDiskUseByDefault` 控制超过 100 MB 内存阈值的相关阶段是否默认写临时文件，单次命令可以用 `allowDiskUse` 覆盖。

```javascript
// allowDiskUse 是超限保护，不是性能优化；maxTimeMS 限制执行时间。
db.orders.aggregate(
  [
    { $match: { status: "PAID" } },
    { $unwind: "$items" },
    {
      $group: {
        _id: "$items.productId",
        revenue: {
          $sum: {
            $multiply: ["$items.unitPrice", "$items.quantity"]
          }
        }
      }
    },
    { $sort: { revenue: -1 } }
  ],
  {
    allowDiskUse: true,
    maxTimeMS: 5000
  }
);
```

允许落盘是避免内存超限失败的保护手段，不是性能优化；临时文件会占用磁盘并增加延迟。应先通过前置 `$match`、索引支持的排序、合理 `$limit` 和数据模型减少输入，再决定是否允许落盘。诊断日志和 Profiler（数据库分析器）中的 `usedDisk` 可帮助确认是否发生落盘。具体限制参见 [Aggregation Pipeline Limits](https://www.mongodb.com/docs/manual/core/aggregation-pipeline-limits/)。

`$out` 与 `$merge` 会把聚合结果写入集合，适合构建物化结果或增量汇总，但它们引入真实写副作用、权限、唯一键、并发覆盖和失败恢复问题。上线前应在隔离集合验证结果数量和主键策略，并决定重跑是覆盖、合并还是拒绝冲突。

### 6.6 聚合性能验证

尽量让能利用索引的 `$match` 和 `$sort` 靠前，减少后续阶段输入量。MongoDB 会进行部分管道优化，但不要依赖优化器修复错误模型。官方说明可见 [Aggregation Pipeline Optimization](https://www.mongodb.com/docs/manual/core/aggregation-pipeline-optimization/)。

```javascript
// executionStats 会实际执行管道并返回扫描量等统计信息。
db.orders.explain("executionStats").aggregate([
  { $match: { status: "PAID" } },
  { $sort: { createdAt: -1 } },
  { $limit: 20 }
]);
```

重点观察 `winningPlan`、`totalKeysExamined`、`totalDocsExamined`、`nReturned` 和是否出现内存或磁盘排序。`totalDocsExamined` 很高而 `nReturned` 很低，通常意味着索引或过滤选择性有问题。

## 7 索引与查询计划

### 7.1 索引为什么既能加速也会拖慢

索引用额外存储和写入维护成本，换取更少的数据扫描和排序成本。每次插入、删除或修改索引字段，都可能更新多个索引。索引并非越多越好，低价值索引会增加磁盘、缓存压力、写延迟和维护成本。

### 7.2 常见索引类型

MongoDB 的常规索引可以先用“按键排序的 B-tree（平衡树）结构”建立心智模型：查询不必逐个读取集合中的所有文档，而是沿有序索引键缩小范围，再按需回表读取文档。这能解释等值、范围、前缀和排序查询为什么可能受益于索引，也能解释随机写入为什么需要维护额外树结构。具体执行仍以目标版本的查询计划为准。

| 类型 | 示例 | 适用场景 | 关键边界 |
| --- | --- | --- | --- |
| 单字段 | `{ status: 1 }` | 单条件过滤 | 选择性低时价值有限 |
| 复合索引 | `{ status: 1, createdAt: -1 }` | 过滤加排序 | 字段顺序决定可用前缀 |
| 多键索引 | `{ tags: 1 }` | 数组元素查询 | 一个文档可产生多个索引键 |
| 唯一索引 | `{ username: 1 }` | 数据唯一约束 | 空值、缺失和部分索引语义需验证 |
| 部分索引 | 带 `partialFilterExpression` | 只索引活跃子集 | 查询必须满足过滤条件 |
| TTL 索引 | `{ expireAt: 1 }` | 自动过期 | 删除非实时，有后台延迟 |
| 文本索引 | `{ name: "text" }` | 基础全文搜索 | 复杂搜索可评估 MongoDB Search |
| 地理空间索引 | `{ location: "2dsphere" }` | 附近搜索 | 坐标顺序和 GeoJSON 格式要正确 |
| Hashed 索引 | `{ userId: "hashed" }` | 均匀哈希分片 | 不支持高效范围定位 |

TTL 是 Time To Live（生存时间）。TTL 后台任务不会在到期瞬间精确删除数据，因此不能把它用作必须准点执行的业务调度器。

地理空间查询不能只把经纬度保存成两个普通数字字段。GeoJSON Point 使用 `[longitude, latitude]`，即“经度在前、纬度在后”；顺序写反后仍可能是合法数字，却会落到错误位置。

```javascript
// 先插入 GeoJSON Point，再创建 2dsphere 索引并执行附近查询。
db.stores.insertMany([
  {
    _id: "STORE-CBD",
    name: "市中心门店",
    location: {
      type: "Point",
      coordinates: [
        103.851959,
        1.290270
      ]
    }
  },
  {
    _id: "STORE-EAST",
    name: "东部门店",
    location: {
      type: "Point",
      coordinates: [
        103.944000,
        1.353000
      ]
    }
  }
]);

db.stores.createIndex({
  location: "2dsphere"
});

db.stores.find({
  location: {
    $near: {
      $geometry: {
        type: "Point",
        coordinates: [
          103.852000,
          1.290000
        ]
      },
      $maxDistance: 3000
    }
  }
});
```

`$maxDistance` 在该查询中使用米；预期返回距离查询点三公里内的门店，并按距离从近到远排序。上线前应验证坐标系、经纬度顺序、距离单位和边界点，不能只看到索引创建成功。地球表面位置通常使用 `2dsphere`；平面坐标和旧式坐标对的语义不同，必须按目标索引类型查看限制。参见 [2dsphere Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/geospatial/2dsphere/)。

### 7.3 复合索引与 ESR 原则

ESR 是 Equality、Sort、Range（等值、排序、范围）的经验顺序。它用于形成候选索引，不是不可违背的定律；最终必须用真实数据和 `explain()` 验证。

查询：

```javascript
// 查询形状包含等值、范围和排序，候选索引需要一起评估。
db.orders.find({
  status: "PAID",
  createdAt: {
    $gte: ISODate("2026-07-01T00:00:00Z")
  }
}).sort({ totalAmount: -1 });
```

候选索引：

```javascript
// ESR 候选顺序：status 等值、totalAmount 排序、createdAt 范围。
db.orders.createIndex({
  status: 1,
  totalAmount: -1,
  createdAt: 1
});
```

`status` 是等值，`totalAmount` 支持排序，`createdAt` 是范围。但如果日期范围极具选择性而排序结果很少，另一种字段顺序可能更优。生产决策必须以目标查询的延迟、扫描比和写入代价为依据。

复合索引还具有前缀边界。例如索引 `{ tenantId: 1, status: 1, createdAt: -1 }` 的可用前缀依次是 `{ tenantId }`、`{ tenantId, status }` 和完整三字段。只按 `status` 查询缺少最左侧 `tenantId`，不能假设它会像独立 `{ status: 1 }` 索引一样高效；跳过中间字段后，后续字段也可能只能参与扫描内过滤而不能继续缩小连续索引边界。字段是否真正参与定位、排序或过滤，应以 `explain()` 的索引边界和扫描量为准。

### 7.4 覆盖查询、部分索引与 Collation

覆盖查询指过滤、排序和返回字段全部由同一个索引提供，执行器无需读取集合文档。它能降低回表与磁盘访问，但会增加索引宽度和写入成本，不应为了追求“零回表”把整个文档塞进索引。

```javascript
// 索引同时覆盖过滤、排序和返回字段，目标是避免回表读取文档。
db.products.createIndex(
  { isDeleted: 1, category: 1, price: 1, sku: 1, name: 1 },
  { name: "idx_products_category_price_cover" }
);

db.products.find(
  {
    isDeleted: false,
    category: "keyboard",
    price: { $gte: NumberDecimal("300") }
  },
  {
    _id: 0,
    sku: 1,
    name: 1,
    price: 1
  }
).explain("executionStats");
```

若优化器选择该索引，覆盖计划通常表现为不需要 `FETCH`，且 `totalDocsExamined` 为 `0`。执行计划字段会随版本和引擎变化，判据应关注是否读取文档、扫描量和真实延迟，不要只匹配某个阶段名称。分片集合上的覆盖查询还要求索引包含分片键。参见 [Query Optimization](https://www.mongodb.com/docs/manual/core/query-optimization/)。

Partial Index（部分索引）只索引满足表达式的文档；Sparse Index（稀疏索引）主要按索引字段是否存在决定是否收录。部分索引表达能力更强，通常应优先使用。查询若没有蕴含部分索引的过滤条件，优化器不能使用它返回完整结果，这正是第 4.7 节软删除查询必须携带活跃条件的原因。

Collation（排序规则）决定字符串比较、排序和大小写等语言规则。使用特定 Collation 创建的索引，通常只有采用兼容 Collation 的查询才能利用其字符串键；唯一索引的唯一性也按该规则判断。设计大小写不敏感用户名时，必须先决定是保存规范化字段，还是统一使用 Collation，不能只在应用中调用 `toLowerCase()` 后假设数据库约束一致。

```javascript
// 查询与索引使用相同 Collation，才能复用对应的字符串比较规则。
db.users.createIndex(
  { "profile.displayName": 1 },
  {
    name: "idx_users_display_name_en",
    collation: { locale: "en", strength: 2 }
  }
);

db.users.find({
  "profile.displayName": "alice"
}).collation({
  locale: "en",
  strength: 2
});
```

验证时比较带与不带 Collation 的 `explain("executionStats")`。语言规则和 `strength` 会改变比较语义，应使用真实语言样本测试重音、大小写和特殊字符，而不是只测 ASCII（American Standard Code for Information Interchange，美国信息交换标准代码）。

### 7.5 多键索引与数组边界

当索引字段包含数组时，MongoDB 自动把该索引变为 Multikey Index（多键索引），为数组元素生成索引键。这让 `{ tags: "wireless" }` 能被索引加速，但数组越长，索引键数量和写放大越明显。

```javascript
// tags 是数组，因此该索引会成为多键索引。
db.products.createIndex(
  { isDeleted: 1, tags: 1, sku: 1 },
  { name: "idx_products_tags_sku" }
);

db.products.find(
  { isDeleted: false, tags: "wireless" },
  { _id: 0, sku: 1 }
).explain("executionStats");
```

复合多键索引中，同一文档至多有一个被索引字段是数组；多键索引不能直接作为分片键索引，Hashed Index（哈希索引）也不能成为多键索引。多键索引可以在受限条件下覆盖非数组返回字段，但返回数组字段或使用 `$elemMatch` 时不能按普通覆盖查询理解。数组索引设计必须结合数组上限、元素重复度和更新频率评估。参见 [Multikey Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-multikey/)。

### 7.6 阅读 `explain()`

```javascript
// 不能只看是否出现 IXSCAN，还要比较扫描量与返回量。
db.orders.find({
  status: "PAID"
}).sort({
  createdAt: -1
}).limit(20).explain("executionStats");
```

| 信号 | 含义 | 诊断方向 |
| --- | --- | --- |
| `COLLSCAN` | 全集合扫描 | 是否缺少适用索引 |
| `IXSCAN` | 使用索引扫描 | 不代表扫描量一定合理 |
| `FETCH` | 回表读取文档 | 是否可成为覆盖查询 |
| `SORT` | 内存或磁盘排序 | 索引顺序是否支持排序 |
| `nReturned` | 实际返回条数 | 与扫描量比较 |
| `totalKeysExamined` | 扫描索引键数 | 过滤效率 |
| `totalDocsExamined` | 读取文档数 | 回表和过滤代价 |

不要只看到 `IXSCAN` 就宣布优化成功。一个返回 20 条、扫描 100 万个索引键的查询仍然可能很差。

### 7.7 查询形状、选择性、计划缓存与 `hint()` 的关系

Query Shape（查询形状）把结构相同的一组查询归为一类。过滤字段与操作符、排序、投影、命名空间以及部分聚合结构都可能参与形状；普通参数值不同通常仍可属于同一形状。例如下面两个查询虽然用户和时间不同，但具有相同的过滤、排序与投影结构：

```javascript
// 两个查询的参数值不同，但过滤、投影和排序结构相同。
db.orders.find(
  {
    userId: ObjectId("66a72d000000000000000001"),
    createdAt: {
      $gte: ISODate("2026-07-01T00:00:00Z")
    }
  },
  {
    status: 1,
    totalAmount: 1
  }
).sort({
  createdAt: -1
});

db.orders.find(
  {
    userId: ObjectId("66a72d000000000000000002"),
    createdAt: {
      $gte: ISODate("2026-08-01T00:00:00Z")
    }
  },
  {
    status: 1,
    totalAmount: 1
  }
).sort({
  createdAt: -1
});
```

查询形状重要，是因为索引评审、慢查询聚合和计划缓存都不能只围绕某一个参数值进行。MongoDB 8.0 同时使用新的 Query Shape 和用于计划缓存的 Plan Cache Query Shape，并把旧 `queryHash` 字段重命名为 `planCacheShapeHash`；监控代码必须兼容目标版本，不能把字段名写死后跨大版本直接复用。参见 [Query Shapes](https://www.mongodb.com/docs/manual/core/query-shapes/)。

Selectivity（选择性）描述条件能够把候选文档缩小到什么程度。假设一百万条订单中有五十万条 `status: "PAID"`，单独按状态过滤选择性很低；唯一 `requestId` 最多匹配一条，选择性很高。选择性不是字段固定属性，同一字段的不同值、组合条件和数据倾斜都可能产生不同结果。

验证选择性时至少比较：

```text
扫描放大比 = totalDocsExamined / max(nReturned, 1)
索引放大比 = totalKeysExamined / max(nReturned, 1)
```

比值越接近 `1` 通常越理想，但不能脱离排序、缓存、返回字段、分页深度和业务延迟判断。返回零条时分母用 `1` 只是避免除零，仍需单独分析为什么扫描大量数据却没有结果。

当同一计划缓存查询形状存在多个可行计划时，查询规划器会评估候选计划并缓存获胜结果，后续同形状查询可以复用。缓存不是永久真理：数据分布变化、索引 DDL（Data Definition Language，数据定义语言）、重启、淘汰和重新规划都可能让条目变化。可以在有权限的测试环境观察：

```javascript
// $planCacheStats 只用于有权限的诊断环境，不应成为业务依赖。
db.orders.aggregate([
  {
    $planCacheStats: {}
  },
  {
    $project: {
      planCacheShapeHash: 1,
      isActive: 1,
      works: 1,
      cachedPlan: 1
    }
  }
]);
```

结果字段与执行引擎会随版本变化，不能把某个内部阶段名称作为稳定业务 API。更容易被忽略的是：`explain()` 会绕过已有计划缓存进行规划，也不会把此次获胜计划写入计划缓存。因此 Explain 适合比较候选设计，但还要结合真实慢查询、Profiler、查询形状统计和线上延迟验证。参见 [Query Plans](https://www.mongodb.com/docs/manual/core/query-plans/)。

`hint()` 强制查询使用指定索引，适合对比实验、紧急诊断或已证明优化器选择不合适的受控场景。Hint 指定的索引必须真实存在；为了让下面的示例可以独立复现，先创建一个有明确名称的测试索引：

```javascript
// 先创建真实存在的命名索引，再用 hint() 做受控对比实验。
db.orders.createIndex(
  { status: 1, createdAt: -1 },
  { name: "idx_orders_status_created_at" }
);

db.orders.find({
  status: "PAID"
}).sort({
  createdAt: -1
}).hint("idx_orders_status_created_at")
  .explain("executionStats");
```

把这个结果与不带 `hint()` 的相同查询比较，重点看获胜索引、扫描量、是否需要额外排序以及延迟；如果命名索引不存在，查询会直接失败。它不是“索引失效就加 Hint”的通用修复。被强制索引删除、改名或不再适合新数据分布时，查询可能失败或显著变慢；某些索引过滤器还会覆盖 Hint 行为。MongoDB 8.0 开始推荐 Query Settings（查询设置）替代已弃用的 Index Filters（索引过滤器），但这类集群级干预也必须先证明目标查询形状、回滚方法和不同节点上的实际效果。

### 7.8 索引生命周期治理

1\. 从慢查询和核心查询形状出发提出候选索引。

2\. 在接近生产的数据量和分布上执行 `explain("executionStats")`。

3\. 评估新索引对写入、磁盘、缓存和构建时间的影响。

4\. 使用隐藏索引评估移除风险时，确认目标版本支持并完成回滚方案。

5\. 上线后监控延迟、扫描比、缓存、磁盘和写入吞吐。

6\. 删除索引前保留定义、变更记录和重建时间评估。

现代 MongoDB 的优化索引构建只在开始和结束阶段取得必要的独占集合锁，并在主要构建阶段允许读写交错；`background: true` 已被忽略，不能再把它当作生产安全开关。副本集索引构建还涉及 Commit Quorum（提交法定人数）、成员可达性、磁盘和内存预算。变更前应检查重复值与磁盘余量，使用 `db.currentOp()` 观察进度，并为构建失败、长时间等待和回滚保留处置窗口。参见 [Index Builds on Populated Collections](https://www.mongodb.com/docs/manual/core/index-creation/)。

## 8 Java 小白接入：从可运行工程到 Spring Data MongoDB

本章先让 Java 程序真正连接数据库，再逐步增加对象映射、连接池与 Spring Data MongoDB。第一次阅读按 8.1 至 8.9 顺序完成；8.10 和 8.11 的事务内容依赖第 9、10 章，先跳过不会影响基础接入。

### 8.1 先分清服务端、驱动与 Spring Data

Java 代码不能直接“读一个 MongoDB 文件”。它通过驱动把 Java 对象编码成 BSON 请求，经网络发送给 `mongod` 或 `mongos`，再把响应解码成 Java 对象。先分清各层，才能判断问题发生在业务代码、对象映射、驱动连接还是数据库服务端。

| 层次 | 典型对象 | 职责 | 不负责什么 |
| --- | --- | --- | --- |
| MongoDB Server | `mongod`、副本集、分片集群 | 存储、查询、索引、复制和事务 | 不理解 Java 实体类 |
| Java 同步驱动 | `MongoClient`、`MongoDatabase`、`MongoCollection` | 连接、拓扑发现、BSON 编解码和数据库命令 | 不替业务决定文档模型 |
| Spring Data MongoDB | `MongoRepository`、`MongoTemplate` | 对象映射、常用数据访问抽象和 Spring 集成 | 不自动消除慢查询、错误索引和事务代价 |
| 业务代码 | Service、领域对象、接口 | 定义业务规则、幂等和成功判据 | 不应自行模拟数据库唯一性与原子性 |

```mermaid
flowchart LR
    A["Java 业务对象或 Document"] --> B["Codec / Spring Converter：编码"]
    B --> C["BSON 请求"]
    C --> D["Java Driver：连接池与拓扑选择"]
    D --> E["mongod 或 mongos"]
    E --> F["BSON 响应"]
    F --> G["解码为 Document、POJO 或 Spring 实体"]
```

官方 Java Sync Driver（Java 同步驱动）是阻塞式 API：当前线程会等待数据库操作完成。普通 Spring MVC（Model-View-Controller，模型—视图—控制器）应用可以使用它；响应式事件循环应用应使用 Reactive Streams Driver（响应式流驱动）或 Spring Data Reactive MongoDB，不能在事件循环线程里直接执行同步驱动调用。

### 8.2 创建一个真正可运行的 Maven 工程

本节选择 JDK 17 和 Maven 作为学习环境。官方驱动支持范围仍应以当前兼容矩阵为准；选择 JDK 17 是为了给初学者一个稳定基线，不代表驱动只支持 JDK 17。

先确认工具：

```bash
# 两条命令都要检查：Maven 可能使用与终端不同的 JDK。
java -version
mvn -version
```

两条命令显示的 Java 版本都应为 17。`java -version` 是 17 但 `mvn -version` 显示 Maven 正在使用其他 JDK 时，编译结果仍以 Maven 使用的 JDK 为准，需要先修正 IDE、Maven Toolchain（工具链）或 `JAVA_HOME` 配置。

创建下面的目录结构：

```text
mongodb-java-learning/
├── pom.xml
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── shop/
                        └── ProductQuery.java
```

`pom.xml` 使用 BOM（Bill of Materials，物料清单）保证驱动各模块版本一致。本文在 2026-08-12 复核时，官方入门文档展示的 BOM 版本为 `5.9.1`；将来复制示例时，应先到 [Java Driver Get Started](https://www.mongodb.com/docs/drivers/java/sync/current/get-started/) 核对当前版本。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- BOM 统一驱动模块版本；编译与运行插件也固定版本以保证可复现。 -->
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>mongodb-java-learning</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <!-- 统一使用 JDK 17 的语言和字节码级别。 -->
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencyManagement>
        <!-- BOM 只管理版本，实际依赖仍由 dependencies 引入。 -->
        <dependencies>
            <dependency>
                <groupId>org.mongodb</groupId>
                <artifactId>mongodb-driver-bom</artifactId>
                <version>5.9.1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongodb-driver-sync</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.15.0</version>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.6.3</version>
                <configuration>
                    <!-- exec:java 默认启动的完整类名。 -->
                    <mainClass>com.example.shop.ProductQuery</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

执行 `mvn dependency:tree`，能够看到 `mongodb-driver-sync`、`mongodb-driver-core` 和 `bson` 的同系列版本，才说明依赖解析完成。`ClassNotFoundException` 或 `NoSuchMethodError` 往往意味着依赖缺失或版本冲突，应先看依赖树，而不是修改数据库配置。

### 8.3 看懂连接字符串与认证数据库

下面的连接串与第 2 章的本地实例对应：

```text
mongodb://root:change-this-demo-password@127.0.0.1:27017/shop?authSource=admin
```

| 片段 | 含义 | 常见错误 |
| --- | --- | --- |
| `mongodb://` | 标准连接串协议 | Atlas 常使用 `mongodb+srv://` |
| `root:...@` | 用户名和密码 | 特殊字符未做百分号编码 |
| `127.0.0.1:27017` | 服务端地址与端口 | 容器内的 `127.0.0.1` 指向容器自身 |
| `/shop` | 默认业务数据库 | 误以为它也是认证数据库 |
| `authSource=admin` | `root` 用户创建所在的认证数据库 | 缺失后出现认证失败 |

连接串是 URI（Uniform Resource Identifier，统一资源标识符）。密码含 `@`、`:`、`/`、`?`、`#` 等保留字符时必须正确编码；不要通过“删除特殊字符”掩盖生产凭据处理问题。生产连接串应由部署平台或密钥系统注入，不要写进源码、Git 仓库、镜像层或日志。

```bash
# 仅用于当前终端会话；真实凭据不应写入源码或提交到 Git。
export MONGODB_URI='mongodb://root:change-this-demo-password@127.0.0.1:27017/shop?authSource=admin'
```

### 8.4 完成第一次连接与查询

把下面的完整代码保存为 `src/main/java/com/example/shop/ProductQuery.java`：

```java
package com.example.shop;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

import static com.mongodb.client.model.Filters.and;
import static com.mongodb.client.model.Filters.eq;

public final class ProductQuery {

    private ProductQuery() {
    }

    public static void main(String[] args) {
        // 连接串由运行环境注入，避免把凭据硬编码进源码。
        String uri = System.getenv("MONGODB_URI");
        if (uri == null || uri.isBlank()) {
            throw new IllegalStateException("缺少 MONGODB_URI 环境变量");
        }

        try (MongoClient client = MongoClients.create(uri)) {
            MongoDatabase database = client.getDatabase("shop");

            // 第一次真实网络操作用于证明服务端可达。
            Document pingResult =
                    database.runCommand(new Document("ping", 1));
            System.out.println("ping = " + pingResult.toJson());

            MongoCollection<Document> products =
                    database.getCollection("products");
            // BSON 中 sku 是字符串，因此过滤值也使用 String。
            Document product = products.find(and(
                    eq("sku", "KB-001"),
                    eq("isDeleted", false)
            )).first();

            if (product == null) {
                System.out.println("未找到 sku=KB-001，请先完成第 2.4 节");
                return;
            }

            System.out.println("product = " + product.toJson());
        }
    }
}
```

执行：

```bash
# compile 验证编译，exec:java 再验证连接与查询的运行链路。
mvn compile exec:java
```

预期先看到包含 `"ok": 1.0` 的 `ping` 结果，再看到 `KB-001` 商品文档。`MongoClients.create(uri)` 主要创建客户端对象，连接和服务端选择可能延迟到第一次实际操作；因此“客户端对象创建成功”不是连接成功，`ping` 或真实查询成功才是可观察判据。

这个命令行程序使用 try-with-resources，在程序结束时关闭客户端。长时间运行的 Web 应用不能照搬“每次请求创建并关闭客户端”的生命周期，正确方式见第 8.7 节。

成功执行 `mvn compile` 只能证明依赖解析与 Java 语法兼容；只有连接目标 MongoDB 并观察 `ping`、查询结果和写结果，才能证明运行链路成功。后续 POJO、Builder 和事务代码是增量片段，合并进工程后应再次编译，并分别在单机或副本集环境中验证其适用能力。

### 8.5 用 Java Builder 完成查询与原子更新

驱动的 `Filters`、`Updates`、`Sorts` 和 `Projections` 是 Builder（构建器），用于生成发送给服务端的 BSON 表达式。它们分别对应 `mongosh` 中的过滤文档、更新操作符、排序和投影，不是在 Java 内存里扫描数据。

下面是第 8.4 节的增量片段：把 `import` 放到 `ProductQuery.java` 的 `package` 语句之后，把查询与更新代码放到取得 `products` 变量之后。

```java
// 以下是增量片段：import 放在文件顶部，执行代码放在取得 products 之后。
import com.mongodb.client.MongoCursor;
import com.mongodb.client.result.UpdateResult;
import org.bson.Document;

import static com.mongodb.client.model.Filters.and;
import static com.mongodb.client.model.Filters.eq;
import static com.mongodb.client.model.Filters.gte;
import static com.mongodb.client.model.Projections.excludeId;
import static com.mongodb.client.model.Projections.fields;
import static com.mongodb.client.model.Projections.include;
import static com.mongodb.client.model.Sorts.ascending;
import static com.mongodb.client.model.Updates.inc;

try (MongoCursor<Document> cursor = products.find(
                and(
                        eq("isDeleted", false),
                        eq("category", "keyboard"),
                        gte("stock", 1)
                ))
        .projection(fields(include("sku", "name", "stock"), excludeId()))
        .sort(ascending("sku"))
        .limit(20)
        .iterator()) {
    // 显式关闭游标，及时释放服务端与网络资源。
    while (cursor.hasNext()) {
        System.out.println(cursor.next().toJson());
    }
}

UpdateResult result = products.updateOne(
        // 条件与扣减在服务端原子执行，避免先查后改的竞态。
        and(
                eq("sku", "KB-001"),
                eq("isDeleted", false),
                gte("stock", 1)
        ),
        inc("stock", -1)
);

if (result.getModifiedCount() != 1) {
    throw new IllegalStateException("库存不足或商品不存在");
}
```

游标可能持有服务端和网络资源，因此显式取得 `MongoCursor` 时要关闭。条件 `stock >= 1` 与 `$inc` 在服务端对同一文档原子执行，避免“先查库存、再扣库存”的并发竞态。

| 结果方法 | 含义 | 不能误解为 |
| --- | --- | --- |
| `getMatchedCount()` | 过滤条件命中的文档数 | 一定修改了内容 |
| `getModifiedCount()` | 实际发生修改的文档数 | 未命中与新旧值相同可以混为一谈 |
| `getUpsertedId()` | Upsert 新插入文档的 `_id` | 每次更新都会有值 |
| `getDeletedCount()` | 实际删除的文档数 | 没抛异常就一定删除成功 |

### 8.6 BSON 与 Java 类型映射

Java 实体类字段类型会影响查询、排序、金额精度和空值语义。下面是初学者最需要先掌握的映射：

| BSON 类型 | Java 驱动常用类型 | 关键边界 |
| --- | --- | --- |
| ObjectId | `org.bson.types.ObjectId` | 前端字符串进入数据层后要校验并转换 |
| String | `String` | 不要用字符串保存日期和数字 |
| Int32 / Int64 | `Integer` / `Long` | 有缺失或 `null` 语义时优先包装类型 |
| Decimal128 | `org.bson.types.Decimal128` | 可通过 `bigDecimalValue()` 转为 `BigDecimal` |
| Date | `java.util.Date` 或框架支持的 `Instant` | BSON Date 保存 UTC 时间点，展示时再应用时区 |
| Array | `List<T>` | 元素类型漂移会导致解码或业务判断失败 |
| Embedded Document | `Document` 或嵌套 POJO | 更新子字段时避免误替换整个对象 |
| Null / Missing | `null`、默认值或缺失属性 | 三者不能默认视为同一业务状态 |

POJO 是 Plain Old Java Object（普通 Java 对象）。原生 POJO Codec（编解码器）需要知道如何创建对象、识别属性并映射 `_id`。下面给出能实际参与映射的类，不能只声明几个私有字段后省略所有构造器和访问方法：

```java
package com.example.shop;

import org.bson.codecs.pojo.annotations.BsonId;
import org.bson.codecs.pojo.annotations.BsonProperty;
import org.bson.types.Decimal128;
import org.bson.types.ObjectId;

import java.util.Date;

public final class Product {

    // @BsonId 把该属性映射到 MongoDB 的 _id 字段。
    @BsonId
    private ObjectId id;

    @BsonProperty("sku")
    private String sku;

    private String name;
    private Decimal128 price;
    private Integer stock;
    private Boolean isDeleted;
    private Date createdAt;

    public Product() {
        // POJO Codec 需要可访问的无参构造器来创建对象。
    }

    public ObjectId getId() {
        return id;
    }

    public void setId(ObjectId id) {
        this.id = id;
    }

    public String getSku() {
        return sku;
    }

    public void setSku(String sku) {
        this.sku = sku;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Decimal128 getPrice() {
        return price;
    }

    public void setPrice(Decimal128 price) {
        this.price = price;
    }

    public Integer getStock() {
        return stock;
    }

    public void setStock(Integer stock) {
        this.stock = stock;
    }

    public Boolean getIsDeleted() {
        return isDeleted;
    }

    public void setIsDeleted(Boolean isDeleted) {
        this.isDeleted = isDeleted;
    }

    public Date getCreatedAt() {
        return createdAt;
    }

    public void setCreatedAt(Date createdAt) {
        this.createdAt = createdAt;
    }
}
```

注册 POJO Codec 并查询。下面的 `import` 放在类文件顶部，其余代码放在已经创建 `client` 的方法内：

```java
// 以下代码复用已创建的 client，并为当前 database 注册 POJO Codec。
import com.mongodb.MongoClientSettings;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.codecs.configuration.CodecProvider;
import org.bson.codecs.configuration.CodecRegistry;
import org.bson.codecs.pojo.PojoCodecProvider;

import static com.mongodb.client.model.Filters.and;
import static com.mongodb.client.model.Filters.eq;
import static org.bson.codecs.configuration.CodecRegistries.fromProviders;
import static org.bson.codecs.configuration.CodecRegistries.fromRegistries;

CodecProvider pojoProvider =
        PojoCodecProvider.builder().automatic(true).build();
CodecRegistry registry = fromRegistries(
        // 保留驱动默认 Codec，再叠加自定义 POJO Codec。
        MongoClientSettings.getDefaultCodecRegistry(),
        fromProviders(pojoProvider)
);

MongoDatabase database = client.getDatabase("shop")
        .withCodecRegistry(registry);
MongoCollection<Product> products =
        database.getCollection("products", Product.class);

Product product = products.find(and(
        eq("sku", "KB-001"),
        eq("isDeleted", false)
)).first();
if (product == null) {
    throw new IllegalStateException("商品不存在");
}
System.out.println(product.getName());
```

官方 POJO Codec 默认省略值为 `null` 的属性。旧文档缺失字段、显式 `null`、Java 包装类型的 `null`、基本类型的零值以及业务默认值可能代表不同状态，模型演进时必须用历史样本做反序列化测试。出现 `CodecConfigurationException` 时，应检查类型是否有已注册 Codec、POJO 是否可构造、属性访问方法是否符合约定，而不是把 BSON 全部改成字符串。参见 [Java Driver POJOs](https://www.mongodb.com/docs/drivers/java/sync/current/data-formats/document-data-format-pojo/)。

### 8.7 `MongoClient` 生命周期、连接池与超时

`MongoClient` 是线程安全的重量级对象。它维护集群拓扑、监控连接以及每个服务端对应的连接池，通常在应用启动时创建一次，在应用关闭时统一关闭。`MongoDatabase` 和 `MongoCollection` 是轻量句柄，可以安全复用；不要在每个 Controller 或每次请求中创建新客户端。

```mermaid
flowchart TD
    A["应用启动"] --> B["创建一个 MongoClient"]
    B --> C["复用 MongoDatabase / MongoCollection"]
    C --> D["多个请求从连接池借用连接"]
    D --> E["操作结束后连接归还连接池"]
    E --> C
    C --> F["应用停止"]
    F --> G["关闭 MongoClient"]
```

下面的连接参数只用于说明边界，不是通用生产答案：

```text
mongodb://app-user:<percent-encoded-password>@db1.example.net:27017,db2.example.net:27017/shop?replicaSet=rs0&serverSelectionTimeoutMS=5000&connectTimeoutMS=3000&timeoutMS=10000&maxPoolSize=50
```

`serverSelectionTimeoutMS` 限制驱动寻找合适节点的等待时间，`connectTimeoutMS` 限制建立单条网络连接的时间，`timeoutMS` 是客户端操作的总体时间预算，`maxPoolSize` 是每个目标服务端连接池的上限。Java 驱动 5.x 当前文档已将 `waitQueueTimeoutMS` 标为弃用，优先使用客户端级超时；升级旧项目时不要机械照搬旧连接串。参见 [Java Driver Connection Pools](https://www.mongodb.com/docs/drivers/java/sync/current/connection/specify-connection-options/connection-pools/)。

连接池不是越大越好。池过小会让请求排队，池过大会放大服务端连接、线程和内存压力。配置必须结合实例数量、每实例并发、操作耗时、部署拓扑和服务端容量压测，并监控连接池等待时间、超时率和服务端连接数。

“1000 QPS 需要多少连接”不能直接回答为 1000。QPS（Queries Per Second，每秒查询数）描述单位时间完成多少操作，连接数近似取决于同时在途的数据库操作。可以先用 Little's Law（利特尔法则）建立平均值估算：

```text
平均在途数据库操作数 ≈ 每秒数据库操作数 × 平均数据库操作耗时（秒）
```

若整个服务每秒执行 1000 次 MongoDB 操作，平均每次耗时 20 ms：

```text
1000 × 0.020 = 20
```

这表示稳定状态平均约有 20 个数据库操作同时在途，不表示 `maxPoolSize` 必须设为 20，更不表示需要 1000 条连接。假设有 4 个应用实例，平均落到每个实例约 5 个在途操作；实际配置还要覆盖流量突发、慢请求、事务、批量操作、节点切换和调度抖动。可以从每实例较小的受控上限开始压测，而不是把池直接扩大到数百。

连接预算必须从两个方向核对：

1\. 应用侧：每个实例的峰值在途操作、池等待时间、获取连接失败、操作总超时和重试放大。

2\. 服务端：应用实例数乘以每个目标服务端的池上限，再加监控连接、管理工具和其他服务的连接。

例如 4 个实例都配置 `maxPoolSize=50`，理论上仅这一个应用就可能对某个目标服务端建立最多约 200 条业务池连接，而不是“整个集群只有 50 条”。驱动还会维护拓扑监控连接。最终值必须由接近生产的请求比例、延迟分布和故障切换压测验证；若池等待持续升高，应先找慢查询和资源瓶颈，不能只扩大连接池把压力继续推给数据库。

### 8.8 异常分类与调用方如何观察失败

不要用一个 `catch (Exception)` 把所有数据库错误转成“系统繁忙”，更不能记录日志后继续返回业务成功。先区分错误类别，再决定是否重试、是否暴露为业务冲突以及是否需要告警。

| 异常或信号 | 常见原因 | 处理方向 |
| --- | --- | --- |
| `MongoSecurityException` | 用户名、密码、认证库或机制错误 | 核对凭据来源和 `authSource`，不要重试轰炸 |
| `MongoTimeoutException` | 找不到合适节点、连接池耗尽或网络超时 | 结合拓扑日志、网络和池指标判断，不能只增大超时 |
| `MongoWriteException` 且错误码 `11000` | 唯一索引冲突 | 映射为“资源已存在”或按幂等键读取旧结果 |
| `MongoBulkWriteException` | 批量写入部分失败 | 逐项检查错误和成功项，不能假设全成或全败 |
| `CodecConfigurationException` | Java 类型没有 Codec 或 POJO 不可映射 | 修正类型、构造器、访问方法或注册表 |
| `MongoCommandException` | 命令参数、权限或部署能力不满足 | 检查服务端错误码、命令和部署形态 |
| `modifiedCount == 0` | 未命中或新旧值相同 | 这是业务结果，不一定会抛异常 |

只有满足下面条件时才考虑自动重试：操作本身可安全重试，或使用稳定幂等键、唯一索引和状态查询把重复执行变成同一结果。对写操作的未知结果，不能仅凭客户端超时断言“服务端一定没执行”。

### 8.9 Spring Data MongoDB 的最小闭环

如果项目使用 Spring Boot，通常依赖 Spring Boot 的版本管理，不要再随意叠加另一个驱动 BOM，否则可能破坏框架已经测试过的依赖组合。创建项目时加入：

```xml
<!-- Spring Data MongoDB 底层仍使用官方 MongoDB Java Driver。 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

在 `application.yaml` 中从环境变量读取连接串：

```yaml
spring:
  data:
    mongodb:
      # 连接串由环境变量注入，不把生产凭据写入配置文件。
      uri: ${MONGODB_URI}
```

实体类只保留最小字段，集合名与前文一致：

```java
package com.example.shop;

import org.bson.types.Decimal128;
import org.bson.types.ObjectId;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document("products")
public class ProductEntity {

    // Spring Data 将 @Id 属性映射到 MongoDB 的 _id。
    @Id
    private ObjectId id;
    private String sku;
    private String name;
    private Decimal128 price;
    private Integer stock;
    private Boolean isDeleted;

    protected ProductEntity() {
        // 保留给对象映射框架使用，业务代码无需直接调用。
    }

    public ObjectId getId() {
        return id;
    }

    public String getSku() {
        return sku;
    }

    public String getName() {
        return name;
    }

    public Decimal128 getPrice() {
        return price;
    }

    public Integer getStock() {
        return stock;
    }

    public Boolean getIsDeleted() {
        return isDeleted;
    }
}
```

Repository 根据方法名派生查询：

```java
package com.example.shop;

import org.bson.types.ObjectId;
import org.springframework.data.mongodb.repository.MongoRepository;

import java.util.Optional;

public interface ProductRepository
        extends MongoRepository<ProductEntity, ObjectId> {

    // Spring Data 根据方法名同时约束 SKU 和活跃状态。
    Optional<ProductEntity> findBySkuAndIsDeletedFalse(String sku);
}
```

启动时验证查询结果：

```java
package com.example.shop;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class ShopApplication {

    public static void main(String[] args) {
        SpringApplication.run(ShopApplication.class, args);
    }

    @Bean
    CommandLineRunner verify(ProductRepository repository) {
        // 应用启动后执行一次真实查询，验证映射与连接配置。
        return args -> repository.findBySkuAndIsDeletedFalse("KB-001")
                .ifPresentOrElse(
                        product -> System.out.println(product.getName()),
                        () -> System.out.println(
                                "未找到商品，请先完成第 2.4 节")
                );
    }
}
```

预期输出包含“机械键盘”。Repository 适合简单实体 CRUD 和方法名查询；`MongoTemplate` 适合复杂过滤、原子更新、聚合和需要明确检查写结果的场景。`MongoTemplate` 是可复用、线程安全的核心组件，但这不表示它能自动修复文档模型、索引和查询形状。参见 [Spring Data MongoDB Getting Started](https://docs.spring.io/spring-data/mongodb/reference/mongodb/getting-started.html) 与 [Template API](https://docs.spring.io/spring-data/mongodb/reference/mongodb/template-api.html)。

不要把 `@Indexed` 注解当成“生产索引已经存在”的证据。关键索引应通过受控迁移或显式初始化创建，再用 `db.products.getIndexes()`、Java 驱动的 `listIndexes()` 或等价数据库命令验证实际状态，避免每个应用实例在启动时并发修改生产库。

### 8.10 Java 多文档事务的前置条件与重试边界

这是进阶小节。请先学习第 9 章，并在第 10.7 节的副本集环境中验证；第 2 章的单机容器不能证明事务语义。

事务需要副本集或分片集群。学习者应使用测试用副本集、Atlas 测试集群或自动化集成测试环境验证，不能因为代码能编译就宣布事务可用。

以下片段假设 `client`、`products` 和 `orders` 都来自同一个 `MongoClient`，并且两个集合的类型都是 `MongoCollection<Document>`。订单字段与第 5.4 节的校验规则保持一致。除前文已有的驱动类型外，还需要下面的导入：

```java
// 事务选项应用于整笔事务，而不是只作用于某一个写命令。
import com.mongodb.ReadConcern;
import com.mongodb.TransactionOptions;
import com.mongodb.WriteConcern;
import com.mongodb.client.ClientSession;
import com.mongodb.client.result.UpdateResult;
import org.bson.Document;
import org.bson.types.Decimal128;
import org.bson.types.ObjectId;

import java.math.BigDecimal;
import java.util.Date;
import java.util.List;

import static com.mongodb.client.model.Filters.and;
import static com.mongodb.client.model.Filters.eq;
import static com.mongodb.client.model.Filters.gte;
import static com.mongodb.client.model.Updates.inc;
```

```java
TransactionOptions options = TransactionOptions.builder()
        .readConcern(ReadConcern.SNAPSHOT)
        .writeConcern(WriteConcern.MAJORITY)
        .build();

String transactionRequestId = "java-transaction-order-001";

try (ClientSession session = client.startSession()) {
    // withTransaction 会处理规范允许的瞬态事务与提交重试。
    session.withTransaction(() -> {
        UpdateResult stockResult = products.updateOne(
                session,
                and(
                        eq("sku", "KB-001"),
                        eq("isDeleted", false),
                        gte("stock", 1)
                ),
                inc("stock", -1)
        );

        if (stockResult.getModifiedCount() != 1) {
            // 抛出异常会让订单写入和库存扣减共同回滚。
            throw new IllegalStateException("库存不足");
        }

        orders.insertOne(session, new Document()
                .append("schemaVersion", 1)
                .append("requestId", transactionRequestId)
                .append(
                        "requestFingerprint",
                        "sha256:transaction-payload-hash")
                .append("userId",
                        new ObjectId("66a72d000000000000000001"))
                .append("status", "PAID")
                .append("items", List.of(new Document()
                        .append("productId",
                                new ObjectId(
                                        "66a72c000000000000000001"))
                        .append("nameSnapshot", "机械键盘")
                        .append("unitPrice",
                                new Decimal128(
                                        new BigDecimal("499.00")))
                        .append("quantity", 1)))
                .append("totalAmount",
                        new Decimal128(new BigDecimal("499.00")))
                .append("createdAt", new Date()));
        return null;
    }, options);
}

Document savedOrder = orders.find(
        // 提交返回后按稳定业务键验证最终可观察结果。
        eq("requestId", transactionRequestId)
).first();

if (savedOrder == null) {
    throw new IllegalStateException("事务返回后未查到订单");
}

System.out.println(
        "事务订单已提交: " + savedOrder.getString("requestId")
);
```

首次执行应打印稳定请求标识，并能查到对应订单；立即重跑会触发唯一 `requestId` 冲突，整笔事务应回滚，库存不能再次减少。所有事务操作必须显式传入同一个 `ClientSession`。`withTransaction()` 的回调可能因瞬态错误而再次执行，因此回调内调用支付、发送消息等外部副作用会产生重复风险。应把数据库内业务操作设计为幂等，并使用 Outbox（事务外发箱）等模式在提交后可靠处理外部事件。

事务提交可能出现“服务端已提交，但客户端没有收到确认”的未知结果。业务仍需使用唯一 `requestId`、唯一索引和结果查询处理重试。Java 驱动不支持在同一事务中并行执行多个操作。参见 [Java Driver Transactions](https://www.mongodb.com/docs/drivers/java/sync/current/crud/transactions/)。

### 8.11 Spring 事务代理为什么可能不生效

本节建立在 8.10 的事务前置条件之上。若当前目标只是完成 Spring Data 查询，可以先跳到 8.12 的测试分层，等掌握第 9、10 章后再回来。

Spring 的 `@Transactional` 依赖代理拦截调用。一个类中的普通方法直接调用同类另一个事务方法，调用没有经过代理，事务拦截通常不会触发。即使注解生效，还必须配置适用于 MongoDB 的事务管理器，并连接支持事务的副本集或分片集群。

```mermaid
flowchart LR
    A["Controller 或其他 Bean"] --> B["Spring 代理"]
    B --> C["@Transactional Service 方法"]
    C --> D["MongoTransactionManager"]
    D --> E["同一 ClientSession 中的 MongoDB 操作"]
```

先注册 `MongoTransactionManager`。Spring Boot 已提供 `MongoDatabaseFactory` 和 `MongoTemplate` 时，只需复用同一个工厂，不要再手工创建另一套客户端：

```java
package com.example.shop;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.MongoDatabaseFactory;
import org.springframework.data.mongodb.MongoTransactionManager;

@Configuration
public class MongoTransactionConfiguration {

    @Bean
    MongoTransactionManager transactionManager(
            MongoDatabaseFactory databaseFactory) {
        // 复用 Boot 已创建的工厂，确保事务和 MongoTemplate 使用同一客户端。
        return new MongoTransactionManager(databaseFactory);
    }
}
```

事务方法应由另一个 Spring Bean 调用。下面用 `MongoTemplate` 完成库存条件扣减和订单写入；任一步抛出运行时异常时，Spring 才会让两次数据库写入共同回滚：

```java
package com.example.shop;

import com.mongodb.client.result.UpdateResult;
import org.bson.Document;
import org.bson.types.Decimal128;
import org.bson.types.ObjectId;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;
import java.util.Date;
import java.util.List;

import static org.springframework.data.mongodb.core.query.Criteria.where;

@Service
public class OrderService {

    private final MongoTemplate template;

    public OrderService(MongoTemplate template) {
        this.template = template;
    }

    @Transactional
    public void createPaidOrder(
            String requestId,
            String requestFingerprint) {
        Query stockAvailable = Query.query(
                where("sku").is("KB-001")
                        .and("isDeleted").is(false)
                        .and("stock").gte(1));
        UpdateResult stockResult = template.updateFirst(
                // 条件扣减与后续订单插入绑定到同一事务会话。
                stockAvailable,
                new Update().inc("stock", -1),
                "products");

        if (stockResult.getModifiedCount() != 1) {
            // 运行时异常会触发 Spring 事务回滚。
            throw new IllegalStateException("库存不足或商品不存在");
        }

        Document order = new Document()
                .append("schemaVersion", 1)
                .append("requestId", requestId)
                .append("requestFingerprint", requestFingerprint)
                .append("userId",
                        new ObjectId("66a72d000000000000000001"))
                .append("status", "PAID")
                .append("items", List.of(new Document()
                        .append("productId",
                                new ObjectId(
                                        "66a72c000000000000000001"))
                        .append("nameSnapshot", "机械键盘")
                        .append("unitPrice",
                                new Decimal128(
                                        new BigDecimal("499.00")))
                        .append("quantity", 1)))
                .append("totalAmount",
                        new Decimal128(new BigDecimal("499.00")))
                .append("createdAt", new Date());

        template.insert(order, "orders");
    }
}
```

从另一个 Bean 调用 `createPaidOrder()`，故意让订单写入因重复 `requestId` 失败，再确认库存没有减少，才能证明代理、事务管理器、会话和回滚链路都生效。调用方应按第 9.4 节生成稳定的 `requestFingerprint`，不能信任客户端随意提交的摘要。`@Transactional` 本身不会替业务自动解决幂等或所有瞬态错误重试；不要在事务方法中直接调用支付、发送消息等外部系统。

排查时按入口、代理、事务管理器、会话和部署能力逐层确认：

1\. 调用是否从另一个 Spring Bean 经过代理进入事务方法。

2\. 事务方法是否为可被代理拦截的调用路径。

3\. 是否注册 `MongoTransactionManager`，且与 `MongoTemplate` 使用同一个 `MongoDatabaseFactory`。

4\. 事务内操作是否实际绑定到同一会话。

5\. 当前 MongoDB 部署是否支持事务。

6\. 测试是否故意抛异常，并确认所有相关写入都回滚。

详细机制见 [Spring Data MongoDB Sessions & Transactions](https://docs.spring.io/spring-data/mongodb/reference/mongodb/client-session-transactions.html)。

### 8.12 Java 接入的测试分层与故障定位

| 测试层 | 能证明什么 | 不能证明什么 |
| --- | --- | --- |
| 纯单元测试 | 业务分支、参数校验、异常映射 | BSON 映射、索引、事务和服务端语义 |
| 真实单机 MongoDB 集成测试 | CRUD、校验、唯一索引、类型映射 | 副本集切换和事务 |
| 真实副本集集成测试 | 事务、读写关注、重试和选举影响 | 生产数据规模下的性能 |
| 接近生产的性能测试 | 查询形状、连接池、索引和容量表现 | 所有未知流量与故障组合 |

Java 小白遇到问题时按下面顺序定位：

1\. `java -version` 与 `mvn -version` 是否指向预期 JDK。

2\. `mvn dependency:tree` 是否存在驱动版本冲突。

3\. 第 2.3 节的 `mongosh ping` 是否成功，先证明服务端可达。

4\. Java 程序是否真的读取到 `MONGODB_URI`，连接串中的认证库是否正确。

5\. 异常是认证、节点选择、网络、唯一冲突还是 Codec 映射问题。

6\. 查询字段的 BSON 实际类型是否与 Java 过滤值一致。

7\. 写操作是否检查确认状态、匹配数和修改数。

8\. 测试替身能证明的边界是否被误当成真实 MongoDB 行为。

## 9 一致性、原子性与事务

### 9.1 单文档原子性是第一选择

MongoDB 对单个文档的写操作具有原子性，即使一次更新修改多个嵌套字段。优先把需要共同变化的数据建模进一个有界文档，通常比直接引入多文档事务更简单、更高效。

库存条件扣减就是典型单文档原子操作。若订单状态变化还要同时扣减另一个集合的库存，则需要接受补偿和最终一致性，或使用多文档事务。

### 9.2 Write Concern、Read Concern 与 Read Preference

Write Concern（写关注）描述写操作需要多少节点确认；Read Concern（读关注）描述读取数据的隔离性和持久性视图；Read Preference（读偏好）描述读取应路由到副本集的哪些成员。三者解决的问题不同。

本节讨论的是副本集或分片集群语义。第 2 章的独立实例可用于熟悉部分参数，但不能证明多数提交、Secondary 读取或因果一致性；需要动手验证时，应先完成第 10.7 节的副本集实验，或使用隔离的 Atlas 测试集群。

| 机制 | 典型值 | 回答的问题 |
| --- | --- | --- |
| Write Concern | `w: 1`、`w: "majority"` | 写到什么程度才向客户端确认 |
| Read Concern | `local`、`majority`、`snapshot` | 允许读取什么一致性视图 |
| Read Preference | `primary`、`secondaryPreferred` | 从哪个成员读取 |

`w: "majority"` 通常降低主节点故障后写入回滚的风险，但会增加等待复制确认的延迟。写关注超时不等价于“写入一定失败”：数据可能已写入部分节点并继续复制，因此重试必须依赖幂等键或可查询的操作标识。参见 [Replica Set Read and Write Semantics](https://www.mongodb.com/docs/manual/applications/replication/)。

Write Concern 的关键字段不能只记 `w`：

| 字段 | 作用 | 失败语义 |
| --- | --- | --- |
| `w` | 要求多少成员或哪种标签完成确认 | 数量不可满足时写入可能等待后超时 |
| `j` | 是否要求确认节点把操作写入 Journal | 增加持久性等待，不等于备份 |
| `wtimeout` | 等待写关注满足的最长毫秒数 | 超时表示未得到目标确认，不证明写入未发生 |

```javascript
// 多数写关注同时要求 Journal；超时仍不能证明服务端没有执行。
db.products.updateOne(
  { sku: "KB-001", isDeleted: false },
  { $set: { consistencyCheckedAt: new Date() } },
  {
    writeConcern: {
      w: "majority",
      j: true,
      wtimeout: 5000
    }
  }
);
```

必须同时检查更新结果和可能的写关注错误。`w: 0` 是未确认写入，调用方得不到可靠的服务端执行结果，也不能使用普通可重试写入保证。

Read Concern 各层级解决的并不是同一个问题：

| 层级 | 核心语义 | 常见边界 |
| --- | --- | --- |
| `local` | 返回当前节点本地可见数据 | 可能包含尚未多数提交的数据 |
| `available` | 优先保证立即返回可用数据 | 主要用于分片读取，不提供因果保证 |
| `majority` | 只返回多数提交的数据视图 | 不自动决定从哪个节点读取 |
| `snapshot` | 读取同一时间点的快照 | 常用于事务，也可用于受支持的事务外读取 |
| `linearizable` | 对 Primary 上单文档读取提供实时顺序保证 | 延迟和可用性代价更高，适用操作受限 |

```javascript
// majority 读关注限制可见数据，不会自动改变读取节点。
db.orders.find({
  requestId: "tutorial-order-001"
}).readConcern("majority");
```

Read Preference 的 `primary`、`primaryPreferred`、`secondary`、`secondaryPreferred`、`nearest` 决定节点选择，不决定数据是否多数提交。标签集和 `maxStalenessSeconds` 可继续限制候选 Secondary，但配置错误可能造成无节点可选。强一致接口不应为了“分担读压力”直接切到 Secondary。

MongoDB 8.0 中，`w: "majority"` 在多数数据承载成员持久写入 Oplog 后即可确认，成员随后异步把操作应用到集合。因此，多数写刚返回后立即从任意 Secondary 普通读取，仍可能暂时看不到数据。需要跨操作因果顺序时，应在同一 Causally Consistent Session（因果一致会话）中使用 `majority` 读关注与多数写关注，让驱动携带集群时间，而不是自行拼接时间戳。参见 [Read Concern](https://www.mongodb.com/docs/manual/reference/read-concern/) 与 [Write Concern](https://www.mongodb.com/docs/manual/reference/write-concern/)。

### 9.3 多文档事务

事务适合必须原子修改多个文档或集合的场景，但不应掩盖不合理的数据模型。事务会延长资源占用，增加冲突、重试、超时和分布式协调成本。

ACID 分别是 Atomicity（原子性）、Consistency（一致性）、Isolation（隔离性）和 Durability（持久性）。原子性表示事务内写入共同提交或共同中止；隔离性描述并发事务可见的数据视图；持久性受提交与写关注影响；一致性中的业务不变量仍需由模型、校验、唯一索引和应用逻辑定义，数据库不会自动知道“订单总额必须等于订单项之和”。

下面的实验必须连接副本集或分片集群，不能在第 2 章的独立实例上执行。`products`、`orders`、模式校验器和唯一 `requestId` 索引都应在事务开始前创建；不要把集合或索引初始化混进业务事务。

```javascript
// withTransaction 负责事务边界和规范允许的重试，finally 始终关闭会话。
var session = db.getMongo().startSession();
var txDb = session.getDatabase("shop");

try {
  session.withTransaction(function() {
    const stockResult = txDb.products.updateOne(
      { sku: "KB-001", isDeleted: false, stock: { $gte: 1 } },
      { $inc: { stock: -1 } }
    );

    if (stockResult.modifiedCount !== 1) {
      // 抛出异常会中止事务，已执行的库存修改不会单独提交。
      throw new Error("库存不足");
    }

    txDb.orders.insertOne({
      schemaVersion: NumberInt(1),
      requestId: "mongosh-transaction-order-001",
      requestFingerprint: "sha256:transaction-payload-hash",
      userId: ObjectId("66a72d000000000000000001"),
      status: "PAID",
      items: [
        {
          productId: ObjectId("66a72c000000000000000001"),
          nameSnapshot: "机械键盘",
          unitPrice: NumberDecimal("499.00"),
          quantity: NumberInt(1)
        }
      ],
      totalAmount: NumberDecimal("499.00"),
      createdAt: new Date()
    });
  }, {
    readConcern: { level: "snapshot" },
    writeConcern: { w: "majority" }
  });
} finally {
  session.endSession();
}

db.getSiblingDB("shop").orders.findOne(
  { requestId: "mongosh-transaction-order-001" },
  { _id: 0, requestId: 1, status: 1, totalAmount: 1 }
);
```

首次执行后应查到订单，并观察库存只减少 `1`。立即重跑会命中唯一 `requestId` 冲突，整笔事务应回滚，因此库存不能再次减少；这同时验证了原子性和幂等约束，而不只是“没有报错”。`withTransaction()` 会在服务端允许时处理瞬态事务错误和不确定提交结果的重试，但回调本身可能被重新执行，不能在其中发送邮件、调用支付或执行其他非幂等外部副作用。事务内所有数据库操作必须使用同一个会话；Java 驱动也不支持在同一事务内并行执行操作。参见 [Transactions](https://www.mongodb.com/docs/manual/core/transactions/) 与 [`Session.withTransaction()`](https://www.mongodb.com/docs/mongodb-shell/reference/method/session-withtransaction/)。

### 9.4 幂等与未知结果

分布式系统中可能出现“服务端成功，但客户端超时未收到响应”。盲目重试创建订单会重复扣款或重复写入。正确做法是为业务操作分配稳定的 `requestId`，建立唯一索引，并让重试查询或更新同一业务实体。第 2.4 节已经创建该索引；独立运行本节时使用相同名称和定义：

```javascript
// requestId 唯一索引是数据库层的最终幂等约束。
db.orders.createIndex(
  { requestId: 1 },
  { name: "uk_orders_request_id", unique: true }
);
```

唯一 `requestId` 只能证明“这个键已使用”，不能证明两次请求的载荷相同。调用方应对会影响业务结果的规范化请求计算 SHA-256（Secure Hash Algorithm 256-bit，256 位安全散列算法）等指纹，并与结果一起保存。发生重复键时先读取旧记录：

```javascript
// 重试前读取旧结果，并比较请求指纹是否代表同一份业务载荷。
db.orders.findOne(
  { requestId: "tutorial-order-001" },
  {
    _id: 1,
    status: 1,
    requestFingerprint: 1
  }
);
```

指纹一致时可返回已有结果；不一致表示同一个幂等键被用于不同请求，应返回冲突并告警，不能直接伪装成成功。指纹只保存摘要和必要的规范化版本，不应泄露敏感原文。多租户系统通常把唯一范围设计为 `{ tenantId, requestId }`；还要明确幂等记录的保留时间，确保它至少覆盖客户端、消息队列和人工补偿可能重试的最长窗口。

## 10 副本集：复制、选举与故障转移

### 10.1 副本集的职责

副本集由一个 Primary（主节点）和多个 Secondary（从节点）组成。客户端通常向 Primary 写入，Secondary 持续复制并应用 Oplog。Primary 不可用时，满足条件的成员进行选举并产生新 Primary。

```mermaid
sequenceDiagram
    participant App as "应用驱动"
    participant P as "Primary"
    participant O as "Oplog"
    participant S1 as "Secondary A"
    participant S2 as "Secondary B"
    App->>P: "写入订单，w=majority"
    P->>O: "记录操作"
    O-->>S1: "拉取并应用"
    O-->>S2: "拉取并应用"
    S1-->>P: "确认"
    P-->>App: "达到多数确认后返回"
```

副本集提供高可用和冗余，但不是备份。逻辑误删会被复制到所有成员，勒索、误操作和数据损坏仍需要独立备份与恢复能力。

### 10.2 Oplog、复制窗口与回滚

Oplog（Operations Log，操作日志）是 `local` 数据库中的特殊有界集合，Primary 把可复制的数据变化记录进去，Secondary 拉取并重放。它保存的是有限时间窗口，不是永久审计日志；窗口取决于容量和写入速率，写流量激增会缩短可追赶时间。

```mermaid
sequenceDiagram
    participant C as "客户端"
    participant P as "Primary"
    participant O as "Primary Oplog"
    participant S as "Secondary"
    C->>P: "写入，w=1"
    P->>O: "追加操作记录"
    P-->>C: "Primary 确认"
    S->>O: "按时间拉取"
    O-->>S: "返回新操作"
    S->>S: "写入本地 Oplog 并应用数据"
```

具有相应权限时可在测试副本集检查窗口和最近操作：

```javascript
// 这些命令只能在具有相应权限的测试副本集上执行。
rs.printReplicationInfo();
rs.printSecondaryReplicationInfo();

db.getSiblingDB("local")
  .getCollection("oplog.rs")
  .find()
  .sort({ $natural: -1 })
  .limit(5);
```

`rs.printReplicationInfo()` 展示 Oplog 大小和时间跨度；Secondary 延迟若超过窗口，节点无法只靠现有 Oplog 追赶，可能需要重新初始同步。不要在业务程序中依赖 `local.oplog.rs` 的内部结构，它不是稳定的业务 API。

如果旧 Primary 接受了尚未复制到多数成员的写入后失去主节点资格，新 Primary 可能不包含这些写入。旧节点重新加入时会回滚与新历史冲突的操作。`w: "majority"` 降低这种已确认写被回滚的风险，但无法替代业务幂等、备份和恢复演练。

### 10.3 选举与故障转移

成员通过心跳观察彼此状态。Primary 不可达时，具备投票权的成员发起选举；新 Primary 必须获得多数票。故障转移期间会有短暂不可写窗口，驱动重新选择服务端后再恢复。

副本集配置要同时区分“是否保存数据”“是否投票”“是否可能成为 Primary”。普通数据承载成员保存数据并可按 `priority` 参与选举；`priority: 0` 成员不会成为 Primary；Hidden Member（隐藏成员）不向普通驱动提供读取候选；Delayed Member（延迟成员）故意滞后应用操作。Arbiter（仲裁节点）只投票、不保存数据，能帮助形成选举多数，但不增加数据副本和写入持久性。

当前延迟成员配置字段是 `secondaryDelaySecs`，旧资料中的 `slaveDelay` 已不应继续使用。下面只展示测试副本集中的配置关系，执行前必须确认目标成员索引和剩余投票多数：

```javascript
// 修改配置前读取当前版本，并确保被延迟成员不会参与 Primary 选举。
var delayedConfig = rs.conf();
delayedConfig.members[2].priority = 0;
delayedConfig.members[2].hidden = true;
delayedConfig.members[2].secondaryDelaySecs = 3600;
rs.reconfig(delayedConfig);
```

延迟成员保存“一小时前”的可见状态，但窗口仍受 Oplog、磁盘、成员故障和误配置影响。它可以帮助调查部分误操作，不能替代离线备份；恢复数据前还要阻止延迟成员继续应用那次误操作，并经过受控导出或恢复流程。参见 [Self-Managed Replica Set Configuration](https://www.mongodb.com/docs/manual/reference/replica-configuration/)。

三个投票成员的副本集通常能在失去一个投票成员后继续形成多数；若网络把 Primary 隔离在少数侧，少数侧不能继续成为可写 Primary。成员数量只是起点，还要把成员分散到独立故障域，并核对云区、机架、电源和网络是否真的独立。延迟成员与隐藏成员仍有有限窗口和拓扑边界，不能替代离线备份。参见 [Hidden Replica Set Members](https://www.mongodb.com/docs/manual/core/replica-set-hidden-member/) 与 [Replica Set Arbiter](https://www.mongodb.com/docs/manual/core/replica-set-arbiter/)。

生产应用应使用副本集连接串，而不是只写一个节点：

```text
mongodb://app-user:<percent-encoded-password>@db1.example.net:27017,db2.example.net:27017,db3.example.net:27017/shop?replicaSet=rs0&retryWrites=true&w=majority
```

`<percent-encoded-password>` 是占位符，实际密码应做百分号编码并由密钥系统注入。DNS、证书、网络访问和连接池配置必须覆盖所有成员。只连接 Primary 的固定地址会让自动故障转移失效。

### 10.4 驱动重试不能替代业务幂等

与 MongoDB 4.2 及以上兼容的官方驱动默认启用 Retryable Writes（可重试写入）。它只适用于副本集或分片集群，单机部署不支持；使用 `w: 0` 的未确认写入也不能重试。驱动重试解决的是单个数据库命令遇到特定瞬态故障的问题，并受服务端选择和客户端超时预算限制。

业务幂等解决的是完整业务操作被调用多次的问题，可能跨越多个数据库命令、事务、消息和外部支付。事务中的写命令不会作为普通可重试写入逐条重试，因此订单创建仍需第 9.4 节的稳定 `requestId`、唯一索引、请求指纹和结果查询。两层机制应同时存在，不能因为连接串里有 `retryWrites=true` 就删除业务幂等设计。参见 [Retryable Writes](https://www.mongodb.com/docs/manual/core/retryable-writes/)。

### 10.5 复制延迟与读偏好

从 Secondary 读取可能降低 Primary 的部分读取压力，但会读到旧数据，并不能自动提高总体性能。若查询没有合适索引，把它转移到 Secondary 只是把高成本扫描转移到另一个节点。

强依赖“写后立即读”的请求通常使用 `primary`，或在满足条件的会话中使用因果一致性。报表、搜索建议等可容忍陈旧数据的请求才适合评估 Secondary 读取。

### 10.6 副本集健康检查

```javascript
// 同时检查成员状态、Oplog 窗口、复制延迟和当前节点身份。
rs.status();
rs.printReplicationInfo();
rs.printSecondaryReplicationInfo();
db.hello();
```

重点观察成员状态、健康标志、任期、Oplog 时间窗口和复制延迟。复制延迟升高时，继续检查磁盘 I/O（Input/Output，输入输出）、网络、长查询、索引构建、Secondary 应用速率和 Oplog 容量。

### 10.7 用三个 Docker 节点验证复制、选举与应用重连

第 2 章的单机容器只能学习 CRUD，不能证明选举、事务或 Change Streams。下面创建一个完全独立的三节点学习环境，固定使用 MongoDB 8.0，不启用认证和 TLS，因此只能在本机非生产环境运行。

#### 10.7.1 创建网络与三个临时成员

三个容器使用不同端口，避免与第 2 章的 `27017` 冲突：

```bash
# 仅用于本机实验：宿主机端口绑定 127.0.0.1，容器间通过专用网络通信。
docker network create mongodb-rs-lab

docker run -d \
  --name mongodb-rs1 \
  --hostname mongodb-rs1 \
  --network mongodb-rs-lab \
  -p 127.0.0.1:27117:27117 \
  mongo:8.0 \
  --replSet rs0 \
  --bind_ip_all \
  --port 27117

docker run -d \
  --name mongodb-rs2 \
  --hostname mongodb-rs2 \
  --network mongodb-rs-lab \
  -p 127.0.0.1:27118:27118 \
  mongo:8.0 \
  --replSet rs0 \
  --bind_ip_all \
  --port 27118

docker run -d \
  --name mongodb-rs3 \
  --hostname mongodb-rs3 \
  --network mongodb-rs-lab \
  -p 127.0.0.1:27119:27119 \
  mongo:8.0 \
  --replSet rs0 \
  --bind_ip_all \
  --port 27119
```

确认三个进程都能接受连接：

```bash
# 初始化前逐个确认三个 mongod 进程都能响应 hello。
docker exec mongodb-rs1 mongosh \
  --quiet \
  --port 27117 \
  --eval 'db.hello().ok'

docker exec mongodb-rs2 mongosh \
  --quiet \
  --port 27118 \
  --eval 'db.hello().ok'

docker exec mongodb-rs3 mongosh \
  --quiet \
  --port 27119 \
  --eval 'db.hello().ok'
```

预期三个命令都输出 `1`。若连接失败，先执行 `docker ps -a` 和 `docker logs mongodb-rs1`，检查镜像下载、端口占用和进程启动错误。

#### 10.7.2 初始化并等待 Primary

只在一个成员上执行一次 `rs.initiate()`：

```bash
# rs.initiate() 只执行一次，成员地址必须能被其他成员和客户端解析。
docker exec mongodb-rs1 mongosh \
  --quiet \
  --port 27117 \
  --eval '
    rs.initiate({
      _id: "rs0",
      members: [
        {
          _id: 0,
          host: "mongodb-rs1:27117"
        },
        {
          _id: 1,
          host: "mongodb-rs2:27118"
        },
        {
          _id: 2,
          host: "mongodb-rs3:27119"
        }
      ]
    })
  '
```

等待选举后查看状态：

```bash
# 使用完整种子列表连接，等待出现一个 PRIMARY 和两个 SECONDARY。
docker exec mongodb-rs1 mongosh \
  --quiet \
  'mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/admin?replicaSet=rs0' \
  --eval '
    rs.status().members.map(function(member) {
      return {
        name: member.name,
        stateStr: member.stateStr,
        health: member.health
      };
    })
  '
```

成功判据是恰好一个 `PRIMARY`、两个 `SECONDARY`，并且三个成员 `health` 都为 `1`。初始化命令返回成功但尚未选出 Primary 时，短暂等待后重新查询状态，不要重复执行 `rs.initiate()`。

#### 10.7.3 验证多数写与复制

通过副本集连接串写入一条测试文档：

```bash
# 连接串指定 w=majority，让本次写入等待多数确认。
docker exec mongodb-rs2 mongosh \
  --quiet \
  'mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/shop?replicaSet=rs0&w=majority' \
  --eval '
    db.replicaLab.insertOne({
      _id: "LAB-001",
      message: "majority write",
      createdAt: new Date()
    })
  '
```

然后直接连接一个 Secondary，并显式使用 Secondary 读偏好验证复制：

```bash
# directConnection 固定连接该成员，readPreference 明确允许 Secondary 读取。
docker exec mongodb-rs3 mongosh \
  --quiet \
  'mongodb://mongodb-rs3:27119/shop?directConnection=true&readPreference=secondary' \
  --eval '
    db.replicaLab.findOne({
      _id: "LAB-001"
    })
  '
```

预期读取到刚写入的文档。这个结果只证明本次复制已经完成，不代表所有 Secondary 读取都具有写后立即可见性；第 9.2 和 10.5 节解释了多数写、读关注与读偏好的区别。

#### 10.7.4 主动降级 Primary 并观察重新选举

不要假设固定容器一定是 Primary。先通过完整种子列表让驱动选择当前 Primary，并记录成员名：

```bash
# 通过种子列表定位当前 Primary，不能假定 rs1 永远可写。
docker exec mongodb-rs1 mongosh \
  --quiet \
  'mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/admin?replicaSet=rs0' \
  --eval '
    var hello = db.hello();
    {
      primary: hello.primary,
      isWritablePrimary: hello.isWritablePrimary
    }
  '
```

`isWritablePrimary` 应为 `true`。随后仍通过完整副本集连接串执行主动降级，这样无论当前 Primary 是哪个成员，命令都会发往正确节点：

```bash
# 命令会主动让当前 Primary 在 60 秒内放弃主节点资格。
docker exec mongodb-rs1 mongosh \
  --quiet \
  'mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/admin?replicaSet=rs0' \
  --eval 'rs.stepDown(60)'
```

连接被关闭或命令返回网络错误可能是正常现象，因为原 Primary 在执行期间主动失去可写身份。通过完整种子列表重新查询：

```bash
# 重新通过完整拓扑连接，观察驱动选择的新 Primary。
docker exec mongodb-rs2 mongosh \
  --quiet \
  'mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/admin?replicaSet=rs0' \
  --eval '
    var hello = db.hello();
    {
      primary: hello.primary,
      isWritablePrimary: hello.isWritablePrimary
    }
  '
```

成功判据是 `primary` 与降级前记录的成员不同，并且连接所选择的节点可写。若 60 秒内原成员又当选，实验环境的选举优先级或成员可用性可能不足，应结合 `rs.status()` 判断，不能只比较一次输出。随后再次使用相同副本集连接串执行插入，确认客户端不需要改成新 Primary 的固定地址。

#### 10.7.5 让宿主机 Java 程序识别容器成员

副本集会向驱动返回配置中的 `mongodb-rs1`、`mongodb-rs2`、`mongodb-rs3`，所以驱动所在环境必须能解析这些名称。最简单的隔离方式是让 Java 测试也运行在 `mongodb-rs-lab` 网络中。若要从本机直接运行第 8 章的 Java 示例，可在本机 hosts 文件中为学习环境增加：

```text
127.0.0.1 mongodb-rs1 mongodb-rs2 mongodb-rs3
```

然后使用：

```text
mongodb://mongodb-rs1:27117,mongodb-rs2:27118,mongodb-rs3:27119/shop?replicaSet=rs0&retryWrites=true&w=majority
```

hosts 文件修改需要管理员权限，且只适合本地实验；不要把这种做法复制到生产。生产应使用稳定 DNS、TLS、内部成员认证和受控网络。Java 验证应在一次持续写入测试中触发 `rs.stepDown()`，记录短暂失败、服务端重新选择耗时和最终成功数，同时通过稳定 `requestId` 检查没有重复业务结果。

#### 10.7.6 清理学习环境

确认不再需要测试数据后执行：

```bash
# 只删除本节明确创建的三个临时容器和专用网络。
docker rm -f \
  mongodb-rs1 \
  mongodb-rs2 \
  mongodb-rs3

docker network rm mongodb-rs-lab
```

这些容器没有挂载持久卷，删除后实验数据无法恢复。若命令提示对象不存在，先用 `docker ps -a` 和 `docker network ls` 确认实际名称；不要使用宽泛名称或通配符删除其他容器。

## 11 分片集群：超过单副本集边界后的水平扩展

### 11.1 分片组件

```mermaid
flowchart TD
    A["应用程序"] --> M["mongos 路由"]
    M --> C["Config Server Replica Set：元数据"]
    M --> S1["Shard 1：副本集"]
    M --> S2["Shard 2：副本集"]
    M --> S3["Shard 3：副本集"]
    B["Balancer：范围迁移"] --> S1
    B --> S2
    B --> S3
```

Shard 保存业务数据，Config Server Replica Set（配置服务器副本集）保存分片元数据，`mongos` 根据分片键路由请求，Balancer（均衡器）在分片间迁移数据范围。应用连接 `mongos`，不应绕过路由直接访问某个分片完成正常业务。

### 11.2 分片键决定系统上限

理想分片键需要同时考虑：

1\. 基数足够高，能够产生足够多的数据分区。

2\. 值分布均匀，避免热点集中。

3\. 高频查询包含分片键或其复合前缀，减少广播。

4\. 写入不会永久集中到单一范围。

5\. 字段稳定，业务生命周期内不会频繁变化。

单调递增时间作为范围分片键容易让最新写入集中到一个分片。哈希分片可改善分布，但会削弱范围查询的定向能力。常见折中是结合租户、哈希和时间设计复合键，并用真实流量回放验证。

### 11.3 范围、Chunk 与哈希分片

MongoDB 把分片键值空间切成互不重叠的 Range（范围），每个范围由一个 Chunk（数据块）表示并归属于某个 Shard。Chunk 是路由和迁移的逻辑单位，不应把它理解成应用可直接管理的固定磁盘文件。

Range Sharding（范围分片）保留键的顺序，适合范围查询和按前缀定向，但低基数、单调递增或高频值会形成热点。Hashed Sharding（哈希分片）对分片键值计算哈希后分布，通常更均匀，但原字段的连续范围会散落到多个分片。

下面是目标设计示例，只能在已正确配置的测试分片集群中执行：

```javascript
// 这是目标设计语法，不可直接用于缺少 tenantId 的教程订单集合。
sh.shardCollection(
  "shop.orders",
  {
    tenantId: 1
  }
);
```

这里先用单字段范围键说明语法，并不表示 `tenantId` 一定是生产最佳选择；超大租户会形成热点。第 2 章数据没有 `tenantId`，且已有不含分片键前缀的全局唯一 `requestId` 索引，因此不能直接执行这条命令。真实迁移必须先补齐租户字段、替换不兼容唯一索引、建立分片键索引并验证全量数据。

若把键方向写成 `"hashed"`，才表示该字段采用哈希分布。选择复合哈希键时，还要用真实租户大小、写入热点和查询过滤测试；“看起来随机”不是均匀分布的证据。MongoDB 使用范围与 Chunk 的机制参见 [Shard Keys](https://www.mongodb.com/docs/manual/core/sharding-shard-key/)。

### 11.4 定向查询与广播查询

包含完整分片键的查询通常能路由到目标分片；缺失分片键的查询可能 Scatter-Gather（分散—收集），即向多个分片广播再合并结果。

```javascript
// 查询携带完整分片键 tenantId，mongos 才能执行定向路由。
db.orders.find({
  tenantId: "tenant-a",
  requestId: "tutorial-order-001"
});
```

若分片键是 `{ tenantId: 1 }`，这类查询包含完整分片键，可以路由到保存该租户范围的 Shard。只按 `status` 查询全体租户则可能广播。复合分片键只提供前缀时可能缩小目标范围，但不应直接假设只访问一个 Shard。广播并非绝对禁止，但高频低延迟接口大量广播会让扩容收益消失。

### 11.5 分片集合中的唯一约束

分片后，唯一约束必须能够沿分片路由验证。MongoDB 只允许分片键本身，或以完整分片键为前缀的复合索引，在分片集合上声明为唯一索引。例如分片键是 `{ tenantId: 1 }` 时，可把订单幂等范围设计为租户内唯一：

```javascript
// 唯一索引以完整分片键为前缀，只保证租户内 requestId 唯一。
db.orders.createIndex(
  { tenantId: 1, requestId: 1 },
  {
    name: "uk_orders_tenant_request",
    unique: true
  }
);
```

该索引保证 `{ tenantId, requestId }` 元组唯一，不保证脱离租户的 `requestId` 全局唯一。若业务必须维护独立于分片键的全局唯一值，需要重新设计标识生成方式，或引入单独的唯一性登记写路径并评估它的新瓶颈。把已有集合改为分片集合前，必须盘点现有唯一索引；写入过滤条件也应尽量携带完整分片键。参见 [Unique Indexes](https://www.mongodb.com/docs/manual/core/index-unique/)。

### 11.6 均衡、迁移与扩容

Balancer 根据数据分布迁移范围。迁移会消耗网络、磁盘和计算资源，新增分片后数据重新均衡也需要时间。不能把“加一台机器”理解为吞吐量瞬间线性增长。

Balancer 运行在 Config Server Replica Set（配置服务器副本集）的 Primary 上，并遵守 Zone（区域）约束。迁移过程中会复制范围数据、提交元数据变化，再异步清理旧范围；路由元数据、临时磁盘、网络和 Secondary 复制都会承受额外压力。

```javascript
// 同时检查均衡状态与集合在各 Shard 上的真实分布。
sh.status();
sh.balancerCollectionStatus("shop.orders");
db.orders.getShardDistribution();
```

验证不能只看“每个 Shard 的数据量接近”。还要观察请求是否定向、热点租户的吞吐与延迟、Chunk 迁移失败、Jumbo Chunk（不可再拆分的大数据块）、复制延迟和磁盘余量。高频分片键值会让一个 Chunk 无法按值继续拆分，增加热点与迁移困难；这通常是分片键基数或频率分布问题。

### 11.7 Refinement、Resharding 与 Zone

Refine Shard Key（细化分片键）是在现有分片键末尾追加字段，用更细粒度的范围提高基数；它不能删除原字段，也不能改变原字段的范围或哈希类型。Resharding（重新分片）则用新分片键重新分布集合，可以彻底改变键，但会产生明显的存储、网络、复制和协调成本。

```javascript
// 细化分片键只能在原键末尾追加字段，不能替换原前缀。
db.adminCommand({
  refineCollectionShardKey: "shop.orders",
  key: {
    tenantId: 1,
    requestId: 1
  }
});
```

执行前必须建立支持新键的索引，并确认唯一索引、校验器、查询和写入都兼容新字段。细化不能修复原前缀本身造成的所有热点；需要改变前缀或分布策略时才评估 `reshardCollection`。参见 [Refine a Shard Key](https://www.mongodb.com/docs/current/core/sharding-refine-a-shard-key/)。

Zone Sharding（区域分片）把分片键范围关联到一组 Shard，可用于地域、硬件等级或数据隔离。Zone 依赖分片键前缀和 Balancer 迁移，不单独构成合规保证；网络、备份、密钥、运维访问和跨区读取仍需共同治理。修改 Zone 或重新分片后，应通过 `sh.status()`、分布统计和目标查询的 `explain()` 验证真实路由，而不是只确认命令返回成功。

官方分片说明见 [Sharding](https://www.mongodb.com/docs/manual/sharding/)，分片键设计见 [Choose a Shard Key](https://www.mongodb.com/docs/manual/core/sharding-choose-a-shard-key/)。

### 11.8 在测试分片集群验证路由，而不是照搬旧拓扑脚本

完整分片集群至少包含 `mongos`、Config Server Replica Set 和一个或多个 Shard Replica Set。它比副本集实验消耗更多资源，且版本、主机名、认证和网络配置高度相关，因此不应复制固定 IP、关闭认证、`bindIp: 0.0.0.0` 的旧教程作为生产方案。自管理学习环境应从 [Deploy a Self-Managed Sharded Cluster](https://www.mongodb.com/docs/manual/tutorial/deploy-shard-cluster/) 的目标版本步骤开始；Atlas 用户则直接在测试项目创建支持分片的集群。

连接后先证明当前入口确实是 `mongos`：

```javascript
// hello 返回 isdbgrid，才能证明当前入口是 mongos。
db.adminCommand({
  hello: 1
});
```

返回结果应包含 `msg: "isdbgrid"`。若直接连接某个 Shard 成员，后续命令不能证明应用经过路由层。

下面的实验要求至少两个 Shard，并会短暂停止整个测试集群的 Balancer（均衡器），只能在无重要数据的专用实验集群执行。先创建范围分片集合，再把 `tenant-b` 所在 Chunk（数据块）明确移动到另一个 Shard；如果不做这一步，少量测试数据可能全在同一 Shard，所谓“广播查询”就没有跨 Shard 的可观察证据。

```javascript
// 仅限至少两个 Shard、无重要数据的专用实验集群。
use shop;

sh.enableSharding("shop");

db.routingLab.createIndex({
  tenantId: 1,
  eventId: 1
});

sh.shardCollection(
  "shop.routingLab",
  {
    tenantId: 1,
    eventId: 1
  }
);

var shardIds = db.adminCommand({ listShards: 1 }).shards.map(
  function(shard) {
    return shard._id;
  }
);

if (shardIds.length < 2) {
  throw new Error("路由实验至少需要两个 Shard");
}

var databaseMetadata = db.getSiblingDB("config").databases.findOne({
  _id: "shop"
});

if (databaseMetadata === null) {
  throw new Error("未找到 shop 数据库的分片元数据");
}

var destinationShard = shardIds.find(function(shardId) {
  // 新集合的初始范围位于数据库 Primary，目标选择另一个 Shard。
  return shardId !== databaseMetadata.primary;
});

var balancerWasRunning = sh.getBalancerState();

if (balancerWasRunning) {
  sh.stopBalancer();
}

try {
  // 在 tenant-b 处切分，再把右侧 Chunk 明确移动到另一个 Shard。
  sh.splitAt(
    "shop.routingLab",
    { tenantId: "tenant-b", eventId: MinKey }
  );

  sh.moveChunk(
    "shop.routingLab",
    { tenantId: "tenant-b", eventId: MinKey },
    destinationShard
  );
} finally {
  // 只恢复实验前原本处于运行状态的 Balancer。
  if (balancerWasRunning) {
    sh.startBalancer();
  }
}

db.routingLab.insertMany([
  {
    tenantId: "tenant-a",
    eventId: "A-001",
    status: "NEW"
  },
  {
    tenantId: "tenant-b",
    eventId: "B-001",
    status: "NEW"
  }
]);

db.routingLab.getShardDistribution();
```

分布结果应显示 `tenant-a` 与 `tenant-b` 的文档由两个不同 Shard 持有。若 `splitAt` 或 `moveChunk` 报错，先确认连接的是 `mongos`、集合使用范围分片键、账号具备集群管理权限、Balancer 状态已恢复，并检查旧实验是否留下同名集合或边界。`sh.splitAt()` 与 `sh.moveChunk()` 是人为构造测试分布的管理命令，不是生产请求的日常路由方式；具体参数与限制参见 [`sh.splitAt()`](https://www.mongodb.com/docs/manual/reference/method/sh.splitAt/) 和 [`sh.moveChunk()`](https://www.mongodb.com/docs/manual/reference/method/sh.moveChunk/)。

分别比较完整分片键与缺失分片键的执行计划：

```javascript
// 完整分片键查询应定向；缺少分片键的查询应访问两个 Shard。
db.routingLab.find({
  tenantId: "tenant-a",
  eventId: "A-001"
}).explain("executionStats");

db.routingLab.find({
  status: "NEW"
}).explain("executionStats");
```

第一个查询具备完整分片键，应只定向到 `tenant-a` 所在 Shard；第二个查询没有分片键，应访问当前持有集合数据块的两个 Shard。执行计划结构随版本变化，验证重点是命中的 Shard 数量、每个 Shard 的扫描量和总延迟，而不是机械搜索某一个阶段名。

继续检查：

```javascript
// 用元数据和数据分布共同确认人工切分、迁移是否真实生效。
sh.status();
db.routingLab.getShardDistribution();
```

`sh.status()` 应显示人工切分的边界，分布统计应与前面的检查一致。这个实验只证明定向与广播语义，不代表数据会自然均匀，也不能证明生产负载没有热点；均衡、迁移和热点测试仍需要足够的数据量、多个 Shard、持续负载和可观察的 Balancer 活动。实验结束后只删除明确的测试集合：

```javascript
// 清理时只删除本节创建的测试集合。
db.routingLab.drop();
```

生产验收还必须覆盖成员认证、TLS、备份、故障域、某个 Shard 整体不可用、Config Server 失去多数、Balancer 迁移、Jumbo Chunk、扩容时间和应用对部分失败的处理。一个“命令执行成功”的最小集群不能证明这些能力。

## 12 生产可靠性、安全、备份与监控

### 12.1 最小安全基线

1\. 启用身份认证和基于角色的授权，坚持最小权限。

2\. 应用账号与管理员账号分离，不让业务服务使用 `root`。

3\. 限制网络访问范围，不把数据库端口直接暴露到公网。

4\. 客户端到服务端以及节点间通信启用 TLS（Transport Layer Security，传输层安全协议）。

5\. 密码、证书和密钥放入密钥管理系统，定期轮换并审计使用。

6\. 对管理操作、权限变更和敏感访问配置审计能力。

7\. 及时应用受支持版本的安全更新，并在预发布环境验证升级。

身份认证证明“你是谁”，授权决定“你能做什么”。只启用认证但给所有应用高权限，仍然不安全。官方入口见 [Authentication on Self-Managed Deployments](https://www.mongodb.com/docs/manual/core/authentication/)。

#### 12.1.1 客户端认证、授权与查询入口控制

应用账号应只获得业务运行时需要的动作。下面在 `shop` 认证数据库创建自定义角色和用户，命令只能由具备用户与角色管理权限的管理员执行：

```javascript
// 这些命令只能由具备用户与角色管理权限的管理员执行。
use shop;

db.createRole({
  role: "shopOrderWriter",
  privileges: [
    {
      resource: {
        db: "shop",
        collection: "orders"
      },
      actions: ["find", "insert", "update"]
    }
  ],
  roles: []
});

db.createUser({
  user: "shop-app",
  pwd: passwordPrompt(),
  roles: [
    {
      role: "shopOrderWriter",
      db: "shop"
    }
  ]
});
```

该账号可以查询和写订单，但没有删除集合、管理用户或创建索引的权限。验证时用独立会话连接 `authSource=shop`，分别执行一次允许的 `find()` 和一次应被拒绝的管理命令，不能只看到“登录成功”就认为最小权限正确。

MongoDB 的 RBAC（Role-Based Access Control，基于角色的访问控制）约束数据库资源和动作，不会自动理解“用户只能看自己租户订单”这种业务行级规则。多租户应用仍必须从可信身份上下文注入 `tenantId`、统一构造过滤条件并做越权测试，不能使用客户端提交的租户字段直接授权。

API 也不应接受客户端提供的任意 MQL 文档后直接执行。攻击者可能注入 `$ne`、`$regex` 或其他操作符，绕过原本的等值条件。服务端应从经过类型校验的 DTO（Data Transfer Object，数据传输对象）读取普通值，并用受控 Builder 构造允许的字段和操作符：

```java
// tenantId 必须来自可信身份上下文，不能直接相信请求体字段。
Bson userFilter = Filters.and(
        Filters.eq("tenantId", trustedTenantId),
        Filters.eq("username", submittedUsername)
);
```

`trustedTenantId` 必须来自已经验证的身份上下文，不能来自请求体。动态排序、投影和聚合字段也需要白名单；正则搜索还要限制模式、长度和超时，避免注入与高成本查询。

#### 12.1.2 副本集与分片集群的内部成员认证

客户端认证解决“应用或管理员是否允许访问数据库”，Internal/Membership Authentication（内部成员认证）解决“某个 `mongod` 或 `mongos` 是否真是这个集群的成员”。只创建业务用户而不配置成员认证，不能阻止伪造节点尝试加入自管理集群。

自管理部署常见的内部认证方式有：

| 方式 | 机制 | 适用边界 |
| --- | --- | --- |
| Keyfile | 所有合法成员至少共享一个密钥 | 最低限度方案，适合开发、测试或迁移过渡 |
| X.509 | 使用证书主题和信任链验证成员身份 | 生产优先评估，可结合 TLS 保护节点通信 |

Keyfile 不是普通应用密码。它由 `mongod`、`mongos` 进程读取，不应放进代码仓库、容器镜像公共层或应用配置。所有成员必须共享至少一个有效密钥；UNIX 系统上文件不能开放给组或其他用户。示意配置如下：

```yaml
security:
  # Keyfile 是成员间共享密钥，文件权限必须限制为 MongoDB 进程可读。
  keyFile: /run/secrets/mongodb-internal-key
replication:
  replSetName: rs0
net:
  bindIp: localhost,db1.example.net
  tls:
    # 生产环境要求客户端和成员通信都使用 TLS。
    mode: requireTLS
    certificateKeyFile: /run/secrets/db1.pem
    CAFile: /run/secrets/cluster-ca.pem
```

示例路径是占位符。实际部署应让 MongoDB 进程用户拥有最小读取权限，并使用稳定 DNS 主机名；从 MongoDB 5.0 起，只用 IP 地址配置某些副本集成员会触发启动校验问题。启用 `security.keyFile` 同时会启用客户端访问控制，所以切换前必须先准备管理员和应用用户。

已有集群不能在所有成员上同时停机修改。滚动启用时要始终保留可选举多数，先处理 Secondary，再降级并处理原 Primary；无停机迁移可以按官方步骤使用 `transitionToAuth` 过渡，但过渡状态会同时接受已认证和未认证连接，必须缩短窗口、限制网络并记录变更。Keyfile 支持同时保存多个密钥，可利用共同旧密钥与共同新密钥完成滚动轮换，不能一次替换到彼此没有交集的密钥集合。

验证不能只看进程启动：

1\. `rs.status()` 或 `sh.status()` 显示全部预期成员健康。

2\. 合法成员能够完成心跳、复制和选举。

3\. 没有共同密钥或不受信任证书的测试节点无法加入。

4\. 未认证客户端被拒绝，应用账号只能执行其角色允许的动作。

5\. 轮换后旧密钥或旧证书已移除，并完成一次故障切换。

MongoDB 官方把 Keyfile 定位为最低限度安全形式，并建议生产环境使用 X.509。实施时应严格按目标版本的 [Update Self-Managed Replica Set to Keyfile Authentication](https://www.mongodb.com/docs/manual/tutorial/enforce-keyfile-access-control-in-existing-replica-set/) 或对应分片集群教程执行，不能直接复制第 10.7 节的无认证实验配置。

### 12.2 传输、静态与使用中加密

数据加密需要区分三个阶段。Transport Encryption（传输加密）用 TLS 保护客户端与节点之间的网络流量；Encryption at Rest（静态加密）保护磁盘、快照或存储介质；In-Use Encryption（使用中加密）让敏感字段在到达服务端前由客户端加密。

```mermaid
flowchart LR
    A["应用明文与业务权限"] --> B["客户端字段加密：可选"]
    B --> C["TLS 加密网络通道"]
    C --> D["MongoDB 服务端处理密文或普通字段"]
    D --> E["静态加密的磁盘与备份"]
    K["客户主密钥与数据密钥"] --> B
```

TLS 不能防止拥有数据库读取权限的人看到服务端明文，静态加密也不能阻止已获授权的数据库进程读取数据。Queryable Encryption（可查询加密）和 CSFLE（Client-Side Field Level Encryption，客户端字段级加密）使用信封加密：数据密钥加密字段，客户主密钥再保护数据密钥。两种机制的查询能力、版本要求、驱动配置、索引限制和同集合兼容性不同，不能只增加一个连接参数。

生产设计必须明确密钥管理系统、密钥库权限、轮换与灾难恢复；主密钥丢失会导致数据不可恢复，应用日志、追踪、异常和缓存也不能泄露解密后的敏感值。实施前用目标驱动和服务端版本验证允许的查询类型、模式校验、索引、备份恢复与故障切换。参见 [Encryption](https://www.mongodb.com/docs/manual/core/security-data-encryption/) 与 [In-Use Encryption](https://www.mongodb.com/docs/manual/core/security-in-use-encryption/)。

### 12.3 备份不等于复制

副本集同步当前状态，备份保留可恢复的历史状态。生产备份要定义 RPO（Recovery Point Objective，恢复点目标）和 RTO（Recovery Time Objective，恢复时间目标）。

| 目标 | 问题 | 示例 |
| --- | --- | --- |
| RPO | 最多能接受丢失多久的数据 | 5 分钟 |
| RTO | 故障后多久必须恢复服务 | 30 分钟 |

`mongodump` 和 `mongorestore` 适合小型部署、迁移和逻辑备份，但会给运行实例带来读取和缓存压力。大型生产环境通常评估文件系统快照、块级快照或 Atlas Cloud Backup。官方建议见 [Back Up and Restore with MongoDB Tools](https://www.mongodb.com/docs/manual/tutorial/backup-and-restore-tools/)。

```bash
# 从副本集创建一致性逻辑备份；凭据由交互提示或密钥系统提供。
mongodump \
  --uri='mongodb://backup-user@db.example.net:27017/?authSource=admin' \
  --archive='shop-20260728.archive' \
  --gzip \
  --oplog
```

不要在命令行硬编码真实密码；可省略密码让工具交互提示，或使用受控凭据机制。`--oplog` 用于副本集一致性恢复，完整适用条件必须按当前工具文档核对。

恢复演练的目标必须是新建、空白且与生产隔离的集群。直接恢复到已有同名数据的环境，可能出现重复键、覆盖语义或新旧数据混杂，使结果既不安全也无法作为恢复证据。

```bash
# 只恢复到新建、空白、与生产隔离的演练集群。
mongorestore \
  --uri='mongodb://restore-user@restore.example.net:27017/?authSource=admin' \
  --archive='shop-20260728.archive' \
  --gzip \
  --oplogReplay
```

备份成功的判据不是生成了文件，而是定期在隔离环境恢复、校验数据和索引、记录实际 RPO/RTO，并确认应用能连接恢复后的集群。

### 12.4 监控指标

| 层面 | 关键观察项 | 异常含义示例 |
| --- | --- | --- |
| 请求 | 吞吐、P50/P95/P99 延迟、错误率 | 慢查询或资源饱和 |
| 查询 | 扫描文档数/返回数、慢查询、排序 | 索引或查询形状问题 |
| 连接 | 当前连接、等待队列、建立速率 | 连接泄漏或连接风暴 |
| 内存 | WiredTiger Cache、缺页、驱逐 | 工作集超过内存 |
| 磁盘 | IOPS、吞吐、延迟、空间 | 写入瓶颈或容量风险 |
| 复制 | Secondary 延迟、Oplog 窗口 | 节点追不上或恢复窗口不足 |
| 锁与票据 | 排队、并发票据 | 长操作或资源竞争 |
| 分片 | 数据倾斜、迁移、广播查询 | 分片键或均衡问题 |

`serverStatus`、`db.stats()`、`collStats`、`db.currentOp()`、`mongostat` 和 `mongotop` 是自管理部署的重要观察入口。参见 [Monitoring a Self-Managed MongoDB Deployment](https://www.mongodb.com/docs/manual/administration/monitoring/)。

```javascript
// 这些命令开销和输出体量不同，生产采集必须控制频率与权限。
db.serverStatus();
db.stats();
db.orders.stats();
db.currentOp({ active: true });
```

不要高频采集体量巨大的诊断结果，也不要在没有筛选条件时随意终止操作。监控需要建立正常基线，否则单个瞬时值很难解释。

### 12.5 慢查询排查闭环

```mermaid
flowchart TD
    A["接口 P99 升高"] --> B["按时间与操作标识定位查询"]
    B --> C["记录过滤、排序、投影和返回量"]
    C --> D["explain executionStats"]
    D --> E{"扫描与返回比是否异常？"}
    E -->|是| F["调整查询、模型或索引"]
    E -->|否| G["检查锁、磁盘、缓存、网络、复制延迟"]
    F --> H["接近生产数据回归测试"]
    G --> H
    H --> I["灰度上线并观察 P95/P99 与写入代价"]
```

排查时必须保留原查询形状、参数分布、数据量、索引列表和执行计划。只在测试库用几十条数据执行成功，不能证明生产性能。

### 12.6 常见故障现象

| 现象 | 可能的知识缺口 | 排查入口 | 正确方向 |
| --- | --- | --- | --- |
| 本地能连，部署后超时 | 网络、DNS、TLS、访问列表 | 连接串、端口、证书、节点解析 | 验证每个副本集成员可达 |
| 查询返回空但数据存在 | BSON 类型不一致 | `$type`、样本文档 | 对齐 ObjectId、Date、Decimal 类型 |
| 更新没有报错但没变化 | 未检查影响条数 | `matchedCount`、`modifiedCount` | 把成功判据写进业务代码 |
| 主从切换后大量失败 | 固定单节点地址或超时不合理 | 连接串、驱动拓扑日志 | 使用副本集连接串并测试切换 |
| 磁盘增长远超数据量 | 索引过多或模型膨胀 | `db.collection.stats()`、索引列表 | 治理索引和无界数组 |
| Secondary 越来越慢 | 复制应用或磁盘瓶颈 | 复制延迟、Oplog 窗口 | 优化慢操作并扩容资源 |
| 分片后性能反而下降 | 广播查询或数据倾斜 | 查询路由、分片分布 | 重新评估分片键与查询模式 |
| 备份存在但无法恢复 | 未做恢复演练 | 恢复日志和校验脚本 | 定期自动恢复验证 |
| 异常关闭后实例无法启动 | 文件损坏、权限或磁盘问题 | 启动日志、磁盘、备份和健康副本 | 先保全现场，再按拓扑恢复，不能直接运行 `--repair` |

### 12.7 容量规划：从负载、工作集和恢复空间推导

容量规划不是“当前数据大小乘一个固定系数”。它要同时回答多久会用完磁盘、热点能否留在内存、峰值吞吐是否满足、故障时是否还有恢复和迁移空间，以及副本和备份需要多少额外资源。

先建立基线：

| 维度 | 至少收集什么 | 为什么重要 |
| --- | --- | --- |
| 数据 | 当前逻辑数据量、平均与 P99 文档大小、日增量、保留期 | 推算正常增长和极端文档 |
| 索引 | 每个索引大小、增长率、命中查询、构建时间 | 索引也会占磁盘、缓存和写入预算 |
| 工作集 | 高频数据和索引、缓存命中、页面读取与驱逐 | 决定稳定延迟是否依赖磁盘 |
| 吞吐 | 读写比例、峰值 QPS、批量大小、聚合和事务比例 | 不能用平均流量选择硬件 |
| 延迟 | P50、P95、P99 和超时率 | 尾延迟比平均值更早暴露饱和 |
| 复制 | Oplog 产生速度、窗口、Secondary 应用速率 | 决定故障成员能否追赶 |
| 运维 | 索引构建、备份、恢复、均衡和升级所需临时空间 | 正常运行空间不等于可维护空间 |

单个数据承载成员的粗略磁盘下限可以先按下面的关系检查：

```text
成员所需可用容量
≈ 数据
 + 索引
 + Oplog
 + 临时文件与索引构建空间
 + 预测增长
 + 故障和迁移余量
```

若希望正常状态磁盘使用率不超过 70%，上面总量还要除以 `0.70`。例如预计一年后数据 2 TB、索引 0.6 TB、Oplog 与临时空间 0.2 TB、维护余量 0.4 TB：

```text
(2 + 0.6 + 0.2 + 0.4) / 0.70 ≈ 4.57 TB
```

这表示每个保存完整数据的副本集成员都应具备约 4.57 TB 的可用规划容量；三数据成员不是共同分享这一份数据，而是各自保存副本。备份、快照保留和隔离恢复环境还要单独计算，不能算进副本集成员的可用空间。

内存不能只看数据总量。更有意义的是热点工作集：高频访问的数据页、索引页和并发操作状态是否能在 WiredTiger Cache 与操作系统缓存的共同预算内稳定运行。工作集超过内存不一定立即失败，但会增加页面读取、驱逐和磁盘延迟。必须用接近真实的键分布与请求比例压测，而不是假设“内存等于数据量的 60%”适用于所有系统。

评估分片数量时，可以分别得到三个候选下限：

1\. 容量下限：预测数据与索引除以单个 Shard 在目标利用率下的可用容量。

2\. 工作集下限：热点数据与索引除以单个 Shard 可稳定承载的缓存预算。

3\. 吞吐下限：峰值目标吞吐除以压测得到的单个 Shard 安全吞吐。

初始 Shard 数量至少不能低于三者中的最大值，但这个结果仍不是最终答案。还要考虑分片键能否真正分散写入、查询是否定向、最大租户能否拆分、故障后剩余容量、Balancer 迁移时间和未来扩容步长。若压测数据分布与生产不同，公式会给出精确但错误的数字。

容量告警应至少覆盖增长趋势、预测耗尽时间、磁盘延迟、缓存驱逐、Oplog 窗口、复制延迟、连接池等待和分片倾斜。扩容触发条件要早于资源耗尽，因为新增节点、初始同步、索引构建和数据均衡都需要时间和额外空间。

### 12.8 数据损坏与 `mongod --repair`：最后手段而非常规恢复

异常关机后，启用 Journal 的 WiredTiger 实例通常会在重新启动时自动恢复检查点之后的日志。看到非正常关闭记录不等于必须执行 Repair（修复），更不应先删除 `mongod.lock` 或修改数据文件。

```mermaid
flowchart TD
    A["实例异常关闭或数据校验失败"] --> B["停止写入并保全日志、配置和磁盘现场"]
    B --> C{"是否有健康副本或可验证备份？"}
    C -->|"有健康副本"| D["副本集成员重新同步"]
    C -->|"有备份"| E["在隔离环境恢复并校验"]
    C -->|"都没有"| F{"是否为独立实例且确认磁盘级损坏？"}
    F -->|"否"| G["先修复权限、空间、配置或启动参数"]
    F -->|"是，且无其他选择"| H["复制 dbPath 后评估 mongod --repair"]
    H --> I["校验数据、索引和业务不变量"]
    I --> J["立即建立新的可恢复备份"]
```

先按拓扑选择恢复方式：

1\. 副本集某个 Secondary 损坏，但其他成员健康：停止损坏成员，保留诊断证据，从健康成员重新初始同步。不要为了保住一个可替代副本而冒险原地 Repair。

2\. Primary 所在磁盘损坏，但副本集仍有多数：让副本集选举新 Primary，再重建损坏成员；先确认客户端连接串和写关注没有绕过副本集。

3\. 独立实例无法启动，但存在可验证备份：优先在新目录或新实例恢复备份，验证集合、索引、权限和应用连接后再决定切换。

4\. 独立实例没有健康副本或可用备份，并且日志确认是磁盘级文件损坏：`mongod --repair` 才进入候选。

在实例仍可启动、且维护窗口允许时，可以对明确集合执行校验：

```javascript
// full 校验可能产生大量 I/O，只对明确集合在维护窗口执行。
db.runCommand({
  validate: "orders",
  full: true
});
```

完整校验可能消耗大量 I/O 和时间，不能在生产高峰对所有集合无差别执行。返回 `ok: 1` 只说明命令执行完成，还要检查 `valid`、警告、错误以及业务数量和索引约束。

如果最终只能 Repair，必须先停止实例、复制整个 `dbPath`、确认副本可回退，并使用平时运行 `mongod` 的同一操作系统用户执行：

```bash
# 最后手段：执行前必须停止实例并复制、核对整个 dbPath。
mongod \
  --dbpath /srv/mongodb \
  --repair
```

`/srv/mongodb` 只是明确示例路径，执行前必须替换为已经核对的目标目录。Repair 会尝试修复不一致和重建必要索引，但会直接丢弃无法恢复的损坏数据；它不是无损操作。对副本集成员执行 Repair 后，如果数据或元数据被修改，成员仍需要完整重新同步才能重新加入。

Repair 完成后至少要执行：

1\. 查看完整启动和 Repair 日志，记录被丢弃或重建的对象。

2\. 校验关键集合、索引、文档数量和业务不变量。

3\. 用应用只读流量验证 BSON 类型、查询和权限。

4\. 立即生成新的可恢复备份并完成隔离恢复抽样。

5\. 查明磁盘、文件系统、内存、虚拟化或操作流程的根因，避免把 Repair 当成周期性维护。

MongoDB 官方明确说明 `mongod --repair` 只应在没有其他选择时使用，并且会删除而不保存损坏数据。具体操作必须按目标版本的 [Recover a Self-Managed Standalone after Unexpected Shutdown](https://www.mongodb.com/docs/manual/tutorial/recover-data-following-unexpected-shutdown/) 执行。

### 12.9 生产与进阶阶段再建立工具全景

完成基础 CRUD 和 Java 接入后，再按任务理解下面的工具边界。零基础阶段只需准备 Docker 和 `mongosh`，需要图形浏览时再安装 Compass；备份、迁移、监控和集群管理工具不是第一次读写数据的前置条件。

MongoDB 的工具不是功能重复的“不同界面”，而是分别作用于数据、部署、迁移、诊断和应用开发。选错工具最常见的后果，是把导出文件当成可恢复备份、把瞬时诊断命令当成监控系统，或者用图形界面直接修改生产数据却没有审计与回滚方案。

#### 12.9.1 先按任务选择工具

| 任务 | 首选工具 | 为什么选它 | 不应把它当成什么 |
| --- | --- | --- | --- |
| 交互式学习、CRUD、聚合、管理命令 | `mongosh` | 直接执行 MongoDB 命令，结果最接近驱动实际发送的操作，适合复现与脚本化 | 数据库服务端、长期监控平台 |
| 可视化浏览文档、分析字段类型、查看查询计划 | MongoDB Compass | GUI（Graphical User Interface，图形用户界面）降低探索门槛，可抽样分析模式并可视化 Explain Plan（执行计划） | 无人值守部署工具、生产变更审批系统 |
| Atlas 集群、项目、用户和 Search 等资源管理 | Atlas UI 或 Atlas CLI（Command-Line Interface，命令行界面） | UI 适合人工操作，CLI 适合可重复脚本和 CI/CD（Continuous Integration/Continuous Delivery，持续集成/持续交付） | 用于查询业务文档的 Shell |
| Java 应用读写数据 | MongoDB Java Driver | 官方驱动负责连接发现、连接池、BSON 编解码、重试与命令发送 | 临时人工查询工具 |
| Spring 应用的数据访问抽象 | Spring Data MongoDB | 提供 Repository、`MongoTemplate`、对象映射和 Spring 事务集成 | MongoDB 驱动的替代品；其底层仍使用驱动 |
| 逻辑备份与恢复、环境间复制一批 MongoDB 数据 | `mongodump`、`mongorestore` | 保留 BSON 类型和集合元数据，恢复端与导出格式配套 | 大规模生产系统唯一的灾难恢复方案 |
| 与其他系统交换 JSON、CSV 或 TSV 数据 | `mongoexport`、`mongoimport` | 使用人类和其他系统易处理的文本格式 | 完整保真备份；它们不能完整保留全部 BSON 类型和数据库元数据 |
| 检查 Dump 中的 BSON 内容 | `bsondump` | 把 BSON Dump 转成可读 JSON，适合排查和抽样验证 | 恢复工具、通用数据库导出方案 |
| 操作 GridFS 中的文件 | `mongofiles` | 提供命令行上传、下载、列出和删除 GridFS 对象的能力 | 通用对象存储客户端；GridFS 的边界见第 13.4 节 |
| 两个 MongoDB 集群间低停机的一次性在线迁移 | `mongosync` | 初始复制后继续应用源端变化，直至提交切换 | 持续灾备复制、分析副本或备份系统 |
| 从关系型数据库重塑模型并迁入 MongoDB | MongoDB Relational Migrator | 可把表和外键映射为集合、嵌入文档或引用，再执行迁移 | 无需建模和校验的“一键转换器” |
| 本地开发环境 | Docker | 快速得到版本固定、可销毁的 MongoDB 实例 | 生产高可用架构本身 |
| Java 自动化集成测试 | Testcontainers MongoDB Module | 测试进程按需启动隔离容器，可获得动态连接地址并测试真实驱动行为 | Mockito 一类纯单元测试替代品、完整多节点生产集群 |
| Atlas 持续监控与告警 | Atlas Metrics、Performance Advisor、Query Profiler 和 Alerts | 持续保存指标、分析慢查询并通知责任人 | 只在故障时临时执行的命令 |
| 自管理 Enterprise 部署的集中运维 | MongoDB Ops Manager | 提供自动化、监控、告警和备份能力 | Community Edition（社区版）自带的免费本地命令 |
| 自管理实例的即时观察 | `mongostat`、`mongotop` | 快速查看实例活动和命名空间读写耗时 | 有历史留存、告警和容量趋势的完整可观测平台 |
| 单条查询或当前故障深挖 | `explain()`、Profiler、日志、`db.currentOp()` | 分别回答执行计划、慢操作记录、事件细节和当前操作的问题 | 可相互完全替代的一组工具 |

MongoDB Database Tools（数据库工具）是独立于 MongoDB Server 发布并单独编号的工具集合，其中包括 `mongodump`、`mongorestore`、`mongoexport`、`mongoimport`、`bsondump`、`mongostat`、`mongotop` 和 `mongofiles`。安装了 `mongod` 或 `mongosh`，不代表这些命令一定已经安装；选版时应查官方兼容矩阵，而不是假设工具版本必须与服务端版本号相同。参见 [MongoDB Database Tools](https://www.mongodb.com/docs/database-tools/)。

#### 12.9.2 Shell、Compass 与 Atlas CLI 的边界

这三个工具都能“连接 MongoDB”，但控制对象不同：

| 问题 | 应选工具 | 成功判据 |
| --- | --- | --- |
| “这条过滤、更新或聚合到底会得到什么？” | `mongosh` | 命令结果、文档计数或 `explain()` 与预期一致 |
| “集合里有哪些字段类型，查询计划能否直观看懂？” | Compass | Schema 抽样结果和 Explain Plan 中的扫描量得到确认 |
| “如何创建 Atlas 集群、配置项目资源并把步骤自动化？” | Atlas CLI | 命令返回目标资源，随后通过 Atlas API、CLI 或 UI 再次查询确认 |
| “Java 服务在并发和故障下如何访问数据库？” | Java Driver 或 Spring Data MongoDB | 自动化测试证明连接池、超时、重试和业务语义正确 |

Compass 的 Schema 视图基于样本文档分析字段分布，不等于强制模式，也不能证明全集中不存在异常类型。Compass 的 Explain Plan 适合可视化学习，生产优化仍应保存查询形状、关键执行统计和前后对比，详见第 7.6 节。官方说明见 [Analyze Your Data Schema](https://www.mongodb.com/docs/compass/schema/) 与 [View Query Performance](https://www.mongodb.com/docs/compass/query-plan/)。

Atlas CLI 管理的是 Atlas 资源，`mongosh` 操作的是连接目标中的数据库数据。前者适合把环境创建和配置纳入自动化，后者适合验证数据库命令；二者可能在一个流程中连续出现，但不能互换。参见 [Atlas CLI](https://www.mongodb.com/docs/atlas/cli/current/)。

#### 12.9.3 Compass、编辑器插件与第三方客户端如何选择

若问题只是“初学 MongoDB 应安装哪个图形工具”，默认从官方 Compass 开始。它与 MongoDB 文档和概念最接近，能够浏览文档、构建查询、分析模式、管理索引并查看执行计划。团队已经长期使用某个编辑器或多数据库 IDE（Integrated Development Environment，集成开发环境）时，再按工作流选择其他客户端：

| 客户端 | 更适合谁 | 主要价值 | 选择前必须核对 |
| --- | --- | --- | --- |
| MongoDB Compass | 初学者、MongoDB 专项开发与查询优化 | 官方 GUI，模式抽样、聚合构建、索引和可视化执行计划集中在一个工具中 | Compass 版本与目标部署兼容性、生产连接权限 |
| MongoDB for VS Code | 主要在 Visual Studio Code 中开发的团队 | 在编辑器中浏览数据，使用 Playground（演练场脚本）原型化查询和聚合，并把 `.mongodb.js` 文件随项目保存 | Playground 是开发原型，不应绕过应用测试、审批和迁移流程直接改生产 |
| JetBrains DataGrip 或 JetBrains IDE 数据库工具 | 同时操作 MongoDB 与多种关系型数据库的团队 | 统一的数据源管理、控制台、补全、数据编辑和多数据库工作区 | 支持的 MongoDB 版本、驱动版本、授权方式以及凭据保存策略 |
| Studio 3T | 需要 MongoDB 专项的可视化查询、数据比较或 SQL 与 MongoDB 迁移工作流的团队 | 面向 MongoDB 的查询、比较同步和映射类能力较集中 | 所需功能属于哪个版本、商业授权、目标版本兼容性和同步操作的回滚风险 |
| Navicat for MongoDB 或 Navicat Premium | 已统一使用 Navicat 管理多类数据库的团队 | 复用现有连接管理和数据操作习惯 | 版本支持、商业授权、MongoDB 专项能力是否满足需求 |

MongoDB for VS Code 的 Playground 是带 MongoDB API 自动补全的 JavaScript 环境，适合保存可复查的查询原型；业务运行时仍应使用官方驱动。参见 [MongoDB Playgrounds](https://www.mongodb.com/docs/mongodb-vscode/playgrounds/)。

DataGrip、Studio 3T 与 Navicat 都属于可选的第三方客户端，不是学习 MongoDB 的前置条件。选型不要只比较界面，而应实际验证目标认证机制、TLS、Atlas 或自管理拓扑、BSON 类型显示、Explain Plan、只读模式、凭据存储、审计、批量修改确认和误操作恢复。版本、功能分级与许可会变化，采购前应查看供应商当前文档，并用非生产数据完成试用。

无论使用哪种 GUI，都必须保留对原始 MQL（MongoDB Query Language，MongoDB 查询语言）和执行结果的理解。可视化查询构建器生成的过滤或聚合应能导出、审查和复现；“界面显示成功”仍要通过影响条数、最终数据或执行计划验证。生产连接建议默认只读，写操作使用独立身份和审批窗口。

#### 12.9.4 备份、文本交换与在线迁移不能混用

```mermaid
flowchart TD
    A["要移动或保护数据"] --> B{"目标是什么？"}
    B -->|"可恢复的 MongoDB 逻辑副本"| C["mongodump + mongorestore"]
    B -->|"与外部系统交换 JSON/CSV/TSV"| D["mongoexport + mongoimport"]
    B -->|"两个 MongoDB 集群低停机切换"| E["mongosync"]
    B -->|"关系型数据库迁入并重塑文档模型"| F["Relational Migrator"]
    B -->|"生产级时间点恢复与灾难恢复"| G["Atlas Cloud Backup、Ops Manager 或经过验证的快照方案"]
    C --> H["执行恢复演练并校验数据、索引和权限"]
    D --> I["显式约定类型、编码、分隔符与空值"]
    E --> J["核对支持矩阵、Oplog 窗口、验证和切换步骤"]
    F --> K["评审目标模型并做行数、校验和与业务抽样"]
    G --> L["验证 RPO、RTO、保留期和跨故障域恢复"]
```

RPO（Recovery Point Objective，恢复点目标）回答最多允许丢失多少时间的数据，RTO（Recovery Time Objective，恢复时间目标）回答故障后多久必须恢复服务。工具名称不能替代这两个目标和恢复演练。

`mongodump` 与 `mongorestore` 更接近“MongoDB 到 MongoDB 的逻辑备份恢复”；`mongoexport` 与 `mongoimport` 更接近“MongoDB 与文本文件之间的数据交换”。JSON 或 CSV 导出可能丢失类型表达、索引、验证规则、用户角色等信息，所以不能把“文件能打开”当成“数据库可恢复”。第 12.3 节给出备份与恢复命令及生产边界。

`mongosync` 用于一次性集群迁移，可以在复制数据期间继续跟进源端写入，但只有完成提交且目标端报告可写后才能安全切流。它不会同步用户和角色，也不支持用目标集群长期承担 Disaster Recovery（灾难恢复）或分析副本职责。工具与 Server 的支持组合会变化；例如当前 MongoDB 8.2 发行说明已明确警告 `mongosync` 不支持 MongoDB 8.3。迁移前要核对当日的拓扑、功能、认证与版本支持矩阵，保证源端 Oplog 窗口覆盖迁移时间，并对数据执行独立验证。参见 [About `mongosync`](https://www.mongodb.com/docs/mongosync/current/about-mongosync/) 与 [MongoDB 8.2 Release Notes](https://www.mongodb.com/docs/manual/release-notes/8.2/)。

Relational Migrator 解决的是关系模型到文档模型的迁移，不只是复制字段。工具给出的映射仍需根据访问模式评审；迁移任务还要验证主键映射、外键嵌入、空值、时间与金额类型、重复执行语义和失败后的清理方式。正式使用前必须阅读当前发行说明，因为迁移能力、限制和数据完整性公告可能随版本变化。参见 [Relational Migrator](https://www.mongodb.com/docs/relational-migrator/)。

#### 12.9.5 监控工具如何分层

一次性命令、持续指标和慢查询证据回答的是不同问题：

| 层级 | 典型工具 | 回答的问题 | 使用边界 |
| --- | --- | --- | --- |
| 持续监控与告警 | Atlas Monitoring、Ops Manager 或组织统一监控平台 | 延迟、吞吐、连接、缓存、复制延迟和磁盘趋势是否越界 | 必须设置负责人、阈值、通知通道和处置手册 |
| 实例即时状态 | `mongostat` | 当前每秒操作、队列、连接和资源概况是否异常 | 适合快速观察，不保留完整历史 |
| 命名空间即时热点 | `mongotop` | 哪些数据库或集合消耗了较多读写时间 | 不能直接说明某条查询为什么慢 |
| 当前操作 | `db.currentOp()` | 此刻有哪些长时间或阻塞操作 | 结果是瞬时快照，使用和终止操作都需要相应权限 |
| 单条查询计划 | `explain("executionStats")` | 是否使用索引、扫描多少键和文档、返回多少文档 | 执行统计受当时数据与缓存影响，不能只看阶段名称 |
| 慢操作取证 | Database Profiler、Query Profiler 和日志 | 哪些查询长期或反复变慢 | Profiler 有额外开销，生产启用范围和阈值需受控 |

Atlas 已提供指标、告警、日志、Performance Advisor（性能顾问）、Query Profiler（查询分析器）和实时性能面板；自管理 Enterprise 环境可评估 Ops Manager。`mongostat` 与 `mongotop` 适合现场诊断，但不能替代趋势留存和告警。完整排查闭环见第 12.4、12.5 节，Atlas 能力见 [Monitor Your Clusters](https://www.mongodb.com/docs/atlas/monitoring-alerts/)。

#### 12.9.6 Java 学习与项目落地的最小工具组合

| 场景 | 建议组合 | 暂时不必引入 |
| --- | --- | --- |
| 零基础学习 | Docker、`mongosh`、Compass、固定版本的 MongoDB 镜像 | 分片集群、Ops Manager、在线迁移工具 |
| 原生 Java 项目 | 上述工具加 MongoDB Java Driver、Maven、JUnit、Testcontainers | 未使用 Spring 时不要为了 Repository 抽象引入 Spring Data |
| Spring Boot 项目 | 上述工具加 Spring Data MongoDB、Spring Boot Test、Testcontainers | 不要同时维护两套无必要的数据访问封装 |
| Atlas 生产项目 | Java 访问栈、Atlas UI/CLI、Atlas Monitoring 与 Alerts、备份恢复方案、Database Tools | 不要把本地 Docker 实例当成生产拓扑证明 |
| 自管理生产项目 | Java 访问栈、集中监控告警、备份平台、Database Tools；Enterprise 团队可评估 Ops Manager | 不要只靠 `mongostat`、手工 Dump 和个人脚本值守 |
| 数据迁移项目 | 源目标兼容性检查、对应迁移工具、容量与 Oplog 评估、校验程序、切换和回滚手册 | 未完成验证前不要删除或开放目标数据 |

Testcontainers 适合证明真实驱动与数据库的集成行为。测试事务和 Change Streams 时要启用副本集能力；单节点容器可以覆盖许多语义，但不能证明多节点选举、网络分区和复制延迟。多层测试边界见第 8.12 和 15.2 节，模块用法见 [Testcontainers MongoDB Module](https://java.testcontainers.org/modules/databases/mongodb/)。

#### 12.9.7 安装验证与生产安全边界

安装后先证明“命令存在且版本可见”，再验证连接：

```bash
# 先确认各工具是否已安装；版本可见不代表已经连接成功。
mongosh --version
mongodump --version
mongoexport --version
atlas --version
docker --version
```

某条命令不存在时，应安装它所属的独立软件包，而不是反复重装 MongoDB Server。版本命令成功只证明本机可执行文件存在，不证明它与目标 MongoDB 版本、操作系统和认证方式兼容；下一步还要查看官方兼容矩阵，并在非生产环境完成最小读写、导出或恢复验证。

生产连接必须使用最小权限账号。Compass 建议使用只读角色和 Read-Only Mode（只读模式）完成浏览分析；需要写入时再通过受控账号和变更流程授权。`mongosh`、Atlas CLI 和 Database Tools 不要把明文密码直接写进命令、脚本、Shell 历史或版本库，应使用交互式提示、受控配置文件或 Secret Manager（密钥管理服务）。JSON、CSV、BSON Dump 和诊断包都可能包含敏感数据，必须设置访问控制、加密、保留期和销毁流程。

最后用一个原则检查选型：工具输出必须有验证动作。执行查询要核对结果与执行计划，执行导出要做类型与数量校验，执行备份要做恢复演练，执行迁移要做源目标一致性校验，启用监控要触发一次测试告警。只有“命令成功”而没有业务和恢复判据，不算完成。

### 12.10 WiredTiger、Journal 与 Checkpoint 的持久化链路

这一节解释单节点内部如何把写入落到稳定存储，属于生产原理，不是第一次 CRUD 的前置知识。

MongoDB 的“写入成功”不是简单地把一个 JSON 文件写到磁盘。自管理 MongoDB 的默认存储引擎是 WiredTiger：它管理内存中的页面、并发控制、压缩和磁盘文件；Journal（预写日志）记录尚未进入稳定检查点的数据变化；Checkpoint（检查点）周期性形成一致的磁盘数据视图。

```mermaid
flowchart LR
    A["应用写请求"] --> B["mongod 执行并修改 WiredTiger Cache"]
    B --> C["Journal：记录可恢复变化"]
    B --> D["Checkpoint：形成一致磁盘快照"]
    C --> E["异常重启时重放检查点之后的日志"]
    D --> F["集合与索引数据文件"]
    E --> F
```

WiredTiger Cache（缓存）与操作系统文件缓存共同参与读写。热点工作集能留在内存时，查询通常更稳定；工作集明显大于可用内存时，页面读取与驱逐会放大磁盘延迟。集合与索引压缩可以降低磁盘和 I/O（Input/Output，输入输出）消耗，但也会使用 CPU（Central Processing Unit，中央处理器）完成压缩与解压。

Journal 与 Checkpoint 解决单节点进程崩溃后的恢复，不等于副本集冗余，更不等于历史备份。客户端何时收到确认由 Write Concern（写关注）决定；看到 `acknowledged: true` 也不能脱离 `w`、`j`、副本集状态和操作是否可重试来推断持久性。

在有监控权限的自管理测试环境中，可以查看实际引擎和缓存状态：

```javascript
// serverStatus() 需要相应监控权限，只应在受控环境按需执行。
var server = db.serverStatus();
server.storageEngine;
server.wiredTiger.cache;
server.wiredTiger.log;
```

预期 `storageEngine.name` 为 `wiredTiger`，其余结果包含缓存使用、页面读写、驱逐和日志统计。字段会随版本变化，监控系统应提取已核对的指标，不能依赖整份输出的固定结构。恢复机制参见 [WiredTiger Storage Engine](https://www.mongodb.com/docs/manual/core/wiredtiger/) 与 [Journaling](https://www.mongodb.com/docs/manual/core/journaling/)。

## 13 进阶能力与适用边界

### 13.1 Change Streams

Change Streams（变更流）让应用订阅集合、数据库或部署中的数据变化，常用于缓存失效、搜索索引同步和事件驱动集成。它依赖复制日志语义，消费方必须保存 Resume Token（恢复令牌）、处理重复事件并设计断线恢复。

变更流需要副本集或分片集群，不能在第 2 章的单机容器上验证。先在一个测试副本集会话打开游标：

```javascript
// 打开变更流后，hasNext() 会阻塞等待符合过滤条件的事件。
var changeStream = db.products.watch(
  [
    {
      $match: {
        operationType: {
          $in: ["insert", "update", "replace", "delete"]
        }
      }
    }
  ],
  {
    fullDocument: "updateLookup"
  }
);

changeStream.hasNext();
var event = changeStream.next();
event;
// Resume Token 必须在幂等处理成功后持久化。
var resumeToken = event._id;
```

`hasNext()` 会等待事件。在另一个会话更新一件商品后，`next()` 返回包含 `operationType`、`documentKey`、`clusterTime`、`updateDescription` 和 Resume Token 的事件。`fullDocument: "updateLookup"` 会额外查询更新后的当前文档；如果事件产生后文档又被修改，查到的内容不一定是该事件发生瞬间的精确后镜像。需要精确 Pre/Post Image（前/后镜像）时，必须在受支持版本上显式启用并评估额外存储。

断线后可以用已持久化的令牌继续：

```javascript
// 使用已持久化的 Resume Token 从断点继续消费。
var resumedStream = db.products.watch(
  [],
  {
    resumeAfter: resumeToken,
    fullDocument: "updateLookup"
  }
);
```

处理顺序应设计为“幂等处理事件，再持久化对应 Resume Token”。若在业务副作用之前保存令牌，崩溃可能丢事件；若在副作用之后保存，崩溃可能重复处理，因此消费方仍需按事件标识或业务键幂等。令牌对应的历史若已离开 Oplog 窗口，恢复会失败，系统必须能从全量数据重建派生状态。

集合被删除或重命名可能产生 `invalidate` 并关闭流；`resumeAfter` 不能越过该事件，新流需要按官方语义评估 `startAfter`。变更流不是天然“恰好一次”消息队列，也不自带死信队列、消费组再均衡和无限保留。若消费方更新外部系统，需要失败重试、死信治理、消费延迟和 Oplog 窗口告警。参见 [Change Streams](https://www.mongodb.com/docs/manual/changestreams/)。

### 13.2 TTL 与生命周期数据

TTL（Time To Live，生存时间）索引由后台任务删除过期文档，适合会话、临时令牌和可自动清理的事件数据。它是生命周期回收机制，不是准点调度器。

```javascript
// expireAfterSeconds: 0 表示按每条文档的 expireAt 绝对时间过期。
db.sessions.createIndex(
  { expireAt: 1 },
  {
    name: "ttl_sessions_expire_at",
    expireAfterSeconds: 0
  }
);

db.sessions.insertOne({
  tokenHash: "sha256-placeholder",
  expireAt: new Date(Date.now() + 10 * 60 * 1000)
});

db.sessions.getIndexes();
db.sessions.find({
  tokenHash: "sha256-placeholder"
});
```

`expireAfterSeconds: 0` 表示按文档中的绝对日期过期。TTL 索引是单字段索引，不能把复合索引直接声明为 TTL；字段必须包含 BSON Date 或日期数组，其他类型不会按预期过期。删除由 Primary 的后台机制触发并复制，负载高时存在延迟，因此认证请求仍必须在应用层检查 `expireAt`。

验证时先确认索引定义，再观察文档最终被删除；不要把“到点后一秒仍存在”当成立即故障。大批文档同时到期可能形成删除和复制峰值，应打散到期时间、监控删除量并评估 Partial TTL Index（部分 TTL 索引）。参见 [TTL Indexes](https://www.mongodb.com/docs/manual/core/index-ttl/)。

### 13.3 Time Series Collections

Time Series Collection（时序集合）适合设备指标、传感器读数等“按时间不断追加、按元数据和时间范围查询”的测量数据。MongoDB 在内部把测量值组织成压缩 Bucket（桶）；应用仍通过逻辑时序集合读写，不应直接修改内部 Bucket 集合。

```javascript
// timeField 必须是 BSON Date，metaField 应保持稳定以利于内部分桶。
db.createCollection("deviceMetrics", {
  timeseries: {
    timeField: "observedAt",
    metaField: "device",
    granularity: "minutes"
  },
  expireAfterSeconds: 2592000
});

db.deviceMetrics.insertMany([
  {
    observedAt: new Date(),
    device: {
      id: "sensor-001",
      site: "warehouse-a"
    },
    temperatureCelsius: 24.8
  },
  {
    observedAt: new Date(),
    device: {
      id: "sensor-002",
      site: "warehouse-a"
    },
    temperatureCelsius: 25.1
  }
]);

db.deviceMetrics.createIndex({
  "device.id": 1,
  observedAt: -1
});

db.deviceMetrics.find({
  "device.id": "sensor-001",
  observedAt: {
    $gte: new Date(Date.now() - 60 * 60 * 1000)
  }
}).sort({
  observedAt: -1
});
```

`timeField` 是每条测量的 BSON Date，`metaField` 用于组织同一数据源且应尽量稳定，测量值放在其他字段。`granularity` 应接近同一数据源的采样间隔：过细会产生更多 Bucket，过粗会让短时间查询读取过大的时间跨度。高基数且频繁变化的元数据、严重乱序写入和不匹配的保留期都会降低压缩与查询效率。

预期查询返回 `sensor-001` 最近一小时的记录。上线前还要验证目标版本对更新、删除、分片、索引和 TTL 的限制，因为时序集合并非普通集合的完全等价替代。参见 [Time Series Collections Considerations](https://www.mongodb.com/docs/manual/core/timeseries/timeseries-considerations/)。

### 13.4 GridFS

GridFS 把超过 BSON 文档上限的文件拆成多个块，并保存文件元数据。它适合需要由 MongoDB 一并管理文件和元数据的场景；大规模静态文件、视频和 CDN（Content Delivery Network，内容分发网络）分发通常更适合对象存储。

默认 Bucket 使用 `fs.files` 保存文件元数据，使用 `fs.chunks` 保存二进制块；这里的 Chunk 与第 11 章的分片 Chunk 不是同一个概念。下面是第 8.4 节代码的增量片段，复用已经创建的 `database`，并通过流上传和下载，避免把整个大文件读入堆内存：

```java
// 复用已有 database，通过流传输文件，避免一次性占用大量堆内存。
import com.mongodb.WriteConcern;
import com.mongodb.client.gridfs.GridFSBucket;
import com.mongodb.client.gridfs.GridFSBuckets;
import com.mongodb.client.gridfs.model.GridFSUploadOptions;
import org.bson.Document;
import org.bson.types.ObjectId;

import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

MongoDatabase gridFsDatabase =
        // 多数写关注降低选举期间上传部分块丢失的风险。
        database.withWriteConcern(WriteConcern.MAJORITY);
GridFSBucket assets = GridFSBuckets.create(
        gridFsDatabase,
        "assets"
);

GridFSUploadOptions uploadOptions = new GridFSUploadOptions()
        .metadata(new Document()
                .append("contentType", "application/pdf")
                .append("businessType", "product-manual"));

ObjectId fileId;
try (InputStream input = Files.newInputStream(
        Path.of("/path/to/product-manual.pdf"))) {
    // uploadFromStream 返回的 fileId 用于后续下载和元数据查询。
    fileId = assets.uploadFromStream(
            "product-manual.pdf",
            input,
            uploadOptions
    );
}

try (OutputStream output = Files.newOutputStream(
        Path.of("/path/to/downloaded-manual.pdf"))) {
    assets.downloadToStream(fileId, output);
}
```

上传成功后 `fileId` 标识 `assets.files` 中的元数据，关联块位于 `assets.chunks`。应用应验证文件长度、业务元数据和自定义安全摘要；GridFS 中历史 MD5 字段不应作为新的安全校验设计。中断的上传可能留下孤立块，GridFS 也不支持多文档事务，因此必须有失败清理、重试和完整性审计。

官方 Java 驱动建议 GridFS 写入使用多数写关注，以降低选举期间上传被中断并丢失部分块的风险。大文件下载还要配置客户端总体超时、流式响应和限速。参见 [Java Driver GridFS](https://www.mongodb.com/docs/drivers/java/sync/current/crud/gridfs/)。

### 13.5 搜索与向量能力

基础 Text Index（文本索引）适合简单词项搜索。一个集合最多只能有一个文本索引，但该索引可以覆盖多个字段：

```javascript
// 一个文本索引可覆盖多个字段，weights 控制各字段对相关性得分的贡献。
db.products.createIndex(
  {
    name: "text",
    tags: "text"
  },
  {
    name: "idx_products_text",
    weights: {
      name: 10,
      tags: 2
    }
  }
);

db.products.find(
  {
    $text: {
      $search: "wireless"
    },
    isDeleted: false
  },
  {
    _id: 0,
    sku: 1,
    name: 1,
    score: {
      $meta: "textScore"
    }
  }
).sort({
  score: {
    $meta: "textScore"
  }
});
```

`weights` 让名称匹配比标签匹配贡献更高分。两个教程商品的标签都包含 `wireless`，但第 4.7 节已软删除鼠标，因此活跃条件下预期只返回键盘。验证时还要使用真实中文分词、同义词、拼写和排序样本；基础文本索引无法自动满足复杂相关性、模糊搜索、多语言分析器和运营调权需求。

MongoDB Search 使用独立的 `mongot` 进程维护搜索索引，并执行 `$search`、`$searchMeta` 和 `$vectorSearch` 等聚合阶段。Atlas 代为管理 `mongot`；当前 Community Edition（社区版）和 MongoDB Enterprise 也可以自管理部署搜索与向量搜索，因此这些能力已不是 Atlas 独有。`mongot` 会通过变更流与 `mongod` 同步数据，并在独立存储中维护索引；生产系统要把索引新鲜度、同步延迟、重建时间、`mongot` 高可用与资源隔离纳入容量和故障演练。

本地评估可使用包含 `mongod` 与 `mongot` 的 `mongodb/mongodb-atlas-local` 镜像，但它是单节点开发环境，不能作为生产高可用证据。自管理部署的 Server、`mongot`、操作系统和 Kubernetes 管理组件具有明确兼容矩阵；升级时应核对目标路径，不应只看 `mongod` 版本。参见 [Self-Managed MongoDB Search and Vector Search](https://www.mongodb.com/docs/search/self-managed/current/) 与 [`mongot` Compatibility and Requirements](https://www.mongodb.com/docs/search/self-managed/current/deployment/compatibility-requirements/)。

Vector Search（向量搜索）通过向量相似度做语义召回。向量来自 Embedding Model（嵌入模型），模型版本、维度、距离度量、过滤条件和重建流程共同决定结果。向量召回不是生成答案，也不保证事实正确，仍需离线评测 Recall（召回率）、Precision（准确率）和端到端延迟。选型还要比较部署约束、同步成本、搜索质量与团队运维能力。

### 13.6 FCV、Stable API 与升级边界

MongoDB Server 二进制版本、FCV（Feature Compatibility Version，功能兼容版本）和 Stable API（稳定 API）解决三个不同问题。二进制版本决定正在运行的软件；FCV 控制可能产生向旧版本不兼容持久化数据的服务端功能；Stable API 让应用声明使用的命令 API 版本，降低服务端升级导致应用行为不兼容的风险。

```javascript
// 分别查看当前 FCV 和 Stable API 的使用指标，二者含义不同。
db.adminCommand({
  getParameter: 1,
  featureCompatibilityVersion: 1
});

db.runCommand({
  serverStatus: 1
}).metrics.apiVersions;
```

升级不能只替换二进制后立即提升 FCV。正确过程包括：核对支持矩阵和破坏性变更、完成可恢复备份、升级测试环境、滚动升级成员、观察稳定性，再按目标版本文档显式提升 FCV。提升后可能启用旧版本无法理解的数据格式，回退能力会变化，因此不能把 `setFeatureCompatibilityVersion` 当成普通配置开关。

Java 驱动可以在客户端声明 Stable API V1。下面是第 8.4 节客户端创建逻辑的替代片段，复用已经读取的 `uri`：

```java
// Stable API 约束应用命令接口，但不固定查询性能或数据模型。
import com.mongodb.ConnectionString;
import com.mongodb.MongoClientSettings;
import com.mongodb.ServerApi;
import com.mongodb.ServerApiVersion;

ServerApi serverApi = ServerApi.builder()
        .version(ServerApiVersion.V1)
        // 严格模式拒绝不属于已声明 API 的命令。
        .strict(true)
        .deprecationErrors(true)
        .build();

MongoClientSettings settings = MongoClientSettings.builder()
        .applyConnectionString(new ConnectionString(uri))
        .serverApi(serverApi)
        .build();

try (MongoClient stableClient = MongoClients.create(settings)) {
    stableClient.getDatabase("shop")
            .runCommand(new Document("ping", 1));
}
```

`strict(true)` 会拒绝不属于所声明 API 的命令，`deprecationErrors(true)` 会把该 API 中已弃用行为暴露为错误，适合在升级测试中提前发现兼容问题。Stable API 不固定查询性能、数据模型或所有服务端管理命令，也不替代驱动与服务端兼容矩阵。参见 [MongoDB Stable API](https://www.mongodb.com/docs/manual/reference/stable-api/)。

## 14 面试复习：用递归追问检验判断过程

本章用高频追问检查概念边界，不提供需要背诵的话术。复习时应先说明当前条件和判断依据，再给出选择、代价与验证证据；条件改变后，结论也可能改变。

### 14.1 MongoDB 与关系型数据库如何选择

选型应从文档模型、访问模式、事务边界、约束能力和扩展方式展开，“哪个更快”在没有数据分布与查询形状时无法作为选型依据。

若继续讨论 Join，需要比较嵌入、引用、扩展引用、`$lookup` 与预计算的读写代价。若继续讨论反范式一致性，则要能指出权威数据源、单写入口、版本字段、事件更新、幂等、补偿任务和审计修复路径。

### 14.2 MongoDB 如何保证高可用

副本集通过 Primary、Secondary、Oplog、心跳和选举完成在线冗余与故障转移。多数写确认能降低已确认写入回滚的风险，读关注、读路由、网络分区和故障组合仍会影响调用方观察到的结果。

当问题进入故障切换时，判断链应继续到驱动拓扑发现、服务器选择、可重试写入、业务幂等和超时预算，并以主动降级实验中的错误率与恢复时间作为证据。

### 14.3 索引为什么会失效或效果不好

可从复合索引前缀、字段顺序、范围条件、排序、数组多键、低选择性、类型不一致、表达式和查询形状解释。最终使用 `explain("executionStats")` 验证，不用“有索引所以快”作为结论。

索引评审还要说明写放大、内存与磁盘成本、构建与维护时间、查询规划复杂度。这些代价解释了为什么不应对所有字段机械建索引。

### 14.4 单文档原子性与事务如何选择

优先通过数据建模让强一致变化落在一个文档中；只有业务不变量跨多个文档且无法合理合并时，才使用事务。事务需关注会话传递、冲突、超时、重试、未知提交结果和分片代价。

单商品条件扣减可以用单文档原子更新完成；当库存还要与订单、账户等多个文档共同提交时，再比较多文档事务与 Saga（长事务分解与补偿）模式。验证证据分别是原子更新的影响条数，以及故意制造中途失败后的数据不变量。

### 14.5 分片键如何选择

分片键判断需覆盖基数、频率分布、单调性、查询定向、写热点和字段稳定性。范围分片保留范围查询的局部性，哈希分片通常更容易均匀分布写入；选择应由真实键分布、路由结果和压测证据支撑。

### 14.6 读关注、写关注与读偏好的区别

Write Concern 决定写入确认程度，Read Concern 决定可读取的一致性视图，Read Preference 决定从哪里读取。三者组合影响一致性、可用性和延迟。

Secondary 读取的延迟由复制进度、缓存状态、索引、网络路径和负载共同决定，既可能更慢，也可能返回旧数据。是否从 Secondary 读应回到数据新鲜度目标和实测结果。

### 14.7 MongoDB 为什么仍需要模式设计

灵活模式降低演进门槛，不是取消设计。类型漂移会破坏查询、排序、聚合和索引；成熟系统应由领域模型、数据库校验、迁移工具和测试共同治理。

常见数据模式要按访问模式和数据分布选择：类型形状不同看多态模式，动态同类字段看属性模式，冷热数据分离看子集模式，减少重复关联看扩展引用模式，有界分组看桶模式，极少数超大文档看异常值模式，固定槽位看预分配模式，允许误差看近似值模式，重复派生计算看计算模式，层级查询看树形模式，需要保留历史修订看文档版本控制模式。判断时还应区分子集与扩展引用的数据来源、计算值与近似值的精确性、结构版本与业务修订版本的职责，并用真实查询和更新路径证明收益。

### 14.8 备份和副本集有什么区别

副本集提供在线冗余并复制当前变化，误删也会被复制；备份提供历史恢复点。完整判断要包含 RPO、RTO、保留期、故障域和最近一次隔离恢复演练的实测结果。

### 14.9 Java 驱动与 Spring Data 如何选择

Spring Data MongoDB 建立在 MongoDB Java 驱动之上：驱动提供连接、拓扑发现、BSON 编解码和底层数据库 API；Spring Data 增加对象映射、Repository、`MongoTemplate`、异常转换和 Spring 事务集成。简单实体 CRUD 和稳定的方法名查询可优先使用 Repository；复杂过滤、聚合、原子更新和需要检查写结果时更适合 `MongoTemplate`。两者可以在同一项目中按场景组合。

`MongoClient` 内部维护拓扑监控和连接池，频繁创建会带来握手成本和连接风暴，因此应按应用生命周期复用。`@Transactional` 未回滚时，沿调用是否经过 Spring 代理、`MongoTransactionManager` 是否存在、是否复用同一 `MongoDatabaseFactory`、会话是否绑定、部署是否支持事务逐层定位。

### 14.10 `null`、缺失字段与空字符串有什么区别

它们是不同的 BSON 和业务状态，`{ field: null }` 会同时匹配显式 `null` 和缺失字段。只匹配 Null 使用 `$type: 10`，只匹配缺失使用 `$exists: false`。Java 结果不一致时，应检查 POJO 缺省值、包装类型、序列化时是否省略 `null`、历史数据类型漂移和校验器，并用 `$type` 与原始 BSON 样本验证。

### 14.11 多数写返回后为什么 Secondary 仍可能读不到

原因要从 Oplog 持久写入、Secondary 应用数据和读取路由三个阶段分析。MongoDB 8.0 的多数写确认不表示每个 Secondary 已把操作应用到集合；需要跨操作因果顺序时使用因果一致会话、`majority` 读关注与多数写关注。是否读 Primary 则由业务新鲜度目标、复制延迟、索引、降级策略和实测延迟共同决定。

### 14.12 Change Streams 如何避免丢事件和重复消费

判断链包括 Resume Token 的保存时机、Oplog 保留窗口、幂等消费、`invalidate`、派生状态重建和消费延迟监控。先处理副作用再保存令牌可能重复，先保存令牌再处理可能丢失，因此常见设计是允许重复并让业务幂等。它没有天然提供所有消息队列的消费组、长期保留、死信和再均衡语义，是否代替消息系统需要逐项比较需求。

### 14.13 Journal、复制与备份分别解决什么问题

Journal 与 Checkpoint 解决节点异常重启后的本地恢复；副本集解决在线冗余和故障转移；备份保留可恢复的历史点。三者相互补充，任何一个都不能替代另外两个。

升级追问会引出另一组边界：二进制版本决定运行代码，FCV 决定是否启用可能产生不兼容持久化数据的功能，Stable API 约束应用命令接口。三者都要检查，但它们保护的对象不同。

### 14.14 MongoDB 常用工具如何选择

工具应按控制对象分类：`mongosh` 和 Compass 用于交互查询与分析，Java Driver 和 Spring Data MongoDB 用于应用运行时，Atlas CLI 用于 Atlas 资源管理，Database Tools 用于备份恢复、数据交换和即时诊断，持续监控使用 Atlas Monitoring、Ops Manager 或组织统一平台。

JSON 或 CSV 面向文本交换，不完整保留 BSON 类型、索引、验证规则和权限等恢复信息，所以 `mongoexport` 不是备份方案。逻辑备份可使用 `mongodump` 与 `mongorestore`，生产灾难恢复还要根据 RPO、RTO、数据量和部署形态选择云备份或快照，并用恢复演练验证。低停机在线迁移还需持续跟进源端写入；`mongosync` 面向一次性 MongoDB 集群迁移，并不承担持续灾备职责。

GUI 客户端默认可从官方 Compass 开始；需要编辑器内原型可评估 MongoDB for VS Code，多数据库团队可评估 DataGrip、Studio 3T 或 Navicat。判断依据包括目标版本、认证支持、只读控制、凭据存储、审计、批量变更防护、现有工作流和许可成本。

### 14.15 查询形状、选择性和计划缓存有什么关系

查询形状把过滤、排序、投影等结构相同的查询归类；选择性描述条件能把候选数据缩小到什么程度；计划缓存让同一计划缓存查询形状复用获胜计划。三者共同解释“同一条代码为什么换一组参数后突然变慢”。

`explain()` 会绕过已有计划缓存，也不会写入此次获胜计划，因此它不是“查看线上当前缓存计划”的命令。`hint()` 是受控干预，无法修复数据倾斜、错误模型或失效索引；MongoDB 8.0 的 Query Settings 与已弃用 Index Filters 也具有不同边界。

### 14.16 `schemaVersion`、文档版本、计算值和近似值如何区分

`schemaVersion` 表示字段结构版本，用于兼容新旧文档；文档版本控制中的 `revision` 表示同一业务对象的历史修订。计算模式保存可由权威源数据重建的派生结果；近似值模式则明确接受误差以减少写入或计算成本。

陈旧描述更新时间，近似描述算法或持久化结果允许误差。周期计算结果虽然暂时不新鲜，重新从同一源数据计算仍可得到精确值，因此不应把“陈旧”与“近似”当成同一性质。

### 14.17 1000 QPS 需要多少连接，什么时候需要分片

连接数先由吞吐乘平均数据库操作耗时估算平均在途操作，再结合应用实例数、峰值、慢请求、事务、切换和等待时间压测。1000 QPS 不等于 1000 条连接，连接池上限也通常是每个应用实例、每个目标服务端的预算。

是否分片则分别评估容量、工作集和安全吞吐下限，再取不能低于的最大候选值；最终还要证明分片键能够分散写入、查询定向、最大租户可拆分、故障后有余量。固定“每多少 TB 或 QPS 就加一个 Shard”的答案没有真实数据分布支撑。

### 14.18 实例损坏后为什么不能直接执行 `mongod --repair`

启用 Journal 的异常关闭通常会自动恢复；副本集损坏成员优先从健康成员重新同步，有备份则优先隔离恢复。`mongod --repair` 只适合没有健康副本和可用备份的独立实例磁盘级损坏，并且可能直接丢弃损坏数据。

Repair 返回成功之后，还要检查丢弃与重建日志，校验集合、索引和业务不变量，建立新备份并查明硬件或流程根因。副本集成员若被 Repair 修改，仍应完整重新同步。

### 14.19 客户端认证与集群成员认证有什么区别

客户端认证验证应用、管理员或工具身份，RBAC 决定它能执行什么操作；内部成员认证验证 `mongod` 与 `mongos` 是否属于合法副本集或分片集群。创建数据库用户不能替代 Keyfile 或 X.509 成员认证。

Keyfile 是共享密钥形式，轮换、分发和成员身份粒度受限。自管理生产应优先评估基于 TLS 信任链的 X.509，并对证书生命周期、密钥恢复和滚动切换进行演练。

## 15 项目落地模板

### 15.1 需求与模型评审

| 检查项 | 需要产出的证据 |
| --- | --- |
| 核心查询 | 过滤、排序、投影、参数分布、查询形状、频率和延迟目标 |
| 写入路径 | 单文档还是跨文档，幂等键是什么 |
| 数据规模 | 当前量、日增量、保留期、工作集、峰值和一年容量预测 |
| 文档结构 | 嵌入与引用理由、最大文档估算 |
| 类型约束 | ObjectId、Date、Decimal128、枚举规则 |
| 字段状态 | 缺失、`null`、空字符串、零值和默认值的业务语义 |
| 索引 | 每个索引服务哪些查询，写入代价如何 |
| 一致性 | 读写关注、读偏好、事务和补偿策略 |
| 可用性 | 副本集、故障转移和降级方案 |
| 扩展性 | 是否需要分片，分片键验证结果 |
| 生命周期 | TTL、归档、删除、Change Streams 与重建窗口 |
| 合规安全 | 客户端与成员认证、权限、加密、审计、保留和删除要求 |
| 恢复 | 健康副本、备份、损坏成员重建和最后手段的决策路径 |
| 升级兼容 | 服务端、驱动、FCV、Stable API 与回退条件 |

### 15.2 测试分层

1\. 单元测试验证过滤器、更新表达式和业务分支，但不证明 MongoDB 的真实行为。

2\. 集成测试连接与生产主版本相近的真实 MongoDB，验证 BSON 映射、唯一索引、模式校验和原子更新。

3\. 副本集测试验证事务、读写关注、主从切换和重试。

4\. 性能测试使用接近生产的数据量、字段分布、并发和查询比例。

5\. 恢复演练验证备份、索引、权限、应用连接和 RPO/RTO。

6\. 分片演练验证路由、热点、均衡、扩容和广播查询。

### 15.3 上线检查表

1\. MongoDB Server 和驱动版本处于支持范围，兼容矩阵已核对。

2\. 连接串包含正确的副本集或 `mongos` 地址，DNS 和 TLS 已从部署环境验证。

3\. 应用账号遵守最小权限，管理员凭据未进入代码、镜像或日志。

4\. 关键集合有模式校验，历史数据已通过违规扫描。

5\. 核心查询有接近生产数据的 `explain("executionStats")` 证据。

6\. 唯一性依赖唯一索引，不依赖应用层先查再写。

7\. 所有写入检查影响条数，超时重试具备幂等策略。

8\. 多文档事务有会话传递、超时、重试和异常测试。

9\. 连接池、服务器选择、连接和客户端总体超时已配置并压测。

10\. 副本集切换演练通过，应用错误率和恢复时间符合目标。

11\. 容量覆盖数据、索引、Oplog、临时空间和增长余量。

12\. 监控覆盖请求、查询、连接、缓存、磁盘、复制和分片。

13\. 告警包含负责人、阈值理由、处理手册和升级路径。

14\. 备份策略满足 RPO/RTO，最近一次隔离恢复演练成功。

15\. 数据迁移、索引构建和回滚操作都有窗口、进度指标和停止条件。

16\. 缺失字段、`null`、空字符串和默认值的查询与 Java 映射测试已通过。

17\. Change Streams 消费方已验证 Resume Token、重复消费、Oplog 窗口和全量重建。

18\. 服务端升级已核对驱动兼容、FCV、Stable API、备份和回退边界。

19\. TLS、静态加密、字段加密与密钥恢复职责已经明确并演练。

20\. Shell、GUI、驱动、备份、迁移和监控工具的职责已分开，版本兼容、最小权限、审计和验证动作已有记录。

21\. 查询形状、参数分布、选择性和计划缓存风险已有测试证据，生产查询未依赖未经治理的 `hint()`。

22\. 容量规划覆盖一年增长、工作集、每个副本、备份、索引构建、恢复和分片迁移空间，并设置提前量告警。

23\. 自管理副本集或分片集群已启用内部成员认证，Keyfile 或 X.509 的轮换与故障切换完成演练。

24\. 数据损坏 Runbook 优先选择重新同步或备份恢复，`mongod --repair` 的适用条件、现场保全和事后校验已经明确。

## 16 官方资料与复习自测

### 16.1 官方资料入口

1\. [MongoDB Database Manual](https://www.mongodb.com/docs/manual/)

2\. [Documents](https://www.mongodb.com/docs/manual/core/document/)

3\. [Data Modeling](https://www.mongodb.com/docs/manual/data-modeling/)

4\. [CRUD Operations](https://www.mongodb.com/docs/manual/crud/)

5\. [Aggregation Stages](https://www.mongodb.com/docs/current/reference/operator/aggregation-pipeline/)

6\. [Indexes](https://www.mongodb.com/docs/manual/indexes/)

7\. [Replication](https://www.mongodb.com/docs/manual/replication/)

8\. [Transactions](https://www.mongodb.com/docs/manual/core/transactions/)

9\. [Sharding](https://www.mongodb.com/docs/manual/sharding/)

10\. [Security](https://www.mongodb.com/docs/manual/security/)

11\. [Monitoring](https://www.mongodb.com/docs/manual/administration/monitoring/)

12\. [MongoDB Java Sync Driver](https://www.mongodb.com/docs/drivers/java/sync/current/)

13\. [Java Driver Get Started](https://www.mongodb.com/docs/drivers/java/sync/current/get-started/)

14\. [Java Driver POJOs](https://www.mongodb.com/docs/drivers/java/sync/current/data-formats/document-data-format-pojo/)

15\. [Java Driver Connection Pools](https://www.mongodb.com/docs/drivers/java/sync/current/connection/specify-connection-options/connection-pools/)

16\. [Spring Data MongoDB Reference](https://docs.spring.io/spring-data/mongodb/reference/mongodb.html)

17\. [Spring Data MongoDB Sessions & Transactions](https://docs.spring.io/spring-data/mongodb/reference/mongodb/client-session-transactions.html)

18\. [Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)

19\. [Schema Versioning Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/schema-versioning/)

20\. [Atomicity and Transactions](https://www.mongodb.com/docs/manual/core/write-operations-atomicity/)

21\. [Retryable Writes](https://www.mongodb.com/docs/manual/core/retryable-writes/)

22\. [Unique Indexes](https://www.mongodb.com/docs/manual/core/index-unique/)

23\. [WiredTiger Storage Engine](https://www.mongodb.com/docs/manual/core/wiredtiger/)

24\. [Query for Null or Missing Fields](https://www.mongodb.com/docs/manual/tutorial/query-for-null-fields/)

25\. [`bulkWrite()`](https://www.mongodb.com/docs/manual/reference/method/db.collection.bulkWrite/)

26\. [Aggregation Pipeline Limits](https://www.mongodb.com/docs/manual/core/aggregation-pipeline-limits/)

27\. [Query Optimization](https://www.mongodb.com/docs/manual/core/query-optimization/)

28\. [Multikey Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-multikey/)

29\. [Read Concern](https://www.mongodb.com/docs/manual/reference/read-concern/)

30\. [Write Concern](https://www.mongodb.com/docs/manual/reference/write-concern/)

31\. [Shard Keys](https://www.mongodb.com/docs/manual/core/sharding-shard-key/)

32\. [Change Streams](https://www.mongodb.com/docs/manual/changestreams/)

33\. [Encryption](https://www.mongodb.com/docs/manual/core/security-data-encryption/)

34\. [TTL Indexes](https://www.mongodb.com/docs/manual/core/index-ttl/)

35\. [Java Driver GridFS](https://www.mongodb.com/docs/drivers/java/sync/current/crud/gridfs/)

36\. [MongoDB Stable API](https://www.mongodb.com/docs/manual/reference/stable-api/)

37\. [Hidden Replica Set Members](https://www.mongodb.com/docs/manual/core/replica-set-hidden-member/)

38\. [Replica Set Arbiter](https://www.mongodb.com/docs/manual/core/replica-set-arbiter/)

39\. [MongoDB Shell](https://www.mongodb.com/docs/mongodb-shell/)

40\. [MongoDB Compass](https://www.mongodb.com/docs/compass/)

41\. [MongoDB Database Tools](https://www.mongodb.com/docs/database-tools/)

42\. [Atlas CLI](https://www.mongodb.com/docs/atlas/cli/current/)

43\. [MongoDB Mongosync](https://www.mongodb.com/docs/mongosync/current/)

44\. [Atlas Monitoring and Alerts](https://www.mongodb.com/docs/atlas/monitoring-alerts/)

45\. [MongoDB Ops Manager](https://www.mongodb.com/docs/ops-manager/current/)

46\. [MongoDB Relational Migrator](https://www.mongodb.com/docs/relational-migrator/)

47\. [Testcontainers MongoDB Module](https://java.testcontainers.org/modules/databases/mongodb/)

48\. [MongoDB for Visual Studio Code](https://www.mongodb.com/docs/mongodb-vscode/)

49\. [DataGrip for MongoDB](https://www.jetbrains.com/help/datagrip/mongodb.html)

50\. [Studio 3T Knowledge Base](https://studio3t.com/knowledge-base/)

51\. [Navicat for MongoDB](https://www.navicat.com/en/products/navicat-for-mongodb)

52\. [Polymorphic Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/polymorphic-data/)

53\. [Attribute Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/attribute-pattern/)

54\. [Subset Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/subset-pattern/)

55\. [Bucket Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/bucket-pattern/)

56\. [Outlier Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/outlier-pattern/)

57\. [Approximation Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/approximation-schema-pattern/)

58\. [Extended Reference Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-extended-reference-pattern)

59\. [Preallocation Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-preallocation-pattern)

60\. [Computed Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/computed-schema-pattern/)

61\. [Model Tree Structures](https://www.mongodb.com/docs/manual/applications/data-models-tree-structures/)

62\. [Document Versioning Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/document-versioning/)

63\. [Query Shapes](https://www.mongodb.com/docs/manual/core/query-shapes/)

64\. [Query Plans](https://www.mongodb.com/docs/manual/core/query-plans/)

65\. [2dsphere Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/geospatial/2dsphere/)

66\. [Index Builds on Populated Collections](https://www.mongodb.com/docs/manual/core/index-creation/)

67\. [Deploy a Self-Managed Sharded Cluster](https://www.mongodb.com/docs/manual/tutorial/deploy-shard-cluster/)

68\. [Keyfile Authentication for a Self-Managed Replica Set](https://www.mongodb.com/docs/manual/tutorial/enforce-keyfile-access-control-in-existing-replica-set/)

69\. [Recover a Self-Managed Standalone after Unexpected Shutdown](https://www.mongodb.com/docs/manual/tutorial/recover-data-following-unexpected-shutdown/)

70\. [Map-Reduce Deprecation and Aggregation Alternative](https://www.mongodb.com/docs/manual/core/map-reduce/)

71\. [Self-Managed Replica Set Configuration](https://www.mongodb.com/docs/manual/reference/replica-configuration/)

72\. [`Session.withTransaction()`](https://www.mongodb.com/docs/mongodb-shell/reference/method/session-withtransaction/)

73\. [`sh.splitAt()`](https://www.mongodb.com/docs/manual/reference/method/sh.splitAt/)

74\. [`sh.moveChunk()`](https://www.mongodb.com/docs/manual/reference/method/sh.moveChunk/)

75\. [Partial Indexes](https://www.mongodb.com/docs/manual/core/index-partial/)

76\. [Modify Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/update-schema-validation/)

77\. [Self-Managed MongoDB Search and Vector Search](https://www.mongodb.com/docs/search/self-managed/current/)

78\. [`mongot` Compatibility and Requirements](https://www.mongodb.com/docs/search/self-managed/current/deployment/compatibility-requirements/)

79\. [MongoDB 8.2 Release Notes](https://www.mongodb.com/docs/manual/release-notes/8.2/)

80\. [MongoDB Versioning](https://www.mongodb.com/docs/v8.2/reference/versioning/)

### 16.2 复习时的自测标准

学完后不应只会背命令。合格标准是能够为一个订单或商品业务画出文档模型，解释嵌入与引用的理由，写出带成功判据的原子更新，用 `explain()` 证明索引有效，说明读写关注和副本集切换的后果，设计幂等事务或补偿流程，并拿出可恢复的备份与监控方案。

还应能准确区分缺失字段、`null` 与空值，解释 WiredTiger Cache、Journal、Checkpoint、Oplog 和备份各自解决的问题，判断聚合何时发生阻塞与落盘，说明多键和部分索引的边界，分析查询形状、选择性、计划缓存与 `hint()` 的关系，验证分片查询路由与唯一约束，并设计可以从 Resume Token 断点恢复或从全量数据重建的 Change Streams 消费流程。

对 Java 学习者，还应能从空 Maven 工程跑通 `ping` 和查询，解释 `MongoClient`、`MongoDatabase`、`MongoCollection` 的职责与生命周期，用吞吐和平均耗时估算在途操作而不是用 QPS 直接等同连接数，正确处理 ObjectId、Decimal128、日期和空值映射，区分 Repository 与 `MongoTemplate`，并说明单元测试、单机集成测试和副本集测试各自能证明什么。

对工具选型，还应能根据交互查询、图形分析、应用开发、备份恢复、文本交换、在线迁移和持续监控等任务选择正确工具，解释 Compass 与第三方客户端的取舍，说明 `mongoexport`、`mongodump` 与 `mongosync` 不能互换，并为每次工具操作给出权限边界和验证判据。

对数据建模模式，还应能为多态、属性、子集、扩展引用、桶、异常值、预分配、近似值、计算、树形和文档版本控制模式各举一个业务例子，指出数据权威来源、读取路径、更新与修复策略，并判断真实数据分布不满足什么条件时应放弃该模式。还要准确区分 `schemaVersion` 与业务 `revision`、周期计算值与近似值。

对生产运维，还应能从数据、索引、工作集、Oplog、临时空间、增长和副本数量推导容量预算，解释客户端认证与内部成员认证的区别，完成一次副本集切换和分片路由实验，并在数据损坏时优先选择健康成员重同步或备份恢复，说明为什么 `mongod --repair` 只能作为可能丢数据的最后手段。
