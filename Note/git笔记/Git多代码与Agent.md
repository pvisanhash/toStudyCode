# Git Submodule、Subtree、Worktree 与 Monorepo：Java、IDE 与 Agent 编程实战

> 目标读者：Java 开发人员、技术负责人、需要使用智能编程 Agent 进行并行开发的工程团队。
>
> 阅读目标：理解 Git Submodule（子模块）、Git Subtree（子树）、Git Worktree（多工作树）、Monorepo（单体仓库）与普通 Java 多模块项目的本质区别，完成可验证的命令行与 IDE（Integrated Development Environment，集成开发环境）操作，并建立可落地的多 Agent 协作规范。

| 项目 | 本文约定 |
| --- | --- |
| Git 命令拼写 | 命令使用小写 `submodule`、`subtree`、`worktree`；首字母大写形式表示概念名称 |
| Java 构建示例 | Maven 为主，同时说明 Gradle 对应关系 |
| 主要 IDE | IntelliJ IDEA；补充 Visual Studio Code（VS Code） |
| 示例业务 | `shop-platform` 用 Maven 多模块组织 `common-domain` 与 `order-service`；`order-platform` 通过 Submodule 引用 `risk-sdk`，通过 Subtree 导入 `risk-rules` |
| 安全约定 | 示例不包含真实 Token、密码、内网地址或个人信息 |
| 资料核对日期 | 2026-08-06；版本相关界面以所用 IDE 的官方文档为准 |

## 1 从一个并行开发现场建立主线

### 1.1 具体问题：同一天出现四种“多代码”需求

假设订单团队在同一天收到四个任务：支付超时要紧急热修复，风控 SDK（Software Development Kit，软件开发工具包）由另一个仓库维护，统一风控规则需要连源码一起导入，订单接口与公共领域模型又经常同时修改。团队还希望让两个编程 Agent 并行工作，但不能覆盖开发者尚未提交的文件。

这些需求看起来都在处理“多份代码”，实际分属五层问题。选错层次，常见结果是用 Submodule 代替 Java 依赖管理、把 Maven 多模块误叫成多个 Git 仓库，或误以为 Worktree 已经隔离数据库和凭据。

| 层次 | 要回答的问题 | 本文中的机制 |
| --- | --- | --- |
| 仓库边界 | 哪些代码共享一套提交历史、权限和评审入口 | Monorepo（单体仓库）、Polyrepo（多仓库）、Submodule、Subtree |
| 构建边界 | 哪些模块参加同一次编译、测试和依赖排序 | Maven Reactor、Gradle Multi-Project Build（多项目构建） |
| 工作区边界 | 同一仓库的多个分支如何同时出现在磁盘上 | Worktree 或独立 clone |
| 运行时边界 | 端口、数据库、消息队列、缓存和凭据如何互不污染 | 独立配置、容器、沙箱或虚拟机 |
| 协作边界 | 谁能改什么、从哪个提交开始、怎样验收和集成 | Agent 任务契约、唯一写入者、Pull Request（PR，拉取请求）与持续集成 |

先记住一个总原则：仓库组合、构建依赖、并行工作目录和安全隔离是四种不同能力；一种工具不会自动补齐其他层。

### 1.2 第一个可观察结果：五分钟识别当前项目的真实边界

本节只读取状态，不创建提交、不切换分支、不访问远程，适合在任意已有 Git 项目中执行。输入是“当前项目目录”，动作是依次询问 Git 和构建文件，输出是仓库根、工作树管理区、子模块定义和构建入口。

~~~bash
# 确认当前 Shell 位置；目录开错是很多误操作的起点
pwd

# 输出当前 Git 仓库根；失败通常表示当前目录不在 Git 工作树中
git rev-parse --show-toplevel

# 当前工作树自己的管理目录与所有 Worktree 共享的公共管理目录
git rev-parse --git-dir
git rev-parse --git-common-dir

# 列出同一仓库已经登记的所有 Worktree；普通 clone 通常只有一项
git worktree list --porcelain

# 只有 HEAD 提交包含 .gitmodules 时才读取，不受暂存区新增或删除影响
if git cat-file -e HEAD:.gitmodules 2>/dev/null; then
  git config --blob HEAD:.gitmodules \
    --get-regexp '^submodule\..*\.(path|url)$'
fi

# 只检查常见构建入口是否被 Git 跟踪，不把“目录存在”误当成构建已接入
git ls-files pom.xml settings.gradle settings.gradle.kts
~~~

命令结果按下面方式解释。

| 观察结果 | 能证明什么 | 不能证明什么 |
| --- | --- | --- |
| `--show-toplevel` 输出一个路径 | 找到了当前 Git 仓库根 | 该路径一定也是 Maven 或 Gradle 构建根 |
| `--git-dir` 与 `--git-common-dir` 不同 | 当前目录通常是 Linked Worktree（链接工作树） | 进程、端口、数据库已经隔离 |
| `.gitmodules` 被跟踪且能读出配置 | 当前提交定义了 Submodule 获取关系 | 所有子模块都已初始化或提交匹配 |
| 没有 `.gitmodules` | 当前提交没有标准 Submodule 定义 | 仓库一定没有 Subtree；Subtree 没有标准标记文件 |
| 根目录跟踪 `pom.xml` | 存在一个候选 Maven 入口 | POM 一定是聚合根，或所有模块都能构建 |

成功判据是你能明确写出“Git 仓库根在哪里、是否处于 Linked Worktree、是否存在 Submodule、候选构建入口是什么”。某条命令失败时先保留输出：`not a git repository` 通常表示目录不对；读取 `.gitmodules` 无结果可能只是项目没有 Submodule，不应因此执行初始化或清理命令。

Subtree 只能通过团队清单、提交历史或已知 Prefix（前缀目录）识别，不能靠 `.gitmodules`。已知候选路径时，用 `git ls-tree HEAD <path>` 验证：`160000 commit` 是 gitlink，`040000 tree` 是父仓库普通目录；普通目录可能是 Subtree，也可能只是项目自身代码。

### 1.3 从观察结果回看五层方案

Monorepo、普通多模块项目、Submodule、Subtree 与 Worktree 都可能让一个目录中出现多组代码，但它们解决的不是同一层问题。

1\. Monorepo 或普通多模块项目回答：“同一个 Git 仓库里的多组代码，如何组织？”其中 Monorepo 描述仓库边界，Maven 多模块或 Gradle 多项目描述构建边界，二者不能画等号。

2\. Submodule 回答：“父仓库如何用 gitlink 固定另一个独立仓库的精确提交？”它不是 Maven 依赖管理器，也不会自动跟踪子仓库 `main` 的最新提交。

3\. Subtree 回答：“如何把上游源码导入父仓库的普通目录，并保留显式拉取和回推能力？”普通 clone 能直接得到文件，但 Git 不会保存类似 `.gitmodules` 的标准上游清单。

4\. Worktree 回答：“同一个仓库如何同时检出多个分支？”每个 Worktree 有独立工作文件、Index（索引/暂存区）和 `HEAD`，但共享对象库、分支、标签、远程跟踪引用和默认仓库配置。

5\. Agent 工作流回答：“谁从哪个不可变基线修改哪些路径，运行什么验证，如何交付和清理？”Worktree 只是其中的工作区隔离手段，不是任务调度器或安全沙箱。

~~~mermaid
flowchart TD
    A["先确定代码与权限边界"] --> B{"能否放入同一 Git 仓库？"}
    B -->|"能，且经常原子修改"| C["Monorepo 或普通同仓项目"]
    B -->|"不能，需要独立历史"| D{"消费制品还是组合源码？"}
    D -->|"只消费已发布 Java 库"| E["Maven/Gradle 制品依赖"]
    D -->|"父仓库只固定外部提交"| F["Submodule"]
    D -->|"父仓库直接保存导入文件"| G["Subtree"]
    C --> H{"是否需要并行检出多个任务？"}
    G --> H
    H -->|"是"| I["一任务一分支一 Worktree"]
    F --> J["含 Submodule 时默认一任务一独立 clone"]
    I --> K["再隔离端口、数据、凭据与进程"]
    J --> K
~~~

可以把 Submodule 类比为“源码层被锁定版本的依赖”，但这个类比只解释固定版本。Submodule 不提供传递依赖解析、版本冲突仲裁、制品缓存和仓库发布能力。

可以把 Subtree 类比为“带同步历史的源码导入”。文件确实进入父仓库，但同步不会自动发生，上游地址、分支、导入策略和回推权限必须另外治理。

可以把 Worktree 类比为“共享 Git 数据库的多个检出目录”。类比在工作文件隔离上成立，在安全隔离上失效：它不隔离凭据、网络、进程、数据库或操作系统权限。

### 1.4 一页选型表与生产不变量

| 判断问题 | 推荐选择 | 直接原因 |
| --- | --- | --- |
| Java 库已经按版本发布到制品仓库 | Maven/Gradle 依赖 | 具备标准版本、缓存、校验和供应链工具 |
| 多个模块经常一起修改、评审和验证 | Monorepo 或普通同仓多模块项目 | 一个提交可原子修改接口与调用方 |
| 必须在源码树中固定另一个独立仓库的提交 | Submodule | 父仓库 gitlink 精确记录子提交 |
| 普通 clone 必须直接获得外部源码，且需要后续双向同步 | Subtree | 导入文件成为父仓库普通 Tree 与 Blob |
| 同一仓库同时进行功能、热修复或代码审查 | Worktree | 不用反复 stash 或切换工作目录 |
| 多个 Agent 修改不含 Submodule 的仓库 | 每个任务独立分支与 Worktree | 隔离工作文件、Index 和构建输出 |
| 多个 Agent 修改包含 Subtree 的仓库 | 独立 Worktree，加一个 Subtree 同步所有者 | 文件可隔离，上游同步仍要串行治理 |
| 多个 Agent 修改包含 Submodule 的父仓库 | 默认每任务独立 `clone --recurse-submodules` | Git 2.55.0 手册仍说明该组合支持不完整 |
| 需要隔离端口、数据、凭据或不可信代码 | Worktree/clone 加容器、沙箱或独立运行配置 | Git 工作区不是安全边界 |

贯穿全文的生产不变量如下。

1\. 父仓库记录 Submodule 的精确提交，不记录“永远使用某分支最新代码”。消费子模块时出现 detached HEAD（分离头指针）通常是正确的可复现状态。

2\. 修改自研 Submodule 时，先让子仓库提交通过评审并远程可达，再提交和推送父仓库 gitlink。`git push --recurse-submodules=check` 是带校验的推送，不是只读检查。

3\. Subtree 文件属于父仓库普通快照，但上游来源不是 Git 标准元数据；Prefix、上游提交、历史策略和同步所有者必须版本化记录。

4\. 多 Agent 使用“一任务、一分支、一个隔离目录、一个写入者”，所有并行任务从协调者冻结的同一个精确 Commit 开始，而不是各自在不同时间读取会移动的 `origin/main`。

5\. 路径白名单和任务 YAML 只有在编排器、文件系统权限或审查门禁真正执行时才是强约束；只写在提示词中仍属于协作约定。

6\. IDE（Integrated Development Environment，集成开发环境）是 Git 与构建工具的操作界面，不是另一套状态真相。排障时回到可复制的命令输出。

### 1.5 推荐学习顺序与阶段门槛

| 学习阶段 | 阅读范围 | 完成标志 | 可暂时跳过 |
| --- | --- | --- | --- |
| 第一次学习 | 第 1～3 章、4.1、5.4、6.1～6.4、7.1～7.4、8.1 | 能识别边界，并用本地实验验证 gitlink、普通 Tree 和两个 Worktree | IDE、复杂同步、生产治理 |
| Java 项目实战 | 4.2～4.10、5.5～5.9、第 6 章、8.2～8.6、第 9 章 | 能完成接入、构建、同步、并行运行和安全清理 | 面试追问与迁移高级选项 |
| Agent 编程 | 第 10～11 章、16.3～16.6 | 能冻结基线、分配隔离目录、执行任务契约并提交证据 | Subtree 高级拆分参数 |
| 生产与排障 | 第 12～17 章 | 能处理供应链、CI、冲突、故障恢复和技术选型 | 无 |

初学者的最短执行路径是：先完成第 1.2 节只读识别，再理解第 2 章 Git 状态模型；随后分别完成第 4.1 节 Submodule、第 5.4 节 Subtree、第 6.3 节 Maven 多模块和第 8.1 节 Worktree 实验。四个实验都能在本地观察结果，失败时也都有第一步证据入口。

### 1.6 小白如何阅读和复制代码示例

本文在代码块中直接解释关键命令与配置。复制示例前先认识以下约定。

1\. Bash 和 YAML 使用 `#` 开头的注释，INI 配置也支持这种写法；Shell 执行时会忽略从 `#` 到行尾的内容。

2\. XML 使用 `<!-- 说明 -->`，Markdown 模板也使用同样形式。这些注释用于解释模板字段，不是需要替换的业务内容。

3\. VS Code 的 `settings.json` 实际支持 JSONC（JSON with Comments，带注释的 JSON），因此示例可使用 `//` 注释；把配置复制到只接受严格 JSON 的其他工具时，应先删除注释。

4\. `<order-repo-url>`、`<commit>`、`<changed-module>` 等尖括号内容都是占位符，必须替换为真实值，并且不要保留尖括号。

5\. 以 `~~~text` 标记的块是预期输出或目录结构，只用于对照，不能复制到终端执行；`~~~mermaid` 标记的块是图示源码。

6\. 连续多行 Bash 命令末尾的反斜杠 `\` 表示命令尚未结束。复制时必须连同下一行一起执行，反斜杠后面不要再添加空格或注释。

7\. Bash 示例适用于 macOS、Linux、Windows 的 Git Bash 或 Windows Subsystem for Linux（WSL，适用于 Linux 的 Windows 子系统）。直接使用 PowerShell 时，`printf`、引号和路径写法可能需要调整。

## 2 必要的 Git 基础：先分清仓库、提交与工作目录

### 2.1 Git 的三个日常状态区域

一次普通修改至少涉及三个区域。

| 区域 | 作用 | 常用观察命令 |
| --- | --- | --- |
| Working Tree（工作树/工作目录） | 开发者实际编辑的文件 | `git status --short`、`git diff` |
| Index（索引/暂存区） | 下一次提交准备包含的快照 | `git diff --cached` |
| Repository（仓库） | 已提交的对象、引用和历史 | `git log --oneline --graph` |

`git add` 把工作目录的内容写入暂存区，`git commit` 根据暂存区创建提交。Worktree 的核心价值之一，就是为同一仓库提供多个相互独立的工作目录和暂存区。

### 2.2 提交、分支与 HEAD

Commit（提交）是一个不可变快照，它引用一个目录树、父提交和作者等元数据。Branch（分支）本质上是一个可移动引用，通常指向最新提交。`HEAD` 表示当前工作目录正在查看的位置，通常指向当前分支，也可能直接指向某个提交。

~~~mermaid
gitGraph
    commit id: "C1"
    commit id: "C2"
    branch feature
    checkout feature
    commit id: "F1"
    checkout main
    commit id: "C3"
    merge feature id: "M1"
~~~

当 `HEAD` 直接指向提交而不指向本地分支时，就是 detached HEAD。此时可以阅读、构建和测试代码，但如果直接产生提交又没有及时创建分支，后续容易失去可见的分支入口。Submodule 默认更新后常处于这种状态，因为父仓库要复现的是一个精确提交，不是子仓库某个可移动分支的最新位置。

### 2.3 两种常说的“提交树”：历史 DAG 与目录快照 Tree

“提交树”在口语中容易混淆两个不同结构。

1\. 提交历史是 DAG（Directed Acyclic Graph，有向无环图）：节点是 Commit，边是 parent 关系。普通提交通常有一个父提交，根提交没有父提交，Merge Commit（合并提交）可以有多个父提交。第 2.2 节的 `gitGraph` 展示的就是这层历史关系。

2\. 每个 Commit 又保存一个根 Tree 对象标识：Tree 递归描述该次提交的目录快照，条目可以指向 Blob、下级 Tree，或者模式为 `160000` 的 gitlink。

下图把两层关系放在一起。实线表示父仓库对象或引用之间的关系；虚线只表示父仓库 gitlink 在语义上固定子仓库提交，子提交对象本身通常存放在子仓库的对象数据库中。

~~~mermaid
flowchart LR
    subgraph ParentRepo["父仓库：引用与对象"]
        Head["HEAD"] --> MainRef["refs/heads/main"]
        MainRef --> C3["Commit C3"]
        C3 -->|"parent"| C2["Commit C2"]
        C3 -->|"tree"| RootTree["根 Tree T0"]
        RootTree -->|"100644"| PomBlob["Blob：pom.xml 内容"]
        RootTree -->|"040000"| ComponentsTree["Tree：components/"]
        ComponentsTree -->|"160000"| Gitlink["gitlink：risk-sdk → R10"]
    end

    subgraph ChildRepo["risk-sdk 子仓库：独立对象数据库"]
        R10["Commit R10"] -->|"tree"| RiskTree["根 Tree RT0"]
        RiskTree -->|"040000 / 100644"| RiskBlob["下级 Tree 与 Blob：RiskClient.java 等"]
    end

    Gitlink -. 逻辑固定子仓库提交标识 .-> R10
~~~

各结构保存的信息不同。

| 结构 | 保存什么 | 不保存什么 |
| --- | --- | --- |
| Blob 对象 | 文件内容的字节 | 文件名、目录路径、提交历史 |
| Tree 对象 | 条目名称、模式与对象标识；条目指向 Blob、下级 Tree 或 gitlink | 作者、提交说明、可移动分支 |
| Commit 对象 | 根 Tree、父提交、作者/提交者、时间与提交说明 | 可直接编辑的工作目录 |
| Branch 引用 | 当前分支尖端的 Commit 标识 | 不可变的源码快照 |
| gitlink 树条目 | `160000` 模式与一个子仓库 Commit 标识 | 子仓库源码文件和子仓库对象历史 |

可以在任意普通仓库中用 [`git-cat-file`](https://git-scm.com/docs/git-cat-file) 和 [`git-ls-tree`](https://git-scm.com/docs/git-ls-tree) 验证对象关系：

~~~bash
# -t 只输出对象类型；HEAD 通常会得到 commit
git cat-file -t HEAD

# -p 以便于阅读的格式展开对象，可看到 tree、parent、author 等字段
git cat-file -p HEAD

# HEAD^{tree} 表示“取 HEAD 提交所引用的根 Tree”
git cat-file -t HEAD^{tree}

# 列出 HEAD 快照的第一层目录条目，不递归展开所有文件
git ls-tree HEAD

# 只查看 risk-sdk 路径；Submodule 应显示为 160000 commit
git ls-tree HEAD components/risk-sdk

# -C 让 Git 临时在指定目录运行，无需先执行 cd
# 把占位符替换为上一条命令看到的子仓库提交标识
git -C components/risk-sdk cat-file -t <risk-commit-id>
~~~

关键输出形态如下：

~~~text
commit
tree <root-tree-id>
parent <parent-commit-id>
...

tree
160000 commit <risk-commit-id>	components/risk-sdk
commit
~~~

如果在父仓库直接执行 `git cat-file -t <risk-commit-id>`，可能得到“不是有效对象”之类的错误；这并不否定 gitlink，而是说明父仓库只记录子提交的标识，子 Commit 对象需要从子仓库读取。完整对象语义可对照 [Git Book：Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects.html) 和 [`git-commit-tree`](https://git-scm.com/docs/git-commit-tree)。

### 2.4 一个目录下出现另一个 Git 仓库不等于 Submodule

如果直接在父仓库目录中 `git clone` 另一个仓库，而没有执行 `git submodule add`，它只是一个嵌套仓库。父仓库通常不会记录嵌套仓库中的普通文件，也没有标准的 `.gitmodules` 映射供其他开发者复现。

从父仓库提交中可共享的版本化信息，以及当前克隆才具有的本地状态来看，一个 Submodule 可能涉及以下内容。前两项属于父仓库可提交的版本化定义，后两项只描述初始化、检出后的本地形态。

1\. 父仓库提交树中的 `gitlink` 条目，文件模式为 `160000`，值是子仓库提交标识。

2\. 父仓库版本控制下的 `.gitmodules`，记录子模块名称、路径和获取地址。

3\. 初始化后，父仓库本地的 `$GIT_DIR/config` 会保存当前克隆对该子模块的 URL、Active（是否启用）等设置；它不会进入父仓库提交。

4\. 初始化并检出后，父工作树中会出现子模块工作目录。子模块自己的 Git 目录通常位于父仓库的 `$GIT_DIR/modules/<name>/` 下，子模块根目录中的 `.git` 文件指向它；这只是物理存放关系，子模块仍拥有独立的对象库、配置和提交历史。

尚未初始化的 Submodule 可以只有 gitlink 和 `.gitmodules`，没有已经检出的子模块工作目录。执行 `git submodule deinit` 后，工作目录会被清空，本地注册配置会被移除，但子模块 Git 目录通常仍会保留。旧式 Submodule 或先独立克隆、再添加为 Submodule 的仓库，也可能把 `.git` 目录放在子模块工作目录内部。不同形态可对照 [Git 官方 gitsubmodules 手册](https://git-scm.com/docs/gitsubmodules)；不能因为子模块 Git 目录物理上位于父仓库 `$GIT_DIR` 下，就认为子模块对象已经进入父仓库提交或会随父仓库一起推送。

Subtree 的结果恰好相反：导入目录中没有独立 `.git` 管理区，父仓库把其中每个文件当成普通内容保存。目录嵌套、Submodule 和 Subtree 不能只凭文件夹外观判断，应使用 `git ls-tree`、`.gitmodules` 和文件跟踪状态验证。

### 2.5 Git Submodule 与 Java Module 不是同一层概念

Java 项目中“模块”可能同时表示不同东西，必须先按职责拆开。

| 名称 | 数据来源 | 解决的问题 | 生命周期 |
| --- | --- | --- | --- |
| Git Submodule | `.gitmodules` 与父提交的 gitlink | 跨 Git 仓库组合与版本固定 | Git 提交、拉取、推送 |
| Git Subtree Prefix | 父仓库普通 Tree/Blob 与 Subtree 同步约定 | 导入源码并与独立上游同步 | 父仓库提交、显式 pull/split/push |
| Maven Module | 父 `pom.xml` 的 `<modules>` | 同一次 Maven Reactor 构建 | `mvn` 构建生命周期 |
| Gradle Subproject | `settings.gradle(.kts)` 的 `include` | Gradle 多项目构建 | Gradle Task Graph（任务图） |
| Java Platform Module System（JPMS，Java 平台模块系统）模块 | `module-info.java` | Java 编译与运行时封装、依赖可读性 | 编译、链接、运行 |
| IntelliJ IDEA Module | IDE 项目模型或构建工具导入结果 | 索引、类路径、运行配置 | IDE 会话与项目导入 |

一个目录可以同时是 Git Submodule 或 Subtree Prefix、Maven Module 和 IntelliJ IDEA Module，但同一路径不能在同一提交中既是 `160000` gitlink 又是普通 Tree。Git 组合方式、构建方式和 IDE 项目模型属于不同层次，必须分别验证。

## 3 Git Submodule 的本质、机制与适用边界

### 3.1 Submodule 解决什么问题

Submodule 允许一个 Superproject（父仓库/超级项目）在自己的目录树中挂载另一个拥有独立历史的仓库。父仓库不复制子仓库每个文件的历史，只保存“此路径应当对应子仓库的哪个提交”。

以订单平台为例：

~~~text
order-platform/                 # 父仓库
├── .gitmodules
├── pom.xml
├── order-service/
└── components/
    └── risk-sdk/               # 子仓库工作目录
~~~

父仓库的提交 A 可以固定 `risk-sdk` 的提交 R10，父仓库的提交 B 可以固定 R12。只要 R10 和 R12 能从子仓库远程获取，任何开发者或持续集成（Continuous Integration，CI）环境都能复现对应组合。

### 3.2 父仓库到底保存了什么

添加子模块后，应分别观察父仓库已提交快照、暂存区和子仓库当前 `HEAD`，不能把三者混成一个状态。

~~~bash
# 查看“上一次父仓库提交”已经保存的子仓库指针
git ls-tree HEAD components/risk-sdk

# 查看暂存区；这里代表“下一次父仓库提交”准备保存的指针
git ls-files --stage components/risk-sdk

# rev-parse HEAD 输出子仓库当前提交的完整标识
git -C components/risk-sdk rev-parse HEAD

# 同时检查子仓库分支名、detached HEAD 和未提交文件
git -C components/risk-sdk status --short --branch

# 未加 --cached：比较工作目录与暂存区
git diff --submodule

# 加 --cached：比较暂存区与父仓库 HEAD
git diff --cached --submodule
~~~

`git ls-tree HEAD` 与 `git ls-files --stage` 分别输出已提交 Tree 条目和 Index 条目，形态如下：

~~~text
160000 commit 6f1e2c3d4a5b...	components/risk-sdk
160000 6f1e2c3d4a5b... 0	components/risk-sdk
~~~

`160000` 表示 gitlink，但两条命令观察的时间点不同：`git ls-tree HEAD` 中的对象标识是上一次父仓库提交已经记录的子提交；`git ls-files --stage` 中的对象标识是下一次父仓库提交准备记录的子提交。`git -C components/risk-sdk rev-parse HEAD` 则是子工作目录实际检出的提交。

| 状态来源 | 含义 | 常见差异原因 |
| --- | --- | --- |
| 父仓库 `HEAD` 的 gitlink | 已提交、可被其他人获取的父仓库快照 | 尚未提交子模块升级 |
| 父仓库 Index 的 gitlink | 下一次父仓库提交准备记录的子提交 | 已执行 `git add components/risk-sdk` |
| 子仓库 `HEAD` | 当前子工作目录实际检出的提交 | 手工切换、`update --remote` 或本地开发 |
| 子仓库工作目录 | 子仓库未提交的文件状态 | 正在编辑、生成文件或冲突 |

父仓库没有把 `components/risk-sdk/src` 中的每个文件当成自己的普通文件。一次规范的子模块升级通常先让子仓库 `HEAD` 前进，再把该目录 `git add` 到父仓库 Index，最后提交父仓库的新 gitlink。

用一个不涉及命令细节的小例子，可以观察 R10 如何逐步变成 R11。

| 时刻 | 父仓库 `HEAD` | 父仓库 Index | 子仓库 `HEAD` | 初学者应该理解什么 |
| --- | --- | --- | --- | --- |
| 初始状态 | R10 | R10 | R10 | 三层一致，父仓库状态干净 |
| 只编辑子仓库文件 | R10 | R10 | R10，工作目录有修改 | 还没有产生新提交，父仓库只能知道子模块“内部变脏” |
| 子仓库提交为 R11 | R10 | R10 | R11 | 子提交已经存在，但父仓库还没有准备记录它 |
| 父仓库执行 `git add components/risk-sdk` | R10 | R11 | R11 | 新 gitlink 已进入父仓库暂存区 |
| 父仓库完成提交 | R11 | R11 | R11 | 新的父仓库快照可以复现 R11 组合 |

`.gitmodules` 示例：

~~~ini
# 方括号中的名称是子模块的逻辑名称，可以与目录名不同
[submodule "components/risk-sdk"]
    # path 是子模块在父仓库工作目录中的相对路径
    path = components/risk-sdk

    # url 是其他开发者和 CI 获取子仓库时使用的地址
    # .invalid 是本文专用占位域名，实际使用时必须替换
    url = https://git.example.invalid/platform/risk-sdk.git
~~~

示例域名使用 `.invalid`，不会指向真实服务。实际项目应填写团队成员和 CI 都能读取的地址，不要把访问令牌写入 URL（Uniform Resource Locator，统一资源定位符）。

### 3.3 初始化、更新和远程跟踪不是同一动作

| 动作 | 发生的变化 | 不会发生的变化 |
| --- | --- | --- |
| `git submodule init` | 把 `.gitmodules` 中必要配置注册到本地 `.git/config` | 不下载或检出源码 |
| `git submodule update` | 按父仓库记录的 gitlink 获取并检出子仓库提交 | 默认不会追逐远程分支最新提交 |
| `git submodule update --init --recursive` | 初始化缺失子模块并递归检出精确提交 | 不会自动把父仓库指针提交 |
| `git submodule update --remote` | 根据配置的远程跟踪分支选择较新的提交 | 仍需在父仓库提交新的 gitlink |
| `git submodule sync --recursive` | 将 `.gitmodules` 的新 URL 同步到本地配置 | 不负责检出目标提交 |

这解释了常见疑问：“我在 `.gitmodules` 配了 branch，为什么父仓库仍然显示一个固定提交？”branch 配置只影响 `update --remote` 如何选择候选提交；父仓库快照最终仍记录精确提交。

### 3.4 detached HEAD 为什么通常正确

普通开发者分支会移动，但父仓库需要可复现。假设父仓库记录 `risk-sdk` 的 R10，而 `main` 已经移动到 R12，如果初始化时自动检出 R12，同一父提交在不同时间会得到不同源码，构建就不可复现。

因此，消费子模块时保持 detached HEAD 很合理。只有要直接修改子仓库时，才进入子模块创建或检出本地分支：

~~~bash
# 进入子仓库目录；后续 Git 命令操作的是子仓库，不是父仓库
cd components/risk-sdk

# -c 表示创建并立即切换到新分支，避免在 detached HEAD 上开发
git switch -c fix/null-risk-result
~~~

完成子仓库提交并推送后，再回到父仓库记录新指针。

### 3.5 适合与不适合 Submodule 的场景

适合场景如下。

1\. 多个产品必须引用同一套源码，但子仓库需要独立权限、独立历史和独立发布。

2\. 需要固定第三方或供应商源码的精确修订，并在构建时直接使用源码。

3\. 父项目与子项目职责边界稳定，跨仓库变更相对少。

4\. 部署包或硬件交付物要求在一个目录树中组合多个独立仓库。

通常不适合的场景如下。

1\. 普通 Java 库依赖已经能发布到 Maven Central、Nexus 或 Artifactory；此时制品依赖更符合 Java 生态。

2\. 父子项目几乎每次需求都要一起修改、一起评审、一起发布；Monorepo 往往更简单。

3\. 团队不愿意维护双仓库提交顺序、CI 凭据和子模块故障排查。

4\. 只是希望临时复用几段代码；应优先提取标准库或重构边界，而不是用 Submodule 隐藏耦合。

### 3.6 Submodule、Maven 依赖、Subtree 与 Monorepo 的取舍

| 维度 | Submodule | Maven/Gradle 制品依赖 | `git subtree` | Monorepo |
| --- | --- | --- | --- | --- |
| 源码是否直接出现 | 是 | 通常否 | 是 | 是 |
| 历史是否独立 | 是 | 是 | 可保留上游关联，但内容进入当前仓库 | 否 |
| 使用者是否要额外初始化 | 是 | 构建工具自动下载 | 否 | 否 |
| 版本固定方式 | 子仓库提交 | 版本坐标/锁文件 | 合入的提交内容 | 同一仓库提交 |
| 跨项目原子提交 | 困难，需要至少两个提交 | 通过制品发布分步完成 | 合入后可在本仓库提交 | 最容易 |
| 权限复杂度 | 父子仓库均需权限 | 制品仓库权限 | 拉取上游时需要权限 | 单仓库权限 |
| Java 依赖治理 | 较弱 | 最强 | 较弱 | 取决于构建系统 |

选择原则不是“哪种技术更高级”，而是边界、发布节奏与权限模型是否匹配。需要普通克隆、上游同步和回推流程时，继续完成第 5 章的 Subtree 实验；只需要标准 Java 库时仍应优先制品依赖。

## 4 Submodule 教程：在 Java 多模块项目中完成最小闭环

### 4.1 小白先做：不用远程仓库的本地 Submodule 实验

#### 4.1.1 实验目标与安全边界

第一次学习不要同时处理公司仓库权限、网络、Maven 和 IDE。这个实验只用本机创建两个小仓库，目标是亲眼确认三件事。

1\. `risk-sdk` 是拥有独立提交历史的子仓库。

2\. `order-platform` 是父仓库，只用 gitlink 固定 `risk-sdk` 的某个提交。

3\. 从父仓库重新克隆时，Git 能按照 `.gitmodules` 恢复相同子提交。

请选择一个专门用于练习的空目录执行，不要在真实项目目录中照抄。实验只要求已经安装 Git；`README.md` 只是为了产生可观察文件，暂时不需要 Java 和 Maven。

#### 4.1.2 创建独立的 risk-sdk 子仓库

~~~bash
# 新建实验总目录；如果目录已存在，mkdir 会拒绝，避免覆盖旧实验
mkdir git-submodule-lab
cd git-submodule-lab

# 创建以 main 为初始分支的子仓库
git init -b main risk-sdk

# 只为这个练习仓库配置提交身份，不修改用户全局配置
git -C risk-sdk config user.name "Learning User"
git -C risk-sdk config user.email "learner@example.invalid"

# 创建一个可观察文件；printf 会把一行文字写入 README.md
printf 'risk-sdk v1\n' > risk-sdk/README.md

# 暂存并创建子仓库的第一个提交
git -C risk-sdk add README.md
git -C risk-sdk commit -m "feat: initialize risk sdk"

# 验证当前分支、工作目录状态和最新提交
git -C risk-sdk status --short --branch
git -C risk-sdk log -1 --oneline
~~~

成功时，`status` 第一行包含 `main`，并且没有额外的文件修改行；`log` 显示刚创建的 `feat: initialize risk sdk`。此时 `risk-sdk` 已经是完整仓库，还不是任何仓库的 Submodule。

#### 4.1.3 创建父仓库并添加 Submodule

~~~bash
# 创建父仓库，并为它单独配置实验身份
git init -b main order-platform
git -C order-platform config user.name "Learning User"
git -C order-platform config user.email "learner@example.invalid"

# 父仓库也先拥有自己的普通文件和初始提交
printf 'order-platform\n' > order-platform/README.md
git -C order-platform add README.md
git -C order-platform commit -m "chore: initialize order platform"

# 把相邻的 risk-sdk 仓库添加到父仓库的 components/risk-sdk
# protocol.file.allow=always 只对这一条命令生效，用于允许本地路径实验
git -C order-platform \
  -c protocol.file.allow=always \
  submodule add ../risk-sdk components/risk-sdk

# 查看父仓库准备提交的两个条目
git -C order-platform status --short
git -C order-platform ls-files --stage components/risk-sdk
git -C order-platform diff --cached --submodule

# 提交 .gitmodules 与 gitlink
git -C order-platform commit -m "build: add risk sdk submodule"

# 从已提交 Tree 中再次查看 gitlink
git -C order-platform ls-tree HEAD components/risk-sdk
~~~

关键输出形态如下，实际提交标识会不同。

~~~text
A  .gitmodules
A  components/risk-sdk
160000 <risk-commit-id> 0	components/risk-sdk
160000 commit <risk-commit-id>	components/risk-sdk
~~~

`protocol.file.allow=always` 只用于允许这次受控的本地文件协议访问。这里使用单次 `-c` 配置，而不是修改 `--global`，避免扩大安全影响；文件协议的当前默认策略可对照 [`git-config` 官方文档](https://git-scm.com/docs/git-config.html)。

再验证父仓库没有逐个跟踪子仓库文件：

~~~bash
# 正常结果为空：父仓库没有把这个 README.md 当作自己的普通文件
git -C order-platform ls-files components/risk-sdk/README.md

# 子仓库自己可以看到并跟踪该文件
git -C order-platform/components/risk-sdk ls-files README.md
~~~

第一条无输出是成功判据，不是命令失败；第二条应输出 `README.md`。这正是“父仓库跟踪 gitlink，子仓库跟踪自身文件”的区别。

#### 4.1.4 从父仓库恢复同一子提交

~~~bash
# 从实验父仓库创建一个新副本，并递归恢复子模块
# 本地文件协议仍只对当前 clone 命令临时开放
git -c protocol.file.allow=always \
  clone --recurse-submodules order-platform order-platform-copy

# 检查新副本中的子模块提交
git -C order-platform-copy submodule status

# 进入子仓库视角，确认文件和提交确实存在
git -C order-platform-copy/components/risk-sdk ls-files README.md
git -C order-platform-copy/components/risk-sdk log -1 --oneline
~~~

如果 `submodule status` 输出一个提交标识和 `components/risk-sdk`，`ls-files` 输出 `README.md`，实验就完成了。你已经在没有网络、没有 IDE、没有 Maven 的情况下走通“创建子提交 → 父仓库记录 gitlink → 新副本恢复子提交”的最小闭环。

实验结束后会留下 `risk-sdk`、`order-platform` 和 `order-platform-copy` 三个目录。本文不提供直接递归删除命令；请先用 `pwd` 和 `git status` 确认它们确实只是练习数据，再通过系统回收站或团队认可的可恢复方式清理。

### 4.2 真实 Java 项目的前置条件与目标

假设已有两个远程仓库：

1\. `order-platform`：Spring Boot 订单服务父仓库。

2\. `risk-sdk`：带有独立 `pom.xml` 的风控 Java 源码仓库。

目标是把 `risk-sdk` 放到 `components/risk-sdk`，由父 Maven 项目参与构建。示例命令中的 `<order-repo-url>` 与 `<risk-repo-url>` 必须替换为团队可访问的地址。

开始前验证：

~~~bash
# 确认 Git 已安装并记录版本，版本过旧可能不支持部分参数
git --version

# 确认 Maven 和 Java 环境可用
mvn --version

# 确认当前父仓库没有意外的未提交修改
git status --short
~~~

父仓库必须处于你理解的状态。若已有未提交修改，先判断它们是否属于当前任务，不要用强制清理命令掩盖问题。

### 4.3 在父仓库添加子模块

~~~bash
# 把父仓库克隆到本地 order-platform 目录
git clone <order-repo-url> order-platform

# 进入刚创建的父仓库工作目录
cd order-platform

# switch 专门用于切换分支；-c 表示创建 chore/add-risk-sdk 后立即切换
git switch -c chore/add-risk-sdk

# 注册子仓库，并把源码检出到 components/risk-sdk
# 此命令还会生成或更新 .gitmodules，并暂存 gitlink
git submodule add <risk-repo-url> components/risk-sdk

# 查看新增了哪些文件；通常会看到 .gitmodules 和子模块路径
git status --short

# --cached 查看暂存区；--submodule 展开显示子仓库提交变化
git diff --cached --submodule
~~~

预期结果是 `.gitmodules` 和 `components/risk-sdk` 两项进入暂存区。第二项显示子仓库提交，而不是大量 Java 文件。

确认目标提交：

~~~bash
# 输出子仓库当前检出的提交标识，记住这个值便于交叉核对
git -C components/risk-sdk rev-parse HEAD

# 查看父仓库暂存区中的 gitlink；开头应为 160000
git ls-files --stage components/risk-sdk
~~~

然后提交父仓库：

~~~bash
# -m 后是本次父仓库提交的说明；这里只提交子模块“接入关系”
git commit -m "build: add risk sdk submodule"
~~~

这里没有修改 `risk-sdk`，所以只产生父仓库提交。

### 4.4 把子模块接入 Maven Reactor 构建

Maven Reactor（反应堆）是 Maven 本次命令实际选中并按依赖顺序构建的一组模块。它不是 Git 概念，也不会自动识别 Submodule。如果 `components/risk-sdk/pom.xml` 可作为当前 Reactor 的模块参与构建，可以在父 `pom.xml` 中加入：

~~~xml
<project>
    <!-- Maven 模型版本，不是项目业务版本 -->
    <modelVersion>4.0.0</modelVersion>

    <!-- groupId、artifactId、version 共同标识这个父 Maven 项目 -->
    <groupId>com.example.order</groupId>
    <artifactId>order-platform</artifactId>
    <version>1.0.0-SNAPSHOT</version>

    <!-- pom 表示该项目主要负责聚合子模块，本身通常不生成 JAR -->
    <packaging>pom</packaging>

    <!-- Maven 按这里的相对路径把两个目录加入同一次 Reactor 构建 -->
    <modules>
        <!-- risk-sdk 的源码由 Git Submodule 提供 -->
        <module>components/risk-sdk</module>

        <!-- order-service 是父仓库中的普通 Maven 模块 -->
        <module>order-service</module>
    </modules>
</project>
~~~

验证：

~~~bash
# -B 使用适合 CI 的批处理模式；-ntp 关闭下载进度噪声
# clean 删除旧产物，verify 执行编译、测试和校验生命周期
mvn -B -ntp clean verify
~~~

成功判据不是“IDE 没有红线”，而是 Maven 输出 `BUILD SUCCESS`，并且父 Reactor 中能看到 `risk-sdk` 和 `order-service`。如果 `risk-sdk` 拥有不兼容的 parent POM（Project Object Model，项目对象模型）、插件配置或 Java 版本，它虽然是合法 Submodule，也未必能直接成为同一个 Maven Reactor 模块。

Gradle 对应做法是在 `settings.gradle` 或 `settings.gradle.kts` 中包含该目录。Git 组合是否成功与 Gradle 项目是否成功导入仍是两套判据。

### 4.5 新成员正确克隆项目

推荐一次完成：

~~~bash
# --recurse-submodules 在克隆父仓库后继续初始化所有嵌套子模块
git clone --recurse-submodules <order-repo-url> order-platform

# 进入父仓库，后续检查与构建都从这里执行
cd order-platform

# --recursive 同时检查更深层的嵌套子模块
git submodule status --recursive

# 用命令行完成一次干净构建，排除只在 IDE 中可用的假成功
mvn -B -ntp clean verify
~~~

如果已经普通克隆：

~~~bash
# --init 注册尚未初始化的子模块；--recursive 继续处理嵌套子模块
git submodule update --init --recursive
~~~

`git clone --recurse-submodules` 等价于克隆父仓库后初始化并更新子模块；嵌套子模块也会递归处理。官方 `git clone` 文档还提供 `--jobs`、`--shallow-submodules` 和部分克隆选项，但浅克隆可能让历史诊断、标签解析或旧提交获取更复杂，应先验证 CI 需求再启用。

### 4.6 正确更新子模块版本

#### 4.6.1 仅消费已有子仓库提交

~~~bash
# 进入子仓库，明确接下来要选择哪个已批准提交
cd components/risk-sdk

# 只更新远程引用，不会自动修改工作目录
git fetch origin

# 按精确提交检出；--detach 明确接受 detached HEAD 的只读消费状态
git switch --detach <approved-risk-commit>

# 返回父仓库根目录
cd ../..

# 先在父子代码组合状态下运行完整验证
mvn -B -ntp clean verify

# 暂存子模块目录时，父仓库记录的是新的 gitlink，不是子仓库文件
git add components/risk-sdk

# 提交前确认旧、新子仓库提交范围
git diff --cached --submodule

# 创建父仓库提交，使团队成员能复现这次版本组合
git commit -m "build: update risk sdk revision"
~~~

`<approved-risk-commit>` 应是经过评审、CI 通过且团队可从远程获取的提交。父仓库提交后，其他人执行 `git submodule update --init --recursive` 就会得到相同版本。

#### 4.6.2 同时开发子仓库

~~~bash
# 先进入子仓库并创建开发分支，避免直接在 detached HEAD 上提交
cd components/risk-sdk
git switch -c fix/risk-timeout

# 修改完成后，先独立验证子仓库
mvn -B -ntp clean verify

# 暂存并提交子仓库自己的源码变化
git add .
git commit -m "fix: handle risk service timeout"

# -u 建立本地分支与远程分支的跟踪关系；必须先让子提交远程可达
git push -u origin fix/risk-timeout

# 返回父仓库，验证新的父子源码组合
cd ../..
mvn -B -ntp clean verify

# 父仓库只暂存 risk-sdk 的新提交指针
git add components/risk-sdk
git commit -m "build: use risk timeout fix"

# 先预演：检查父分支与子提交，但不把父分支更新发送到远程
git push --dry-run \
  --recurse-submodules=check \
  origin chore/add-risk-sdk

# 预演通过后执行真实推送，并在推送时再次检查子提交可达性
git push -u \
  --recurse-submodules=check \
  origin chore/add-risk-sdk
~~~

顺序非常重要。父仓库一旦公开引用尚未推送的子仓库提交，其他开发者和 CI 会得到“找不到目标提交”之类的失败。`git push --recurse-submodules=check` 本身仍是推送命令：检查通过后会发送父仓库更新；只有同时加 `--dry-run` 才是不发送更新的预演。`check` 失败时不会替你推送子仓库提交，而且它只证明相关子提交出现在当前克隆认识的至少一个子仓库远程跟踪分支上，不能证明 CI 身份能读取预期远程。团队也可以评估 `on-demand`，但仍应把“子仓库先经过独立验证并可由全新 CI 获取”作为评审和发布准则。

### 4.7 跟踪远程分支的正确理解

可以为 `update --remote` 设置默认分支：

~~~bash
# 把 update --remote 的候选来源配置为子仓库 main 分支
git submodule set-branch --branch main components/risk-sdk

# 分支配置写入 .gitmodules，需要作为父仓库变更提交
git add .gitmodules
git commit -m "build: configure risk sdk update branch"

# 根据上面的远程分支配置推进子仓库工作目录
git submodule update --remote components/risk-sdk

# update --remote 不会自动创建父仓库提交，仍要暂存新 gitlink
git add components/risk-sdk
git commit -m "build: advance risk sdk revision"
~~~

这不会把父仓库变成“动态依赖 main”。执行 `update --remote` 时会选取更新后的提交，但只有父仓库再次提交 gitlink，团队才得到新的固定版本。

生产 CI 通常不应在每次构建时执行 `update --remote`，否则相同父提交可能构建出不同子模块版本。CI 应检出父仓库记录的提交；依赖升级由显式变更完成。

### 4.8 拉取、切换分支与 URL 迁移

父仓库拉取后同步子模块：

~~~bash
# 拉取父仓库提交，并要求支持递归的操作同步处理子模块
git pull --recurse-submodules

# 再显式恢复父仓库当前提交所要求的所有子模块版本
git submodule update --init --recursive
~~~

也可以为当前克隆启用递归行为：

~~~bash
# 只修改当前克隆的仓库级配置，不会写入项目源码
# true 让多个支持递归参数的 Git 命令默认考虑子模块
git config submodule.recurse true
~~~

此配置影响支持 `--recurse-submodules` 的多个命令，但 `clone` 仍需显式参数。团队脚本中继续写明参数，通常比依赖每位开发者的本地隐式配置更可验证。

当 `.gitmodules` 中 URL 发生变化：

~~~bash
# 先把 .gitmodules 的新 URL 同步到当前克隆的本地配置
git submodule sync --recursive

# 再用新地址获取并检出父仓库固定的子提交
git submodule update --init --recursive
~~~

切换父分支时，如果两个分支固定的子模块提交不同，可以使用：

~~~bash
# 切换父仓库分支；此时子模块目录可能仍停留在旧提交
git switch <target-parent-branch>

# 让子模块工作目录追上新父分支记录的 gitlink
git submodule update --init --recursive
~~~

不要看到子模块显示“modified: new commits”就立即提交。先比较当前子模块 `HEAD` 与父仓库期望值：

~~~bash
# 第一列前缀可帮助识别未初始化、漂移或冲突状态
git submodule status --recursive

# 查看父仓库记录的子提交与当前子仓库提交之间的差异
git diff --submodule

# 最后进入子仓库视角，检查分支与未提交文件
git -C components/risk-sdk status --short --branch
~~~

### 4.9 停用与从仓库删除子模块

只是不想在当前工作目录检出它，可以停用：

~~~bash
# 只停用当前工作目录中的子模块，不会删除父仓库历史
git submodule deinit components/risk-sdk
~~~

这主要改变本地状态，不是从项目历史中删除 Submodule。

要从父仓库删除并提交变更：

~~~bash
# 从父仓库当前版本中移除 gitlink，并更新 .gitmodules 对应条目
git rm components/risk-sdk

# 确认删除范围，没有误删其他业务文件
git status --short
git diff --cached

# 提交的是父仓库结构变化，旧提交仍可引用旧子模块
git commit -m "build: remove risk sdk submodule"
~~~

现代 Git 会处理父仓库的 gitlink 与 `.gitmodules` 对应条目。子模块仓库的本地管理数据可能仍保留，以便检出旧提交。除非已确认路径、备份需求和所有未推送提交，否则不要手工删除 Git 管理目录。

### 4.10 Submodule 的 CI 构建闭环

CI 的逻辑应明确写出获取、验证、构建三个阶段：

~~~bash
# 阶段一：同步地址，再并行初始化；--jobs 4 表示最多四个并发获取任务
git submodule sync --recursive
git submodule update --init --recursive --jobs 4

# 阶段二：验证提交是否匹配，并检查每个子仓库是否有未提交文件
git submodule status --recursive
git submodule foreach --recursive 'git status --short'

# 阶段三：使用当前固定的父子源码组合完成构建
mvn -B -ntp clean verify
~~~

关键成功判据如下。

1\. 所有子模块都能使用 CI 身份读取。

2\. `git submodule status --recursive` 不以 `-` 表示未初始化，不以 `+` 表示当前提交与父仓库记录不一致，不以 `U` 表示冲突。

3\. 构建使用父仓库固定的提交，没有执行不受控的 `update --remote`。

4\. Maven 或 Gradle 从命令行完成，而不是只依赖 IDE 缓存。

5\. 父提交、各子模块提交和构建产物版本可以在日志或制品元数据中追溯。

## 5 Git Subtree 的本质、Java 教程与双向同步

### 5.1 Subtree 解决什么问题

Git Subtree（子树）把另一个项目的文件导入当前仓库的某个普通子目录，同时保留以后从上游拉取变化、或把本地目录变化拆回上游仓库的能力。

这里的关键字是“普通文件”。与 Submodule 不同，父仓库会把 Subtree 目录中的 Java 文件、`pom.xml` 和资源文件保存为自己的 Blob 与 Tree 对象；它不创建 `.gitmodules`，也不使用模式为 `160000` 的 gitlink。其他开发者普通 `git clone` 后就能直接得到完整文件。

~~~mermaid
flowchart LR
    Upstream["独立仓库 risk-rules"] -->|"git subtree add / pull"| Prefix["父仓库目录 components/risk-rules/"]
    Prefix --> ParentTree["父仓库 Commit 的普通 Tree 与 Blob"]
    ParentTree --> Clone["普通 git clone：文件立即可用"]
    Prefix -->|"git subtree split / push"| Synthetic["只包含该目录的合成历史"]
    Synthetic --> Upstream
~~~

同样是把源码放到 `components/` 下，父仓库实际保存的对象不同：

| 方案 | `git ls-tree HEAD components/<name>` 的典型结果 | 克隆后的额外动作 |
| --- | --- | --- |
| Submodule | `160000 commit <child-commit>` | 需要初始化和更新子模块 |
| Subtree | `040000 tree <tree-id>` | 无；目录文件已经属于父仓库快照 |
| 手工复制源码 | `040000 tree <tree-id>` | 无，但没有标准的上游同步命令与历史约定 |

Subtree 也不能与 Git 的 `subtree` Merge Strategy（子树合并策略）混为一谈。`git subtree` 是位于 Git 源码 `contrib/subtree` 中的高层命令，提供 `add`、`pull`、`merge`、`split` 和 `push`；子树合并策略则是更底层的合并方式。本文使用前者。当前命令语义以 [Git 源码中的 `git-subtree` 文档](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.adoc) 为准。

### 5.2 什么时候选择 Subtree

Subtree 适合“消费方希望像普通源码一样使用，但维护者仍需要与独立上游同步”的场景。例如，订单平台需要内置一套公司统一的构建脚本或少量风控规则，所有开发者和 CI 都必须在普通克隆后立即拿到它们。

| 判断问题 | 更适合 Subtree | 更适合 Submodule |
| --- | --- | --- |
| 使用者是否愿意学习额外初始化命令 | 不愿意，期望普通 clone 即可 | 可以接受递归初始化 |
| 父仓库是否应保存子项目全部文件 | 是，作为普通目录评审和构建 | 否，只记录独立子仓库提交指针 |
| 是否要求子仓库权限与父仓库权限分离 | 通常不是核心诉求 | 是 |
| 父仓库历史和体积是否敏感 | 能接受导入文件及相关历史 | 希望保持父仓库较轻 |
| 是否频繁双向同步 | 可以，但必须制定提交与同步规范 | 独立仓库开发通常更自然 |
| 是否需要精确表达依赖仓库提交 | 通过导入提交与提交说明追溯 | gitlink 直接记录子提交标识 |

以下场景通常不优先使用 Subtree。

1\. 普通 Java 库已经能发布到 Maven 或 Gradle 制品仓库，应先使用标准制品依赖。

2\. 上游源码巨大、更新频繁，导入会显著增加父仓库对象和历史体积。

3\. 父子项目要求严格权限隔离，不能让父仓库读者获得子项目源码。

4\. 多个消费仓库都会直接修改同一份上游源码，但团队没有明确的同步所有者和冲突治理流程。

5\. 代码本来就属于同一产品、同一团队和同一发布节奏，应评估 Monorepo，而不是人为维护双向同步边界。

### 5.3 开始前先检查命令与三个目录角色

`git subtree` 随许多 Git 发行版提供，但它位于 Git 源码的 `contrib` 区域，具体安装包可能没有包含。第一次使用先运行：

~~~bash
# 显示 subtree 用法即可证明命令存在；该帮助命令可能以非零状态结束
# 因此只适合人工检查，不要直接把它作为 CI 的成功判据
git subtree -h

# 同时记录 Git 版本，便于团队复现命令差异
git --version
~~~

如果看到 `git: 'subtree' is not a git command`，应通过团队批准的 Git 安装包补齐组件，而不是从未知网页复制脚本。团队还应让本地开发机和 CI 使用一致的命令版本。

下面的无网络实验使用三个角色。Bare Repository（裸仓库）只有 Git 对象与引用，没有可编辑工作目录，适合在本机模拟 GitHub、GitLab 等代码托管服务器。

| 目录 | 角色 | 为什么需要 |
| --- | --- | --- |
| `risk-rules.git/` | 本地裸仓库，模拟上游服务器 | 可以安全接收 `push` |
| `risk-rules-author/` | 上游维护者的普通工作目录 | 模拟上游发布新版本 |
| `order-platform/` | 消费方父仓库 | 导入、更新并可能回推 Subtree |

### 5.4 小白实验：普通 clone 就能得到 Subtree 文件

#### 5.4.1 创建本地上游仓库

在一个专门用于练习的空目录中执行。示例只设置仓库级用户名和邮箱，不修改用户全局 Git 配置。

~~~bash
# 创建并进入独立实验目录，避免污染真实项目
mkdir git-subtree-lab
cd git-subtree-lab

# 裸仓库没有工作目录，用来模拟代码托管服务器
git init --bare risk-rules.git

# 作者克隆用于编辑上游源码；空仓库还没有默认分支
git clone risk-rules.git risk-rules-author
git -C risk-rules-author switch -c main

# 配置只对这个练习仓库生效
git -C risk-rules-author config user.name "Subtree Lab"
git -C risk-rules-author config user.email "subtree-lab@example.invalid"

# 创建一个最小 Java 风控规则类
mkdir -p risk-rules-author/src/main/java/com/example/risk
printf '%s\n' \
  'package com.example.risk;' \
  '' \
  '// 练习类只用于证明 Subtree 能导入并跟踪普通 Java 文件。' \
  'public final class RiskRules {' \
  '    // 工具类不需要创建对象，因此构造方法设为私有。' \
  '    private RiskRules() {}' \
  '' \
  '    public static int maxOrderAmount() {' \
  '        // 固定返回值让初学者能直接观察源码版本变化。' \
  '        return 1000;' \
  '    }' \
  '}' \
  > risk-rules-author/src/main/java/com/example/risk/RiskRules.java

# 提交并推送上游的第一个版本
git -C risk-rules-author add .
git -C risk-rules-author commit -m "feat: add initial risk rule"
git -C risk-rules-author push -u origin main

# 让裸仓库的默认分支指向 main，后续普通 clone 不会误找 master
git --git-dir=risk-rules.git symbolic-ref HEAD refs/heads/main
~~~

成功判据是最后一次 `push` 显示创建了远程 `main` 分支，并且以下命令能看到 Java 文件：

~~~bash
# 直接从裸仓库的 main 分支读取文件，不需要创建额外工作目录
git --git-dir=risk-rules.git \
  show main:src/main/java/com/example/risk/RiskRules.java
~~~

#### 5.4.2 导入到订单平台

现在创建消费方仓库，并把上游内容导入 `components/risk-rules`。`--prefix` 指定父仓库中的目标目录；`--squash` 表示不在父仓库历史中展开上游每个细小提交，而用一个汇总提交代表本次上游内容。`subtree add` 仍会创建用于把该汇总内容接入父项目的提交，因此不要把它理解为“整次操作总共只有一个新提交”。

~~~bash
# 创建订单平台父仓库
mkdir order-platform
git -C order-platform init -b main
git -C order-platform config user.name "Subtree Lab"
git -C order-platform config user.email "subtree-lab@example.invalid"

# 父仓库必须先有至少一个提交，便于 subtree 创建导入提交
printf '%s\n' '# order-platform' > order-platform/README.md
git -C order-platform add README.md
git -C order-platform commit -m "chore: initialize parent repository"

# 进入父仓库后，从同级裸仓库导入 main 分支
cd order-platform
git subtree add \
  --prefix=components/risk-rules \
  ../risk-rules.git main \
  --squash
~~~

执行成功时会看到 `Added dir 'components/risk-rules'`。接着验证父仓库保存的是普通目录 Tree，而不是 Submodule 的 gitlink：

~~~bash
# Subtree 路径应显示 040000 tree，而不是 160000 commit
git ls-tree HEAD components/risk-rules

# 这些 Java 文件是父仓库直接跟踪的普通文件
git ls-files components/risk-rules

# Subtree 不创建 .gitmodules；没有输出就是预期结果
git ls-files .gitmodules

# 导入命令已经自动创建提交，状态应当干净
git status --short --branch
~~~

关键输出形态如下：

~~~text
040000 tree <tree-id>    components/risk-rules
components/risk-rules/src/main/java/com/example/risk/RiskRules.java
## main
~~~

#### 5.4.3 用普通 clone 验证使用者体验

Subtree 最直观的优势是使用者不需要知道上游仓库。仍在 `order-platform` 目录中执行：

~~~bash
# -C .. 让 clone 在实验根目录执行，但不会改变当前 Shell 目录
git -C .. clone order-platform order-platform-copy

# 普通 clone 后文件已经存在，不需要 --recurse-submodules
git -C ../order-platform-copy \
  ls-files components/risk-rules

# 新克隆应当没有未提交修改
git -C ../order-platform-copy status --short --branch
~~~

这一步证明的是“父仓库提交足以恢复 Subtree 文件”。它不证明父仓库知道上游 URL：Subtree 没有 `.gitmodules`，上游地址、分支与 `--squash` 策略必须由团队文档或同步脚本记录。

### 5.5 从上游拉取新版本

先在上游作者目录创建新提交并推送。下面新增说明文件，避免使用不同系统参数不一致的 `sed -i`。

~~~bash
# 当前 Shell 仍位于 order-platform；上游作者目录在同级
printf '%s\n' \
  '# Risk Rules' \
  '' \
  'Maximum order amount: 2000' \
  > ../risk-rules-author/README.md

# 上游维护者提交并发布变化
git -C ../risk-rules-author add README.md
git -C ../risk-rules-author commit -m "docs: describe maximum order amount"
git -C ../risk-rules-author push origin main
~~~

父仓库不会自动得到变化，必须由有权限的维护者显式同步：

~~~bash
# pull 先获取上游 main，再把变化合并到指定 prefix
# 与首次导入保持相同的 --squash 策略，减少历史形态混乱
git subtree pull \
  --prefix=components/risk-rules \
  ../risk-rules.git main \
  --squash

# 验证新文件已进入父仓库提交
git show HEAD:components/risk-rules/README.md

# 查看最近提交，确认这是一次明确的上游同步
git log --oneline --decorate -5
~~~

`git subtree pull` 会自动创建合并提交。运行前应保持工作目录干净，先评审上游差异；发生冲突时，像普通合并一样解决文件冲突、运行测试并提交，不能重复执行 `pull` 试图覆盖问题。

### 5.6 把父仓库中的 Subtree 修改回推上游

父仓库直接跟踪这些普通文件，因此开发者可以修改它们。但要回推上游，最好让一次提交只修改 Subtree 目录，不要同时混入订单业务文件。这样拆出的提交说明在独立上游仓库中仍然有意义。

~~~bash
# 这里只修改 Subtree 内的说明文件，不混入 order-service 等父项目文件
printf '%s\n' \
  '# Risk Rules' \
  '' \
  'Maximum order amount: 3000' \
  > components/risk-rules/README.md

# 先检查提交范围，确保只包含目标 prefix
git status --short
git diff -- components/risk-rules

# 创建独立、可拆分的 Subtree 提交
git add components/risk-rules
git commit -m "docs(risk-rules): raise documented order amount"
~~~

`split` 会遍历父仓库历史，生成一条只包含 `components/risk-rules` 内容的合成历史，并把该目录提升为新历史的仓库根目录：

~~~bash
# -b 创建本地分支，方便在真正推送前人工检查拆分结果
# 该分支名必须尚不存在；重复实验请使用新的练习目录
git subtree split \
  --prefix=components/risk-rules \
  -b export/risk-rules

# 拆分分支的仓库根目录应直接包含 README.md 和 src/
git ls-tree --name-only export/risk-rules
git log --oneline export/risk-rules -5
~~~

确认拆分历史、测试和上游权限后，才能推送：

~~~bash
# push 内部也会执行 split，然后把结果推到上游 main
# 这会改变远程状态，真实项目必须先获得明确授权
git subtree push \
  --prefix=components/risk-rules \
  ../risk-rules.git main

# 从裸仓库直接读取结果，验证上游 main 已收到新内容
git --git-dir=../risk-rules.git show main:README.md
~~~

不要把 `subtree push` 的目标写成另一个正检出 `main` 的普通本地仓库。Git 默认会拒绝更新远端正在检出的分支，以免该仓库的分支、Index 和工作目录互相矛盾；本实验使用裸仓库正是为了模拟安全的托管端。

如果上游在本地提交之后已经前进，推送可能因为非 Fast-forward（非快进）被拒绝。此时先获取并通过 `git subtree pull` 集成上游，再重新测试和推送；不能用强制推送覆盖其他维护者的提交。

### 5.7 接入 Maven、Gradle 与 IDE

Subtree 只负责源码和历史同步，不会自动把目录加入 Java 构建。若 `components/risk-rules` 本身是一个 Maven 模块，父 `pom.xml` 仍需显式加入 Reactor：

~~~xml
<modules>
    <!-- 父项目原有业务模块。 -->
    <module>order-service</module>

    <!-- Subtree 是普通目录；Maven 通过这里才会把它加入同一次构建。 -->
    <module>components/risk-rules</module>
</modules>
~~~

`order-service` 还需要通过 `<dependency>` 使用 `risk-rules` 的 `groupId`、`artifactId` 与 `version`。Gradle 项目则在 `settings.gradle(.kts)` 中 `include` 对应子项目并配置目录。Git 导入成功、Maven Reactor 构建成功和 Java 依赖解析成功是三个不同判据，必须分别验证。

在 IntelliJ IDEA 和 VS Code 中，Subtree 默认表现为父仓库中的普通文件夹，而不是新的 Git Root（Git 根目录）。IDE 不会从这些文件自动推断独立上游地址，也不会提供等价于 `.gitmodules` 的同步关系；日常编辑、Diff 和提交使用父仓库界面，`subtree pull/split/push` 通过终端或经过评审的团队脚本执行。

### 5.8 `--squash`、完整历史与可追溯性

| 策略 | 父仓库历史表现 | 优点 | 代价 |
| --- | --- | --- | --- |
| 使用 `--squash` | 每次导入以汇总提交为主 | 日志较简洁，可在版本间切换 | 父仓库看不到上游每个原始提交，需要额外记录来源版本 |
| 不使用 `--squash` | 导入并连接上游完整历史 | 追溯上游提交更直接 | 父仓库日志和对象量明显增加，重复导入更复杂 |

团队应在第一次导入前决定策略，并在同步脚本和评审清单中固定以下信息。

1\. Prefix，例如 `components/risk-rules`。

2\. 上游仓库地址或统一 Remote 名称。

3\. 上游 Branch 或 Tag，以及本次实际导入的 Commit 标识。

4\. 是否使用 `--squash`，以及升级和回滚的批准流程。

5\. 是否允许从父仓库回推；若允许，谁负责 `split`、测试、评审和 `push`。

6\. 上游许可证、版权声明、漏洞扫描和来源审计要求。

Subtree 文件进入父仓库后，构建的可复现性由父仓库 Commit 直接保证；但“这些文件来自哪里、对应上游哪个版本”不会像 Submodule gitlink 那样天然显式，必须通过提交说明、清单或自动化元数据补足。

### 5.9 Agent 编程中的 Subtree 边界

对 Agent 而言，Subtree 看起来只是普通目录，因此既方便，也更容易越权修改。任务契约至少要声明：该 Prefix 是只读供应商代码、允许父仓库本地修补，还是允许回推上游。

1\. 未获授权的 Agent 可以读取、构建和测试 Subtree，但不得执行 `subtree pull` 或 `subtree push`，因为前者会引入大量外部变化，后者会修改远程仓库。

2\. 允许修改时，Subtree 与父项目业务代码分成不同 Commit；这既便于评审，也便于 `split` 后保留有意义的提交说明。

3\. Agent 交付必须报告 Prefix、父仓库 Commit、上游 Commit、是否使用 `--squash`、执行过的同步命令和未完成的远程操作。

4\. 多 Agent 不应同时担任同一个 Prefix 的同步所有者；协调者先固定上游基线，再按顺序集成。

5\. Subtree 在 Worktree 中就是普通文件，能够随分支独立检出；但 Git 引用、Remote 和对象库仍由多个 Worktree 共享，外部上游同步操作依然需要单一协调者。

### 5.10 Subtree 的安全清理与迁移

删除当前版本中的 Subtree，本质上是删除父仓库的一组普通文件：

~~~bash
# 先确认路径确实是受管 Subtree，并检查是否有未提交修改
git status --short -- components/risk-rules
git log --oneline -- components/risk-rules

# 使用 Git 记录目录删除；旧提交仍会保留历史内容
git rm -r components/risk-rules
git diff --cached --stat
git commit -m "build: remove risk rules subtree"
~~~

删除不会自动通知或删除上游仓库，也不会从旧提交和对象数据库立即抹除历史内容。若未来重新添加同一 Prefix，先评估历史连接与 `--squash` 策略；不要通过手工复制目录假装恢复同步关系。

若要把父仓库现有普通目录迁移成独立仓库，先用 `git subtree split --prefix=<path> -b <branch>` 生成可审查的独立历史，再推送到新建远程。迁移会改变权限、CI、发布和代码所有权，不能只执行一条 Git 命令就视为完成。

实验结束后，先用 `pwd` 和各仓库 `git status` 确认没有要保留的提交，再通过操作系统废纸篓等可恢复方式处理整个 `git-subtree-lab`。本文不提供递归强制删除命令。

## 6 Monorepo 与普通多模块项目：先分清仓库边界和构建边界

### 6.1 What：Monorepo 和多模块不是同义词

Monorepo 是 Monolithic Repository 的缩写，本文译为“单体仓库”。它表示多个应用、库、脚本或基础设施配置共同存放在一个 Git 仓库中，共享提交历史、分支、标签和权限入口。Git 不会给 Monorepo 创建特殊对象；对 Git 来说，它仍然只是一个普通仓库。

多模块项目描述的是构建结构。Maven 通过 Reactor（反应堆）收集模块、计算构建顺序并执行生命周期；Gradle 把一个 Root Project（根项目）和若干 Subproject（子项目）组成 Multi-Project Build（多项目构建）。

二者经常重叠，但必须分开理解。

| 例子 | 仓库边界 | 构建边界 | 更准确的叫法 |
| --- | --- | --- | --- |
| 一个仓库只有父 `pom.xml`、`common-domain`、`order-service`，三者统一构建和发布 | 一个 Git 仓库 | 一个 Maven Reactor | 普通 Maven 多模块项目；广义上也符合 Monorepo |
| 一个仓库包含十个微服务、三个前端、部署脚本，每个服务有独立 `pom.xml` 和发布节奏 | 一个 Git 仓库 | 多个独立构建 | 典型组织级 Monorepo |
| 一个仓库包含 `api`、`service`、`repository` 三个模块，最终只打成一个应用 | 一个 Git 仓库 | 一个多模块构建 | 模块化单体项目 |
| 三个服务各有独立 Git 仓库和独立构建 | 三个 Git 仓库 | 三个构建 | Polyrepo（多仓库） |
| 父仓库通过 Submodule 固定另一个仓库 | 两个 Git 仓库 | 可以接入同一次构建 | 仍然是多仓库组合，不是 Monorepo |

还要排除一个常见误解：Monolith（单体应用）与 Monorepo 不是一回事。前者描述运行和部署架构，后者描述源码存放位置。一个 Monorepo 可以包含多个独立部署的微服务；一个单体应用也可以拆在多个仓库中，只是这种做法通常增加协作成本。

~~~mermaid
flowchart TD
    A["源码如何存放？"] --> B{"一个 Git 仓库？"}
    B -->|"否"| C["Polyrepo、多仓库依赖或 Submodule"]
    B -->|"是"| D["普通仓库或 Monorepo"]
    D --> E{"一次构建是否统一聚合？"}
    E -->|"是"| F["Maven 多模块或 Gradle 多项目"]
    E -->|"否"| G["Monorepo 中的多个独立构建根"]
    F --> H{"最终如何部署？"}
    H -->|"一个部署单元"| I["可能是模块化单体"]
    H -->|"多个部署单元"| J["可能是多个服务"]
~~~

### 6.2 Why：什么时候优先放进同一个仓库

以下信号越多，越适合 Monorepo 或普通多模块项目。

1\. 接口提供方和调用方经常在同一个需求中修改，需要一个原子提交同时更新。

2\. 模块属于同一产品，代码所有者、保密等级和合规要求接近。

3\. 团队希望统一 Java Development Kit（JDK，Java 开发工具包）、插件版本、代码规范和测试门禁。

4\. 本地与 CI 需要根据模块依赖图选择受影响模块，而不是先发布临时制品再联调。

5\. Agent 任务可以按稳定目录切分，并且希望协调者在一个提交图中审查完整变化。

以下信号越多，越应慎重合仓，或继续使用独立仓库与制品依赖。

1\. 两组代码的访问权限和审计范围完全不同，无法通过目录级规则充分隔离。

2\. 生命周期、发布节奏和技术栈几乎没有交集，合仓只会扩大检出、索引和 CI 成本。

3\. 对外库已经通过 Maven 仓库发布稳定版本，使用者只需要二进制制品，不需要共同修改源码。

4\. 团队缺少受影响范围计算、远程缓存、代码所有权和构建标准化，仓库规模增长后会让所有任务都运行全量构建。

选型时可以按下面顺序提问。

| 问题 | 是 | 否 |
| --- | --- | --- |
| 代码能否位于同一权限边界？ | 继续判断 | 保持独立仓库 |
| 是否频繁跨模块原子修改？ | 优先同仓 | 继续判断 |
| 使用者只需要稳定 Java 制品？ | 优先 Maven/Gradle 依赖 | 继续判断 |
| 必须保留独立历史但把源码检出到父项目？ | 考虑 Submodule | 继续判断 |
| 使用者必须普通 clone 即得源码，且仍要与上游同步？ | 考虑 Subtree | 普通 Monorepo 或多模块项目即可 |

### 6.3 How：从零完成一个 Maven 多模块项目

下面的练习只有一个 Git 仓库和一个 Maven 构建根。`common-domain` 提供金额规则，`order-service` 调用它。示例使用 Java 8 兼容语法和编译级别，以便仍在旧环境中的初学者也能运行；新生产项目应按团队基线改为受支持的长期支持版 JDK，并由 Maven Toolchains 或 CI 镜像固定版本。

先创建目录。请选择空的练习目录，不要在真实项目中照抄 `mkdir` 命令。

~~~bash
# 创建两个模块的标准 Maven 源码目录；-p 会同时创建缺失的父目录
mkdir -p shop-platform/common-domain/src/main/java/com/example/shop/domain
mkdir -p shop-platform/order-service/src/main/java/com/example/shop/order

# 进入唯一的仓库与构建根；后续命令都从这里运行
cd shop-platform

# 初始化一个普通 Git 仓库；Monorepo 不需要特殊 Git 命令
git init -b main

# 只为练习仓库设置提交身份，不会修改用户的全局 Git 配置
git config user.name "Monorepo Lab"
git config user.email "monorepo-lab@example.invalid"
~~~

目录完成后应接近下面的结构。

~~~text
shop-platform/                         # 唯一 Git 仓库根，也是 Maven 聚合根
├── pom.xml                            # 父 POM，同时列出参与 Reactor 的模块
├── common-domain/
│   ├── pom.xml                        # 领域库模块
│   └── src/main/java/com/example/shop/domain/
│       └── OrderAmountPolicy.java
└── order-service/
    ├── pom.xml                        # 服务模块，依赖 common-domain
    └── src/main/java/com/example/shop/order/
        └── OrderApplication.java
~~~

根目录 `pom.xml` 负责聚合两个模块，并提供可继承的公共坐标和编译配置。

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- 所有 Maven POM 都要声明模型版本；这里不是项目版本。 -->
    <modelVersion>4.0.0</modelVersion>

    <!-- groupId 类似组织命名空间，三个模块通过父子关系共享它。 -->
    <groupId>com.example.shop</groupId>
    <artifactId>shop-platform</artifactId>
    <version>1.0.0-SNAPSHOT</version>

    <!-- pom 表示根项目主要用于聚合与配置，不生成业务 JAR。 -->
    <packaging>pom</packaging>

    <properties>
        <!-- 为了让练习可在 Java 8 环境编译；生产项目请改为团队基线。 -->
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <modules>
        <!-- 路径相对于当前 pom.xml；目录名不必等于 artifactId，但建议一致。 -->
        <module>common-domain</module>
        <module>order-service</module>
    </modules>
</project>
~~~

`common-domain/pom.xml` 声明自己继承根 POM。没有显式写 `packaging` 时，Maven 默认生成 Java Archive（JAR，Java 归档）制品。

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <!-- 三项必须和根 POM 的坐标完全一致。 -->
        <groupId>com.example.shop</groupId>
        <artifactId>shop-platform</artifactId>
        <version>1.0.0-SNAPSHOT</version>

        <!-- 从当前模块目录向上找到父 POM，不需要先发布父 POM。 -->
        <relativePath>../pom.xml</relativePath>
    </parent>

    <!-- 完整坐标由继承的 groupId、version 和本模块 artifactId 组成。 -->
    <artifactId>common-domain</artifactId>
</project>
~~~

`common-domain/src/main/java/com/example/shop/domain/OrderAmountPolicy.java` 提供一个最小公共规则。

~~~java
package com.example.shop.domain;

/**
 * 演示可被其他模块调用的领域规则。
 */
public final class OrderAmountPolicy {

    private OrderAmountPolicy() {
        // 这个类只提供静态方法，不需要创建对象，所以隐藏构造方法。
    }

    public static boolean canSubmit(long amountInCents) {
        // 金额用“分”表示，避免初学示例使用浮点数直接计算人民币金额。
        return amountInCents > 0 && amountInCents <= 100_000;
    }
}
~~~

`order-service/pom.xml` 通过标准 Maven 坐标依赖仓库内的 `common-domain`。Reactor 会发现这个依赖，并把公共模块排在服务模块之前构建。

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example.shop</groupId>
        <artifactId>shop-platform</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <artifactId>order-service</artifactId>

    <dependencies>
        <dependency>
            <!-- 这是仓库内另一个模块的 Maven 坐标，不是目录引用。 -->
            <groupId>com.example.shop</groupId>
            <artifactId>common-domain</artifactId>

            <!-- project.version 继承自父 POM，避免手写两个不同版本。 -->
            <version>${project.version}</version>
        </dependency>
    </dependencies>
</project>
~~~

`order-service/src/main/java/com/example/shop/order/OrderApplication.java` 调用公共模块。

~~~java
package com.example.shop.order;

// import 成功的前提是 order-service 的 POM 已声明 common-domain 依赖。
import com.example.shop.domain.OrderAmountPolicy;

public final class OrderApplication {

    private OrderApplication() {
        // 示例只运行 main 方法，因此不允许误创建入口类对象。
    }

    public static void main(String[] args) {
        // 9_900 表示 99 元；下划线只帮助阅读，不影响数值。
        long amountInCents = 9_900;

        // 预期输出 true，证明服务模块已经调用公共模块的代码。
        System.out.println(OrderAmountPolicy.canSubmit(amountInCents));
    }
}
~~~

从根目录构建并运行。

~~~bash
# -B 启用批处理模式；-ntp 关闭下载进度；verify 编译并执行校验阶段
mvn -B -ntp clean verify

# Unix 类系统用冒号分隔两个 classes 目录；Windows cmd.exe 改用分号
java -cp order-service/target/classes:common-domain/target/classes \
  com.example.shop.order.OrderApplication
~~~

构建日志中的关键结果应接近下面内容；时间和插件日志可以不同。

~~~text
Reactor Build Order:
shop-platform
common-domain
order-service

Reactor Summary:
shop-platform ........................ SUCCESS
common-domain ........................ SUCCESS
order-service ........................ SUCCESS

BUILD SUCCESS
true
~~~

最后再提交一次，体验同仓库原子变更。

~~~bash
# 先确认 Git 只看到本练习创建的文件
git status --short

# 一个提交同时记录父 POM、公共接口和调用方，三者不会版本错配
git add pom.xml common-domain order-service
git commit -m "feat: add order amount policy modules"
~~~

### 6.4 Maven 关键点：聚合、继承与局部构建

Aggregation（聚合）与 Inheritance（继承）经常写在同一个根 POM 中，但不是同一件事。

| 机制 | 配置位置 | 解决的问题 | 是否必须双向声明 |
| --- | --- | --- | --- |
| 聚合 | 根 POM 的 `<modules>` | 哪些模块进入本次 Reactor 构建 | 根列出子即可 |
| 继承 | 子 POM 的 `<parent>` | 子模块复用版本、属性、插件和依赖管理 | 子声明父即可 |
| 模块依赖 | 使用方的 `<dependencies>` | Java 编译与运行时谁依赖谁 | 使用方必须声明 |

因此，根 POM 列出了 `common-domain`，不代表 `order-service` 自动获得它的类。必须在 `order-service/pom.xml` 中写 `<dependency>`。反过来，一个子项目可以继承某个父 POM，却不被该父 POM 聚合；它就不会自动进入从父目录启动的 Reactor。

常用局部构建如下。

~~~bash
# -pl 是 projects list：只选择 order-service 作为目标项目
# -am 是 also make：同时构建它依赖的仓库内模块 common-domain
mvn -B -ntp -pl order-service -am verify

# 只构建 common-domain；它没有仓库内前置依赖，所以不需要 -am
mvn -B -ntp -pl common-domain verify

# 假设当前位于 order-service/src；-f 显式指定两层之上的根 POM
mvn -B -ntp -f ../../pom.xml -pl order-service -am verify
~~~

第三条命令中的 `../../pom.xml` 只在当前目录距离根目录两层时成立。真实项目应先运行 `pwd` 并确认路径，不要机械复制。最终 CI 是否允许局部构建，要根据接口影响、插件行为和测试覆盖决定；高风险公共模块变更仍应运行全量 `mvn verify`。

### 6.5 Gradle 对应做法：Multi-Project Build

Gradle 多项目构建由 `settings.gradle.kts` 声明参与构建的子项目。下面使用 Kotlin Domain-Specific Language（Kotlin DSL，Kotlin 领域特定语言），Java 源码可以复用上一节。

~~~text
shop-platform/
├── settings.gradle.kts                 # 声明根项目和子项目
├── build.gradle.kts                    # 所有子项目共享的仓库配置
├── common-domain/
│   └── build.gradle.kts                # Java 库
└── order-service/
    └── build.gradle.kts                # Java 应用，依赖 common-domain
~~~

`settings.gradle.kts` 内容如下。

~~~kotlin
// 设置构建在日志和 IDE 中显示的根项目名称。
rootProject.name = "shop-platform"

// include 把两个目录注册为当前 Multi-Project Build 的子项目。
include("common-domain", "order-service")
~~~

根 `build.gradle.kts` 只放真正通用的配置。

~~~kotlin
plugins {
    // base 提供基础生命周期任务，但根项目本身不编译 Java。
    base
}

subprojects {
    repositories {
        // 子项目需要外部依赖时统一从 Maven Central 解析。
        mavenCentral()
    }
}
~~~

`common-domain/build.gradle.kts` 把模块定义为可被其他模块依赖的 Java 库。

~~~kotlin
plugins {
    // java-library 区分暴露给调用者的 api 依赖和内部 implementation 依赖。
    `java-library`
}

java {
    toolchain {
        // Gradle 尝试使用 Java 17 工具链；团队应在 CI 中固定可用来源。
        languageVersion.set(JavaLanguageVersion.of(17))
    }
}
~~~

`order-service/build.gradle.kts` 声明项目依赖和入口类。

~~~kotlin
plugins {
    // application 插件提供编译、打包和 run 任务。
    application
}

dependencies {
    // 冒号表示根项目下的 common-domain，不会去远程仓库下载同名制品。
    implementation(project(":common-domain"))
}

java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(17))
    }
}

application {
    // 使用完整包名和类名指定 main 方法所在类。
    mainClass.set("com.example.shop.order.OrderApplication")
}
~~~

项目应提交 Gradle Wrapper，开发者和 CI 都用 Wrapper 固定 Gradle 版本。

~~~bash
# Windows 使用 gradlew.bat；Unix 类系统使用 ./gradlew
./gradlew clean build

# 冒号路径精确选择 order-service 的 build 任务
./gradlew :order-service:build

# application 插件提供 run，预期输出 true
./gradlew :order-service:run
~~~

不要只因为 Maven 使用“module”、Gradle 使用“project”就认为二者原理完全相同。两者都能表达仓库内构建图，但生命周期、依赖配置、缓存和任务选择语义不同，应分别以 Wrapper 和官方文档验证。

### 6.6 IDE：正确导入整个构建，而不是逐个打开源码目录

IntelliJ IDEA 中，优先打开根 `pom.xml`、根 `build.gradle(.kts)` 或 `settings.gradle(.kts)`，让构建工具模型决定 Java Module。不要先把每个 `src` 目录手工标为 Sources Root 来“消除红线”，否则命令行构建仍可能失败。

Maven 项目的推荐检查顺序如下。

1\. 打开根 `pom.xml` 所在目录，选择按 Maven 项目加载。

2\. 在 Maven 工具窗口确认 `shop-platform` 下出现 `common-domain` 与 `order-service`。

3\. 打开 Project Structure，确认两个 Java Module 使用预期 JDK，且 `order-service` 的依赖中存在 `common-domain`。

4\. 修改 POM 后使用 Reload All Maven Projects，再观察 IDE 是否仍有未解析类。

5\. 最后在根目录执行 `mvn -B -ntp clean verify`；IDE 无红线不是构建成功的充分条件。

Gradle 项目则应确认 `settings.gradle(.kts)` 已包含目标子项目，并使用 Gradle 工具窗口刷新。运行配置中的“Use classpath of module”应选择真正包含入口类的 `order-service`，不能选择只负责聚合的根项目。

大型 Monorepo 可能包含多个独立构建根。第一次打开时不要无条件导入所有服务；按任务打开相关子目录或使用 IDE 支持的项目附加能力，同时保留从仓库根运行治理脚本的终端。需要修改跨构建公共配置时，再扩大导入范围。

### 6.7 Agent 编程：按路径和构建图分工

Monorepo 对 Agent 的优势是一个提交可以同时修改提供方与使用方，风险则是 Agent 很容易看见并误改整个仓库。任务契约至少要同时限定 Git 范围和构建范围。

~~~yaml
# 人与 Agent 共用的任务编号，用于分支、Worktree 和交付记录。
task_id: ORDER-231

# 所有 Agent 从同一个完整提交标识开始，避免“最新 main”在执行中漂移。
base_commit: 4f2a9c1e8d7b6a5049382716f5e4d3c2b1a09876

# writable_paths 是允许写入的白名单；公共根配置默认不开放。
writable_paths:
  - common-domain/src/main/java/com/example/shop/domain/
  - order-service/src/main/java/com/example/shop/order/
  - order-service/src/test/

# 这些文件影响全仓库，必须由协调者明确批准后才能修改。
protected_paths:
  - pom.xml
  - settings.gradle.kts
  - build.gradle.kts
  - .github/workflows/

# 快速反馈只构建受影响模块及其仓库内依赖。
fast_verify: mvn -B -ntp -pl order-service -am test

# 公共模块变化可能影响更多调用方，交付前执行全量校验。
final_verify: mvn -B -ntp verify
~~~

稳定的并行分工遵循以下规则。

1\. 每个 Agent 使用独立 Worktree 和独立分支；Monorepo 仍然只有一套共享 Git 引用，不能让两个 Agent 检出同一普通分支。

2\. 优先按叶子模块分配任务。根 `pom.xml`、版本目录、公共接口和数据库迁移属于高扇出 Hotspot（热点），同一时间只指定一个写入者。

3\. 路径不重叠不代表语义不冲突。一个 Agent 修改 `common-domain` 接口，另一个 Agent 修改 `order-service` 调用方，仍需共享接口契约或按依赖顺序集成。

4\. Agent 交付必须列出修改模块、受影响模块、实际执行命令、测试结果和未验证范围，不能只说“构建通过”。

5\. 如果 Monorepo 中存在多个 Maven 或 Gradle 构建根，任务契约必须写明从哪个目录运行命令；仓库根不一定就是构建根。

### 6.8 CI：从全量正确开始，再优化受影响范围

小型普通多模块项目应先建立简单、可复现的全量流水线。只有当全量构建已经稳定且时间明显不可接受时，再引入增量选择、远程缓存或任务分片。

~~~yaml
name: java-verify

on:
  pull_request:

# 当前流水线只需读取仓库内容，不授予默认写权限。
permissions:
  contents: read

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      # 检出当前 Pull Request 的文件快照；默认不会获取完整提交历史。
      - name: Checkout
        uses: actions/checkout@v7

      # 固定 JDK 主版本；真实项目还应按组织规则校验发行版和校验和。
      - name: Set up Java
        uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: "17"
          cache: maven

      # 从唯一 Maven 根执行全量验证，任一模块失败都会使任务失败。
      - name: Verify all modules
        run: ./mvnw -B -ntp verify
~~~

示例中的 `actions/checkout@v7` 与 `actions/setup-java@v5` 是截至 2026-08-06 的当前主版本写法，便于阅读但主版本标签仍可移动。高保障项目应按组织供应链策略固定到已审查的完整 Commit 标识，并用受控自动化提交升级 PR；`setup-java` v5 基于 Node.js 24，自托管 Runner（运行器）当前至少需要 `2.327.1`。`checkout` v7 会默认阻止 `pull_request_target` 或 `workflow_run` 在高信任上下文中检出 Fork PR 代码；不要为了沿用旧流水线而直接设置 `allow-unsafe-pr-checkout: true`。固定 Action（操作）实现版本与固定 JDK 主版本解决的是不同输入，二者都不能省略。

规模变大后的优化不能只做“哪些目录变了”的字符串判断，还要考虑依赖方向。

| 变化 | 最小候选范围 | 仍需扩大范围的原因 |
| --- | --- | --- |
| 只改 `order-service` 内部实现 | `order-service` 及其测试 | 端到端测试可能位于独立模块 |
| 改 `common-domain` 实现但不改接口 | `common-domain` 与所有直接、间接消费者 | 行为变化仍可能破坏调用方 |
| 改公共接口 | 全部下游模块 | 编译与契约都可能受影响 |
| 改根 POM、Gradle Convention Plugin 或 Wrapper | 通常全量 | 构建语义影响所有模块 |
| 只改文档 | 文档检查 | 需确认没有可执行示例或生成代码变化 |

生产治理还应包含 CODEOWNERS（代码所有者规则）、模块依赖约束、禁止循环依赖、统一 Wrapper、构建缓存命中率、测试分层和每个部署单元的发布元数据。Monorepo 允许原子提交，不代表所有模块必须同版本、同一时间部署。

### 6.9 从普通多模块扩展到组织级 Monorepo

不要因为模块数增加就立即引入复杂平台。可以按下面顺序演进。

1\. 先定义目录责任：`apps/` 放可部署应用，`libs/` 放内部库，`build-logic/` 放构建约定；每个目录有明确 Owner（所有者）。

2\. 固定工具链：提交 Maven Wrapper 或 Gradle Wrapper，在 CI 和本地使用同一入口。

3\. 建立依赖规则：禁止业务层反向依赖基础设施实现，禁止随意跨域引用内部包。

4\. 建立受影响范围计算：根据 Git Diff（差异）和构建依赖图选择任务，同时为根配置与公共模块保留全量兜底。

5\. 拆分发布：仓库提交可以原子化，但服务和库仍应拥有独立版本、制品、部署记录与回滚单位。

6\. 最后才评估稀疏检出、远程构建缓存、分布式执行或专用 Monorepo 工具；工具不能替代清晰模块边界。

从多仓库迁入 Monorepo 时，先决定是否保留历史、如何处理同名路径、权限和流水线，再选择 `git subtree`、`git filter-repo` 等迁移手段。迁移会改变远程仓库和团队工作流，必须单独设计与演练，不能把本章的练习命令直接用于生产。

### 6.10 常见误区与快速排查

| 现象 | 常见原因 | 初学者先做什么 |
| --- | --- | --- |
| 根目录执行 Maven 只构建一个项目 | 根 POM 缺少 `<modules>`，或执行的不是聚合根 | 检查 `pwd`、根 POM 和 Reactor Build Order |
| `order-service` 找不到公共类 | 只在根 POM 聚合模块，没有在使用方声明 `<dependency>` | 检查 `order-service/pom.xml` 的完整坐标 |
| Maven 构建顺序似乎不按 `<modules>` 顺序 | Reactor 会根据项目依赖等信息排序 | 检查真实依赖图，不要靠调整目录顺序修复 |
| 子 POM 继承失败 | 父坐标或 `<relativePath>` 错误 | 对照父 POM 的 groupId、artifactId、version |
| Gradle 看不到子项目 | `settings.gradle(.kts)` 没有 `include`，或打开了错误根目录 | 运行 `./gradlew projects` 并刷新 IDE |
| IDE 能运行，命令行却失败 | 手工配置了 IDE 模块，构建文件并不完整 | 从干净终端使用 Wrapper 构建 |
| 改一个公共模块导致大量任务运行 | 下游依赖面大，这是构建图的真实影响 | 先确认依赖设计，再优化缓存，不要漏测下游 |
| 两个模块互相依赖，构建无法排序 | 出现循环依赖 | 提取更低层的稳定接口模块，打破环路 |
| 团队把每个多模块项目都叫“大型 Monorepo” | 混淆仓库边界与规模、治理复杂度 | 用“一个仓库、几个构建根、几个部署单元”精确描述 |
| 微服务放在一个仓库就被说成单体应用 | 混淆源码布局与运行架构 | 分别画仓库图、构建图和部署图 |

排查时先收集证据。

~~~bash
# 确认当前所在目录与 Git 仓库根；两者不一定相同
pwd
git rev-parse --show-toplevel

# Maven：显示 Reactor 解析结果并执行校验
mvn -B -ntp verify

# Gradle：列出根项目认识的所有子项目
./gradlew projects

# 只查看本次变化触及哪些模块，不修改任何文件
git diff --name-only origin/main...HEAD
~~~

本章最小闭环是：能解释 Monorepo 与多模块的差别，能让构建工具识别模块依赖，能从根目录完成一次构建，并能说清局部构建为什么仍可能漏掉跨模块风险。

## 7 Git Worktree 的本质、共享状态与适用边界

### 7.1 Worktree 解决什么问题

普通克隆只有一个工作目录。如果正在 `feature/order-discount` 上修改，又要紧急修复生产问题，传统做法是提交半成品、使用 stash（临时收起未提交修改的仓库级记录），或丢弃修改后切换分支。Worktree 允许保留当前目录不动，同时在相邻目录检出热修复分支。

一个非裸仓库有一个 Main Worktree（主工作树），还可以有零个或多个 Linked Worktree（链接工作树）。它们连接到同一个仓库公共管理区。

~~~text
workspace/
├── order-platform-main/          # main 分支，主工作树
├── order-platform-hotfix/        # fix/payment-timeout，链接工作树
└── order-platform-agent-142/     # agent/142-add-idempotency，链接工作树
~~~

### 7.2 哪些状态共享，哪些状态独立

| 状态 | 是否共享 | 实际影响 |
| --- | --- | --- |
| Git 对象库与提交历史 | 共享 | 节省磁盘与网络；任一 Worktree fetch 后对象可被其他 Worktree 使用 |
| 本地分支与远程跟踪引用 | 共享 | 一个目录创建、删除或移动分支，其他目录立即可见 |
| 标签 | 共享 | 标签操作影响整个仓库 |
| 默认仓库级 `.git/config` | 共享 | 修改远程地址或通用配置可能影响所有 Worktree |
| 工作目录文件 | 独立 | 各分支源码和未跟踪文件互不覆盖 |
| Index（暂存区） | 独立 | 每个 Worktree 可以独立 `git add` 和提交 |
| `HEAD` | 独立 | 每个 Worktree 指向不同分支或提交 |
| 合并、变基进行状态 | 独立 | 冲突处理局限在当前 Worktree |
| 构建输出，如 `target/` | 独立 | 前提是输出位于各自目录内 |
| Stash 引用 | 仓库可见 | 不应把 stash 当成 Agent 私有隔离区 |
| 外部数据库、端口、Maven 本地仓库 | 不由 Worktree 隔离 | 需额外运行配置、容器或目录约定 |

Worktree 的本质不是复制整个 `.git`。链接工作树根目录中的 `.git` 通常是一个文本文件，指向主仓库管理区下对应的管理目录。因此不要让脚本假设 `.git` 永远是目录；需要 Git 路径时使用 `git rev-parse --git-dir`、`--git-common-dir` 或 `--show-toplevel`。

### 7.3 为什么同一分支通常不能同时检出两次

如果两个工作目录同时写同一个分支引用，目录 A 提交后分支指针已经移动，而目录 B 的工作文件和暂存区仍基于旧提交，会形成极易误判的状态。Git 默认阻止这种做法，并提示分支已在另一个 Worktree 检出。

正确做法是每个任务创建独立分支：

`origin` 通常是克隆时自动创建的默认远程名称，`origin/main` 是本地对远程 `main` 分支状态的记录。没有远程仓库时，也可以像第 8.1 节一样直接以本地 `main` 为起点。

~~~bash
# -b 创建新分支；第二个参数是新目录；origin/main 是起点
# 命令成功后，当前目录不会切换，新工作树出现在相邻目录
git worktree add -b fix/payment-timeout ../order-platform-hotfix origin/main
~~~

不要用强制参数绕过保护来建立常规开发流程。

### 7.4 Worktree 与 clone 的区别

| 维度 | Worktree | 独立 `git clone` |
| --- | --- | --- |
| 对象库 | 共享 | 默认独立 |
| 本地分支和标签 | 共享 | 独立 |
| 远程配置 | 默认共享 | 独立 |
| 工作目录与暂存区 | 独立 | 独立 |
| 网络与磁盘成本 | 较低 | 较高 |
| 隔离强度 | 中等 | 更强 |
| 适合场景 | 同一开发者或同一可信机器并行任务 | 不同权限、不同实验配置、强隔离任务 |

如果 Agent 要执行不可信脚本、修改全局工具链、访问不同凭据或需要严格资源隔离，独立克隆仍不够，应该配合容器、沙箱或虚拟机。Worktree 只是源码工作区隔离。

### 7.5 何时使用 Worktree

1\. 同时进行功能开发和生产热修复。

2\. 本地检出 Pull Request（PR，拉取请求）进行构建或调试，不打断当前工作。

3\. 长时间运行一个分支的集成测试，同时在另一分支继续开发。

4\. 让多个可信 Agent 并行修改不含 Submodule 的同一仓库中的不同任务。

5\. 比较两个实现或 Java/JDK（Java Development Kit，Java 开发工具包）升级分支。

不适合把 Worktree 当成以下能力的替代品。

1\. 不替代分支设计与代码评审。

2\. 不替代容器、独立数据库 Schema、端口和消息队列隔离。

3\. 不替代备份；所有 Worktree 仍依赖同一个公共 Git 管理区。

4\. 不适合在网络盘或会被随时清理的临时盘上裸奔；这种场景至少使用 `git worktree lock --reason` 并建立恢复方案。

## 8 Worktree 教程：Java 功能开发与热修复并行

### 8.1 小白先做：不用远程仓库的本地 Worktree 实验

#### 8.1.1 创建只有 main 分支的练习仓库

这个实验不使用 `origin/main`，也不要求 Maven 项目。它先创建一个主工作树，再从本地 `main` 创建第二个工作树，让你直接观察“分支引用共享，文件与暂存区独立”。

~~~bash
# 在单独的练习位置创建总目录，避免污染真实项目
mkdir git-worktree-lab
cd git-worktree-lab

# 创建主工作树，目录名特意带 main，方便观察
git init -b main order-platform-main
git -C order-platform-main config user.name "Learning User"
git -C order-platform-main config user.email "learner@example.invalid"

# 用一个 Spring Boot 风格配置文件模拟 main 分支内容
printf 'server.port=8080\n' > order-platform-main/application.properties
git -C order-platform-main add application.properties
git -C order-platform-main commit -m "chore: initialize local app"

# 基线应在 main 分支且工作目录干净
git -C order-platform-main status --short --branch
~~~

这里的 `application.properties` 不要求真实 Spring Boot 工程；它只是用 Java 开发者熟悉的端口配置展示两个分支可以拥有不同文件内容。

#### 8.1.2 创建第二个工作树

~~~bash
# 从本地 main 创建 feature/port-8081 分支
# 新工作树位于主工作树的相邻目录 order-platform-feature
git -C order-platform-main worktree add \
  -b feature/port-8081 \
  ../order-platform-feature \
  main

# Git 应列出 main 和 feature 两个工作树
git -C order-platform-main worktree list

# 两个目录分别检出了不同分支
git -C order-platform-main branch --show-current
git -C order-platform-feature branch --show-current

# 从任一工作树都能看到仓库级共享的两个分支引用
git -C order-platform-main branch --list
~~~

预期能看到 `main` 和 `feature/port-8081` 两个分支，以及两个不同目录。`git worktree add` 没有复制第二套提交历史；它只创建了另一套工作文件、`HEAD` 与 Index，并继续使用同一个仓库公共管理区。

#### 8.1.3 验证两个目录的文件和暂存区互不覆盖

~~~bash
# 只修改 feature 工作树中的端口
printf 'server.port=8081\n' > order-platform-feature/application.properties

# feature 应显示 application.properties 已修改
git -C order-platform-feature status --short --branch

# main 仍应干净，因为它有自己的工作目录和 Index
git -C order-platform-main status --short --branch

# 只在 feature 分支暂存并提交
git -C order-platform-feature add application.properties
git -C order-platform-feature commit -m "feat: use port 8081"

# 直接读取两个分支最新提交中的文件，结果应分别是 8080 和 8081
git -C order-platform-main show HEAD:application.properties
git -C order-platform-feature show HEAD:application.properties
~~~

如果最后两条命令分别输出 `server.port=8080` 与 `server.port=8081`，说明隔离成立。此时两个工作树能看到同一组分支名称，但各自的 `HEAD`、Index 和工作文件不同。

#### 8.1.4 合并并安全结束实验

~~~bash
# 在 main 工作树中合并 feature；--no-ff 保留一个清晰的合并提交
git -C order-platform-main merge \
  --no-ff feature/port-8081 \
  -m "merge: local port experiment"

# feature 工作树已经干净且提交已合并，可以让 Git 安全移除
git -C order-platform-main worktree remove ../order-platform-feature

# -d 只删除已合并分支，比强制删除 -D 更适合初学者
git -C order-platform-main branch -d feature/port-8081

# 最终应只剩 order-platform-main
git -C order-platform-main worktree list
git -C order-platform-main status --short --branch
~~~

至此已走通“创建 → 并行修改 → 提交 → 合并 → 移除 Worktree”的完整闭环。`git-worktree` 官方手册也把 `git worktree remove` 作为链接工作树完成后的正常入口。若要删除剩余的 `git-worktree-lab` 练习目录，仍应先确认 `pwd`、`status` 和路径，再使用系统回收站等可恢复方式。

### 8.2 在真实 Java 仓库创建热修复 Worktree

本教程假设仓库不含 Submodule。先执行 `git ls-files .gitmodules`；如果有输出，停止通用 Worktree 流程并按第 11 章选择独立 clone。确认是普通仓库后，在主工作树中：

~~~bash
# 更新 origin/main 等远程跟踪引用，不会自动合并当前分支
git fetch origin

# 创建前确认主工作树没有自己尚未处理的修改
git status --short

# 同时创建任务分支和相邻的工作目录
git worktree add -b fix/payment-timeout ../order-platform-hotfix origin/main

# 查看 Git 实际登记的所有工作树、提交和分支
git worktree list
~~~

命令含义如下。

1\. `-b fix/payment-timeout` 创建新本地分支。

2\. `../order-platform-hotfix` 是新工作目录，建议放在主项目同级，而不是嵌套在主项目内部。

3\. `origin/main` 是起点提交，避免基于本地主分支的过期状态。

预期 `git worktree list` 同时显示主目录与新目录，各自对应不同分支。

进入热修复目录验证：

~~~bash
# 进入新工作树；从这里开始，文件和暂存区都属于热修复任务
cd ../order-platform-hotfix

# 应显示 fix/payment-timeout，且没有意外修改
git status --short --branch

# 输出当前工作树根目录，用来防止在错误目录执行后续命令
git rev-parse --show-toplevel

# 在修改前先跑基线构建，区分既有失败与本次引入的失败
mvn -B -ntp clean verify
~~~

如果项目包含 Submodule，不要把下一步简单理解为“再初始化一下就行”。当前 Git 官方 `git-worktree` 手册仍明确说明 Submodule 支持不完整，并不推荐对 Superproject 建立多个检出。生产任务默认使用独立 `git clone --recurse-submodules`；只有团队已经针对当前 Git 版本、目录结构、初始化、更新、移动和清理完成专项验证时，才按第 11 章启用受控组合。

### 8.3 为两个 Spring Boot 实例隔离运行参数

两个 Worktree 的源码独立，但默认都可能监听 `8080`，连接相同数据库。可以使用不同 Spring Profile（配置剖面）与端口：

~~~bash
# 主工作树：使用 8080 端口和 main-local 配置
# 反斜杠表示命令在下一行继续，Shell 会把两行当成一条命令
mvn spring-boot:run \
  -Dspring-boot.run.arguments="--server.port=8080 --spring.profiles.active=main-local"

# 热修复 Worktree：改用 8081，避免与主工作树抢占同一端口
mvn spring-boot:run \
  -Dspring-boot.run.arguments="--server.port=8081 --spring.profiles.active=hotfix-local"
~~~

数据库层面应使用独立容器、独立数据库或独立 Schema。否则一个分支运行 Flyway/Liquibase 迁移后，另一个分支可能因数据库结构变化而失败。Worktree 无法保护进程外共享状态。

### 8.4 完成、集成与清理

在热修复 Worktree 中：

~~~bash
# 暂存当前工作树的修改；提交前仍应先用 git diff 检查范围
git add .

# 创建只属于热修复分支的本地提交
git commit -m "fix: handle payment provider timeout"

# -u 建立上游跟踪，后续可直接使用 git push
git push -u origin fix/payment-timeout
~~~

通过 PR 合并后，回到任意属于同一仓库的 Worktree 检查：

~~~bash
# 从任意同仓库工作树查看 Git 登记的全部工作树
git worktree list

# -C 在不切换当前 Shell 目录的情况下检查目标工作树
# 正常清理前，这条命令应没有文件状态输出
git -C ../order-platform-hotfix status --short
~~~

移除普通 Worktree：

~~~bash
# 让 Git 安全移除已登记且干净的链接工作树
git worktree remove ../order-platform-hotfix

# 再次列出，确认目标路径已从管理记录中消失
git worktree list
~~~

如果意外发现该 Worktree 含 Submodule，不套用此通用清理步骤，转到第 11.6 节。

Worktree 删除后，分支仍然存在。确认已经合并再删除：

~~~bash
# 先列出已合入 main 的分支，人工确认目标分支确实在结果中
git branch --merged main

# -d 只删除已合并分支；若 Git 拒绝，不要直接改用 -D
git branch -d fix/payment-timeout
~~~

`git worktree remove` 默认只移除干净工作树。不要为了省一步直接使用 `--force`；先检查未跟踪文件、未提交修改和子模块状态。

### 8.5 移动、锁定、修复与清理记录

以下是一个不含 Submodule、尚未清理的长时间测试 Worktree。安全移动：

~~~bash
# 使用 Git 命令移动工作树，同时更新内部管理路径
# 不要用文件管理器直接拖动，否则 Git 仍会记录旧位置
git worktree move ../order-platform-long-test ../archive/order-platform-long-test
~~~

`git worktree move` 不能移动主工作树，也不能移动包含 Submodule 的链接工作树。

为可移动磁盘或暂时离线路径加锁：

~~~bash
# lock 防止离线磁盘暂时消失时被 prune；reason 会记录锁定原因
git worktree lock --reason "external SSD used for long-running test" ../archive/order-platform-long-test

# 路径恢复稳定后解除锁定，重新允许正常维护
git worktree unlock ../archive/order-platform-long-test
~~~

SSD 是 Solid-State Drive（固态硬盘）的缩写。

如果目录被手工移动，使用官方修复入口：

~~~bash
# 目录被手工移动后，用真实的新路径修复 Git 管理记录
git worktree repair ../new-location/order-platform-hotfix
~~~

如果目录被手工删除，先预览可清理记录：

~~~bash
# dry-run 只预览将清理哪些失效记录，不执行修改
git worktree prune --dry-run --verbose

# 确认预览安全后才真正清理；它不用于删除正常工作的目录
git worktree prune --verbose
~~~

`prune` 清理的是失效的 Worktree 管理记录，不是正常结束任务的首选；正常流程应使用 `git worktree remove`。

### 8.6 Worktree 专属 Git 配置

默认 `.git/config` 在多个 Worktree 间共享。确实需要每个 Worktree 不同的 Git 配置时，可启用：

~~~bash
# 开启 Worktree 专属配置扩展；启用前先确认团队 Git 版本兼容
git config extensions.worktreeConfig true

# --worktree 只为当前工作树设置提交邮箱，不影响同仓库其他工作树
git config --worktree user.email "agent-142@example.invalid"

# 显示配置来源，确认值来自预期的 local 或 worktree 配置文件
git config --show-origin --get-all user.email
~~~

官方文档提醒，较旧 Git 可能拒绝访问启用该扩展的仓库；`core.worktree`、`core.bare` 和 Sparse Checkout（稀疏检出）相关配置还需要额外审查。应用端口、数据库地址和业务密钥更适合放在不提交的环境配置或运行配置中，不要滥用 Git 配置承载业务环境。

## 9 IntelliJ IDEA 与 VS Code 的操作映射

### 9.1 IDE 与命令行的职责边界

IDE 适合查看改动、分支图、冲突、构建模型和运行配置；命令行更适合验证 Submodule、Subtree、Worktree 与多模块构建的精确状态。建议保持以下映射。

| 目标 | IDE 入口 | 命令行真相 |
| --- | --- | --- |
| 查看当前分支 | 状态栏或 Git Widget | `git branch --show-current` |
| 查看修改与暂存 | Commit/Source Control 工具窗口 | `git status --short` |
| 查看子模块提交 | 多 Git Root 或仓库视图 | `git submodule status --recursive` |
| 判断目录是 Submodule 还是 Subtree | IDE 目录外观不足以判断 | `git ls-tree HEAD <path>` |
| 查看所有 Worktree | Worktrees 视图 | `git worktree list --porcelain` |
| 查看父仓库指针差异 | Git Diff | `git diff --submodule` |
| 验证 Java 构建 | Maven/Gradle 工具窗口 | `mvn -B -ntp clean verify` 或 Gradle Wrapper |

### 9.2 IntelliJ IDEA 使用 Submodule

当前 JetBrains 官方设置提供“递归克隆项目中的子模块”选项，它会在检出时使用 `git clone --recurse-submodules`。对已有项目，命令行执行 `git submodule update --init --recursive` 最直接。

导入后的检查顺序如下。

1\. 打开 `Settings | Version Control`，确认父仓库与子模块仓库被识别为正确的 Version Control System（VCS，版本控制系统）Root。

2\. 在 Git 工具窗口观察当前操作针对哪个 Root；父仓库与每个 Submodule 有各自分支和提交历史。

3\. 在 Maven 工具窗口重新加载项目，确认 `components/risk-sdk` 是否进入 Reactor。

4\. 在终端运行 `git submodule status --recursive`，确认 IDE 看到的提交与父仓库 gitlink 一致。

5\. 修改子模块时先在子模块 Root 创建分支；提交子仓库后，再切到父 Root 提交指针变化。

JetBrains 官方同步文档说明：当子模块处于 detached HEAD 时，IDE 更新项目会调用 `git submodule update`，将其检出到父仓库引用的提交。这是可复现行为，不应仅为去掉 detached HEAD 提示而随意切到最新 `main`。

### 9.3 IntelliJ IDEA 与 VS Code 使用 Subtree

Subtree 目录没有独立 Git 管理区，因此 IDE 通常只显示父仓库这一个 VCS Root。`components/risk-rules` 中的修改会和 `order-service` 修改一起出现在父仓库 Commit 窗口中，这是正常现象。

1\. 把 Subtree 中的 `pom.xml` 或 `build.gradle(.kts)` 按普通子项目导入；IDE 能否识别 Java Module 取决于构建配置，不取决于 `git subtree add`。

2\. 提交前按目录筛选 Diff。需要回推上游时，让一个 Commit 只包含目标 Prefix，避免把父项目业务变化带入合成历史。

3\. IDE 的 Pull 按钮默认拉取父仓库当前分支，不等价于 `git subtree pull`；上游同步必须执行带 `--prefix`、上游仓库和分支的明确命令。

4\. 不要把 `risk-rules-author` 等上游工作副本嵌套在父项目目录中，否则 IDE 可能把它识别为额外 Git Root，初学者会误以为它就是 Subtree。

5\. 执行 `subtree split` 后出现的导出分支属于父仓库引用，IDE 分支列表中可以看到；在确认推送或不再需要之前不要随意删除。

### 9.4 IntelliJ IDEA 与 VS Code 使用 Monorepo 或多模块项目

对于单一 Maven Reactor，IntelliJ IDEA 应从根 `pom.xml` 导入，VS Code 应从包含根 POM 的目录打开，并通过 Java 扩展读取 Maven 模型。对于 Gradle Multi-Project Build，应从包含 `settings.gradle(.kts)` 的目录打开。构建文件才是模块关系的事实来源，IDE 中手工添加的模块不能代替它。

| 目标 | IntelliJ IDEA | VS Code | 命令行验证 |
| --- | --- | --- | --- |
| 查看 Maven 模块 | Maven 工具窗口 | Maven Projects 视图 | `./mvnw -B -ntp verify` |
| 查看 Gradle 子项目与任务 | Gradle 工具窗口 | Gradle 视图 | `./gradlew projects` |
| 重新加载构建模型 | Reload All Maven Projects 或 Reload Gradle Project | Java/Gradle 项目刷新命令 | 删除 IDE 缓存前先运行 Wrapper |
| 运行单个服务 | 选择服务模块的 Classpath 和入口类 | 在服务模块建立运行配置 | `./mvnw -pl <module> -am test` 或 `./gradlew :<project>:run` |
| 判断是否打开错误根目录 | 查看工具窗口是否缺少模块 | 查看工作区是否只识别一个子目录 | `git rev-parse --show-toplevel` 加构建根文件检查 |

大型 Monorepo 有多个独立构建根时，IDE 不一定需要一次索引整个仓库。可以只打开任务相关的服务目录，但 Agent 和开发者必须记住：IDE 项目根、构建根和 Git 仓库根可能是三个不同路径。提交前仍要从 Git 根检查 Diff，并按任务契约从正确构建根运行验证。

### 9.5 IntelliJ IDEA 使用 Worktree

JetBrains IntelliJ IDEA 2026.2 官方文档提供 `Git | New Worktree` 和 Git 工具窗口中的 Worktrees 视图。创建后，新 Worktree 会作为独立项目打开。官方文档同时说明当前 Worktree 界面支持包含单个仓库的项目。含 Submodule 的项目属于多仓库场景，而且 Git 官方本身仍不推荐 Superproject 的多 Worktree 检出；改用命令行并不能消除这一底层限制，生产 Agent 默认应使用独立 clone。

推荐步骤如下。

1\. 在主项目终端执行 `git worktree add -b <branch> <sibling-path> <start-point>`，或在满足单仓库限制时使用 `Git | New Worktree`。

2\. 把新目录以“New Window”方式打开，不要替换当前项目窗口。

3\. 等待 Maven/Gradle 同步，确认 Project SDK（Software Development Kit，软件开发工具包；Java 项目中通常指项目 JDK）和语言级别。

4\. 为每个 Worktree 建立独立运行配置，至少区分端口、Profile、数据库和日志目录。

5\. 不要把 Worktree 创建在当前项目目录内部。JetBrains 官方文档指出，嵌套目录可能被误判为多 Root 项目并破坏 Worktree 集成。

6\. 不要提交 `.idea/workspace.xml`。该文件包含本地工作区状态；复制到 Worktree 后可能导致 IDEA 把不同目录识别成同一项目。

可共享团队运行配置，但不要共享本地密钥。例如将可提交的 `.run/OrderMain.run.xml` 只保存参数模板，通过环境变量注入本机凭据。

### 9.6 VS Code 使用 Worktree

VS Code 当前官方文档提供内置 Worktree 操作：在 Source Control Repositories 视图中选择 `Worktrees > Create Worktree`，并可在新窗口打开。也可以先用命令行创建，再执行 `Git: Open Worktree in New Window`。

可选设置示例：

~~~jsonc
{
  // 自动发现当前仓库创建的 Git Worktree
  "git.detectWorktrees": true,

  // 最多检测 20 个，避免工作树很多时持续扫描拖慢编辑器
  "git.detectWorktreesLimit": 20
}
~~~

VS Code 还支持 `git.worktreeIncludeFiles`，可以在新 Worktree 中复制符合模式且被 `.gitignore` 忽略的文件。对 Agent 场景不要默认复制 `.env`、云凭据或生产配置；便利性不能覆盖最小权限原则。

当前 VS Code 的自动检测上限默认值为 50，上例显式改成 20 是团队性能策略，不是产品默认值。VS Code 还提供 Compare with Workspace（与当前工作区比较）与 Migrate Worktree Changes（迁移 Worktree 变更）；后者会把未提交变化带入当前工作区，不应代替 Agent 的提交、PR、测试和审计流程。

### 9.7 IDE 多窗口的资源隔离

每个 Worktree 首次打开都会进行索引和构建导入，Java 大型项目可能产生明显 CPU（Central Processing Unit，中央处理器）、内存和磁盘压力。合理策略如下。

1\. 同时只打开正在交互的少量 IDEA 窗口，其余任务由命令行 Agent 执行。

2\. 每个 Worktree 保留自己的 `target/` 或 `build/`，不要把构建输出重定向到同一共享目录。

3\. Maven 本地仓库通常共享，可以节省依赖下载；如果 Agent 可能安装同名 SNAPSHOT（快照）并互相污染，使用唯一版本号、独立本地仓库或仅执行 `verify` 而不执行 `install`。

4\. Gradle Daemon（守护进程）和缓存可以共享，但并发构建时要观察锁等待与内存。

5\. IDE 成功编译只证明当前 IDE 模型；最终交付必须使用项目 Wrapper 或团队标准命令验证。

## 10 Agent 编程：用 Worktree 建立可控的并行开发流水线

### 10.1 Agent 编程在本文中的含义

Agent 编程是让具备读取代码、编辑文件、运行命令和反馈结果能力的软件 Agent 承担一段受约束的工程任务。Agent 可以很快，但它仍然会遇到人类开发者相同的 Git 状态、构建依赖、并发写入和合并冲突。

Worktree 对 Agent 的价值，是为每个任务提供独立的源码视图、`HEAD` 和暂存区，使 Agent A 的未提交修改不会直接覆盖 Agent B 的文件。它解决的是工作区并发，不会自动解决需求冲突、接口冲突或外部资源冲突。

### 10.2 推荐的角色与状态流转

一个稳定流程至少包含 Coordinator（协调者）、Worker Agent（执行 Agent）和 Integrator（集成者）三个职责。它们可以由不同工具承担，也可以由同一个人分阶段承担，但职责不能消失。

~~~mermaid
flowchart TD
    A["协调者：拆分任务与定义验收标准"] --> B["从确定的基线提交创建分支"]
    B --> C["按仓库类型创建 Worktree 或独立 clone"]
    C --> D["执行 Agent：读取约束、修改、测试"]
    D --> E["执行 Agent：输出提交、测试结果与风险"]
    E --> F["集成者：复核 Diff 与测试证据"]
    F --> G{"是否满足验收标准"}
    G -- "否" --> D
    G -- "是" --> H["通过 PR、merge 或 cherry-pick 集成"]
    H --> I["主线 CI 再验证"]
    I --> J["清理任务目录，按策略删除分支"]
~~~

协调者负责防止两个任务在语义上互相踩踏，隔离任务目录负责防止两个执行者在磁盘上互相踩踏。二者缺一不可。

### 10.3 一任务、一分支、一个隔离目录、一个写入者

推荐不变量如下。

1\. 一个任务拥有唯一任务标识，例如 `PAY-142`。

2\. 一个任务拥有唯一分支，例如 `agent/PAY-142-idempotency`。

3\. 一个任务拥有唯一隔离目录：普通仓库和包含 Subtree 的仓库使用 Worktree，例如 `../wt-PAY-142`；包含 Submodule 的父仓库默认使用独立 clone，例如 `../clone-PAY-142`。

4\. 同一时刻只有一个 Agent 对该隔离目录拥有写权限。

5\. Agent 不在主工作树直接修改，不在其他任务分支提交。

6\. Agent 不使用共享 stash 作为交付物，交付应是清晰提交或明确 Diff。

7\. 集成完成前不删除任务目录；清理前必须检查未提交和未跟踪文件。

这一规则并不要求每个小步骤都提交。它要求写入所有权清晰，让恢复、审查和归责都有确定入口。

### 10.4 任务契约必须先于代码修改

协调者应把任务写成机器和人都能检查的契约。示例：

~~~yaml
# 人和 Agent 共用的任务编号，用于关联分支、目录和交付记录
task_id: PAY-142

# 只描述本任务要实现的业务结果，不夹带无关重构
goal: "为支付回调增加数据库幂等保护"

# 协调者先解析远程分支，再把完整、不可变的 Commit 标识写入契约
# 这里是示意值；真实任务不得填写仍会移动的 origin/main
base_commit: "4f2a9c1e8d7b6a5049382716f5e4d3c2b1a09876"

# 分支与工作目录都带任务号，便于确认当前写入位置
branch: "agent/PAY-142-idempotency"
worktree: "../wt-PAY-142"

# allowed_paths 是白名单：Agent 只允许修改这些路径
allowed_paths:
  - "payment-service/src/**"
  - "payment-service/pom.xml"
  - "payment-service/src/test/**"

# forbidden_paths 是明确禁区，即使与目标看似相关也不能自行修改
forbidden_paths:
  - ".gitmodules"
  - "components/risk-rules/**"
  - "infra/production/**"

# acceptance 描述用户可观察的成功条件
acceptance:
  - "重复回调只产生一条支付流水"
  - "并发测试覆盖两个相同业务键"

# verify 固定交付前必须实际运行的技术验证命令
verify:
  - "./mvnw -B -ntp -pl payment-service -am test"

# deliver 规定 Agent 最终必须提供的审查证据
deliver:
  - "基线与交付提交标识"
  - "修改摘要"
  - "测试命令与结果"
  - "剩余风险"
~~~

`base_commit` 固定所有并行任务看到的输入快照；若只写 `origin/main`，不同时间启动的 Agent 可能得到不同基线。`allowed_paths` 与 `forbidden_paths` 描述修改边界，`acceptance` 定义业务成功，`verify` 定义技术成功。YAML 本身不会阻止越界写入；强约束还需要编排器、沙箱或文件系统权限执行，集成阶段再用 Diff 和 CI 复核。只有“写一个幂等功能”而没有输入边界、并发语义和测试命令，不足以成为 Agent 任务。

### 10.5 创建 Agent 工作目录

协调者先执行 `git ls-files .gitmodules`。没有输出时可以按普通仓库创建 Worktree；但还要阅读项目的 Subtree 清单或 Agent 指令，确认哪些普通目录具有上游同步边界。Subtree 不会出现在 `.gitmodules` 中。

~~~bash
# 协调者先刷新远程跟踪引用；该操作会影响同仓库所有 Worktree
git fetch origin main

# 只冻结一次，并把完整输出写入所有并行任务的 base_commit
BASE_COMMIT=$(git rev-parse refs/remotes/origin/main)
printf '%s\n' "$BASE_COMMIT"

# -b 创建 Agent 专属分支；起点使用已冻结提交，而不是移动引用
git worktree add \
  -b agent/PAY-142-idempotency \
  ../wt-PAY-142 \
  "$BASE_COMMIT"

# 检查新目录的分支、状态和精确基线
git -C ../wt-PAY-142 status --short --branch
git -C ../wt-PAY-142 rev-parse HEAD

# 从仓库整体视角确认新工作树已登记且分支没有重复占用
git worktree list
~~~

以上流程适用于不含 Submodule 的普通仓库，也适用于把 Subtree 当普通文件开发的任务。若仓库根目录存在 `.gitmodules`，生产默认改为独立克隆：

~~~bash
# 必须替换为任务契约中的完整 base_commit，不能重新解析 origin/main
BASE_COMMIT="<full-base-commit-id>"

# 含 Submodule 的父仓库默认使用独立 clone；先不检出远程默认分支
git clone --no-checkout <parent-repo-url> ../clone-PAY-142

# clone 完成后先确认契约提交存在，再从该精确提交创建任务分支
git -C ../clone-PAY-142 cat-file -e "$BASE_COMMIT^{commit}"
git -C ../clone-PAY-142 switch -c agent/PAY-142-idempotency "$BASE_COMMIT"

# 只在切到冻结基线后初始化，避免先获取远程默认分支的子模块组合
git -C ../clone-PAY-142 submodule update --init --recursive

# 检查子模块是否未初始化、漂移或冲突
git -C ../clone-PAY-142 submodule status --recursive
~~~

`<parent-repo-url>` 使用父仓库标准远程地址，`BASE_COMMIT` 必须沿用协调者已经写入任务契约的值，不能在每个独立 clone 中重新解析。独立 clone 会牺牲一部分磁盘和网络效率，但换取 Git 官方支持路径下更清晰的子模块仓库状态。将 Agent 的进程工作目录明确设置为实际任务目录。只在提示词中说“请在 PAY-142 分支工作”不够，因为进程可能仍处于主工作树。

### 10.6 Agent 开工前的自检

每个 Agent 在修改前应执行并报告：

~~~bash
# 输出 Shell 当前目录，最先排除“终端开错目录”
pwd

# 输出 Git 识别的仓库根目录，应与任务契约的目录一致
git rev-parse --show-toplevel

# 同时查看当前分支和工作目录是否干净
git status --short --branch

# 只输出分支名；空结果通常表示 detached HEAD
git branch --show-current

# 输出当前基线提交，便于与协调者提供的提交交叉核对
git rev-parse HEAD

# 普通仓库通常无输出；含子模块时确认所有子模块版本
git submodule status --recursive

# 任务涉及受管 Subtree 时，确认该路径在基线提交中的精确 Tree
git ls-tree HEAD components/risk-rules
~~~

自检应确认以下条件。

1\. 顶层目录与任务契约中的任务目录一致。

2\. 当前分支与任务分支一致，不是 `main`，也不是 detached HEAD。

3\. 起始提交符合协调者提供的基线。

4\. 工作目录没有不属于当前任务的既有修改。

5\. 子模块状态符合父仓库记录。

发现不一致时应停止写入并报告，而不是自行 reset、强制切换或删除文件。Agent 的“自动修复环境”可能恰好抹掉另一个人的工作。

### 10.7 约束 Agent 的修改过程

任务执行期间采用逐层验证。

1\. 先运行最小相关测试，确认基线状态和命令可用。

2\. 修改最小必要范围，不顺手进行无关重构或依赖升级。

3\. 每次跨模块修改都说明接口、数据模型、事务、并发和兼容性影响。

4\. 先运行局部测试，再运行契约指定的模块级测试。

5\. 使用 `git diff --check` 检查空白错误，使用 `git diff --stat` 检查改动规模。

6\. 对数据库、消息队列或第三方服务使用测试替身时，说明它能证明和不能证明的边界。

7\. 不把“命令无输出”或“没有抛异常”当成业务成功；记录测试数量、退出码和关键断言。

Java 任务的常用验证梯度如下。

~~~bash
# -pl 只选择 payment-service；-Dtest 进一步只运行一个测试类
# 适合修改过程中的快速反馈，但不能替代更大范围验证
./mvnw -B -ntp -pl payment-service \
  -Dtest=PaymentCallbackServiceTest test

# -am 是 also-make：同时构建 payment-service 依赖的仓库内模块
./mvnw -B -ntp -pl payment-service -am test

# verify 运行全仓库直到验证阶段，适合作为高风险改动的最终检查
./mvnw -B -ntp verify
~~~

是否执行全仓库验证由风险和成本决定，但交付时必须明确实际覆盖范围。

### 10.8 Agent 的提交与交付包

推荐在任务目录中形成语义单一的提交：

~~~bash
# 先确认有哪些文件变化，再阅读未暂存的完整差异
git status --short
git diff

# 只检查空白错误和冲突标记；无输出才表示此项通过
git diff --check

# 只暂存任务允许修改的 payment-service，避免把无关文件带入提交
git add payment-service

# 审查真正会进入下一次提交的内容
git diff --cached

# 创建语义单一的任务提交
git commit -m "feat(payment): make callback processing idempotent"

# 提交后确认工作目录状态，并记录最终提交标识
git status --short --branch
git rev-parse HEAD
~~~

交付包至少包含：

1\. 任务标识、分支、任务目录、冻结的基线提交与交付提交标识。

2\. 修改的业务行为和关键实现取舍。

3\. 实际运行的测试命令、退出结果与未运行项目。

4\. 数据库迁移、配置变化、兼容性和回滚风险。

5\. 是否修改 Submodule；若修改，列出子仓库提交与父仓库 gitlink 提交。

6\. 是否修改 Subtree；若修改，列出 Prefix、上游来源、同步策略，以及是否仍需授权后执行 `pull`、`split` 或 `push`。

7\. 未解决问题和建议的人工复核点。

不要只交付“已完成”。集成者需要可复核证据。

### 10.9 集成策略与选择

| 策略 | 适用场景 | 特点 |
| --- | --- | --- |
| Pull Request 合并 | 团队日常协作 | 有审查、CI 与审计记录，默认推荐 |
| `git merge --no-ff` | 本地受控集成、希望保留分支边界 | 形成显式合并提交 |
| `git cherry-pick <commit>` | Agent 只交付少量独立提交 | 精确，但可能遗漏隐含依赖 |
| Rebase 后快进 | 需要线性历史且团队熟悉重写 | 集成前需重新验证，避免重写他人分支 |

无论采用何种策略，集成者都应在目标分支最新状态上重新运行测试。Agent 在旧基线通过，不等于与其他并行任务组合后仍然通过。

### 10.10 多 Agent 的冲突预防

隔离任务目录只推迟文件冲突到集成阶段。协调者在拆任务时还要减少语义冲突。

1\. 按模块或清晰接口边界分配任务，避免两个 Agent 同时重构同一个核心类。

2\. 把共享契约变更先做成小型前置任务，例如先合并 DTO（Data Transfer Object，数据传输对象）或数据库迁移。

3\. 同一个 `pom.xml`、公共配置类和 Schema 迁移目录属于高冲突热点，明确唯一负责人。

4\. 给并行任务固定同一个基线提交；基线变化由协调者统一通知。

5\. 集成顺序按依赖拓扑决定，而不是按哪个 Agent 先结束决定。

6\. 当两个任务改变同一业务不变量时，重新拆分或串行执行，不用 Git 技术掩盖需求冲突。

### 10.11 Agent 环境的安全边界

1\. Agent 只获得任务所需的最小仓库与服务权限。

2\. 默认不向任务目录复制 `.env`、SSH（Secure Shell，安全外壳协议）私钥、云凭据、生产数据库连接和签名密钥。

3\. 对来自不可信分支的构建脚本、Git Hooks（钩子）、Maven 插件和 Gradle 插件先审查，再决定是否执行。

4\. 不允许 Agent 自行更改全局 Git 配置、全局 JDK、系统代理或凭据存储，除非任务明确授权。

5\. 需要运行不可信代码时使用沙箱、容器或临时虚拟机；Worktree 本身不是安全边界。

6\. 在日志中脱敏远程 URL、HTTP（Hypertext Transfer Protocol，超文本传输协议）Header 和环境变量，失败日志也不能泄露 Token。

7\. 对 Submodule 地址变化进行人工或策略校验，防止父仓库把 CI 引向未授权源。

8\. `git subtree pull` 和 `git subtree push` 都属于跨仓库同步操作；Agent 只有在任务明确列出 Prefix、上游和目标分支时才能执行。

### 10.12 清理 Agent 工作目录

只有提交已集成或明确放弃，并确认无待救援修改后才清理：

~~~bash
# 三项只读检查：未提交文件、尚未合入 main 的提交、已合并分支
git -C ../wt-PAY-142 status --short
git log --oneline main..agent/PAY-142-idempotency
git branch --merged main

# 只有上面的人工检查都通过后，才移除任务工作树
git worktree remove ../wt-PAY-142

# -d 只删除 Git 判定为已合并的本地分支
git branch -d agent/PAY-142-idempotency

# 最后只预览是否还有失效管理记录，不直接执行 prune
git worktree prune --dry-run --verbose
~~~

如果 `status` 非空、`main..branch` 仍有提交或分支未合并，不进入自动删除。Squash Merge（压缩合并）或 Rebase Merge（变基合并）不会保留原分支祖先关系，因此还要结合 PR 状态、目标分支内容与 Diff 人工判断是否已集成，不能只依赖 `git branch --merged`。应由集成者决定保存、推送、创建补充 PR 或放弃。

独立 clone 不是 Linked Worktree，不能用 `git worktree remove` 清理。它是完整仓库目录，删除属于破坏性文件操作；应先验证父子仓库状态和未推送提交，再按团队的归档或回收站流程处理。

## 11 Submodule、Subtree、Worktree 与 Monorepo 的组合边界

### 11.1 先接受 Git 官方限制

当前 [Git 官方 `git-worktree` 手册](https://git-scm.com/docs/git-worktree)的 BUGS（已知问题）章节明确指出：多重检出整体仍带实验性质，Submodule 支持不完整，不推荐对 Superproject 建立多个检出。手册同时规定，`git worktree move` 不能移动含 Submodule 的链接工作树，`git worktree remove` 对含 Submodule 的工作树可能要求强制参数。

这不是一句可以忽略的旧版提示。Agent 自动化会扩大初始化、更新和清理错误的影响，因此生产默认方案如下。

| 仓库状态 | 默认隔离方式 | 原因 |
| --- | --- | --- |
| 不含 Submodule | 独立分支加 Worktree | 高效且状态边界清晰 |
| 含 Subtree，不含 Submodule | 独立分支加 Worktree | Subtree 文件属于父仓库普通 Tree；上游同步另行授权 |
| 含 Submodule，只需消费 | `git clone --recurse-submodules` 的独立 clone | 避开 Superproject 多 Worktree 官方限制 |
| 含 Submodule，还要修改子仓库 | 独立 clone，加父子仓库各自任务分支 | 提交、推送和清理链路更清晰 |
| 团队坚持组合 | 仅限经过当前 Git/IDE/CI 专项验证的受控环境 | 需承担不完整支持与升级回归风险 |

不要因为本机一次 `submodule update` 成功，就推断移动、修复、多个嵌套子模块、并发 Agent 和升级后的 Git 全部可靠。

### 11.2 受控组合中的两层状态

当父仓库既使用 Submodule，又为 Agent 创建 Worktree，会同时存在两层 Git 关系。

1\. 父仓库层：每个 Agent 拥有父仓库的独立 Worktree 和任务分支。

2\. 子仓库层：每个父 Worktree 中的子模块目录需要初始化，并可能处于父仓库固定提交的 detached HEAD。

3\. 构建层：Maven/Gradle 在当前父 Worktree 中读取当前子模块源码。

因此，创建 Worktree 不等于子模块已经可构建，必须在新 Worktree 中执行 `git submodule update --init --recursive`。

### 11.3 只消费子模块的受控 Agent

大多数业务 Agent 不应该更新子模块。以下流程只作为已经通过专项验证的组合模式，不是默认生产模板：

~~~bash
# 受控模式：为父仓库创建独立任务分支和工作树
# 本章已说明该模式不作为含 Submodule 仓库的默认方案
git worktree add \
  -b agent/ORD-230-validation \
  ../wt-ORD-230 \
  <full-base-commit-id>

# 在新父工作树中初始化其固定的所有子模块
git -C ../wt-ORD-230 submodule update --init --recursive

# 确认子模块提交与父仓库 gitlink 一致
git -C ../wt-ORD-230 submodule status --recursive

# 进入任务工作树，只构建目标服务及其仓库内依赖
cd ../wt-ORD-230
./mvnw -B -ntp -pl order-service -am test
~~~

任务约束中把 `.gitmodules` 和 `components/risk-sdk/**` 设为禁止修改。Agent 不执行 `submodule update --remote`，从而保持父提交可复现。

### 11.4 需要修改子模块的受控 Agent

如果任务跨越父仓库与 `risk-sdk`，必须建立两个提交链，并遵循依赖顺序。

~~~mermaid
sequenceDiagram
    participant C as "协调者"
    participant A as "Agent Worktree"
    participant S as "risk-sdk 远程仓库"
    participant P as "order-platform 远程仓库"
    C->>A: "创建父仓库任务 Worktree"
    A->>A: "初始化父仓库记录的子模块提交"
    A->>A: "在子模块创建唯一任务分支"
    A->>A: "修改并测试子模块"
    A->>S: "先推送子模块提交"
    A->>A: "父仓库记录新的 gitlink 并全量验证"
    A->>P: "再推送父仓库提交"
    C->>P: "审查父仓库变更"
    C->>S: "确认子仓库提交可达且已审查"
~~~

以下假设父任务分支名为 `agent/RISK-87-parent`。具体命令：

~~~bash
# 进入当前父任务目录里的 risk-sdk 子仓库
cd ../wt-RISK-87/components/risk-sdk

# 为子仓库创建独立任务分支，避免在 detached HEAD 上开发
git switch -c agent/RISK-87-timeout

# 第一阶段：验证、提交并先推送子仓库源码
./mvnw -B -ntp verify
git add .
git commit -m "fix: normalize timeout result"
git push -u origin agent/RISK-87-timeout

# 把子仓库提交标识保存在 Shell 变量中，供交付报告记录
RISK_COMMIT=$(git rev-parse HEAD)

# 第二阶段：回到父仓库，验证父子源码组合
cd ../..
./mvnw -B -ntp verify

# 父仓库只暂存 risk-sdk 的新 gitlink
git add components/risk-sdk
git diff --cached --submodule
git commit -m "build: use normalized risk timeout result"

# --recurse-submodules=check 拒绝推送引用了远程不可达子提交的父提交
git push -u --recurse-submodules=check origin agent/RISK-87-parent
~~~

`RISK_COMMIT` 应写入交付报告，用来连接两个仓库的审查记录。父仓库 PR 说明中应链接子仓库 PR，并明确合并顺序。

### 11.5 并行 Agent 同时修改同一 Submodule

仅仅给父仓库创建不同 Worktree，并不能消除子仓库的语义冲突。每个 Agent 仍应在自己的子模块工作目录中使用唯一子仓库分支，例如：

~~~text
父任务 A：agent/ORDER-301
子任务 A：agent/RISK-301

父任务 B：agent/ORDER-302
子任务 B：agent/RISK-302
~~~

如果两个子任务同时修改 `RiskClient`，协调者应先定义接口基线或改为串行集成。两个父仓库最终引用不同子仓库提交时，父分支合并会产生 gitlink 冲突或最后写入覆盖，必须由人理解子仓库提交图后解决。

### 11.6 组合场景的清理

官方 `git worktree remove` 文档说明，含 Submodule 的 Worktree 需要额外处理，直接删除可能要求 `--force`。先检查并停用子模块，再尝试普通移除：

~~~bash
# 先检查父工作树是否有未提交文件
git -C ../wt-RISK-87 status --short

# 再进入每个子模块检查分支和修改；foreach 会递归执行引号内命令
git -C ../wt-RISK-87 submodule foreach --recursive 'git status --short --branch'

# 确认无待救援修改后，停用当前工作树中的全部子模块
git -C ../wt-RISK-87 submodule deinit --all

# 最后尝试普通移除；若拒绝，不应直接追加 --force
git worktree remove ../wt-RISK-87
~~~

如果普通移除仍拒绝，不把 `--force` 写入无人值守清理脚本。先确认子模块不存在未提交或未推送提交，再由维护者决定处理方式。`deinit --force` 和 `worktree remove --force` 都可能让恢复成本显著增加。频繁遇到这个问题，说明该项目应回到独立 clone 默认方案。

### 11.7 Subtree 与 Worktree 为什么可以按普通仓库使用

Subtree Prefix 在父仓库中只是普通 Tree 和 Blob，因此不同 Worktree 会像检出其他业务目录一样，分别得到各自分支版本的 Subtree 文件。它不触发 Superproject 加 Submodule 的不完整支持限制，也不需要在每个 Worktree 再运行初始化命令。

但“文件检出正常”不等于“可以并发同步上游”。多个 Worktree 共享父仓库的分支、Remote 和对象库，两个 Agent 同时执行 `subtree pull` 或向同一上游分支执行 `subtree push`，仍可能产生重复导入、非快进拒绝或语义冲突。团队应指定一个同步所有者，其他 Agent 只提交本任务的普通文件变化。

~~~bash
# 在任务 Worktree 中证明 Prefix 是普通 Tree；应看到 040000 tree
git -C ../wt-PAY-142 ls-tree HEAD components/risk-rules

# 只查看本任务对 Subtree 的文件变化，不执行任何远程同步
git -C ../wt-PAY-142 diff -- components/risk-rules

# 交付前列出只影响 Prefix 的提交，供同步所有者评审
git -C ../wt-PAY-142 log --oneline \
  <full-base-commit-id>..HEAD -- components/risk-rules
~~~

若一个仓库同时包含 Submodule 和 Subtree，应按更严格的 Submodule 默认策略选择独立 clone；Subtree 的普通文件性质不能消除同一父仓库中的 Submodule 限制。

### 11.8 Monorepo、多模块与 Worktree 的组合

Monorepo 和普通多模块项目对 Git 来说仍是普通仓库，因此非常适合“一任务一 Worktree”。Maven Module 或 Gradle Subproject 不会得到独立 `HEAD`、Index 或分支；整个仓库的所有模块随当前 Worktree 的分支一起检出。

| 状态 | 隔离单位 | 实际影响 |
| --- | --- | --- |
| Git 分支与提交 | 整个 Worktree | 不能让每个 Maven 模块各自切不同分支 |
| 源码和构建输出 | Worktree 内各目录 | 两个 Agent 的 `target/` 或 `build/` 不直接覆盖 |
| Maven/Gradle 模块依赖 | 当前构建图 | `-pl` 或项目路径只选择任务，不创建 Git 隔离 |
| 根 POM、Settings 与 Wrapper | 全仓库共享入口 | 并行任务应指定唯一修改者并扩大验证 |
| Submodule | 独立仓库边界 | 只要 Monorepo 内含 Submodule，仍采用更严格的独立 clone 默认策略 |

大型 Monorepo 即使只修改一个模块，普通 Worktree 默认仍检出完整文件树。Sparse Checkout（稀疏检出）可以减少工作文件，但会引入路径可见性和工具兼容性问题；初学者先使用完整 Worktree，确认构建图和任务边界正确后再专项评估。

## 12 生产工程：可复现构建、安全、性能与治理

### 12.1 Java 可复现构建的七层固定

Submodule 通过 gitlink 固定子仓库提交，Subtree 通过父仓库快照固定导入文件。一个可复现 Java 构建还需要固定更多输入。

| 层次 | 应固定的内容 | 常见手段 |
| --- | --- | --- |
| 父源码 | 父仓库提交 | CI Checkout 精确 Commit ID（提交标识） |
| 子源码 | 各 Submodule 提交 | 父仓库 gitlink |
| 导入源码 | 各 Subtree Prefix 的文件与上游来源 | 父仓库 Tree，加版本化来源清单 |
| JDK | 发行版与版本 | Toolchain、容器镜像、SDK 管理 |
| 构建工具 | Maven/Gradle 版本 | Maven Wrapper/Gradle Wrapper |
| 依赖与插件 | 坐标、仓库与校验信息 | Lock、校验、企业制品仓库 |
| 外部生成输入 | OpenAPI、数据库 Schema、代码生成器 | 版本化输入与生成器版本 |

仅说“代码已经固定”不能证明二进制可复现。SNAPSHOT 依赖、动态 Gradle 版本、可变容器标签、构建时间戳和外部代码生成服务都会引入漂移。

### 12.2 CI 的推荐阶段

~~~mermaid
flowchart LR
    A["Checkout 父提交"] --> B["初始化固定 Submodule（如有）"]
    B --> C["输出父提交、子提交与 Subtree 来源清单"]
    C --> D["验证来源、许可证与权限策略"]
    D --> E["Maven/Gradle 构建与测试"]
    E --> F["SAST/SCA/SBOM"]
    F --> G["制品签名与发布"]
    G --> H["保存源码到制品映射"]
~~~

其中 SAST（Static Application Security Testing，静态应用安全测试）、SCA（Software Composition Analysis，软件成分分析）与 SBOM（Software Bill of Materials，软件物料清单）不能因为源码来自 Submodule 或 Subtree 就省略。

CI 中建议输出：

~~~bash
# 记录父仓库构建所用的精确提交
git rev-parse HEAD

# 记录每个子模块实际使用的提交
git submodule status --recursive

# 记录受管 Subtree 在当前父提交中的 Tree 标识
git ls-tree HEAD components/risk-rules

# 记录远程来源；正式 CI 应先确保输出不会泄露凭据
git remote -v

# 记录 Maven Wrapper 与其使用的 Java 环境
./mvnw --version
java -version

# 最后执行项目标准验证，退出码非 0 即失败
./mvnw -B -ntp verify
~~~

远程地址输出可能含敏感信息时应先由 CI 平台脱敏，或仅输出主机和仓库标识。

### 12.3 Submodule 的供应链安全

1\. `.gitmodules` 进入代码评审范围，URL 变化视为供应链变更。

2\. URL 中不得硬编码用户名、Token 或密码；凭据由受控凭据管理器提供。

3\. CI 使用只读 Deploy Key（部署密钥）或短期令牌读取子仓库，非必要不授予写权限。

4\. 父仓库只引用远程可达且已审查的子仓库提交。

5\. 对高安全项目，在 CI 验证允许的子模块主机、路径、签名提交或签名标签。

6\. 不可信 PR 可以修改 `.gitmodules`；在向子模块操作暴露凭据前，先执行策略校验。

7\. 第三方 Java 库若可使用标准制品，应优先走 Maven/Gradle 供应链扫描，而不是仅靠 Submodule 固定源码。

一个简单的允许列表检查思路如下，实际项目应处理嵌套模块和 URL 规范化：

~~~bash
# 从 .gitmodules 读取所有“子模块名 -> URL”，并逐行交给 while
git config -f .gitmodules --get-regexp '^submodule\..*\.url$' |
while read -r key url; do
  # case 根据 URL 模式决定允许还是拒绝
  case "$url" in
    # 只允许经过批准的主机和路径前缀
    https://git.example.invalid/platform/*) ;;

    # 星号匹配所有其他地址；向标准错误输出原因并让脚本失败
    *)
      echo "Rejected submodule URL for $key" >&2
      exit 1
      ;;
  esac
done
~~~

该脚本只说明策略入口，不是完整安全产品。SSH 别名、相对 URL、URL 编码和重定向都需要按团队威胁模型处理。

### 12.4 Subtree 的来源与供应链治理

Subtree 的 Java 文件已经进入父仓库，静态扫描和 SBOM 不能把它当成“内部代码”而忽略其外部来源。与 Submodule 不同，仓库里没有标准 `.gitmodules` 文件自动声明上游，团队应维护版本化的 Subtree 清单。

| 必填字段 | 示例 | 作用 |
| --- | --- | --- |
| Prefix | `components/risk-rules` | 确定受管目录边界 |
| Upstream | `https://git.example.invalid/platform/risk-rules.git` | 审计来源；真实凭据不能写入 URL |
| Ref | `main` 或批准的 Tag | 说明同步入口 |
| Imported Commit | 上游完整 Commit ID | 在 `--squash` 时补足来源追溯 |
| History Policy | `squash` 或 `full` | 避免维护者混用历史策略 |
| Push Policy | `read-only` 或指定维护者 | 限制回推权限 |
| License/Owner | 许可证和责任团队 | 满足合规与漏洞响应 |

生产规则如下。

1\. `subtree pull` 视为供应链升级：评审来源 Commit、完整 Diff、许可证、依赖和构建脚本后再合并。

2\. CI 默认只读，不执行 `subtree push`；回推由获得上游写权限的维护流程完成。

3\. 使用 `--squash` 时，在提交说明或清单中记录上游 Commit，不能只写“update subtree”。

4\. 使用 CODEOWNERS（代码所有者）或等价机制保护 Prefix 与同步清单，避免普通业务 PR 静默修改来源策略。

5\. 不可信上游可能带入 Maven 插件、Gradle 脚本、Git Hooks 或生成器；同步后先审查，再在隔离环境执行。

6\. 删除 Subtree 不会从旧提交中清除敏感内容或不兼容许可证；涉及历史清理时按独立安全事件处理。

### 12.5 Worktree 的共享状态风险

Worktree 共享 Git 对象和引用，因此以下操作不是“只影响当前目录”。

1\. 删除或重命名未检出的分支会被所有 Worktree 看见。

2\. 修改 `origin` URL、fetch refspec 或仓库级 Git 配置通常影响所有 Worktree。

3\. `git fetch --prune` 会更新共享远程跟踪引用。

4\. Git Hooks 与模板的实际位置可能共享；Agent 不应未经审查修改。

5\. `git gc`、对象清理和仓库损坏影响整个 Worktree 集合。

6\. Stash 列表属于仓库级可见状态，多个 Agent 使用 `stash@{0}` 容易取错。

因此，Agent 可以在自己的工作目录自由编辑，但对引用删除、远程配置、维护命令和全局配置必须执行更严格授权。

### 12.6 构建性能与磁盘治理

Worktree 共享 Git 对象，但不会共享工作文件和 Java 构建输出。大型仓库中，每个 Worktree 的 `target/`、`build/`、前端依赖与 IDE 索引可能远大于 Git 对象库。

建议如下。

1\. 监控 Worktree 数量、磁盘使用、最后活动时间和关联任务。

2\. Agent 构建默认执行必要模块，集成阶段再执行全量验证。

3\. 使用 Maven/Gradle 缓存时，避免多个任务发布相同 SNAPSHOT 坐标。

4\. 把数据库、容器卷、测试报告和日志目录加入任务标识，避免并发覆盖。

5\. 定期执行 `git worktree list --porcelain` 盘点，不以直接扫描目录名代替 Git 记录。

6\. 自动清理必须先验证分支合并状态、工作目录状态和任务保留策略。

### 12.7 团队治理指标

可以观察以下指标判断方案是否健康。

| 指标 | 异常信号 | 改进方向 |
| --- | --- | --- |
| 子模块初始化失败率 | 权限、URL 或目标提交经常不可达 | 统一地址、权限与推送顺序检查 |
| Subtree 来源漂移率 | 清单 Commit 与实际同步说明不一致 | 自动校验 Prefix、来源和导入提交 |
| Subtree 回推冲突率 | 多人双向同步或混合提交过多 | 单一同步所有者与按目录拆分提交 |
| 父子仓库跨 PR 等待时间 | 边界过度耦合 | 评估制品依赖或 Monorepo |
| Worktree 遗留数量 | 清理流程缺失 | 任务结束自动提醒与安全盘点 |
| Agent 合并冲突率 | 任务切分重叠 | 模块所有权与基线协调 |
| Agent 测试返工率 | 验收契约模糊 | 固定测试命令与证据格式 |
| 并行构建资源峰值 | IDE/构建进程过多 | 并发上限与分层测试 |

技术选择应该接受持续复盘。如果 Submodule 的协调成本长期超过独立版本价值，或 Subtree 的双向同步冲突长期高企，应迁移到制品依赖、Monorepo 或重新设计仓库边界。

### 12.8 Monorepo 与多模块项目的生产治理

Monorepo 最重要的生产能力不是“把目录放在一起”，而是根据构建图安全地管理影响范围。

1\. 仓库根、每个构建根和每个部署单元都要有清晰清单；不能默认三者相同。

2\. 公共模块的变化按依赖图触发下游验证。只检查变化文件所在目录，会漏掉未修改但被影响的调用方。

3\. 根 POM、`settings.gradle(.kts)`、Wrapper、构建约定与版本目录属于全仓库高风险输入，使用代码所有者和全量构建保护。

4\. 一个 Git 提交可以原子地修改多个模块，但部署不是天然原子操作。每个服务仍需独立制品标识、发布记录、兼容窗口和回滚方案。

5\. 对大型仓库记录局部构建命中率、缓存命中率、全量构建时长、公共模块扇出与不稳定测试，避免性能优化演变为漏测。

6\. Agent 只获得任务相关路径与命令权限；修改公共接口、数据库 Schema 或根构建配置时，协调者扩大验证范围并安排唯一写入者。

| 变更类型 | 推荐快速反馈 | 合入前最低兜底 |
| --- | --- | --- |
| 叶子服务内部实现 | 服务模块单元测试 | 服务集成测试与主线 CI |
| 仓库内公共库实现 | 公共库加所有受影响下游 | 依赖图覆盖或全量构建 |
| 公共 API/DTO | 提供方和全部消费者编译、契约测试 | 全量构建与兼容性评审 |
| 根构建配置或 Wrapper | 本地验证构建模型 | 全量构建与供应链检查 |
| 多个独立构建根的共享脚本 | 每个被调用构建根 | 仓库级治理流水线 |

## 13 故障排查：从状态证据定位而不是盲目重试

### 13.1 通用排查顺序

1\. 确认当前目录：`pwd` 与 `git rev-parse --show-toplevel`。

2\. 确认父仓库分支和修改：`git status --short --branch`。

3\. 确认所有 Worktree：`git worktree list --porcelain`。

4\. 确认子模块状态：`git submodule status --recursive`。

5\. 确认子仓库自身状态：`git -C <path> status --short --branch`。

6\. 使用 `git ls-tree HEAD <path>` 判断目标是 `160000 commit` 的 Submodule，还是 `040000 tree` 的普通目录/Subtree。

7\. 涉及 Subtree 时，再核对团队清单中的 Prefix、上游、Ref、导入 Commit 与 `--squash` 策略。

8\. 再根据证据执行 `update`、`sync`、`subtree pull`、`repair` 或冲突解决。

不要把 `reset --hard`、强制更新、强制删除或直接清理目录当成第一步。它们可能消灭最有价值的故障证据和未提交工作。

### 13.2 Submodule 常见故障速查

| 现象 | 机制原因 | 正确处理与验证 |
| --- | --- | --- |
| 子模块目录为空 | 只克隆了父仓库，尚未初始化 | `git submodule update --init --recursive`，再看 `submodule status` |
| 状态前缀为 `-` | 子模块未初始化 | 执行带 `--init` 的更新 |
| 状态前缀为 `+` | 当前子模块 `HEAD` 与父仓库记录不一致 | 比较两者，决定恢复固定提交还是提交升级 |
| 状态前缀为 `U` | gitlink 合并冲突 | 理解两个候选子仓库提交后手工选择或合并 |
| detached HEAD | 默认按固定提交检出 | 只消费时接受；开发时创建本地分支 |
| `not our ref` 或找不到提交 | 父仓库引用的子提交未推送、远程错误或浅历史不足 | 检查 URL、fetch、目标提交远程可达性 |
| URL 已改但仍访问旧地址 | 本地 `.git/config` 尚未同步 | `git submodule sync --recursive` 后更新 |
| `update` 拒绝覆盖 | 子模块有本地修改 | 进入子模块检查、提交或安全保存，不强制覆盖 |
| Maven 找不到模块 | 子模块未初始化或 POM 路径/模型不兼容 | 先查目录与 `submodule status`，再查 Maven Reactor |
| 父仓库显示子模块 modified | 子模块提交漂移或内部有未提交修改 | `git diff --submodule` 与子仓库 `status` |

### 13.3 解决 gitlink 合并冲突

父分支 A 记录子仓库提交 R10，父分支 B 记录 R12，Git 不一定知道两者应该选谁。先查看冲突阶段：

~~~bash
# 查看冲突路径在 Index 的第 1、2、3 阶段记录
# 常见情况下，第 2 阶段来自 ours，第 3 阶段来自 theirs
git ls-files -u components/risk-sdk

# 获取子仓库远程历史，确保后续能判断两个候选提交的祖先关系
git -C components/risk-sdk fetch origin

# 用图形化短日志同时观察所有本地和远程引用，最多显示 20 条
git -C components/risk-sdk log --oneline --graph --all --decorate -20
~~~

处理方式有三种。

1\. 如果 R12 已包含 R10 且符合目标，检出 R12。

2\. 如果 R10 与 R12 属于不同子仓库分支，在子仓库创建合并提交 R13，完成子仓库测试并先推送。

3\. 如果其中一方不应进入父分支，明确选择另一方，而不是机械选 “ours” 或 “theirs”。

记录选择：

~~~bash
# 把子仓库检出到人工判断后的正确提交；占位符必须替换
git -C components/risk-sdk switch --detach <resolved-risk-commit>

# 回到父仓库暂存该提交指针，表示 gitlink 冲突已经解决
git add components/risk-sdk

# 提交前再次确认暂存的是预期的旧、新子提交
git diff --cached --submodule

# 打开编辑器填写合并提交说明；也可以按团队规范使用 -m
git commit
~~~

完成后运行父仓库全量相关测试，并确认目标子提交远程可达。

### 13.4 救援 detached HEAD 上的子仓库提交

如果误在 detached HEAD 上提交，提交并没有立刻消失。先创建分支保留它：

~~~bash
# 先确认子仓库是否处于 detached HEAD，以及有没有未提交文件
git -C components/risk-sdk status --short --branch

# 查看刚创建的提交标识，确认要救援的是哪一个提交
git -C components/risk-sdk log -1 --oneline

# 从当前提交创建分支，让该提交获得稳定、可见的引用
git -C components/risk-sdk switch -c rescue/risk-local-change
~~~

如果已经切换离开，使用 reflog 定位：

~~~bash
# reflog 记录本地 HEAD 曾经到过的位置，可用于找到已离开的提交
git -C components/risk-sdk reflog

# 把 <commit> 替换为 reflog 找到的提交，再创建救援分支
git -C components/risk-sdk branch rescue/risk-local-change <commit>
~~~

确认分支可见并推送后，再更新父仓库 gitlink。

### 13.5 Subtree 常见故障速查

| 现象 | 机制原因 | 正确处理与验证 |
| --- | --- | --- |
| `git: 'subtree' is not a git command` | 当前 Git 安装包未包含 `contrib/subtree` | 使用团队批准且与 CI 一致的 Git 发行版，记录 `git --version` |
| `add` 提示 Prefix 已存在 | 目标目录已经有文件或曾被导入 | 先确认历史和同步策略，不通过移动/覆盖文件绕过检查 |
| 普通 clone 后找不到上游 URL | Subtree 没有 `.gitmodules` | 阅读版本化 Subtree 清单；使用者只消费时不需要上游 URL |
| 上游更新后父仓库没有变化 | Subtree 不会自动跟踪上游 | 由同步所有者执行带正确 Prefix、仓库和 Ref 的 `subtree pull` |
| `pull` 产生文件冲突 | 父仓库和上游修改了相同内容 | 像普通 Merge 一样理解两侧变化、解决冲突并运行测试 |
| `split -b` 提示分支已存在 | `--branch` 要求目标分支尚不存在 | 换新的临时分支名，或先确认旧分支能否按团队流程删除 |
| `split` 很慢 | 需要遍历并重建 Prefix 的合成历史 | 减少混合提交；大型仓库评估固定同步流水线和 `--rejoin` |
| `push` 被非快进拒绝 | 上游在当前基线之后产生了新提交 | 先 `subtree pull` 集成上游，测试后再推送；不强制覆盖 |
| 推向本地普通仓库被拒绝 | 目标分支正在该仓库工作目录中检出 | 推向裸仓库/托管远程；不要关闭保护制造不一致状态 |
| Maven 找不到导入模块 | Git 文件已存在，但父构建未声明 Module/Dependency | 分别检查 `ls-tree`、父 POM/Gradle 设置和命令行构建 |
| `--squash` 后难以定位来源 | 父历史只保留汇总导入 | 查同步清单和提交说明中的上游 Commit；补齐审计信息 |

排障时不要手工复制一份新上游目录覆盖 Prefix。这样可能暂时让文件“看起来正确”，却破坏后续 `split`、合并历史和来源审计。

### 13.6 Worktree 常见故障速查

| 现象 | 机制原因 | 正确处理与验证 |
| --- | --- | --- |
| 分支已在其他 Worktree 检出 | Git 防止同一分支被多个工作目录并发使用 | `git worktree list` 找到目录，或创建新任务分支 |
| Worktree 显示 locked | 人工锁定或创建时锁定 | 查看原因，确认安全后 `git worktree unlock` |
| Worktree 显示 prunable | 目录缺失但管理记录还在 | 先 `prune --dry-run`，确认后 prune |
| 手工移动后命令失败 | 管理记录仍指向旧路径 | `git worktree repair <new-path>` |
| `remove` 拒绝 | 有修改、未跟踪文件或已初始化 Submodule | 普通 Worktree 先保存状态；含 Submodule 时按第 11.6 节人工处理 |
| 两个任务互相看到分支变化 | 分支引用本来就共享 | 不等于文件污染；检查各自 `HEAD` 和目录 |
| 构建端口冲突 | Worktree 不隔离进程资源 | 分配唯一端口、Profile 和容器 |
| 测试数据互相污染 | 共享数据库或消息系统 | 独立 Schema、Topic、Consumer Group |
| Maven SNAPSHOT 行为漂移 | 多任务共享本地仓库并 install 同坐标 | 唯一版本、独立本地仓库或避免 install |

### 13.7 IntelliJ IDEA 把两个 Worktree 当成同一项目

常见原因是仓库错误提交了 `.idea/workspace.xml`，其中的项目标识随 Worktree 被复制。处理顺序如下。

1\. 关闭相关 IDEA 窗口。

2\. 确认 `workspace.xml` 没有需要保留的个人配置。

3\. 从版本控制中移除该文件并加入 `.gitignore`；已提交的团队配置应只保留适合共享的部分。

4\. 在受影响 Worktree 删除本地 `workspace.xml`，重新打开让 IDE 生成新标识。

5\. 使用 `git status` 确认本地 IDE 文件不会进入业务提交。

### 13.8 “本地成功、CI 失败”的定位

优先比较以下差异。

1\. CI 是否初始化了所有嵌套 Submodule。

2\. CI 身份是否有父子仓库读取权限。

3\. 子仓库目标提交是否已推送到 CI 使用的远程。

4\. 本地是否误用了 `update --remote`，而 CI 使用父仓库固定提交。

5\. Subtree Prefix 是否已经进入父仓库提交，而不是只存在于本地工作目录；CI 普通 clone 不会另行获取上游。

6\. IDE 是否通过已安装的 SNAPSHOT 提供了依赖，而干净 CI 必须从当前 Reactor 构建。

7\. JDK、Maven/Gradle Wrapper、Profile、环境变量和数据库是否一致。

8\. 浅克隆是否缺少父仓库记录的旧子提交、Subtree 拆分所需历史或标签历史。

9\. Agent 是否只运行了单元测试，而 CI 运行集成测试、格式或安全检查。

### 13.9 Monorepo 与多模块项目常见故障

| 现象 | 先收集的证据 | 处理方向 |
| --- | --- | --- |
| Maven 日志没有目标模块 | `pwd`、根 POM 的 `<modules>`、Reactor Build Order | 从聚合根执行，修正模块路径 |
| 仓库内依赖被当成远程制品下载 | 使用方 POM 坐标与 Reactor 中模块坐标 | 对齐 groupId、artifactId、version，并用 `-am` |
| Gradle 只显示根项目 | `./gradlew projects` 与 `settings.gradle(.kts)` | 补充 `include`，从正确根刷新 |
| IDE 识别模块但 CI 找不到 | IDE 手工模块配置与构建文件差异 | 删除“只在本机成立”的配置，修正 POM/Gradle 文件 |
| 局部构建成功、全量失败 | 修改模块的下游依赖和根插件 | 扩大到受影响模块；公共变化保留全量兜底 |
| 一个小修改触发全仓构建 | 根文件或高扇出公共模块发生变化 | 先确认这是正确影响，再优化缓存和任务拆分 |
| 模块之间出现循环依赖 | Maven/Gradle 依赖图 | 提取稳定的低层接口，禁止双向业务依赖 |
| 同一提交成功但分批部署失败 | 服务间协议缺少兼容窗口 | 使用向后兼容接口、分阶段发布和独立回滚 |

## 14 命令与状态参考

### 14.1 Submodule 高频命令

| 命令 | 用途 | 关键边界 |
| --- | --- | --- |
| `git submodule add <url> <path>` | 添加子模块 | 会修改 `.gitmodules` 并暂存 gitlink |
| `git clone --recurse-submodules <url>` | 克隆父仓库并递归初始化 | 凭据必须覆盖所有子仓库 |
| `git submodule update --init --recursive` | 恢复父提交期望的子模块状态 | 默认可能是 detached HEAD |
| `git submodule status --recursive` | 查看期望与当前状态 | 注意 `-`、`+`、`U` 前缀 |
| `git submodule sync --recursive` | 同步 URL 配置 | URL 变更后常与 update 连用 |
| `git submodule update --remote <path>` | 按配置的远程分支推进 | 需要父仓库显式提交新指针 |
| `git submodule set-branch --branch <b> <path>` | 设置远程更新分支 | 不改变“父仓库记录提交”的本质 |
| `git diff --submodule` | 显示子模块提交范围 | 用于评审依赖升级 |
| `git push --dry-run --recurse-submodules=check <remote> <branch>` | 预演父仓库推送并检查相关子提交 | `--dry-run` 才保证不发送父仓库更新 |
| `git push --recurse-submodules=check <remote> <branch>` | 推送父仓库，并在发送前检查相关子提交 | 这是实际推送，不是只读检查 |
| `git submodule foreach --recursive '<cmd>'` | 对所有子模块执行只读检查或维护 | 谨慎执行修改性命令 |
| `git submodule deinit <path>` | 当前工作树停用子模块 | 不等于从仓库历史删除 |
| `git rm <path>` | 从父仓库删除子模块定义 | 提交前检查 `.gitmodules` 与 gitlink |

### 14.2 Subtree 高频命令

| 命令 | 用途 | 关键边界 |
| --- | --- | --- |
| `git subtree add --prefix=<path> <repo> <ref>` | 首次导入上游到普通子目录 | Prefix 必须明确；自动创建导入提交 |
| `git subtree add ... --squash` | 以汇总提交导入 | 需另外记录实际上游 Commit |
| `git subtree pull --prefix=<path> <repo> <ref>` | 获取并合并上游新变化 | 自动创建合并；运行前保持工作目录清晰 |
| `git subtree merge --prefix=<path> <commit>` | 合并已在本地可见的上游提交 | 不负责 fetch；冲突按普通 Merge 处理 |
| `git subtree split --prefix=<path>` | 输出只包含 Prefix 的合成历史 HEAD | 成功时标准输出是一个 Commit ID |
| `git subtree split --prefix=<path> -b <branch>` | 创建可人工评审的拆分分支 | 分支必须尚不存在 |
| `git subtree push --prefix=<path> <repo> <refspec>` | 拆分并推送到上游 | 修改远程状态，需授权且可能被非快进拒绝 |
| `git subtree ... -P <path>` | `--prefix=<path>` 的短写 | 团队脚本建议统一一种写法 |
| `git subtree split --prefix=<path> --annotate='<text>'` | 给合成提交说明加前缀 | 后续重复 split 必须保持相同 annotation |
| `git subtree split --prefix=<path> --rejoin` | 把合成历史重新并入父历史以优化后续拆分 | 会增加可见提交；与 squash 策略保持一致 |
| `git subtree split --prefix=<path> --onto=<commit>` | 对非 `subtree add` 导入的旧历史指定连接点 | 迁移型高级选项，不用于普通日常同步 |

`add`、`pull` 和 `merge` 主要把上游变化带入父仓库；`split` 和 `push` 主要把 Prefix 变化变成独立上游历史。这两组方向不要混记。

### 14.3 Worktree 高频命令

| 命令 | 用途 | 关键边界 |
| --- | --- | --- |
| `git worktree add -b <branch> <path> <start>` | 创建任务分支和链接工作树 | 路径建议与主项目同级 |
| `git worktree add --detach <path> <commit>` | 创建只读审查/构建目录 | 提交修改前先建分支 |
| `git worktree list --porcelain` | 机器可读地列出所有 Worktree | 自动化盘点的标准入口 |
| `git worktree move <old> <new>` | 安全移动普通链接 Worktree | 不能移动主工作树或含 Submodule 的链接工作树 |
| `git worktree lock --reason <r> <path>` | 防止离线路径被清理 | 恢复后记得 unlock |
| `git worktree remove <path>` | 移除干净链接工作树 | 主工作树不能移除 |
| `git worktree repair <path>` | 修复手工移动导致的连接 | 先确认实际路径 |
| `git worktree prune --dry-run` | 预览失效管理记录 | 先预览再清理 |
| `git config extensions.worktreeConfig true` | 启用 Worktree 专属配置 | 需要评估 Git 版本兼容性 |
| `git config --worktree <key> <value>` | 设置当前 Worktree 配置 | 不是业务环境配置的首选 |

### 14.4 常用机器可读状态

自动化脚本应尽量使用稳定、机器可读的输出：

~~~bash
# v2 提供结构化字段；-z 用 NUL 分隔记录，可安全处理空格和换行文件名
git status --porcelain=v2 -z

# 所有 Worktree：每个字段独占一行，适合自动化盘点
git worktree list --porcelain

# 所有子模块：同时保留 -、+、U 等状态前缀
git submodule status --recursive

# 判断受管目录是 gitlink 还是普通 Tree，并记录精确对象标识
git ls-tree HEAD components/risk-rules

# 只输出 Subtree 拆分后的 Commit ID；--quiet 减少进度信息
# 该命令会计算合成历史，不能把它当成廉价的每秒状态探针
git subtree split --quiet --prefix=components/risk-rules

# 文件名供脚本消费时也使用 NUL 分隔，不能再按文本行读取
git diff --name-only -z

# 只输出已经进入暂存区的文件名，同样使用 NUL 分隔
git diff --cached --name-only -z

# 输出当前工作树根目录
git rev-parse --show-toplevel

# 输出当前 Worktree 自己的 Git 管理目录
git rev-parse --git-dir

# 输出所有 Worktree 共享的 Git 公共管理目录
git rev-parse --git-common-dir
~~~

不要解析带颜色、国际化文本或面向人的默认 `git status` 来做关键自动化决策。使用 `-z` 后，消费者必须按 NUL（Null Character，空字符）拆分，不能用普通的逐行读取；人工查看时再使用 `git status --short --branch`。

### 14.5 团队可选配置

~~~bash
# 让 git status 显示子模块提交摘要；1 表示启用
git config status.submoduleSummary 1

# 让当前克隆中的多个 Git 命令默认递归考虑子模块
# 团队脚本仍建议显式写 --recursive，减少对本机配置的依赖
git config submodule.recurse true

# 开启 Worktree 专属配置扩展；旧 Git 可能不认识该扩展
git config extensions.worktreeConfig true
~~~

配置有 system、global、local、worktree 等作用域。Agent 修改前必须说明作用域，避免把当前任务偏好写入用户全局配置。

### 14.6 Maven 与 Gradle 多模块命令

| 命令 | 用途 | 关键边界 |
| --- | --- | --- |
| `./mvnw -B -ntp verify` | 从 Maven 聚合根验证全部模块 | 以 Reactor Summary 判断模块是否完整 |
| `./mvnw -B -ntp -pl <module> -am test` | 测试目标模块及其仓库内依赖 | 不会自动覆盖所有下游消费者 |
| `./mvnw -B -ntp -pl <module> -amd test` | 选择模块及依赖它的 Reactor 模块 | `-amd` 是 also-make-dependents，范围仍受当前 Reactor 限制 |
| `./mvnw -f <pom> ...` | 明确使用哪个 POM 作为入口 | 路径相对当前 Shell 目录 |
| `./gradlew projects` | 列出 Gradle 当前构建识别的项目 | 用于发现打开或执行了错误构建根 |
| `./gradlew build` | 执行当前 Multi-Project Build 的构建 | 多个独立构建根需分别执行 |
| `./gradlew :<project>:build` | 运行指定子项目的构建任务 | 项目依赖会带入必要任务，但不等于全仓测试 |
| `./gradlew :<project>:dependencies` | 查看指定项目依赖图 | 输出较长时按配置选择，例如 `--configuration runtimeClasspath` |

`./mvnw` 和 `./gradlew` 都表示仓库提交的 Wrapper。若项目尚未提供 Wrapper，可在受控环境临时使用全局工具，但生产应先建立版本固定和校验策略。

## 15 面试递归追问与回答框架

### 15.1 从定义追问到底层对象

| 追问 | 回答要点 | 可能继续追问 |
| --- | --- | --- |
| Submodule 是什么 | 父仓库以 gitlink 固定另一个独立仓库的提交，并用 `.gitmodules` 描述路径与 URL | gitlink 为何是 `160000`；父仓库是否保存子文件 |
| 为什么常是 detached HEAD | 为了精确复现父仓库记录的子提交 | 如何安全开发子模块；误提交如何救援 |
| Subtree 是什么 | 把上游项目导入父仓库普通子目录，并可从 Prefix 重建独立合成历史 | 为什么显示 `040000 tree`；是否需要 `.gitmodules` |
| Submodule 与 Subtree 的本质区别 | 前者保存外部提交指针且历史/对象库独立；后者保存导入文件并把同步关系交给命令与团队约定 | clone、权限、仓库体积与双向同步取舍 |
| Monorepo 是什么 | 多个应用或库共享一个 Git 仓库和提交历史；它不是特殊 Git 对象 | 是否一定统一构建、统一发布或是单体应用 |
| Monorepo 与 Maven 多模块有何区别 | 前者是仓库边界，后者是构建边界；一个 Monorepo 可有多个构建根，一个小项目也可只有一个多模块构建 | 构建根、部署单元和权限如何设计 |
| Worktree 是什么 | 同一仓库的多个工作目录，拥有独立 `HEAD` 和 Index | 哪些引用共享；`.git` 为什么可能是文件 |
| Worktree 与 clone 区别 | Worktree 共享对象、引用和默认配置，clone 默认独立 | 隔离强度、磁盘和安全取舍 |

### 15.2 从日常操作追问到一致性

| 追问 | 回答要点 | 可能继续追问 |
| --- | --- | --- |
| 为什么父仓库 pull 后子模块没变 | 普通 pull 更新父文件和 gitlink，不一定更新子工作目录 | `--recurse-submodules` 与 `submodule.recurse` |
| `update --remote` 后为什么还要父仓库 commit | 远程更新只移动当前子模块检出，父仓库快照仍需记录新 gitlink | CI 为什么不应盲目 remote update |
| 为什么先推子仓库 | 父提交公开后必须能获取其引用的子提交 | `push --recurse-submodules=check` |
| gitlink 冲突如何解决 | 先理解子仓库提交关系，选择或创建正确子提交，再在父仓库 `git add` | 为什么 ours/theirs 可能错误 |
| 为什么普通 clone 能得到 Subtree | 文件已经是父仓库 Commit 的普通 Tree 与 Blob | 为什么上游 URL 不会自动出现 |
| `subtree pull` 与普通 pull 有何区别 | 前者显式获取上游并合并到 Prefix；后者只更新父仓库当前分支 | 如何固定 Prefix、Ref 与 squash 策略 |
| `subtree split` 做了什么 | 从父历史生成只包含 Prefix、且目录提升到根的合成历史 | 重复 split、`--annotate` 与 `--rejoin` |
| 为什么建议拆开业务提交与 Subtree 提交 | 过滤后的提交说明仍有意义，回推评审更清晰 | 混合提交是否仍能 split；性能和审计代价 |
| Maven 聚合与继承有何区别 | `<modules>` 决定 Reactor 收集什么；子 POM 的 `<parent>` 决定继承什么；二者可以分别使用 | 为什么列入 modules 后仍要声明 dependency |
| Reactor 如何决定构建顺序 | 收集模块后根据项目依赖、插件等关系排序，不只是照抄目录顺序 | `-pl`、`-am` 与 `-amd` 各影响什么范围 |
| 同一分支为何不能在两个 Worktree 检出 | 两个独立 Index/工作目录会与同一可移动引用产生危险状态 | 强制参数为何不适合日常流程 |

### 15.3 从 Agent 并行追问到工程治理

| 追问 | 回答要点 | 可能继续追问 |
| --- | --- | --- |
| Worktree 是否解决多 Agent 冲突 | 对普通仓库只解决工作目录与暂存区覆盖，不解决语义和集成冲突；含 Submodule 时默认独立 clone | 如何拆任务、固定基线、安排集成顺序 |
| Agent 为什么不能共用 stash | stash 引用在仓库级可见，序号和所有权容易混淆 | 更好的交付物是什么 |
| Worktree 是否是沙箱 | 不是；分支、配置、对象和外部资源仍可能共享 | 何时使用容器或虚拟机 |
| Java 多 Worktree 最大隐患 | 端口、数据库、Maven SNAPSHOT、Gradle Daemon 与 IDE 资源共享 | 如何按任务标识隔离 |
| Submodule 与 Maven 依赖如何选 | 源码组合与制品依赖解决不同层次；普通库优先制品 | 何时 Monorepo 更合适 |
| Agent 能否直接修改 Subtree | 文件层面可以；是否允许同步上游取决于任务契约和远程授权 | 如何指定单一同步所有者与交付证据 |
| Monorepo 如何拆分 Agent 任务 | 同一基线下一任务一 Worktree；用可写路径、构建根、模块依赖和唯一热点所有者共同约束 | 路径不重叠为什么仍会语义冲突 |
| 局部构建通过能否直接交付 | 不能一概而论；公共接口、根配置和高扇出模块需要下游或全量兜底 | 如何计算受影响范围并记录未验证项 |

回答时先给结论，再说明底层状态，最后给可执行命令与验证方式。只背“Submodule 是仓库里的仓库”“Subtree 就是复制目录”或“Worktree 可以多开分支”无法应对生产追问。

## 16 项目落地模板

### 16.1 推荐目录布局

~~~text
dev/
├── order-platform-main/          # 主工作树，只做协调和主线检查
├── wt-PAY-142/                   # Agent 任务 Worktree
├── wt-ORD-230/                   # 另一个任务 Worktree
└── runtime/
    ├── PAY-142/                  # 任务独立日志、容器编排覆盖
    └── ORD-230/
~~~

Worktree 放在仓库同级，避免 IDE 多 Root 误判，也避免主仓库 `git status` 扫描到嵌套目录。`runtime` 不应进入源码仓库，其权限和清理策略应独立管理。

Monorepo 内部可以从小型结构开始，不必为了“看起来像大厂”预先创建大量空目录。

~~~text
shop-platform/                         # 唯一 Git 仓库根
├── pom.xml                            # 小型项目的单一 Maven 聚合根
├── apps/
│   ├── order-service/                 # 可独立部署应用
│   └── payment-service/
├── libs/
│   └── common-domain/                 # 仓库内公共库，控制下游扇出
├── build-logic/                       # 构建约定；只有明确所有者可修改
├── docs/                              # 架构决策与模块清单
└── .github/workflows/                 # 仓库级 CI；视为高风险共享入口
~~~

若使用根 Maven POM，`<module>` 路径应与上述目录一致，例如 `apps/order-service`。如果各应用有独立构建根，则不要再用一个虚假的根 POM 聚合所有内容；在文档和 CI 清单中明确每个构建根及其命令。

### 16.2 分支与路径命名

| 对象 | 模板 | 示例 |
| --- | --- | --- |
| 人工功能分支 | `feature/<ticket>-<slug>` | `feature/ORD-230-validation` |
| Agent 分支 | `agent/<ticket>-<slug>` | `agent/PAY-142-idempotency` |
| 热修复分支 | `fix/<ticket>-<slug>` | `fix/PAY-199-timeout` |
| Worktree 路径 | `../wt-<ticket>` | `../wt-PAY-142` |
| 子仓库 Agent 分支 | `agent/<ticket>-<slug>` | `agent/RISK-87-timeout` |
| Subtree 同步分支 | `sync/<prefix>-<date>` | `sync/risk-rules-20260802` |
| Subtree 拆分分支 | `export/<prefix>-<ticket>` | `export/risk-rules-RISK-87` |

名称应能从目录定位任务，从任务定位分支，从分支定位所有者。不要用 `test1`、`agent-new` 之类不可追溯名称。

### 16.3 仓库级 Agent 指令模板

项目可以在仓库根目录维护 Agent 指令文件，内容至少覆盖：

~~~markdown
# Repository Instructions

<!-- 强制使用项目 Wrapper，避免不同机器的 Maven 版本漂移。 -->
1. Build with ./mvnw; do not call a globally installed Maven.

<!-- .gitmodules 属于依赖与供应链边界，只能在任务明确授权时修改。 -->
2. Never modify .gitmodules unless the task explicitly allows it.

<!-- Subtree 同步会引入外部代码或修改上游，必须有单独授权。 -->
3. Never run git subtree pull or push unless the task names the prefix, upstream, and target ref.

<!-- <changed-module> 是占位符，执行前替换为真实 Maven 模块名。 -->
4. Run ./mvnw -B -ntp -pl <changed-module> -am test before handoff.

<!-- Agent 没有部署授权，避免把代码修改任务扩大为生产变更。 -->
5. Do not execute production deployment commands.

<!-- 本地配置、密钥、IDE 状态和构建产物都不属于源码提交。 -->
6. Do not commit .env, secrets, .idea/workspace.xml, target, or build outputs.

<!-- 交付必须带证据，不能只回复“已完成”。 -->
7. Report the current branch, commit, tests, and remaining risks.

<!-- Monorepo 根构建配置影响范围大，未授权任务只能读取。 -->
8. Do not modify root build files, wrappers, or CI workflows unless explicitly allowed.

<!-- 交付同时报告修改模块和受影响模块，不能只列文件。 -->
9. Report changed modules, affected downstream modules, and the build root used for verification.
~~~

真实文件的格式取决于 Agent 工具，但原则相同：把仓库事实、禁区、验证命令和交付格式版本化。指令不能代替操作系统权限和 CI 策略。

### 16.4 普通仓库的 Agent Worktree 启动模板

~~~bash
# 任务编号只用于命名和追踪；所有变量都采用任务专属名称
TASK_ID="PAY-142"

# 每个任务使用唯一分支，避免多个 Agent 写同一个可移动引用
BRANCH="agent/PAY-142-idempotency"

# Worktree 放在主仓库同级目录，不嵌套进主工作树
WORKTREE="../wt-PAY-142"

# 协调者在创建第一项任务前刷新一次远程跟踪引用
git fetch origin main

# 冻结完整 Commit 标识，并把同一个值写入所有并行任务契约
BASE_COMMIT=$(git rev-parse refs/remotes/origin/main)
printf '%s\n' "$BASE_COMMIT"

# 从不可变提交创建分支；不要让各任务重新解析移动的 origin/main
git worktree add -b "$BRANCH" "$WORKTREE" "$BASE_COMMIT"

# 检查新工作树分支、状态与精确基线
git -C "$WORKTREE" status --short --branch
git -C "$WORKTREE" rev-parse HEAD

# 从仓库整体确认任务工作树已登记
git worktree list
~~~

这段模板只用于不含 Submodule 的普通仓库；包含 Subtree 但不含 Submodule 的仓库也属于这一类，因为 Subtree 是普通文件。协调者只冻结一次 `BASE_COMMIT`，后续任务复用该值；若希望更新基线，应创建新一批任务或明确执行集成更新，不能让正在并行的任务静默漂移。模板不负责自动删除已有目录或已有分支。若命令失败，协调者先检查 `git worktree list`、`git branch --list` 与实际路径，不能通过强制覆盖继续。

包含 Submodule 的父仓库默认使用：

~~~bash
# 任务标识与分支命名方式和普通仓库保持一致
TASK_ID="PAY-142"
BRANCH="agent/PAY-142-idempotency"

# 必须与任务契约中的完整 base_commit 相同
BASE_COMMIT="<full-base-commit-id>"

# 含 Submodule 时使用独立克隆目录，而不是默认创建 Worktree
TASK_CLONE="../clone-PAY-142"

# 必须替换为团队可访问的真实父仓库地址
PARENT_URL="<parent-repo-url>"

# 先建立独立 clone，但不检出可能已移动的远程默认分支
git clone --no-checkout "$PARENT_URL" "$TASK_CLONE"

# 确认契约提交可获取，再从该不可变提交创建任务分支
git -C "$TASK_CLONE" cat-file -e "$BASE_COMMIT^{commit}"
git -C "$TASK_CLONE" switch -c "$BRANCH" "$BASE_COMMIT"

# 只初始化冻结基线定义的 Submodule，并递归恢复嵌套模块
git -C "$TASK_CLONE" submodule update --init --recursive

# 交给 Agent 前确认子模块状态
git -C "$TASK_CLONE" submodule status --recursive
~~~

### 16.5 Agent 交付模板

~~~markdown
# Task Handoff

<!-- 基本定位信息：让集成者知道任务、分支和精确提交。 -->
Task: PAY-142
Branch: agent/PAY-142-idempotency
Commit: <parent-commit>
Base: <full-base-commit-id>
Build root: <repository-root-or-subdirectory>
Changed modules: <changed-modules>
Affected modules: <affected-modules>

## Behavior

<!-- 写用户能观察到的变化，以及关键实现取舍。 -->
<用户可观察行为与实现取舍>

## Verification

<!-- Command 必须是实际执行过的命令，Result 要包含成功判据。 -->
Command: ./mvnw -B -ntp -pl payment-service -am test
Result: PASS, <test-count> tests

## Repository State

<!-- clean 表示没有未提交和未跟踪的任务文件。 -->
Parent status: clean

<!-- 修改过 Submodule 时，改为列出子仓库名称、分支与提交。 -->
Submodule changes: none

<!-- 修改过 Subtree 时，列出 Prefix、上游 Commit、策略和远程同步状态。 -->
Subtree changes: none

## Risks

<!-- 明确未验证内容，避免把有限测试表述为全面正确。 -->
<未运行的测试、环境限制、迁移或兼容风险>
~~~

若修改 Submodule，再增加子仓库名称、分支、提交、推送状态和子仓库 PR。若修改 Subtree，再增加 Prefix、上游来源与 Commit、`--squash` 策略、拆分分支，以及 `push` 已执行还是等待授权。

### 16.6 创建前、交付前与清理前检查表

创建前：

1\. 基线提交与远程分支明确。

2\. 任务分支和任务目录唯一。

3\. 工作范围、禁区、测试和完成判据明确。

4\. Monorepo 的构建根、修改模块、受影响模块和根配置所有者明确。

5\. 子模块权限、Subtree 同步策略、JDK、Wrapper 和本地资源已准备。

交付前：

1\. 当前目录与分支正确。

2\. Diff 不含无关文件、敏感信息和构建产物。

3\. 局部测试与约定测试已运行并记录。

4\. 公共模块或根构建配置变化已经覆盖下游或执行全量验证。

5\. 父子仓库提交顺序正确，子提交远程可达。

6\. Subtree 修改与父业务修改已按需拆分，来源 Commit 和远程同步状态已报告。

7\. 工作目录状态、提交哈希、风险和验证边界已报告。

清理前：

1\. 提交已集成、已推送或明确放弃。

2\. 任务目录与所有 Submodule 都没有待救援修改。

3\. Subtree 拆分分支已经推送、归档或确认可以删除，没有待回推提交。

4\. `git branch --merged` 与 PR 状态符合删除策略。

5\. 普通 Worktree 使用 `git worktree remove`；独立 clone 按团队归档或可恢复清理流程处理。

6\. 最后执行 `git worktree list` 验证记录。

## 17 自测、上线检查与官方资料

### 17.1 学习自测

完成本文后，应能不看答案解释并演示以下内容。

1\. 在无网络环境完成第 4.1 节实验，并解释为什么本地文件协议只使用单次 `-c` 配置。

2\. 用 `git ls-tree HEAD` 与 `git ls-files --stage` 证明并区分已提交 Tree 和 Index 中的 `160000` gitlink。

3\. 解释 `.gitmodules`、本地 `.git/config` 与 gitlink 的职责差异。

4\. 从普通克隆恢复所有嵌套 Submodule，并验证状态前缀。

5\. 安全更新自研子模块，遵守“先子后父”的推送顺序，并解释为什么 `--recurse-submodules=check` 只有配合 `--dry-run` 才是预演。

6\. 完成第 5.4 节实验，并用 `git ls-tree` 证明 Subtree 是 `040000 tree`，普通 clone 无需初始化即可得到文件。

7\. 演示 Subtree 的上游 `pull`、本地 `split` 与授权后的 `push`，并解释为什么不推向正检出目标分支的普通仓库。

8\. 解释 `--squash` 与完整历史的取舍，并指出 Prefix、上游 Commit 和回推策略记录在哪里。

9\. 完成第 6.3 节 Maven 示例，解释根 POM 的聚合、子 POM 的继承和使用方依赖分别解决什么问题。

10\. 说明 Monorepo、普通多模块、模块化单体与微服务的维度差异，并能画出仓库图、构建图和部署图。

11\. 完成第 8.1 节实验，并证明两个 Worktree 的 Index 与 `HEAD` 独立、分支引用共享。

12\. 为两个 Spring Boot Worktree 分配不同端口和数据库隔离。

13\. 为普通仓库或 Monorepo Agent 冻结完整基线提交，定义任务契约、模块范围与构建根，创建 Worktree，收集测试证据并安全清理。

14\. 解释为何包含 Submodule 的父仓库默认使用独立 clone，而含 Subtree 的仓库可以按普通仓库使用 Worktree。

15\. 解决一次父仓库 gitlink 冲突，而不是机械选择 ours/theirs。

16\. 说明何时应选择 Submodule、Subtree、Maven/Gradle 制品依赖、Monorepo 或独立 clone。

17\. 使用 `git cat-file` 与 `git ls-tree` 解释并演示 `HEAD → Branch → Commit → Tree → Blob/gitlink` 的关系。

### 17.2 生产上线检查

1\. 父仓库和每个子仓库的提交均已推送并可由 CI 获取。

2\. CI 不使用无约束的 `submodule update --remote` 构建发布版本。

3\. `.gitmodules` URL 在允许列表内且不含凭据。

4\. Subtree 清单记录 Prefix、上游、Ref、导入 Commit、历史策略、许可证和同步所有者。

5\. CI 不执行未经授权的 `subtree pull` 或 `subtree push`，但会扫描父仓库中已经导入的全部 Subtree 文件。

6\. Monorepo 的仓库根、构建根、模块依赖、受影响范围和部署单元已记录。

7\. 父提交、子提交、Subtree 来源、JDK、Wrapper、依赖与制品可追溯。

8\. 从全新目录完成递归 Submodule 初始化、普通 Subtree 文件恢复和命令行构建，排除 IDE 缓存影响。

9\. 公共模块、根构建配置和 Wrapper 变化已覆盖下游或执行全量验证。

10\. 所有并行 Agent 任务记录同一个冻结基线提交；修改经过 Diff 审查、测试复跑和主线 CI。

11\. 数据库迁移、端口、Topic、缓存和 SNAPSHOT 冲突已隔离。

12\. 不再使用的 Worktree、Subtree 拆分分支或独立 clone 已按各自安全流程处理，没有遗留未推送提交。

13\. 有回滚方案：回滚父提交能恢复旧 Subtree 文件，且旧 Submodule 子提交仍可从远程获取；多服务部署也有独立回滚顺序。

### 17.3 官方资料入口

Git 对象模型：

1\. [Git Book：Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects.html)：Blob、Tree、Commit 与引用的底层关系。

2\. [`git-cat-file` 命令手册](https://git-scm.com/docs/git-cat-file)：查看对象类型、大小与内容。

3\. [`git-ls-tree` 命令手册](https://git-scm.com/docs/git-ls-tree)：查看提交或 Tree 对象中的目录条目，包括 `160000 commit` 形式的 gitlink。

4\. [`git-commit-tree` 命令手册](https://git-scm.com/docs/git-commit-tree)：Commit 如何连接根 Tree、父提交与提交元数据。

Git Submodule：

1\. [git-submodule 命令手册](https://git-scm.com/docs/git-submodule)：`add`、`status`、`update`、`sync`、`deinit` 等命令的当前定义。

2\. [gitsubmodules 概念手册](https://git-scm.com/docs/gitsubmodules)：gitlink、`.gitmodules`、子模块形态与父子仓库模型。

3\. [gitmodules 配置手册](https://git-scm.com/docs/gitmodules)：`path`、`url`、`branch`、`update` 等配置的语义。

4\. [Git Book：Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)：从添加、克隆、更新到发布子模块提交的完整示例。

5\. [git-clone 命令手册](https://git-scm.com/docs/git-clone)：`--recurse-submodules`、浅克隆、并行任务等克隆选项。

6\. [git-push 命令手册](https://git-scm.com/docs/git-push)：`--dry-run` 与 `--recurse-submodules=check|on-demand|only|no` 的推送语义。

Git Subtree：

1\. [Git 官方源码：`git-subtree` 文档](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.adoc)：`add`、`merge`、`pull`、`split`、`push`、`--squash`、`--annotate` 与 `--rejoin` 的当前定义。

2\. [Git Book：Advanced Merging](https://git-scm.com/book/en/v2/Git-Tools-Advanced-Merging)：底层 Subtree Merge（子树合并）示例，用于理解它与 `git subtree` 高层命令的区别。

Git Worktree：

1\. [git-worktree 命令手册](https://git-scm.com/docs/git-worktree)：`add`、`list`、`remove`、`move`、`lock`、`prune`、`repair`、共享状态，以及 Submodule 支持不完整的官方警告。

2\. [git-config 命令手册](https://git-scm.com/docs/git-config)：配置作用域与 `extensions.worktreeConfig`。

Java 多模块与 Monorepo 构建：

1\. [Maven：Guide to Working with Multiple Modules](https://maven.apache.org/guides/mini/guide-multiple-modules.html)：Reactor 收集项目、排序并构建所选模块的官方说明。

2\. [Maven：Introduction to the POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)：Project Inheritance（项目继承）与 Project Aggregation（项目聚合）的区别和组合方式。

3\. [Gradle：Structuring Multi-Project Builds](https://docs.gradle.org/current/userguide/intro_multi_project_builds.html)：根项目、子项目、`settings.gradle(.kts)` 与项目路径的官方说明。

4\. [Gradle：Project Dependencies](https://docs.gradle.org/current/userguide/declaring_dependencies_basics.html)：同一个 Multi-Project Build 中声明项目依赖的官方示例。

5\. [Gradle：Command-Line Interface](https://docs.gradle.org/current/userguide/command_line_interface.html)：按项目路径选择任务和常用命令行规则。

6\. [GitHub Actions：Checkout](https://github.com/actions/checkout)：CI 检出步骤的当前受支持主版本和配置入口。

7\. [GitHub Actions：Setup Java](https://github.com/actions/setup-java)：CI 安装 JDK、配置 Maven/Gradle 缓存与 Toolchains 的官方说明。

IDE：

1\. [IntelliJ IDEA：Use Git worktrees](https://www.jetbrains.com/help/idea/use-git-worktrees.html)：IDE Worktrees 视图、创建、切换、清理、单仓库限制与项目标识故障。

2\. [IntelliJ IDEA：Advanced Settings](https://www.jetbrains.com/help/idea/advanced-settings.html)：递归克隆 Submodule 等 Git 高级设置。

3\. [IntelliJ IDEA：Sync with a remote Git repository](https://www.jetbrains.com/help/idea/sync-with-a-remote-repository.html)：更新项目与 detached HEAD 子模块行为。

4\. [VS Code：Git Branches and Worktrees](https://code.visualstudio.com/docs/sourcecontrol/branches-worktrees)：创建、检测、打开、比较和迁移 Worktree 的内置操作。

5\. [IntelliJ IDEA：Maven support](https://www.jetbrains.com/help/idea/maven-support.html)：创建、导入和管理多模块 Maven 项目及 Maven 工具窗口。

### 17.4 本文验证边界

本文命令语义已于 2026-08-06 对照上述 Git、Maven、Gradle、JetBrains 与 VS Code 官方资料。当前编写环境可用 Git 2.55.0；第 4.1 节的本地 `submodule add` 与递归克隆、第 5.4～5.6 节的本地 `subtree add`、`pull`、`split`、向裸仓库 `push` 与普通克隆，以及第 8.1 节的 `worktree add`、合并和安全移除核心流程，已在临时本地仓库实际验证。

本次 Review 另外使用本地裸父仓库与子仓库验证了第 4.6.2 节：`git push --dry-run --recurse-submodules=check` 检查通过后，父仓库远程引用保持不变；移除 `--dry-run` 执行真实推送后，远程引用才移动到新的父提交。这项验证证明了示例中的“预演”和“实际推送”差异，不代表远程托管平台的权限、保护分支和服务端 Hook（钩子）已经通过。

第 6.3～6.4 节的 Maven 示例已在 Apache Maven 3.8.9 与 Java 8 环境实际执行：全量 `clean verify`、`-pl order-service -am verify` 和 Java 入口运行均成功。该验证证明示例结构、模块依赖和命令闭环可执行，不代表推荐新生产项目继续使用 Java 8；生产版本应遵守团队支持矩阵。

当前工作区不是用于演练的真实父子远程仓库，Subtree 测试使用本地裸仓库模拟托管端，示例 URL 也刻意使用占位符或 `.invalid` 域名。因此本文没有声称完成真实托管平台权限、远程网络或双仓库 Pull Request（PR，拉取请求）。落地时仍应在团队测试仓库按第 4～6、8～10 章完成端到端演练，再用于生产项目。
