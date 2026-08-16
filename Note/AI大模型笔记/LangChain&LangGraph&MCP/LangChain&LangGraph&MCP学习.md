# LangChain、LangGraph 与 MCP 学习笔记

> 文档定位：从零建立“模型、知识、工具、工作流、协议、生产治理”这一条完整主线，并能独立实现、调试和上线一个可控智能体。

| 项目 | 说明 |
| --- | --- |
| 版本范围 | LangChain 1.x、LangGraph 1.x、MCP 2026-07-28 与 MCP Python SDK v2；旧协议和旧 SDK 仅用于兼容迁移 |
| 核心缩写 | LCEL：LangChain Expression Language，LangChain 表达式语言；RAG：Retrieval-Augmented Generation，检索增强生成；MCP：Model Context Protocol，模型上下文协议 |
| 推荐读法 | 初学者顺序阅读第 1～12 章；有经验者重点阅读第 8、10、11、13、14 章 |

## 1 从一个可验收的问题开始

### 1.1 目标场景：企业知识与实时工具助手

假设要实现一个“企业服务助手”。用户既会问静态制度，例如“差旅报销上限是多少”，也会问动态事实，例如“新加坡今天的天气如何”，还会发起有副作用的动作，例如“替我提交报销申请”。

先把这三类请求拆开看。静态制度来自企业知识库，实时天气来自外部服务，提交申请还会改变业务系统的状态。一次大模型调用只负责理解和生成，无法独自承担知识更新、权限校验、流程恢复和外部系统接入。因此，系统还需要检索、工具、状态管理和能力协议。

最终系统应满足下列可验收结果：

1\. 对知识问题给出带来源的答案，证据不足时明确拒答。

2\. 对实时问题选择正确工具，并保存工具参数、结果和耗时。

3\. 对写操作在执行前暂停，由用户或审批人确认。

4\. 同一会话可恢复，不同会话可按授权共享长期偏好。

5\. 单个模型、向量库或工具服务故障时，系统能超时、重试、降级并留下诊断信息。

### 1.2 三个框架分别解决什么

| 技术 | 主要职责 | 适合解决 | 不应误用为 |
| --- | --- | --- | --- |
| LangChain | 标准化模型、提示词、输出解析、文档、检索器、工具和 Runnable 组合 | 一次请求内的组件编排、模型适配、RAG、工具定义 | 持久化业务数据库或分布式任务平台 |
| LangGraph | 用带状态的图执行长流程，并提供检查点、恢复、流式输出、人工介入 | 循环、分支、并行、长任务、故障恢复、多智能体 | 一种新的大模型或向量数据库 |
| MCP | 约定智能体应用与外部能力之间如何发现、描述和调用工具、资源、提示模板 | 跨语言、跨进程、跨团队的能力接入 | 智能体决策算法、身份系统或业务授权本身 |

实际项目不一定同时使用三者。固定提示词可以直接调用模型；确定性业务规则更适合普通函数或工作流引擎；两个进程内函数也没有必要额外部署 MCP 服务。技术选择取决于问题边界，而不是框架数量。

### 1.3 分阶段学习主线与通过门槛

第一次学习时，先跑通一个结果，再逐步加入模型、状态、网络和生产环境中的不确定因素。按照这个思路，全文分为五个阶段：

| 阶段 | 阅读范围 | 当前只解决什么 | 通过门槛 | 可暂时跳过 |
| --- | --- | --- | --- | --- |
| 第一阶段：看懂数据流 | 第 1～3 章 | 输入怎样依次经过函数、链、图和协议边界 | 能逐步写出每个节点的输入、更新和最终输出 | 模型密钥、向量库、持久化 |
| 第二阶段：控制单次模型调用 | 第 4～7 章 | 消息、Prompt、结构化输出、工具和 RAG 如何约束模型 | 能把一次模型结果变成可校验字段或带引用答案 | 人工审批、多智能体、生产部署 |
| 第三阶段：控制长流程 | 第 8～9 章 | 状态、分支、循环、检查点和中断如何工作 | 能恢复同一 thread，并证明副作用没有重复执行 | MCP 远程授权与多 Worker |
| 第四阶段：跨边界接入能力 | 第 10～12 章 | MCP 如何把工具变成跨进程契约，系统各层怎样组合 | 能分层验证 Server、Client、Adapter、Graph 和 Agent | 大规模压测与复杂多智能体 |
| 第五阶段：达到生产可用 | 第 13～18 章 | 安全、可靠性、观测、评测、迁移和上线门槛 | 能为一次真实发布写出测试证据、告警和回滚条件 | 无 |

每一阶段都使用同一个企业服务助手场景，避免示例在天气、数学、聊天和报销之间无目的切换。天气查询代表只读实时工具，制度问答代表检索增强生成，报销提交代表需要授权、人工确认和幂等性的写操作。

初学者最短路径是第 1～4、7～10、12 章。完成这条路径后再读第 5～6 章的组合细节和第 11 章的多智能体选择，理解成本会明显更低。

## 2 第一个最小闭环：不依赖外部模型密钥

### 2.1 环境与版本策略

本章先解决一个实际问题：把示例运行环境准备好，同时不让不同版本的包互相影响。示例使用 Python 3.11 或更高版本。SDK（Software Development Kit，软件开发工具包）可以理解为“供应用调用某项技术能力的代码库和配套工具”。

第一次练习只需记住两件事：

1\. 第 2～9 章以及第 10.1～10.6 节都使用 `.venv`，其中安装 MCP Python SDK v2。

2\. 第 10.7 节接入 LangChain MCP Adapters 时会创建另一个环境 `.venv-adapter`。现在不用创建它，因为提前混入旧版 MCP SDK 反而容易造成导入错误。

先创建主环境：

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -U \
  "langchain>=1,<2" \
  "langgraph>=1,<2" \
  "langchain-openai>=1,<2" \
  "mcp[cli]>=2,<3" \
  pydantic python-dotenv
```

安装完成后检查 MCP SDK 的实际版本：

```bash
python -c "import importlib.metadata as m; print('mcp', m.version('mcp'))"
```

预期输出以 `mcp 2.` 开头，此时示例使用 `from mcp.server import MCPServer`。

如果看到 `mcp 1.`，通常说明当前终端激活了错误的虚拟环境。运行 `which python` 和 `python -m pip --version`，确认二者都指向项目中的 `.venv`。

这里使用主版本范围，是为了让示例在同一主版本内安装。生产项目还应使用 `uv.lock`、`poetry.lock` 或带哈希的 `requirements.txt` 固定已经测试的精确版本，并让持续集成（Continuous Integration，CI）环境从空环境重建。

常见失败与处理如下：

| 现象 | 原因 | 处理 |
| --- | --- | --- |
| 导入路径不存在 | 旧版依赖与当前主版本混装 | 新建虚拟环境，按项目锁文件安装 |
| Pydantic 模型报兼容错误 | LangChain 与 Pydantic 主版本不匹配 | 查看依赖解析结果，按整组约束调整版本 |
| `ModuleNotFoundError: mcp.server.fastmcp` | 在 v2 环境中运行了 v1 示例 | 本章改用 `MCPServer`；旧版 Adapter 环境按第 10.7 节单独创建 |
| MCP 客户端握手或请求失败 | 客户端、服务端支持的协议修订不同 | 先确认双方 SDK 主版本和协议日期，再启用兼容模式或共同升级 |

### 2.2 用 LCEL 看清数据流

LCEL（LangChain Expression Language，LangChain 表达式语言）用于把多个处理步骤连接成一条可执行的数据流。下面的程序暂时不调用大模型，只把一句问题依次做“清理空白、判断去向、生成结果”三件事。这样可以先看懂数据如何流动，再处理网络、密钥和模型随机性。

代码中会出现四个新写法：

| 写法 | 先这样理解 | 在示例中的作用 |
| --- | --- | --- |
| Runnable | 一个可以接收输入并返回输出的步骤 | 统一每一步的调用方式 |
| `RunnableLambda` | 把普通 Python 函数包装成 Runnable | 包装清理、路由和回答函数 |
| `a \| b` | 把 `a` 的输出交给 `b` | 按顺序连接处理步骤 |
| `RunnableParallel` | 把同一输入同时交给多个分支 | 同时生成业务结果和调试信息 |

此时只需关注 `invoke`：它接收一份输入并返回最终结果。同步、异步、批量和流式等能力放到第 5 章再系统解释。

将以下内容保存为 `minimal_lcel.py`：

```python
from langchain_core.runnables import RunnableLambda, RunnableParallel


normalize = RunnableLambda(
    lambda x: {
        "question": x["question"].strip(),
        "trace_id": x["trace_id"],
    }
)

route = RunnableLambda(
    lambda x: {
        **x,
        "route": "tool" if "天气" in x["question"] else "rag",
    }
)

answer = RunnableLambda(
    lambda x: {
        "answer": (
            "应调用天气工具" if x["route"] == "tool" else "应检索知识库"
        ),
        "route": x["route"],
    }
)

chain = normalize | route | RunnableParallel(
    result=answer,
    debug=RunnableLambda(lambda x: x),
)

print(chain.invoke({"question": " 新加坡天气 ", "trace_id": "t-001"}))
```

运行命令：

```bash
python minimal_lcel.py
```

预期结果中，`result.route` 为 `tool`，`debug` 保留规范化后的输入和路由。把问题换成“差旅报销上限”，路由应变为 `rag`。

这次调用经历四个可观察状态：

| 时点 | 输入 | 动作 | 输出 |
| --- | --- | --- | --- |
| 调用入口 | `question=" 新加坡天气 "`、`trace_id="t-001"` | `chain.invoke` 启动一次同步执行 | 原始字典进入第一个 Runnable |
| `normalize` 后 | 原始字典 | 去掉问题两侧空白，保留关联标识 | `question="新加坡天气"` |
| `route` 后 | 规范化字典 | 根据当前演示规则判断是否包含“天气” | 新增 `route="tool"` |
| `RunnableParallel` 后 | 同一份路由结果 | `answer` 与 `debug` 两个分支接收同一输入 | 用键名聚合两个分支结果 |

典型输出形状如下；字典字段顺序不作为测试条件：

```text
{
  'result': {'answer': '应调用天气工具', 'route': 'tool'},
  'debug': {'question': '新加坡天气', 'trace_id': 't-001', 'route': 'tool'}
}
```

这个例子暂时不用模型，先把组合语义看清楚，避免网络、密钥和模型随机性干扰判断。它验证的是 Runnable 的数据传递和并行聚合；分类质量、工具选择和外部服务可用性还需要后续测试。

若输出不符，按边界逐层调用：

1\. 先运行 `normalize.invoke(...)`，确认空白被删除且 `trace_id` 未丢失。

2\. 再把上一步输出传给 `route.invoke(...)`，确认新增了正确的 `route`。

3\. 最后运行完整 `chain`，确认 `result` 和 `debug` 都存在。

4\. 如果出现 `KeyError`，查看输入是否缺少 `question` 或 `trace_id`；Runnable 不会自动补齐业务字段。

### 2.3 用 LangGraph 显式表达分支

当流程出现“满足条件走 A，否则走 B”时，图比一串嵌套的 `if` 更容易观察。下面仍然处理同一个问题：天气问题走工具分支，制度问题走检索分支。

先认识四个名称：State 保存流程当前已经知道的事实；Node 是读取 State 并返回更新的函数；Edge 决定执行顺序；`START` 与 `END` 分别表示图的入口和出口。这个最小图可以先读成下面的流程：

```mermaid
flowchart LR
    START((START)) --> C["classify：判断问题类型"]
    C -->|"天气"| T["call_tool：返回模拟天气"]
    C -->|"制度"| R["retrieve：返回模拟制度"]
    T --> END((END))
    R --> END
```

将以下内容保存为 `minimal_graph.py`：

```python
from typing import Literal, TypedDict

from langgraph.graph import END, START, StateGraph


class State(TypedDict, total=False):
    question: str
    route: Literal["tool", "rag"]
    answer: str


def classify(state: State) -> dict:
    route = "tool" if "天气" in state["question"] else "rag"
    return {"route": route}


def call_tool(state: State) -> dict:
    return {"answer": "模拟工具结果：新加坡 30℃"}


def retrieve(state: State) -> dict:
    return {"answer": "模拟知识结果：差旅标准见制度第 4 条"}


def choose(state: State) -> str:
    return state["route"]


builder = StateGraph(State)
builder.add_node("classify", classify)
builder.add_node("call_tool", call_tool)
builder.add_node("retrieve", retrieve)
builder.add_edge(START, "classify")
builder.add_conditional_edges(
    "classify",
    choose,
    {"tool": "call_tool", "rag": "retrieve"},
)
builder.add_edge("call_tool", END)
builder.add_edge("retrieve", END)

graph = builder.compile()
print(graph.invoke({"question": "新加坡天气"}))
```

预期最终状态类似：

```text
{'question': '新加坡天气', 'route': 'tool', 'answer': '模拟工具结果：新加坡 30℃'}
```

最终字典由多个节点的更新逐步合并得到。沿着执行顺序看一遍：

1\. 初始状态只有 `question`，`START` 只是运行时入口，不是业务节点。

2\. `classify` 读取 `question`，只返回 `{"route": "tool"}`。运行时把这份增量与原状态合并，因此 `question` 不会消失。

3\. 条件边调用 `choose`。它不修改状态，只返回一个路由键；映射表把 `tool` 解释为下一节点 `call_tool`。

4\. `call_tool` 返回 `answer` 更新，随后无条件边把执行送到 `END`。

5\. 最终返回的是合并后的状态，而不是最后一个节点的返回字典。

把问题改成“差旅报销上限”时，成功判据不仅是答案文本变化，还包括 `route` 变成 `rag` 且执行路径不经过 `call_tool`。若路由值不在映射表中，图会在条件边处失败；生产代码需要为无法识别的路由提供拒绝、重试或人工处理路径。

这里仍然没有并发写同一字段，所以默认覆盖语义足够。第 8.2 节会通过两个并发节点同时写 `evidence` 的例子说明 Reducer（归约器）为什么是状态契约的一部分。

### 2.4 用 MCP 暴露第一个工具

MCP（Model Context Protocol，模型上下文协议）让一个应用用统一方式发现和调用另一个进程提供的能力。这个例子只做三件事：把普通 Python 函数注册为工具，启动本地 Server，再用 Inspector 充当测试 Client。模型还没有参与。

将以下内容保存为 `weather_server.py`：

```python
from mcp.server import MCPServer


mcp = MCPServer("weather-demo")


@mcp.tool()
def get_weather(city: str) -> dict[str, object]:
    """返回演示天气。仅用于验证 MCP 工具契约。"""
    return {"city": city, "temperature_c": 30, "source": "demo"}


if __name__ == "__main__":
    mcp.run()
```

MCP Python SDK v2 的 `run()` 默认通过 STDIO（Standard Input/Output，标准输入输出）启动。使用随 `mcp[cli]` 安装的开发命令打开官方 Inspector：

```bash
npx --version
mcp dev weather_server.py
```

Inspector 是 Node.js 应用，`mcp dev` 需要在 `PATH` 中找到 `npx`。第一条命令若失败，应先安装当前受支持的 Node.js 长期支持版本并重新打开终端；Python 包安装成功不能证明 Inspector 的前端运行时已经存在。

Inspector 中至少完成三次检查：

1\. 在 Tools 列表中确认工具名是 `get_weather`，参数 Schema 中 `city` 为必填字符串。工具能够被列出，说明装饰器注册和 Schema 生成成功。

2\. 传入 `{"city":"新加坡"}`，确认结果同时包含 `city`、`temperature_c=30` 和 `source="demo"`。只看到请求没有报错不算验收，因为工具可能返回了错误形状。

3\. 删除 `city` 或传入非字符串值，确认请求在契约边界被拒绝。这个失败证明输入校验生效，不应通过在函数内部静默填默认城市来掩盖调用错误。

一次调用可分为“发现”和“执行”两个阶段。发现阶段由客户端读取工具列表和 JSON（JavaScript Object Notation，JavaScript 对象表示法）Schema；执行阶段才发送工具名与参数。模型能否选择工具是 Host 上层能力，不属于这个 Server 示例的证明范围。

若 Inspector 无法连接，先直接运行脚本并检查标准错误。STDIO 的协议通道必须保持纯净；调试日志统一写到标准错误。

MCP Python SDK v2 会在服务运行期间隔离多数普通输出，但它无法修复包装脚本、导入期输出、退出时才刷新的缓冲内容或旧 SDK 造成的污染。因此，不能把 SDK 的输出重定向当成唯一保障。

还要确认 Inspector 使用的是 `.venv` 中的 Python。否则可能出现“终端能导入 `MCPServer`，Inspector 启动的子进程却找不到模块”。

这个 STDIO 示例只能证明三件事：工具已经注册、Schema 能生成、调用能返回结果。它不能证明 HTTP（Hypertext Transfer Protocol，超文本传输协议）部署兼容 2026-07-28 规范。

HTTP 验证留到第 10.3、10.6 节。第一次学习时，先完成 Inspector 中的三项检查即可。

### 2.5 最小闭环的验收标准

完成本章后，可以用下面四类证据检查学习结果。程序没有报错只是最基本的条件。

1\. LCEL：保存两组输入输出，证明天气问题走 `tool`、制度问题走 `rag`，并能指出每个字段在哪一步产生。

2\. LangGraph：保存最终 State，并能根据节点与边解释实际路径；把路由改成非法值时，知道故障发生在条件边映射。

3\. MCP：保存 Inspector 中的工具 Schema、一次成功调用和一次参数校验失败。

4\. 环境：记录 Python、LangChain、LangGraph 和 MCP SDK 的实际版本，确认示例使用的是对应主版本 API（Application Programming Interface，应用程序编程接口）。

通过门槛是能够独立回答“输入是什么、哪个组件做了什么、状态如何变化、怎样判断成功、失败先查哪里”。如果只能复述类名或复制代码，就还没有形成最小闭环。

## 3 统一心智模型：模型、链、图与协议

### 3.1 从用户请求到外部能力

```mermaid
flowchart LR
    U["用户"] --> H["智能体应用 / MCP Host"]
    H --> LC["LangChain：模型、提示、检索、工具抽象"]
    H --> LG["LangGraph：状态图、检查点、恢复"]
    LG --> C1["MCP Client A"]
    LG --> C2["MCP Client B"]
    C1 --> S1["本地 STDIO Server"]
    C2 --> S2["远程 Streamable HTTP Server"]
    S1 --> F1["文件、数据库、内部函数"]
    S2 --> F2["天气、工单、支付等服务"]
```

MCP Host 是承载用户体验、权限、模型和多个 MCP Client 的应用。一个 Client 通常负责接入一个 Server 的能力；是否存在传输连接或协议会话取决于所用修订。Server 暴露工具、资源和提示模板，但“是否允许调用”“是否需要人工审批”仍由 Host 的策略决定。

把用户问题“查询 5 级员工的新加坡差旅酒店上限”放进这张图，可以看到每一层处理的是不同问题：

| 层级 | 收到什么 | 负责什么 | 产出什么 | 不负责什么 |
| --- | --- | --- | --- | --- |
| Host | 用户消息、已认证身份、会话信息 | 组织用户体验、权限策略和多个能力来源 | 一次受控任务 | 不把模型判断当成最终授权 |
| LangChain | 消息、Prompt、模型、Retriever、Tool | 把异构组件变成统一调用接口 | 模型响应、检索结果、工具意图 | 不自动提供业务事务与持久化数据库 |
| LangGraph | 初始 State 与图定义 | 执行节点、合并更新、选择边、保存检查点 | 可恢复的状态演进 | 不决定工具协议和外部系统权限 |
| MCP Client | Server 配置与调用参数 | 发现能力、校验协议、发送请求、转换结果 | Tool、Resource 或 Prompt 结果 | 不替 Server 做对象级授权 |
| MCP Server | 已认证调用与结构化参数 | 执行业务能力并返回受约束结果 | 制度数据、天气或提交回执 | 不替 Host 决定用户是否应发起动作 |

一条真实链路通常需要多个标识：

| 标识 | 用途 |
| --- | --- |
| `request_id` | 标记一次 HTTP 请求 |
| `thread_id` | 恢复一个 LangGraph 运行线程 |
| `tool_call_id` | 匹配模型工具调用与工具结果 |
| `idempotency_key` | 防止业务写入被重复执行 |
| `trace_id` | 串联分布式观测数据 |

这些标识可以互相关联，但含义不同。不要把它们强行合并成同一个字符串，否则权限、恢复、重试和审计会互相干扰。

### 3.2 Chain、Workflow 与 Agent

| 形态 | 控制流由谁决定 | 优点 | 典型用途 |
| --- | --- | --- | --- |
| Chain（链） | 开发者预先固定 | 简单、可预测、易测试 | 提取、分类、固定 RAG 流程 |
| Workflow（工作流） | 开发者通过图和规则决定 | 可恢复、可审计、适合强约束业务 | 审批、报告生成、批处理 |
| Agent（智能体） | 模型在允许范围内动态选择工具和下一步 | 适应开放问题 | 研究助理、运维排障、复杂客服 |

Agent 可以理解为一个受约束的决策循环：读取当前信息，选择动作，接收工具结果，再判断是否继续。观察、决策、行动、结果回填和终止条件缺一不可。ReAct（Reasoning and Acting，推理与行动）是常见的循环模式，但这并不要求向用户展示模型的内部推理文本。

选择时先问四个问题：

1\. 下一步是否能由确定规则写出。如果能，优先用普通函数、Chain 或 Workflow。

2\. 流程是否需要跨请求保存状态、暂停或恢复。如果需要，使用带 Checkpointer 的 LangGraph，而不是只在内存里堆消息。

3\. 模型是否必须在运行时选择动作。如果只是把固定字段抽出来，不需要 Agent。

4\. 错误动作的代价是否可逆。代价越高，越应把模型限制在局部判断，把提交、授权和审批交给确定性代码。

以报销为例，“从票据中抽取金额”适合模型加结构化输出；“金额是否超过制度上限”适合确定规则；“证据缺失时还要查哪些资料”可以让 Agent 选择只读工具；提交报销则进入固定工作流，并经过服务端授权和人工确认。一个业务流程可以同时包含三种形态，不需要为整个系统贴上唯一标签。

如果让 Agent 同时决定审批人、修改金额并完成提交，开放推理、权限判断和不可逆副作用就会混在同一个循环中。这样的流程很难测试，也难以说明发生异常时是否会越权。

### 3.3 Function Calling 与 MCP 的关系

Function Calling（函数调用）是模型输出结构化工具意图的能力；MCP 是应用与能力提供方之间的协议。前者回答“模型想调用什么”，后者回答“工具如何被发现、参数如何描述、请求怎样发送、结果怎样返回”。

应用可以把 MCP 工具转换为 LangChain Tool，再绑定给模型。模型从工具 Schema 中选择函数，LangGraph 决定是否调用和如何循环，MCP Client 负责跨边界执行。这三个职责应分开测试。

一次完整工具调用的消息与网络链路如下：

```mermaid
sequenceDiagram
    participant U as 用户
    participant H as Host / Agent
    participant M as 模型
    participant C as MCP Client
    participant S as MCP Server
    U->>H: 查询差旅制度
    H->>M: 消息 + 可见工具 Schema
    M-->>H: tool_call(name, arguments, id)
    H->>H: 权限、参数、预算与审批检查
    H->>C: 调用已授权的 MCP Tool
    C->>S: tools/call
    S->>S: 身份与对象级授权 + 业务执行
    S-->>C: 结构化结果或业务错误
    C-->>H: 转换为 ToolMessage
    H->>M: 原调用 ID（Identifier，标识符）+ 工具结果
    M-->>H: 面向用户的最终回答
    H-->>U: 答案 + 来源或失败说明
```

这里要分清三个独立契约。模型输出需要匹配 Tool Schema；Host 要确认当前用户可以使用这类工具；Server 再校验该主体是否有权访问具体对象。任一契约不成立，调用就停在对应边界。

例如模型正确生成 `get_expense_limit(employee_id="E000042")`，不代表该用户有权查询 E000042。Schema 只能证明参数形状正确，Host 的工具过滤只能降低误选概率，最终对象级授权由拥有数据的 Server 执行。

分层测试也对应这三段：先用固定模型响应验证 LangGraph 是否产生 ToolMessage，再绕过模型直接测 MCP 调用，最后测模型能否在相似工具间选择正确工具。直接从聊天界面测试，只能看见最终症状，无法知道错误发生在哪一段。

## 4 LangChain 基础：让一次模型调用可控

### 4.1 消息与模型配置

聊天模型接收的是一组带角色的消息，而非一段普通字符串。常见角色包括 System、User、Assistant 和 Tool。System 消息提供稳定规则，User 消息表达本轮请求，Tool 消息则要与前面的工具调用标识对应。

角色不只是显示标签，它决定消息在工具循环中的语义：

| 角色 | 数据来源 | 生命周期 | 关键约束 |
| --- | --- | --- | --- |
| System | 应用代码、受控配置 | 通常贯穿一次 Agent 运行 | 不保存授权秘密，也不替代代码权限 |
| User | 当前用户或上游业务 | 每轮新增 | 必须视为不可信输入，限制长度和媒体类型 |
| Assistant | 模型 | 每次模型调用后新增 | 可能是自然语言，也可能带一个或多个工具调用 |
| Tool | 受控工具执行器 | 对应某个模型工具调用 | 必须关联正确的调用 ID，内容仍可能不可信 |

工具消息要回到正确的调用位置。例如模型同时请求天气与制度工具时，仅按工具名拼接结果无法区分同名多次调用；应使用每次调用的唯一 ID 对齐请求和 ToolMessage。错位会让模型把天气结果当作制度证据。

对于 OpenAI 兼容服务，可以显式初始化 `ChatOpenAI`：

```python
import os

from langchain_openai import ChatOpenAI


model = ChatOpenAI(
    model=os.environ["MODEL_NAME"],
    api_key=os.environ["LLM_API_KEY"],
    base_url=os.getenv("LLM_BASE_URL"),
    temperature=0,
    timeout=30,
    max_retries=2,
)
```

`.env` 用于本地开发，并加入版本控制的忽略规则。生产环境从密钥管理系统注入配置。令牌、数据库密码和测试 JWT（JSON Web Token，JSON 网络令牌）即使只是演示值，也不应提交到仓库、输出到笔记或写入追踪平台。

模型参数需要结合具体任务理解：

| 参数 | 作用 | 实践建议 |
| --- | --- | --- |
| `temperature` | 调整采样随机性 | 抽取、路由、工具参数通常设低；创作任务再提高 |
| `timeout` | 限制单次请求等待时间 | 必须设置，并与上游超时预算协调 |
| `max_retries` | SDK 层瞬时错误重试 | 只重试可安全重放的请求，避免叠加多层重试风暴 |
| `max_completion_tokens` | 限制生成长度 | 同时考虑上下文窗口、成本和截断策略；`max_tokens` 仅作为兼容别名 |
| `streaming` | 增量返回内容 | 改善首字延迟，但工具调用和结构化输出仍需完整事件处理 |

参数要区分“没有提供”和“提供了空值”。例如未设置 `base_url` 时，集成包会使用提供商默认地址；把它设为空字符串，反而可能产生无效 URL（Uniform Resource Locator，统一资源定位符）。

`temperature=0` 只是请求较低随机性，不保证同一输入每次逐字相同。模型后端、并行计算和服务版本仍可能改变结果。

模型初始化成功只能说明客户端对象已经创建。接下来应调用一条最小消息，记录实际模型、响应 ID、耗时和 Token 使用，再补一条结构化输出或工具能力测试。许多“本地能够初始化、生产环境却不调用工具”的问题，原因在于后端只兼容普通文本接口，并未完整实现 Tool Calling。

### 4.2 PromptTemplate 与 ChatPromptTemplate

字符串提示使用 `PromptTemplate`，聊天提示优先使用 `ChatPromptTemplate`。后者保留角色边界，也更适合插入历史消息。

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder


prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "你是企业制度助手。只依据 context 回答；证据不足时返回‘无法确认’。",
        ),
        MessagesPlaceholder("history", optional=True),
        ("user", "问题：{question}\n\ncontext：\n{context}"),
    ]
)

chain = prompt | model | StrOutputParser()
result = chain.invoke(
    {
        "question": "报销上限是多少？",
        "context": "差旅制度第 4 条：酒店上限为每晚 800 元。",
        "history": [],
    }
)
```

模板变量按外部数据处理，不能把其中的文字视为可信指令。检索文本、网页和工具结果都可能夹带提示注入内容。系统规则可以说明外部内容不得修改权限和调用策略，而高风险工具的限制仍由代码执行。

模板渲染只负责把变量放到指定位置，不会自动验证变量、过滤提示注入或控制长度。三种状态要显式区分：缺少 `context` 会在渲染阶段报错；`context=""` 表示调用方明确传入空证据；`context` 有内容但无权限标签则表示数据治理缺失。业务层不能把三者都解释为“没有找到答案”。

运行前可以先不调用模型，只验证渲染结果：

```python
rendered = prompt.invoke(
    {
        "question": "报销上限是多少？",
        "context": "差旅制度第 4 条：酒店上限为每晚 800 元。",
        "history": [],
    }
)
for message in rendered.messages:
    print(message.type, repr(message.content))
```

成功判据是 System 与 User 角色没有合并，问题和证据出现在预期位置，空历史没有生成多余占位消息。这样可以把“Prompt 渲染错误”和“模型回答错误”分开。

生产中还应为 Prompt 保存稳定标识、版本、输入变量 Schema 和回归样本。修改一句系统规则也可能改变工具选择和拒答率，应像修改代码一样评审、评测和回滚。

### 4.3 用 Few-shot 明确容易混淆的分类边界

Few-shot（少样本）的作用，是用输入和标准输出说明分类口径、结果格式与边界。它适合那些很难用一条规则完整描述、却能列出代表性案例的任务。

例如企业助手要把请求分为 `knowledge`、`read_tool`、`write_action` 和 `unsupported`。示例最好放在类别容易混淆的位置：

| 用户请求 | 目标分类 | 示例提供的信息 |
| --- | --- | --- |
| “差旅酒店上限是多少” | `knowledge` | 已发布制度属于知识检索 |
| “查询我今天的报销状态” | `read_tool` | 用户私有、实时但无副作用 |
| “提交这张报销单” | `write_action` | 会改变外部系统状态，需要审批 |
| “替别人导出全部薪资” | `unsupported` | 即使存在工具也必须拒绝越权请求 |

只有明显的正例，无法说明分类边界。模型更容易混淆“查询草稿”和“提交草稿”、“公开制度”和“个人记录”这类相邻类别。加入拒绝样例，还能说明工具存在与请求合法是两回事。

示例选择有三种常见方式：固定示例最可预测，适合分类与合规口径；按长度裁剪可以控制 Token，但要避免把唯一拒绝样例裁掉；按语义相似度动态选择更贴近问题，却会引入一次检索、数据投毒和类别偏斜风险。

验证时建立不参与示例选择的测试集，至少统计每类精确率、召回率和混淆矩阵。若添加示例只提高训练样本表现，却让 `read_tool` 更常被误判成 `write_action`，就不是有效改进。

### 4.4 结构化输出

结构化任务更适合先用 Pydantic 定义契约，再让模型按契约返回结果。让模型生成一段 JSON（JavaScript Object Notation，JavaScript 对象表示法）字符串，随后用正则表达式猜字段，既脆弱也不便于报告校验错误。

```python
from pydantic import BaseModel, Field


class ExpenseDecision(BaseModel):
    allowed: bool = Field(description="是否符合制度")
    limit_cny: int | None = Field(description="金额上限；未知时为 null")
    evidence: str = Field(description="制度依据")


structured_model = model.with_structured_output(ExpenseDecision)
decision = structured_model.invoke("制度规定酒店上限 800 元。申请 900 元是否允许？")
print(decision.model_dump())
```

当前 LangChain `create_agent` 也可通过 `response_format` 请求结构化结果，并在最终状态的 `structured_response` 中返回。框架会根据模型能力选择提供商原生结构化输出或工具策略。无论采用哪种策略，都要在业务层校验枚举、金额、日期、引用来源和权限。

这段 Schema 明确了三件事：`allowed` 必须存在且只能是布尔值；`limit_cny` 可以是整数或 `null`；`evidence` 必须是字符串。但它没有保证 `limit_cny` 非负，也没有保证 `evidence` 真来自有效制度。类型校验解决“形状正确”，业务校验解决“含义正确”。

可进一步把金额边界写进 Schema，并把无法确认与明确禁止区分开：

```python
from typing import Literal

from pydantic import BaseModel, Field, model_validator


class ExpenseDecision(BaseModel):
    status: Literal["allowed", "denied", "unknown"]
    limit_cny: int | None = Field(default=None, ge=0)
    evidence: str | None = None

    @model_validator(mode="after")
    def validate_evidence(self):
        if self.status != "unknown" and not self.evidence:
            raise ValueError("明确结论必须包含制度依据")
        return self
```

`unknown` 与 `denied` 不能合并：前者表示证据不足，应该检索、补充输入或转人工；后者表示证据充分且规则不允许。把两者都编码为 `allowed=False`，会让调用方无法决定下一步。

Provider Strategy（提供商原生策略）由模型服务直接约束输出，通常更可靠。Tool Strategy（工具策略）用一次人工工具调用承载结构化结果，适用于支持工具调用但没有原生结构化输出的模型。

选定策略后，要测试 Schema 校验失败、多次结构化输出，以及工具与结构化输出同时启用等路径，不能只测试正常样本。

### 4.5 Tool 的契约与错误边界

Tool 是应用允许模型选择的一项可调用能力。模型不会直接执行下面的 Python 函数，而是根据工具名称、描述和参数 Schema 生成调用请求；应用完成校验和授权后，才真正执行函数。

因此，一个 Tool 至少包含两部分：给模型看的调用契约，以及由应用和服务端强制执行的安全边界。下面先看最小定义：

```python
from typing import Literal

from langchain_core.tools import tool


@tool
def query_policy(
    topic: Literal["travel", "expense"],
    employee_level: int,
) -> dict:
    """查询已发布的公司制度；不得用于修改制度。"""
    if not 1 <= employee_level <= 10:
        raise ValueError("employee_level 必须在 1 到 10 之间")
    return {"topic": topic, "employee_level": employee_level, "text": "..."}
```

工具名应当稳定并且容易区分，描述中写清适用场景、禁用场景和副作用，参数 Schema 则尽量缩小合法空间。像 `execute_sql(sql: str)` 或 `run_shell(command: str)` 这样的万能接口权限过宽，不适合直接交给模型。

错误至少分为四类：参数校验错误应反馈给模型修正；业务拒绝应保留明确错误码；瞬时网络错误可有限重试；权限和安全错误不得由模型通过重试绕过。

一次工具执行至少经过“发现—选择—校验—授权—执行—回填”六个阶段：

| 阶段 | 失败示例 | 应由谁处理 |
| --- | --- | --- |
| 发现 | 工具未绑定或被权限策略裁剪 | Host 配置与动态工具过滤 |
| 选择 | 模型选择了查询天气而不是制度工具 | Tool 名称、描述、示例与模型能力 |
| 参数校验 | `employee_level=99` | Tool Schema 或 Pydantic 校验 |
| 授权 | 当前用户查询其他员工数据 | 工具服务端身份和对象级授权 |
| 执行 | 数据库超时或下游限流 | 工具实现的超时、重试和熔断 |
| 回填 | 结果过大、调用 ID 错位 | ToolNode 或 Host 的结果转换 |

给模型看的错误应当“可行动但不泄密”。例如参数错误可以返回 `INVALID_EMPLOYEE_LEVEL` 和合法范围；数据库连接串、SQL（Structured Query Language，结构化查询语言）、内部路径与堆栈只进入受保护日志。权限拒绝也不提供更换员工 ID 继续试探的建议。

只读工具也不等于无风险。一次昂贵查询、敏感数据读取或任意 URL 抓取仍可能造成拒绝服务、数据外泄或服务器端请求伪造，因此需要作用域、限流、结果大小和网络出口控制。

### 4.6 当前推荐的 Agent 入口

旧版项目常使用 `langgraph.prebuilt.create_react_agent`。在 LangChain v1 中，推荐入口已经是 `langchain.agents.create_agent`，它基于 LangGraph 运行时构建，并以 Middleware（中间件）作为主要扩展机制。

```python
from langchain.agents import create_agent


agent = create_agent(
    model=model,
    tools=[query_policy],
    system_prompt="你是制度助手。只调用与当前用户权限匹配的只读工具。",
)

result = agent.invoke(
    {"messages": [{"role": "user", "content": "查询 5 级员工差旅制度"}]}
)
print(result["messages"][-1].content)
```

当项目确实需要自定义图结构、跨节点状态、精确中断位置或多分支并发时，再下沉到 LangGraph 的 `StateGraph`。一般的工具循环直接使用标准 Agent，代码更少，边界也更完整。

`create_agent` 内部仍然执行工具循环：模型节点读取消息；如果返回工具调用，运行时执行工具并追加 ToolMessage；随后再次调用模型；没有工具调用时结束。标准入口已经处理了消息合并、工具路由和常见错误形状，业务授权与安全策略仍需应用自行提供。

Middleware（中间件）用于在循环的稳定边界插入横切逻辑。例如模型调用前裁剪历史和注入用户偏好，模型调用后检查输出，工具调用前动态隐藏无权限工具，工具调用周围记录耗时和转换错误。把这些逻辑散落进每个工具，会造成规则不一致；全部塞进 System Prompt，又无法形成强制控制。

选择标准 Agent 的验收条件如下：

1\. 固定输入下，能看到正确的工具调用与对应 ToolMessage，而不是只看最终文字。

2\. 无权限用户看不到高风险工具，即使手工构造工具名也会被服务端拒绝。

3\. 工具返回参数错误时，Agent 最多在限定次数内修正；权限错误不会重试绕过。

4\. 达到轮数、时间或费用上限时，返回已完成步骤和失败原因，而不是无限循环或空响应。

### 4.7 模型能力与私有部署后端

LLM（Large Language Model，大语言模型）接收 Token 序列并预测后续内容。Transformer 和 Attention（注意力）解释了模型内部怎样处理序列，但在第一次接入模型时，可以先把模型看成“接收消息、返回消息或工具调用的远程服务”。

应用开发者首先要验证模型能否满足任务：能接收多长的上下文，能否正确调用工具，能否稳定返回结构化数据，以及延迟、吞吐和成本是否可接受。需要做私有部署和性能优化时，再继续学习 Tokenizer（分词器）、量化和并行推理。

Qwen、vLLM 与 SGLang 是常见私有部署选项。vLLM 和 SGLang 都可以提供 OpenAI 兼容接口，使 `ChatOpenAI(base_url=...)` 复用调用方式，但接口一致不等于能力一致。接入时需要实测下列能力：

1\. System / Tool 等消息角色是否正确保留。

2\. Tool Calling 与并行 Tool Calling 是否按 Schema 返回。

3\. Structured Output、JSON Schema 和流式事件是否支持。

4\. 长上下文下的显存、首 Token 延迟和吞吐是否达到目标。

5\. 多模态输入格式、图片数量和大小上限是否一致。

6\. 超时、取消、健康检查和并发排队是否能被网关观测。

模型启动命令、量化参数、张量并行和显存比例高度依赖引擎版本与硬件。正确做法是把模型权重哈希、引擎镜像、启动参数、GPU（Graphics Processing Unit，图形处理器）型号和能力测试结果作为一个可回滚发布单元。

私有部署上线前应使用同一套能力样本做契约测试：

| 能力 | 最小测试 | 成功判据 | 常见“接口兼容但行为不兼容” |
| --- | --- | --- | --- |
| 消息角色 | System 规则与冲突 User 指令 | 稳定遵循受控规则 | 后端丢弃 System 或合并角色 |
| Tool Calling | 必填参数、枚举、并行调用 | 返回合法调用 ID 与参数 | 只输出带 JSON 的普通文本 |
| 结构化输出 | 可空字段、枚举、范围约束 | 服务端或客户端严格校验 | JSON 可解析但违反业务 Schema |
| 流式 | 文本、工具参数、取消 | 事件顺序可消费且取消生效 | 只把最终字符串分片发送 |
| 长上下文 | 近端与中部关键信息 | 召回质量和延迟达标 | 声称窗口足够但中部信息丢失 |
| 多模态 | 尺寸、格式、损坏文件 | 合法输入成功，非法输入安全失败 | 文本接口兼容但图片字段被忽略 |

基准结果应记录 P50、P95 延迟、首 Token 时间、输出 Token/秒、并发失败率和显存占用。平均值无法暴露排队尖峰；单条 Demo 也无法证明并发与长上下文能力。

## 5 LCEL 深入：组合、并发、流式与韧性

### 5.1 Runnable 统一接口

Runnable 是一份可组合的执行契约，并不对应某个固定的“链类”。组件通过这份契约说明自己接收什么、返回什么，以及如何参与调用、批处理、流式、配置和追踪。Prompt、模型、输出解析器、检索器与自定义函数因此可以出现在同一条数据流中。

| 接口 | 含义 | 典型用途 |
| --- | --- | --- |
| `invoke` / `ainvoke` | 单输入同步 / 异步调用 | 在线请求 |
| `batch` / `abatch` | 多输入批量调用 | 离线评测、批处理 |
| `stream` / `astream` | 增量输出 | 聊天界面、长报告 |
| `astream_events` | 观察节点级事件 | 自定义进度、调试与追踪 |
| `with_retry` | 为节点增加重试 | 限定在瞬时错误节点 |
| `with_fallbacks` | 主节点失败后切换备选 | 模型或服务降级 |
| `with_config` | 绑定标签、元数据和运行配置 | 追踪、租户与关联标识 |

同一个 Runnable 可以提供同步、异步、批量和流式入口，但这些入口解决的问题不同。

`batch` 通常只是并发发出多个单请求，不等于模型后端把多个样本合成一次批量推理。`ainvoke` 可以避免阻塞当前事件循环，却不会让模型本身推理得更快。`stream` 只有在上游组件真实产生增量时，才能降低首 Token 等待时间。

下面的例子把“同一输入、并行计算、统一聚合”变成可观察结果：

```python
from langchain_core.runnables import RunnableLambda, RunnableParallel


normalize = RunnableLambda(lambda x: x.strip())
features = normalize | RunnableParallel(
    normalized=RunnableLambda(lambda x: x),
    length=RunnableLambda(len),
    has_question_mark=RunnableLambda(lambda x: "?" in x or "？" in x),
)

assert features.invoke("  酒店上限是多少？  ") == {
    "normalized": "酒店上限是多少？",
    "length": 8,
    "has_question_mark": True,
}
```

这段断言检查了三个边界：`normalize` 的输出会传给每个并行分支；各分支按声明的键聚合；下游收到结构化字典，不需要依赖列表顺序。以后增加 `tenant_id` 时，也应沿这份字典显式传递，避免藏在闭包或全局变量中。

API 卡片：`RunnableSequence`

| 项目 | 内容 |
| --- | --- |
| 作用 | 串行执行，上一步输出作为下一步输入 |
| 构造 | `first \| second \| third` |
| 输入输出 | 由首尾节点决定，中间节点的类型必须能衔接 |
| 关键风险 | 中间加入不支持 transform 的 `RunnableLambda` 会推迟流式输出 |
| 生产建议 | 为每个边界定义清晰的字典或 Pydantic 类型，避免隐式字符串拼接 |

API 卡片：`RunnableParallel`

| 项目 | 内容 |
| --- | --- |
| 作用 | 把同一输入并行发送给多个 Runnable，结果按键聚合 |
| 构造 | `RunnableParallel(a=node_a, b=node_b)`，管道中的字典通常会自动转换 |
| 适用 | 同时取检索证据、用户画像与规则结果 |
| 风险 | 并发放大下游请求量；某一分支失败可能使整体失败 |
| 生产建议 | 设置并发上限、分支超时，并明确部分结果是否可接受 |

### 5.2 Passthrough、Assign 与动态路由

`RunnablePassthrough` 用于保留原输入；`assign` 用于在字典上追加计算字段；`RunnableLambda` 可以根据输入返回另一个 Runnable，实现动态路由。

```python
from langchain_core.runnables import RunnableLambda, RunnablePassthrough


enrich = RunnablePassthrough.assign(
    normalized_question=lambda x: x["question"].strip(),
    question_length=lambda x: len(x["question"].strip()),
)


def select_chain(x):
    return tool_chain if "天气" in x["normalized_question"] else rag_chain


dynamic_chain = enrich | RunnableLambda(select_chain)
```

输入 `{"question": "  新加坡天气  "}` 后，`assign` 会保留原字段，并在同一字典中加入两个计算字段：

```python
{
    "question": "  新加坡天气  ",
    "normalized_question": "新加坡天气",
    "question_length": 4,
}
```

因此，`RunnablePassthrough.assign` 的输入需要具备字典语义。上游若直接输出字符串，可以先执行 `RunnableLambda(lambda x: {"question": x})` 建立结构化边界。这样比让每个 Lambda 分别判断输入类型更清楚。

`select_chain` 返回的是“下一段 Runnable”，框架随后用同一份输入执行它。这里有两个容易漏掉的失败路径：路由函数需要覆盖未知意图，不能返回 `None`；各分支最终输出最好共享同一 Schema，否则下游会出现只在特定路线触发的字段缺失。

动态路由适合单次、无持久状态的选择。如果路由后还会循环、恢复或并发更新共享状态，应改用 LangGraph 条件边。

### 5.3 为重试与降级设置明确预算

下面沿用第 4.1 节的 `ChatOpenAI`。如果把重试责任放在 Runnable 层，应先把该模型初始化参数 `max_retries` 改为 `0`，避免 SDK 内层重试与 Runnable 外层重试相乘；采用 SDK 重试时则不再包同类 Runnable 重试。

```python
from openai import (
    APIConnectionError,
    APITimeoutError,
    InternalServerError,
    RateLimitError,
)


safe_model = model.with_retry(
    retry_if_exception_type=(
        APIConnectionError,
        APITimeoutError,
        InternalServerError,
        RateLimitError,
    ),
    stop_after_attempt=3,
    wait_exponential_jitter=True,
)

resilient_model = safe_model.with_fallbacks([backup_model])
```

这组异常只适用于本文的 OpenAI 兼容集成。更换模型提供商后，要改成该 SDK 对应的连接、超时、限流和服务端异常；捕获所有异常会把参数错误、权限错误等不可恢复问题也重复执行。

重试还会在不同层之间相乘。假设 HTTP 客户端尝试 3 次，Runnable 再尝试 3 次，队列消费者又投递 2 次，一条用户请求最多会产生 $3\times3\times2=18$ 次下游调用。生产系统通常指定一个主要重试层，并统一限制总次数、总耗时和退避时间。

| 错误类型 | 是否通常可重试 | 处理方式 |
| --- | --- | --- |
| 连接重置、短暂 429、部分 5xx | 是，但受总预算限制 | 指数退避、随机抖动并遵守服务端 `Retry-After` |
| 参数校验失败、权限不足、内容违规 | 否 | 立即失败并返回可操作原因 |
| 上下文超限 | 原样重试无效 | 裁剪、摘要或切换明确支持更长上下文的模型 |
| 写操作结果未知 | 不能盲目重试 | 使用幂等键查询结果，再决定补偿或重试 |

重试应尽量包住最小的、不产生副作用的网络节点。若把“扣款后生成回执”整条链一起重试，就可能重复扣款。写操作使用业务幂等键，并在重试前查询上一次执行结果。

降级过程需要对调用方可见。备用模型的上下文窗口、工具调用、结构化输出和安全能力可能不同，因此运行记录中要保留实际模型与降级原因，关键任务还要重新执行结果校验。

例如主模型支持原生结构化输出，备用模型只支持文本 JSON（JavaScript Object Notation，JavaScript 对象表示法）。此时降级路径需要增加解析和 Schema 校验；若无法保证同一业务契约，就应返回“服务暂不可用”，而不是生成看似成功但字段不可信的结果。

### 5.4 流式输出的能力边界

把最终字符串切成若干小块，并不等于端到端流式处理。要降低首字延迟，链上的组件需要逐步接收并转发增量。普通 `RunnableLambda` 没有 `transform` 时，会等待输入完整后再执行，因此它会成为流式链路中的阻塞点。

前端需要区分模型文本、工具开始、工具结束、节点状态和最终完成事件。模型临时文本只用于展示，不能代表业务已经提交；写操作则要等工具参数完整并通过校验后再执行。

一条带工具调用的流通常经历以下时间线：

| 顺序 | 事件 | UI（User Interface，用户界面）可以做什么 | 不能据此做什么 |
| --- | --- | --- | --- |
| 1 | `answer.delta` | 显示临时文本 | 标记业务流程成功 |
| 2 | `tool.pending` | 显示将要查询制度 | 把不完整参数提交给工具 |
| 3 | `tool.completed` | 展示工具已返回 | 假定返回值已通过业务校验 |
| 4 | `state.committed` | 更新确定的流程状态 | 省略审计记录 |
| 5 | `workflow.completed` | 固化最终答案与引用 | 继续接受同一运行的迟到事件 |

客户端应按 `run_id` 与单调递增的业务序号去重，并支持取消、断线重连和最终状态查询。只依赖 WebSocket 是否断开来判断成功，会把网络故障误判成业务失败。

### 5.5 监听器、追踪与配置传播

Runnable 配置中可以传递 `tags`、`metadata`、`run_name` 和并发设置。业务关联标识应贯穿 HTTP 请求、LangGraph thread、Runnable run 和 MCP 调用，但用户原始隐私、完整提示和凭据不应默认进入追踪系统。

```python
result = chain.invoke(
    {"question": "酒店上限是多少？"},
    config={
        "run_name": "policy_answer",
        "tags": ["rag", "production"],
        "metadata": {
            "trace_id": "trace-8f3a",
            "tenant_id": "tenant-a",
            "prompt_version": "policy-v7",
        },
        "max_concurrency": 4,
    },
)
```

配置传播解决的是关联与调度，不自动完成授权。节点读取到 `tenant_id` 后仍须使用服务端身份上下文校验资源范围；调用方传来的元数据不能直接作为可信权限。监听器也不能改变核心业务结果，否则关闭追踪后程序行为会发生变化。

验证配置传播时，至少在入口、检索、模型和 MCP 四处记录同一个 `trace_id`，并确认日志中没有访问令牌、原始证件号或完整敏感 Prompt。若某一层只能接收自己的请求 ID，应记录二者的映射。

## 6 上下文、对话历史与多模态

### 6.1 四种容易混淆的“记忆”

| 名称 | 生命周期 | 示例 | 合适载体 |
| --- | --- | --- | --- |
| 当前输入上下文 | 一次模型调用 | 系统提示、检索片段 | Prompt / Messages |
| 短期会话记忆 | 同一 thread | 最近对话、工具结果 | LangGraph Checkpointer |
| 长期业务记忆 | 跨 thread | 用户语言偏好、已授权档案 | LangGraph Store 或业务数据库 |
| 模型参数记忆 | 训练后固化 | 通用语言知识 | 模型权重，不用于保存用户事实 |

上下文窗口是有限的。把所有历史原样塞回模型，会导致成本、延迟和“中间信息遗失”问题。常见策略包括保留最近消息、按 Token 裁剪、对早期历史做结构化摘要，以及只检索与当前问题相关的长期记忆。

以“用户在会话 A 中询问制度，第二天在会话 B 中提交报销”为例，可以沿数据的有效期判断存放位置。

当前问题与本轮检索证据只服务这次模型调用，因此放入本次 Prompt。会话 A 的工具调用和未完成审批属于这条流程，保存在该 thread 的 Checkpoint。用户偏好的报销币种需要跨会话使用，可以在授权后写入 Store。制度事实会随版本更新，应保存在知识库，而不是写成用户偏好或依赖模型永久记住。

判断一项信息该放在哪里，可以连续问四个问题：本次调用后是否仍需存在；新会话是否仍需存在；它是用户可更改的事实还是通用能力；它是否需要删除、审计或按租户隔离。只要涉及删除和授权，就应落到可治理的数据存储，而不是依赖 Prompt 或模型“记住”。

| 状态变化 | 正确结果 | 常见错误 |
| --- | --- | --- |
| 用户更正“币种应为新币” | 更新可追踪的用户偏好并记录来源 | 继续相信旧摘要 |
| 制度从 v3 升到 v4 | 下一次检索命中新版本，并保留历史引用 | 把旧答案固化为长期记忆 |
| 用户请求删除个人偏好 | 删除 Store 记录并验证索引、副本和缓存 | 只让模型回复“已经忘记” |
| thread 恢复 | 恢复消息、工具结果和待执行节点 | 只恢复聊天文本，重复执行写操作 |

### 6.2 RunnableWithMessageHistory 的适用边界

`RunnableWithMessageHistory` 适合为一条链注入按 `session_id` 管理的历史，可使用内存或 SQLite 等存储实现。复杂 Agent 更推荐使用 LangGraph Checkpointer，因为工具循环、中断和恢复都需要保存完整图状态，而不仅是消息列表。

`RunnableWithMessageHistory` 每轮执行三步：先根据配置找到对应的历史记录，再把历史消息放入 Prompt，最后把本轮输入和输出追加回历史。

字段名需要前后一致。Prompt 使用 `history` 时，包装器的 `history_messages_key` 也应为 `history`。输入消息位于字典中的某个字段时，再通过 `input_messages_key` 指明该字段。

名称不一致时，程序可能顺利启动，却会在实际调用中丢失历史，或者把整个字典误当成一条消息。

最小验证不能只问“记得我的名字吗”，而应执行三轮：第一轮写入一个随机事实；第二轮用同一 `session_id` 读取；第三轮换一个 `session_id`，确认不能读到。随后重启进程：内存实现应丢失，持久化实现应恢复。这四步能分别暴露追加失败、会话串线和伪持久化。

无论采用哪种方式，都要区分 `session_id`、`user_id` 和 `tenant_id`。仅凭客户端传来的会话标识读取记录会造成越权；服务端需要把会话标识与已认证主体绑定。

这三个标识承担不同职责：`session_id` 定位对话序列，`user_id` 表示已认证主体，`tenant_id` 限定数据边界。服务端可以维护三者映射，但不能用字符串拼接代替授权查询。共享设备、转交工单和匿名转登录等场景还需要显式的会话归属迁移规则。

### 6.3 摘要的职责与审计边界

一份可用于后续对话的摘要，需要保留用户目标、已确认事实、未完成事项、工具副作用、引用来源和权限边界。生成摘要后，先核对这些关键字段，再决定是否用它替换提示词中的早期消息。财务、医疗和审批场景的原始记录仍按合规策略保存；压缩上下文与删除审计证据是两个不同问题。

自由文本摘要容易把“用户要求”误写成“已完成”。更稳妥的方式是先生成结构化摘要：

```python
from typing import Literal

from pydantic import BaseModel, Field


class ConversationSummary(BaseModel):
    goal: str
    confirmed_facts: list[str]
    pending_actions: list[str]
    completed_side_effect_ids: list[str]
    source_ids: list[str]
    approval_status: Literal["not_required", "pending", "approved", "rejected"]
    unresolved_questions: list[str] = Field(default_factory=list)
```

摘要节点的前置条件是原消息仍可读取，后置条件是关键业务 ID、待办和审批状态与原记录一致。验证失败时保留原历史并发出告警，不能用坏摘要覆盖唯一副本。摘要版本还应记录所用模型、Prompt 版本和被覆盖的消息范围，便于定位信息在哪次压缩中丢失。

### 6.4 多模态消息

多模态模型可接收文本、图片、音频或文件内容块，但具体格式由模型集成决定。图片常用公开 URL、对象存储签名 URL 或 Base64 编码。Base64 会增大请求体，应限制文件尺寸、像素、类型和数量，并在发送模型前完成病毒扫描和元数据清理。

多模态任务也应结构化验收。例如票据识别不应只返回一段描述，而应返回商户、日期、币种、金额、税额、置信度和证据区域；低置信度字段进入人工复核。

一条生产票据链通常包含六个边界：上传层验证真实文件类型与大小；安全层扫描恶意内容并移除不必要元数据；对象存储生成短时、最小权限引用；模型层接收受支持的内容块；结构化输出层校验金额与币种；业务规则层决定自动接受、补充材料或人工复核。

模型给出 `0.98` 置信度不等于真实准确率为 98%。阈值应通过人工标注集校准，并按字段分别统计。例如，总金额可能达到自动处理标准，而银行账号仍要求二次确认。旋转图片、模糊图、重复上传、手写金额、恶意二维码和超长 PDF（Portable Document Format，便携式文档格式）都应进入失败样本集。

多模态输入会扩大隐私暴露范围。日志通常只记录对象 ID、哈希、尺寸、类型和处理状态，不保存原始图片或长期有效的签名 URL。模型供应商的数据保留策略也要与业务合规要求一致。

删除文件时，还要处理它的派生数据。原文件、缩略图、OCR（Optical Character Recognition，光学字符识别）文本和向量索引都应关联同一个源数据 ID，这样一次删除请求才能覆盖完整数据链路。

## 7 Embedding、向量库与 RAG

### 7.1 RAG 解决的问题

RAG（Retrieval-Augmented Generation，检索增强生成）在生成前为模型提供外部证据。它适合知识经常变化、需要引用来源或不能重新训练模型的场景。RAG 不会自动保证正确：检索可能漏掉文档，排序可能选错片段，模型也可能忽略证据。

RAG 在模型回答前先取回外部证据，再把证据和问题一起交给模型。模型负责组织答案；知识的保存、版本管理和检索由外部系统负责。

整个系统分成两条链路。离线链路把文档清洗、切分并写入索引；在线链路接收问题，执行权限过滤，取回证据并生成答案。Chunk 指文档切分后的一个检索片段，Retriever 指负责查找相关片段的检索器。

两条链路必须使用兼容的 Embedding 模型、预处理规则和元数据结构，否则旧索引与新查询可能无法正确比较。

```mermaid
flowchart LR
    subgraph Offline["离线索引"]
        D["原始文档"] --> L["加载与清洗"]
        L --> S["切分 Chunk"]
        S --> E1["Embedding"]
        E1 --> V["向量库 + 元数据"]
    end
    subgraph Online["在线回答"]
        Q["用户问题"] --> RQ["问题改写 / 权限过滤"]
        RQ --> E2["同一 Embedding 空间"]
        E2 --> K["Top-K 检索 / 重排"]
        V --> K
        K --> P["证据化 Prompt"]
        P --> M["模型生成"]
        M --> A["答案 + 引用 + 置信信号"]
    end
```

以“5 级员工在新加坡的酒店上限是多少”为例，正确链路应产出以下证据：

| 阶段 | 输入 | 输出 | 可验证条件 |
| --- | --- | --- | --- |
| 解析 | `travel-policy-v3.pdf` | 带标题、页码的段落 | 表格金额与正文一致 |
| 索引 | 段落文本与权限标签 | 向量、文档 ID、内容哈希 | 向量维度一致，版本可追踪 |
| 检索 | 问题、租户、地区、职级 | 排序后的候选片段 | 正确条款进入 Top-K |
| 生成 | 问题与候选片段 | 答案、引用、拒答信号 | 金额能在引用中逐字定位 |

如果答案中的金额错误，先查看正确条款是否进入 Top-K。没有进入时，问题位于索引、查询或排序阶段；条款已经进入却未被采用，才继续检查 Prompt 与生成阶段。按这个顺序排查，能够避免在检索失败时反复修改系统提示。

### 7.2 Embedding 与相似度

Embedding（嵌入）把文本、图片等对象映射为固定维度向量，使语义相近对象在向量空间中更接近。最常见的相似度包括余弦相似度、点积和欧氏距离。

余弦相似度为：

$$
\operatorname{cos}(a,b)=\frac{a\cdot b}{\lVert a\rVert_2\lVert b\rVert_2}
$$

选择距离度量时，需要结合模型输出和索引配置。部分模型已经对向量归一化，此时点积与余弦的排序等价；有些索引则在创建时固定距离度量。入库与查询应使用同一向量模型、维度、归一化方式和文本预处理版本。

余弦相似度只比较方向，不直接证明两个句子在业务上等价。“酒店上限 800 元”和“酒店支出 8,000 元”共享大量词，仍然存在决定性数字差异；“不得报销”和“可以报销”也可能因为词汇接近而获得高分。因此向量召回之后常需要关键词约束、元数据过滤、重排器或规则校验。

查询结果还会受到分数定义的影响。部分向量库返回“越大越相似”的分数，另一些返回“越小越近”的距离。统一写死一个阈值通常没有意义。更稳妥的做法是针对具体模型、索引与语料，先用标注集观察正负样本分布，再确定拒绝阈值。

常见选项包括 OpenAI Embeddings、BGE（BAAI General Embedding）和 Qwen Embedding。实际选型至少评估中文、领域术语、最大输入长度、向量维度、推理吞吐、许可证和目标向量库支持。

更换 Embedding 模型通常意味着重建索引。稳妥迁移方式是为旧、新索引使用不同版本号，双写一段时间，在同一评测集上比较 Recall@K、延迟和过滤正确率，再切换查询别名；不能把不同维度或不同模型生成的向量混入同一索引。

### 7.3 文档切分如何影响检索质量

Chunk 太大时，一个向量混合多个主题，检索命中后上下文浪费；太小时，定义与条件被拆开，答案缺少完整证据。建议先按标题、段落、表格和代码块等结构边界切分，再用 Token 上限做二次裁剪。

每个片段应保存下列元数据：

1\. 稳定文档标识、版本和内容哈希。

2\. 标题路径、页码或段落锚点。

3\. 租户、部门、权限标签和生效日期。

4\. 来源 URL 或文件标识、解析器版本和入库时间。

5\. 父片段标识，便于命中小片段后扩展上下文。

切分参数没有适用于所有文档的固定答案，可以先比较 200、400、800 Token 三种大小，以及 0%、10%、20% 三种重叠率。每组参数都使用同一批问题，记录 Recall@5、候选片段长度、重复证据比例和生成费用。

观察失败样本比只看平均分更有用。标题能命中但限制条件丢失，通常说明切分破坏了上下文，需要按结构切分或补充父片段。一个片段混入多个制度主题，则可以减小片段，或先按标题重新划分边界。

表格、代码和扫描 PDF 是最常见的解析陷阱。表格切分必须把表头或行列语义带到每个片段；代码应按函数或类边界保留；OCR 文本应保存页坐标和置信度。仅检查“向量库里有 N 条记录”无法证明内容可用，应随机抽样查看原文、片段、元数据和回链是否一致。

增量更新需要内容哈希与稳定文档 ID。新版本入库成功后再切换可见版本，随后清理旧片段；若先删后建，索引重建期间会出现知识空窗。删除文档时也要按父文档 ID 删除所有派生 Chunk，不能只删源文件。

### 7.4 一个可读的两阶段 RAG

本节假设已经像第 4 章那样创建了 `model`，并配置 `EMBEDDING_MODEL_NAME` 与相应供应商密钥。示例把检索和生成拆成两步，便于分别断言证据与答案。

先在第 2.1 节的 `.venv` 中补齐本节依赖：

```bash
python -m pip install -U \
  "langchain-chroma>=1,<2" \
  "langchain-text-splitters>=1,<2"
```

这里使用 OpenAI 兼容的 Embedding（嵌入）接口，并沿用第 4.1 节的 `LLM_API_KEY` 与可选 `LLM_BASE_URL`。如果模型服务把聊天与 Embedding 部署在不同地址，应为二者设置独立配置，不能默认复用同一个 Base URL（基础地址）。

```python
import os

from langchain_core.documents import Document
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter


documents = [
    Document(
        page_content="差旅制度第 4 条：酒店上限为每晚 800 元。",
        metadata={"source": "travel-policy-v3", "section": "4"},
    )
]

splitter = RecursiveCharacterTextSplitter(chunk_size=400, chunk_overlap=60)
chunks = splitter.split_documents(documents)
vector_store = Chroma.from_documents(
    chunks,
    embedding=OpenAIEmbeddings(
        model=os.environ["EMBEDDING_MODEL_NAME"],
        api_key=os.environ["LLM_API_KEY"],
        base_url=os.getenv("LLM_BASE_URL"),
    ),
)
retriever = vector_store.as_retriever(search_kwargs={"k": 4})


def format_docs(docs: list[Document]) -> str:
    return "\n\n".join(
        f"[{d.metadata['source']}#{d.metadata['section']}] {d.page_content}"
        for d in docs
    )


prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "只依据证据回答；不足时说无法确认，并保留引用标识。"),
        ("user", "问题：{question}\n\n证据：\n{context}"),
    ]
)


def answer(question: str) -> dict:
    docs = retriever.invoke(question)
    if not docs:
        return {"answer": "无法确认：未检索到可用证据。", "sources": []}
    text = (prompt | model | StrOutputParser()).invoke(
        {"question": question, "context": format_docs(docs)}
    )
    return {"answer": text, "sources": [d.metadata for d in docs]}
```

预期答案引用 `travel-policy-v3#4`。答案出现偏差时，分别检查正确片段是否进入 Top-K，以及模型是否忠实使用片段。两项检查对应不同故障层，不能一概归因于模型。

最小验收应保留中间结果：

```python
question = "酒店每晚上限是多少？"
retrieved = retriever.invoke(question)

assert retrieved
assert any(doc.metadata["section"] == "4" for doc in retrieved)

response = answer(question)
assert response["sources"]
assert any(source["source"] == "travel-policy-v3" for source in response["sources"])
```

这组断言不把自然语言答案固定成某一句，因此不会因措辞变化变脆；金额正确性、引用一致性与拒答质量应在独立评测器中检查。真实系统还应让 `sources` 只包含模型实际引用的片段，而不是无条件返回全部 Top-K，否则“有来源列表”并不能证明答案受来源支持。

### 7.5 两阶段、Agentic 与 Hybrid RAG

| 类型 | 检索时机 | 优点 | 风险 |
| --- | --- | --- | --- |
| Two-step RAG | 每个问题先固定检索一次 | 延迟稳定、容易评测 | 复杂问题可能一次检索不够 |
| Agentic RAG | Agent 决定是否检索、查什么、查几次 | 适合开放研究问题 | 成本与路径不确定，可能不检索或过度检索 |
| Hybrid RAG | 固定流程中允许受控改写、重排或二次检索 | 在可控性与灵活性间折中 | 状态和评测更复杂 |

企业问答通常先从 Two-step RAG 建立基线。只有评测证明需要多轮检索时，再增加问题分解、查询改写、关键词与向量混合检索、Cross-Encoder 重排或 Agentic RAG。

例如，“比较新旧差旅制度，并判断 900 元酒店能否报销”同时包含版本比较、身份过滤和金额判断。Hybrid RAG 可以先由代码确定用户职级与出差日期，再分别检索 v3、v4 条款，最后把标明版本的证据交给模型解释差异。

若让 Agent 自己决定所有检索步骤，还要额外验证三件事：它是否同时查询了新旧版本，是否保留权限过滤，以及证据不足时是否会停止推断。只有这些评测证明开放决策带来收益，才值得采用 Agentic RAG。

升级条件应写成评测门槛。例如基线在跨文档问题上的 Recall@5 低于目标且查询分解能稳定改善 8 个百分点，才引入分解节点；若收益只有 1 个百分点但 P95 延迟翻倍，应保留简单方案。

### 7.6 向量库选型与安全

FAISS（Facebook AI Similarity Search）适合本地原型和单机索引；Chroma 易于开发体验；Milvus 等服务型向量数据库更适合分布式容量、索引治理和在线服务。选型应比较过滤能力、更新一致性、备份恢复、多租户隔离、索引构建时间、召回延迟和运维成本。

加载 FAISS 时常见的 `allow_dangerous_deserialization=True` 意味着信任 Python Pickle 反序列化内容，恶意文件可能执行代码。只能加载自己生成且完整性可验证的索引，不能对用户上传或未知下载文件启用。

权限过滤应在检索查询阶段完成。若先跨租户取回文本，再在交给模型前删除，敏感片段可能已经进入日志、缓存或追踪；同时，无权结果还会占满 Top-K，使系统误以为没有可用答案。数据库负责强制执行 tenant、资源级访问控制和生效时间过滤，应用层再补一层防御性校验。

索引备份同样是敏感数据。向量可能泄露成员关系或近似内容，元数据更可能直接包含文件名、部门和用户 ID。备份、快照、测试环境和离线评测副本都应使用与主数据一致的加密、访问控制与删除策略。

### 7.7 RAG 评测闭环

至少建立四层指标：检索层看 Recall@K、MRR（Mean Reciprocal Rank，平均倒数排名）和过滤正确率；生成层看事实一致性、引用正确性和拒答质量；系统层看延迟、Token、费用与失败率；业务层看问题解决率和人工升级率。

若评测集有 $N$ 个问题，问题 $i$ 的相关文档集合为 $R_i$，Top-K 检索结果为 $D_i^K$，则：

$$
\operatorname{Recall@K}=\frac{1}{N}\sum_{i=1}^{N}\frac{|R_i\cap D_i^K|}{|R_i|}
$$

MRR 只关心第一个相关结果的位置，适合“一个核心证据即可回答”的任务；需要组合多个条款时，Recall@K 更有意义。两者都不能判断最终答案是否忠实，因此要用带证据的人工标注或受控评测器检查每个关键断言。

一个最小样本应包含问题、允许访问的租户与身份、期望证据 ID、可接受答案要点、是否应拒答和风险等级。例如 100 条集合中 20 条不可回答，如果系统全部强答，即使可回答问题得分很高，也不能上线。

评测集应覆盖可回答、不可回答、过期制度、权限隔离、同义表达、跨文档组合、表格数字和提示注入。线上点踩样本经脱敏和审核后进入回归集。

| 现象 | 优先检查 | 不宜先做的事 |
| --- | --- | --- |
| 正确文档完全没命中 | 解析、切分、Embedding、过滤条件 | 改生成 Prompt |
| 正确文档在第 20 位 | 查询改写、混合检索、重排 | 盲目扩大上下文到 Top-50 |
| 正确片段在 Top-K 但答案错 | 证据格式、冲突版本、生成忠实度 | 重建整个向量库 |
| 无答案问题仍被强答 | 阈值、拒答指令、负样本 | 只增加温度随机性 |
| A 租户看到 B 租户资料 | 服务端过滤与缓存键 | 仅在界面隐藏引用 |

## 8 LangGraph 核心：用状态图管理不确定流程

### 8.1 State、Node、Edge 与 Reducer

State 是图中共享的事实；Node 读取当前状态并返回更新；Edge 决定执行顺序；Reducer 决定同一个状态键收到多个更新时如何合并。

可以用四个问题理解这些概念：流程此刻掌握哪些事实；当前节点计算什么；下一步转向哪里；多个更新同时到来时如何合并。图中的箭头只是表面结构，更重要的是中间状态、分支依据和恢复位置都变得可检查。

```mermaid
flowchart LR
    START((START)) --> PLAN["plan"]
    PLAN --> RETRIEVE["retrieve"]
    PLAN --> PROFILE["load_profile"]
    RETRIEVE --> ANSWER["answer"]
    PROFILE --> ANSWER
    ANSWER --> CHECK{"evidence_ok?"}
    CHECK -->|"否"| RETRIEVE
    CHECK -->|"是"| END((END))
```

节点宜写成可独立测试的普通函数或 Runnable，并通过返回值提交状态增量。原地修改全局字典会破坏检查点、重放和并行合并的一致性。

LangGraph 会把当前可以运行的一批节点放在同一个 Super-step（超级步）中执行，完成后统一合并它们的状态更新。上图的一次运行可以展开为：

| Super-step | 可运行节点 | 节点返回的增量 | 合并后的关键状态 |
| --- | --- | --- | --- |
| 0 | `plan` | 查询计划与所需资料 | `plan=[policy, profile]` |
| 1 | `retrieve`、`load_profile` | 制度证据；用户职级 | 两个结果同时进入 State |
| 2 | `answer` | 草稿、引用 ID | `draft=...` |
| 3 | `check` | `evidence_ok=False` | 路由回 `retrieve` |
| 4 | `retrieve` | 补充证据 | 证据通过 Reducer 追加 |
| 5 | `answer` | 新草稿、引用 ID | 等待再次校验 |
| 6 | `check` | `evidence_ok=True` | 路由到 END |

Node 返回的是更新量，不是必须返回完整 State。若 `retrieve` 只返回 `{"evidence": [...]}`，其他键由运行时保留；若节点原地修改 `state["evidence"]` 再返回同一对象，测试可能暂时通过，但并发合并和重放的结果不再可靠。

### 8.2 并发更新与 Reducer

```python
import operator
from typing import Annotated, TypedDict

from langchain_core.messages import AnyMessage
from langgraph.graph.message import add_messages


class AgentState(TypedDict, total=False):
    messages: Annotated[list[AnyMessage], add_messages]
    evidence: Annotated[list[str], operator.add]
    final_answer: str
```

`add_messages` 不只是把两个列表拼起来。它会根据消息 ID 更新已有消息，因此适合合并模型消息与 ToolMessage。`operator.add` 的语义更直接：把新的证据列表追加到旧列表后面。

同一 Super-step（超级步）中，两个并发节点可能同时更新 `evidence`。没有 Reducer 时，LangGraph 不知道应该覆盖还是追加，因此会报告并发更新冲突。若某个字段只允许一个节点写入，应在图结构上保证单写，而不是依赖执行先后顺序。

假设 `retrieve_policy` 返回 `{"evidence": ["policy#4"]}`，`retrieve_faq` 同时返回 `{"evidence": ["faq#9"]}`。使用 `operator.add` 后，最终列表会保留两份证据。

如果没有 Reducer，LangGraph 不知道该怎样合并两个并发更新，因此会报告冲突，而不是随意保留最后完成的结果。

Reducer 的合并语义需要保持清晰。列表追加通常满足结合律，但不会自动去重，并发完成顺序也不能代表业务优先级。如果证据要求稳定排序和去重，可以让节点返回带 `source_id`、`rank` 的对象，再由汇合节点集中处理。相关性应来自排序字段，而非网络响应的先后顺序。

| 字段 | 推荐合并语义 | 不变量 |
| --- | --- | --- |
| `messages` | `add_messages` 按消息 ID 追加或更新 | ToolMessage 能关联已有 tool call |
| `evidence` | 追加后在汇合节点去重排序 | 相同来源版本不重复 |
| `risk_level` | 显式取最高风险 | 后续节点不能把高风险静默降级 |
| `final_answer` | 单写 | 只有最终生成节点能更新 |
| `token_usage` | 求和 | 每个调用只计一次 |

### 8.3 条件边、Command 与 Send

条件边适合“节点执行完后根据状态选下一节点”。`Command` 可以在一个节点返回中同时更新状态并指定跳转，适合工具交接、人工恢复和跨图导航。`Send` 适合 Map-Reduce 式动态扇出，例如为多个章节并行生成摘要。

选择原则如下：

1\. 路由逻辑能独立描述时用条件边，图结构更直观。

2\. 路由与状态更新不可分割时用 `Command`，并为返回类型声明可能的目标节点。

3\. 并行任务数量由运行时输入决定时用 `Send`，同时设置并发与聚合策略。

`Command` 会把状态更新和控制决策放进同一个原子返回值。例如审批节点可以在记录决策的同时跳到提交或结束：

```python
from typing import Literal, TypedDict

from langgraph.types import Command


class ApprovalRouteState(TypedDict):
    approved: bool
    status: Literal["pending", "approved", "rejected"]


def route_approval(
    state: ApprovalRouteState,
) -> Command[Literal["submit", "reject"]]:
    if state["approved"]:
        return Command(update={"status": "approved"}, goto="submit")
    return Command(update={"status": "rejected"}, goto="reject")
```

`Send` 用于在运行时创建数量不固定的并行任务。例如输入三个章节时，可以生成三个 `Send("summarize", {"section": section})`。每个任务只处理自己的章节，结果再通过 State 中声明的 Reducer 汇合。

动态并行需要设置上限。系统应限制最大章节数、并发数和单任务耗时，并定义部分任务失败后的处理方式；否则一份异常输入可能展开成数千次模型调用。

选择错误的典型信号是：用条件边维护几十个动态实例，或在一个 `Command` 节点里隐藏所有业务流程。前者需要 `Send`，后者应拆回可观察的节点与边。

### 8.4 手写一个可观察工具循环

标准 Agent 已经提供工具循环，但手写一个最小图有助于看清运行时究竟发生了什么。下面的图只有两个节点：`model` 负责让模型决定是否调用工具，`tools` 负责执行调用并把结果写回消息列表。

```python
from typing import Annotated, TypedDict

from langchain_core.messages import AnyMessage
from langgraph.graph import END, START, StateGraph
from langgraph.graph.message import add_messages
from langgraph.prebuilt import ToolNode


class ToolState(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]


tools = [query_policy]
model_with_tools = model.bind_tools(tools)


def call_model(state: ToolState) -> dict:
    response = model_with_tools.invoke(state["messages"])
    return {"messages": [response]}


def next_step(state: ToolState) -> str:
    last_message = state["messages"][-1]
    return "tools" if last_message.tool_calls else "end"


builder = StateGraph(ToolState)
builder.add_node("model", call_model)
builder.add_node("tools", ToolNode(tools))
builder.add_edge(START, "model")
builder.add_conditional_edges(
    "model",
    next_step,
    {"tools": "tools", "end": END},
)
builder.add_edge("tools", "model")
tool_graph = builder.compile()
```

这段代码展示了 Agent 的基本循环：模型产生工具调用，`ToolNode` 执行并追加 ToolMessage，结果再次交给模型，直到没有新的工具调用。生产环境还要补上最大轮数、工具白名单、超时、参数校验和异常路径，避免模型长期循环。

当前 `ToolNode(tools)` 的默认错误策略会把模型参数不合法等调用错误转换为 ToolMessage，但工具函数内部的执行异常默认继续抛出。只有确认某类错误可以安全反馈给模型修正时，才通过 `handle_tool_errors` 指定异常类型或处理函数；权限错误和结果未知的写操作不应交给模型反复试探。

一次“查询酒店上限”的消息时间线如下：

| 轮次 | `messages` 尾部 | `next_step` | 必须成立的契约 |
| --- | --- | --- | --- |
| 0 | HumanMessage | 进入 `model` | 用户与租户身份已在图外认证 |
| 1 | AIMessage，含 `query_policy` tool call | `tools` | 工具名允许，参数 Schema 合法，call ID 唯一 |
| 2 | ToolMessage，关联同一 call ID | 回 `model` | 工具错误以受控结果或异常呈现 |
| 3 | AIMessage，无 tool call | END | 最终答案引用工具结果，未越权扩展事实 |

验证时应使用可预测的假模型或预制 AIMessage，分别测试无工具直接结束、一次工具后结束、未知工具、工具抛错和超过最大轮数。只用真实模型手点一次，无法覆盖循环终止条件。

`ToolNode` 负责通用调用装配，不代表业务安全已经完成。写工具仍需在执行层做授权、幂等、审计和必要的人工确认；模型说“用户已同意”不是可信授权证据。

### 8.5 区分图状态与业务数据模型

图状态只保留流程需要的最小快照。大文件、完整数据库记录和超长工具结果应存到对象存储或业务数据库，状态中只放稳定引用、摘要和内容哈希。这样检查点更小，序列化更可靠，隐私边界也更清晰。

一个状态字段至少要有名称、类型、写入节点、Reducer、保留期限和敏感级别。`request_id`、`policy_version`、`approval_status` 适合进入图状态；整份 PDF、数据库连接、打开的文件句柄和模型客户端不适合，因为它们体积大、不可序列化或无法在另一个进程恢复。

State Schema 变更需要版本策略。新增可选字段通常可向后兼容；重命名节点、删除必填字段、改变 Reducer 或更改字段含义可能让暂停中的 thread 无法恢复。可在状态中保存 `schema_version`，升级时先读取旧快照、运行纯函数迁移、验证不变量，再交给新图执行。

### 8.6 Graph API 卡片

| API | 作用 | 注意点 |
| --- | --- | --- |
| `StateGraph(State)` | 声明状态图 | State 应有明确类型和合并语义 |
| `add_node` | 注册节点 | 节点名应稳定，改名会影响恢复与观测 |
| `add_edge` | 添加确定边 | 无条件边过多可能触发意外并发 |
| `add_conditional_edges` | 添加动态分支 | 路由返回值与映射必须覆盖异常路径 |
| `compile` | 编译并注入 checkpointer、store 等能力 | 编译不是部署；生产持久化需外部后端 |
| `invoke` / `stream` | 执行或观察图 | 线上调用应带 thread、用户和追踪配置 |

从定义到运行，可以把一张图分成六步：

1\. 定义 State，并为可能并发更新的字段选择 Reducer。

2\. 注册能够独立测试的 Node。

3\. 添加 `START`、`END`、普通 Edge 和异常路由。

4\. 调用 `compile()`，同时注入 Checkpointer、Store 等运行依赖。

5\. 使用输入和配置执行，观察节点更新、最终状态或中断。

6\. 需要继续旧流程时，使用相同 `thread_id` 恢复。

`compile()` 只能检查部分图结构。权限、外部服务、业务终止条件和部署兼容性仍要通过测试验证。

图评审时可以沿每条路径问三个问题：这条边在什么证据下触发；该节点失败后状态是否仍可解释；从此处重放是否会重复真实副作用。若任何答案只存在于开发者脑中，就应转成 State 字段、结构化路由结果或测试。

## 9 持久化、流式、人工介入与故障恢复

### 9.1 Checkpointer 与 Store

Checkpointer 在每个关键 Super-step 后保存 thread 状态，用于短期记忆、中断恢复、时间旅行和容错。Store 保存跨 thread 的长期数据，例如用户偏好或共享记忆。

Checkpoint 保存的是图执行到某一步时的完整快照，不只是聊天消息。快照通常包括当前 State、下一批待执行节点、运行配置、元数据以及上一步的任务或错误。

这些信息让运行时在故障后从明确边界继续，而不用猜测流程执行到了哪里。同一 Super-step 中已经完成的并发写还可能以 pending writes 保存，从而避免恢复时重做已经成功的任务。

```mermaid
flowchart TB
    U["user_id = u-42"] --> T1["thread_id = t-100"]
    U --> T2["thread_id = t-101"]
    T1 --> C1["Checkpoint 1"] --> C2["Checkpoint 2"]
    T2 --> C3["Checkpoint 1"]
    U --> STORE["Store：跨会话偏好"]
```

开发时可以使用 `InMemorySaver`，生产应使用 PostgreSQL、MongoDB、Redis 等受支持的持久化实现，并完成连接池、迁移、备份、加密和保留策略。异步图应使用相应异步 Saver，避免阻塞事件循环。

Checkpointer 与 Store 的区别可以用两个问题判断：数据是否属于一次具体工作流的执行位置；数据是否需要跨多个 thread 被查找。待审批状态属于前者，用户语言偏好属于后者。订单真相、财务账本和权限关系通常属于业务数据库，不能因为 Store 可跨会话就搬进 Agent 记忆。

生产验收要主动杀进程：让图在工具完成后、审批前和并行节点部分完成时分别中断进程，重启后用相同 `thread_id` 恢复，确认不会丢失状态或重复副作用。只验证正常调用两次，不足以证明持久化。

### 9.2 thread_id 只负责定位恢复历史

```python
from langgraph.checkpoint.memory import InMemorySaver


graph = builder.compile(checkpointer=InMemorySaver())
config = {
    "configurable": {"thread_id": "tenant-a:user-42:case-100"},
    "metadata": {"tenant_id": "tenant-a", "user_id": "user-42"},
}

result = graph.invoke(
    {"messages": [{"role": "user", "content": "查询 5 级员工差旅制度"}]},
    config=config,
)
```

这段代码沿用第 8.4 节的 `builder`，所以输入仍要满足 `ToolState.messages`。换成另一套 State，或者传入空消息，都不能证明这条恢复链路有效。

`thread_id` 只负责找到对应的 Checkpoint。服务端应先验证用户身份和业务对象归属，再生成或校验 `thread_id`，并在读取检查点时附加租户过滤。若直接使用用户提交的任意 `thread_id` 读取数据，可能造成会话劫持。

请求处理顺序应是“认证主体 → 查询该主体是否拥有业务 case → 在服务端取得或创建 thread → 调用图”，而不是“接收 thread 字符串 → 直接加载 Checkpoint → 再看是不是该用户”。后一种顺序已经把敏感状态读入了无权请求的进程、日志或缓存。

`thread_id` 还不应承担幂等键职责。它标识一条可继续的执行历史；同一 thread 中可能提交多个业务动作。每个写动作仍需由 `request_id + operation` 等稳定业务信息生成独立幂等键。

### 9.3 Dynamic Interrupt 的执行与恢复

`interrupt()` 让节点暂停并把负载返回调用方；恢复时用相同 `thread_id` 和 `Command(resume=...)` 继续。

下面给出一段独立可运行代码，避免把审批 State 误传给第 8.4 节的工具循环：

```python
from typing import TypedDict

from langgraph.checkpoint.memory import InMemorySaver
from langgraph.graph import END, START, StateGraph
from langgraph.types import Command, interrupt


class ApprovalState(TypedDict, total=False):
    amount: int
    evidence: str
    approved: bool


def approve_expense(state: ApprovalState) -> dict:
    decision = interrupt(
        {
            "kind": "expense_approval",
            "amount": state["amount"],
            "evidence": state["evidence"],
        }
    )
    return {"approved": bool(decision)}


approval_builder = StateGraph(ApprovalState)
approval_builder.add_node("approve_expense", approve_expense)
approval_builder.add_edge(START, "approve_expense")
approval_builder.add_edge("approve_expense", END)
approval_graph = approval_builder.compile(checkpointer=InMemorySaver())

approval_config = {"configurable": {"thread_id": "expense-100"}}
first = approval_graph.invoke(
    {"amount": 900, "evidence": "制度上限为 800 元"},
    config=approval_config,
)
print(first["__interrupt__"][0].value)

resumed = approval_graph.invoke(
    Command(resume=True),
    config=approval_config,
)
print(resumed["approved"])
```

第一次调用的成功判据是 `first["__interrupt__"]` 中出现审批负载。此时流程处于暂停状态，还不会返回最终 `approved`。

第二次使用相同 `thread_id` 恢复后，`resumed["approved"]` 才应为 `True`。生产中还要把 `InMemorySaver` 换成持久化实现，并验证进程重启后仍可恢复。

调用方还要保存一个明确的审批记录：审批人身份、显示给他的证据版本、选择、时间、原因与待恢复的 thread。`Command(resume=True)` 只是把值送回图，不等于证明这个值来自有权限的审批人。

这里最容易忽略的是恢复语义：包含 `interrupt()` 的节点会从头重新执行。因而，中断前的副作用需要具备幂等性，或者移到后续独立节点。邮件发送、扣款和无法去重的工单创建都不适合放在 `interrupt()` 之前。

中断还有三条硬规则：

1\. 避免用宽泛 `try/except` 捕获 `interrupt()`，它通过特殊控制流暂停图。

2\. 同一节点内多个中断的顺序不能随条件变化，否则恢复值可能对应错误位置。

3\. 返回给审批人的负载必须可序列化，且不能泄露不属于审批人权限的信息。

若审批人在等待期间制度版本变化，恢复节点必须决定继续使用当时证据、重新校验还是让审批失效。这个策略应进入 State，例如保存 `evidence_hash` 和 `approval_expires_at`；否则“批准的对象”和“最终提交的对象”可能不是同一个版本。

### 9.4 为副作用节点建立幂等语义

```python
def submit_expense(state: dict) -> dict:
    idempotency_key = f"{state['tenant_id']}:{state['request_id']}:submit"
    existing = expense_api.find_by_idempotency_key(idempotency_key)
    if existing:
        return {"submission_id": existing.id}

    created = expense_api.create(
        payload=state["validated_payload"],
        idempotency_key=idempotency_key,
    )
    return {"submission_id": created.id}
```

幂等键应来自稳定业务标识，而不是每次重试都会变化的随机值。数据库写入还可用唯一约束、事务 Outbox 或状态机防止重复执行。

上面的“先查再创建”若没有服务端唯一约束仍存在竞态：两个并发请求可能都查询不到，然后分别创建。幂等保障应由目标服务或数据库在同一原子操作中接受幂等键；客户端查询只是优化和恢复手段。

副作用节点应返回稳定的外部结果 ID 与结果状态，并把它们写入 Checkpoint。若调用超时但服务端可能已成功，下一次执行先用幂等键查询，再判断是否需要创建。对于不支持幂等的旧系统，可用本地事务表、Outbox（事务发件箱）和单消费者串行化降低风险，但仍需定义人工对账与补偿流程。

### 9.5 流模式与前端消费

LangGraph 可以流出状态更新、完整值、消息 Token、自定义进度和调试事件。不同版本会调整事件版本与字段，前端宜通过业务事件适配层消费，避免直接绑定全部内部节点对象。

这里容易混淆，是因为 LangGraph 同时提供了两层流式 API（Application Programming Interface，应用程序编程接口）：

| 入口 | 能拿到什么 | 更适合谁 |
| --- | --- | --- |
| `stream` / `astream`，事件外壳为 `version="v2"` | `updates`、`values`、`messages`、`custom`、`checkpoints`、`tasks`、`debug` 等底层事件 | 需要观察节点和状态变化的框架代码 |
| `stream_events(..., version="v3")` | 分开的 `stream.messages`、`stream.values`、`stream.subgraphs`、`stream.interrupts` 和 `stream.output` | 只想消费消息、状态与最终结果的应用代码 |

这里的 v2 和 v3 属于不同调用层。v3 并不表示所有 `stream_mode` 都已失效，只是给应用提供了更容易消费的返回方式。

```python
stream = graph.stream_events(
    {"messages": [{"role": "user", "content": "酒店上限是多少？"}]},
    version="v3",
)

for message in stream.messages:
    for token in message.text:
        print(token, end="", flush=True)

final_state = stream.output
```

如果需要逐节点原始增量，继续使用底层 `stream_mode="updates"`；如果应用只想分别消费模型消息、状态和最终输出，typed projection（类型化投影）更少依赖内部事件结构。升级前应先记录锁定版本的真实事件样本，再让适配层把它们转成业务事件。

前端可将稳定的业务事件封装为自己的协议，例如 `answer.delta`、`tool.pending`、`approval.required`、`workflow.completed` 和 `workflow.failed`。这样框架升级不会直接破坏客户端。

每个业务事件至少包含 `event_id`、`run_id`、`sequence`、`type`、`occurred_at` 和脱敏后的 `payload`。同一次运行内用严格递增的 `sequence` 排序，时钟时间只负责记录发生时刻。

断线重连后，客户端根据最后确认的序号续传，或者直接查询最终状态。服务端还要区分两种情况：只是网络流断开，但图仍在运行；图本身已经失败。

### 9.6 时间旅行、重放与外部副作用

检查点历史可以用于查看过去状态、从某个点分叉和复现错误。但外部世界不会随状态一起倒退：已经发送的邮件、订单和支付仍然存在。重放前必须标记哪些节点是纯计算、只读调用、可幂等写入或不可重放副作用。

“Replay（重放）”通常复用某个历史检查点及其后续已记录步骤，以重现路径；“Fork（分叉）”先用 `update_state` 一类能力在历史点写入修正值，再从新配置执行另一条分支。两者都创建新的执行历史，不会修改过去已经发生的事实。

例如，原流程依据 `policy-v3` 拒绝了 900 元报销。调试时可以从检索后的 Checkpoint 分叉，把证据换成 `policy-v4`，再观察判断是否改变。

这次分叉不能再次调用真实提交工具，因为已经发生的外部操作不会被撤销。安全做法是替换所有副作用节点，或者由可信代码设置 `execution_mode="simulation"`，强制工具只做模拟。这个开关不能来自模型输出，还要用测试证明模型无法修改它。

### 9.7 LangGraph 本地服务与部署边界

`langgraph.json` 把图名称映射到 Python 模块中的已编译 Graph，可供 LangGraph CLI（Command-Line Interface，命令行界面）、本地开发服务和 SDK 加载。它适合在 Studio 中观察状态、节点和运行轨迹，但开发服务不应直接暴露到公网。

部署时应分别验证 Graph 导入、依赖构建、环境变量、持久化后端、健康检查、并发、取消、滚动升级和旧 Checkpoint 兼容。Graph 代码升级若修改节点名或 State Schema，需要显式迁移策略；否则正在暂停的 thread 可能无法恢复。

滚动升级前要逐项验证兼容性：

1\. 旧代码能否读取新 Checkpoint？

2\. 新代码能否恢复旧 Checkpoint？

3\. 暂停在不同节点的 thread 能否继续运行？

4\. Store 与业务数据库的 Schema 是否同时兼容？

如果不能双向兼容，可以让旧版本实例只处理完旧 thread，新版本只接收新 thread，避免混合恢复。

健康检查要分层。进程存活只证明 Web 服务响应；就绪检查还应验证图可导入、Checkpoint 后端可写、关键配置存在；深度合成检查则定期执行不产生费用或副作用的最小图，确认流事件和恢复契约未损坏。

Gradio 等轻量 UI 适合快速演示，但 UI 层不应直接持有模型或 MCP 密钥，也不应自行决定 thread 的租户归属。生产请求应先经过统一 API 的认证、限流和审计。

## 10 MCP：把工具从代码细节升级为协议契约

### 10.1 MCP 的组件与能力

MCP 采用 Host、Client、Server 架构。Server 能暴露三类核心能力：

| 能力 | 谁主导选择 | 用途 | 示例 |
| --- | --- | --- | --- |
| Tools（工具） | 通常由模型或应用决定调用 | 执行动作或计算 | 查询天气、创建工单 |
| Resources（资源） | 通常由应用选择并提供给模型 | 读取可寻址上下文 | 文件、数据库记录、服务状态 |
| Prompts（提示模板） | 通常由用户或应用显式选择 | 提供可复用交互模板 | 代码审查、事故复盘模板 |

工具、资源和提示都只是能力描述，不等于授权。Server 必须再次验证调用主体、租户、参数和资源范围。

Host 是用户直接使用并信任的应用，负责权限确认、模型协调和 Server 隔离。Client 是 Host 内部连接某个 Server 的协议客户端。Server 负责发布边界清晰的外部能力。

一个 Host 可以连接多个 Server，但每条连接都要独立处理认证、能力缓存和故障。某个 Server 不应看到其他 Server 的凭据或完整上下文。

一次工具调用通常经过下面六步：

1\. Host 取得工具列表和输入 Schema。

2\. 模型或业务代码选择工具。

3\. 高风险操作先让用户确认。

4\. Client 发出带唯一请求 ID 的调用。

5\. Server 校验身份和参数，再返回结构化结果。

6\. Host 裁剪敏感内容后，把结果交给模型。

模型选择了工具，不代表 Server 已经授权。用户确认和 Server 校验都不能省略。

MCP 负责让不同进程、语言和产品通过同一协议使用能力，但它不会取代内部业务 API。Server 内部仍可调用 REST（Representational State Transfer，表述性状态转移）、gRPC（Google Remote Procedure Call，Google 远程过程调用）或数据库。

可以把 MCP 理解为面向 Agent 的统一门面。真正的业务规则、数据一致性和事务处理，仍由内部 API 与数据库负责。

### 10.2 数据层与传输层

数据层定义消息和能力语义；传输层负责把消息送达。STDIO 适合 Host 启动本地子进程，部署简单且不暴露网络端口；Streamable HTTP（可流式 HTTP）适合远程多客户端服务，需要 TLS（Transport Layer Security，传输层安全）、认证、限流和代理配置。

| 传输 | 适合场景 | 关键风险 |
| --- | --- | --- |
| STDIO | 桌面应用、本地开发、单用户工具 | 标准输出污染、子进程权限过大、路径和环境变量不受控 |
| Streamable HTTP | 远程服务、团队共享、弹性部署 | 未授权访问、跨租户泄露、代理超时、请求伪造 |
| 旧 HTTP+SSE | 仅旧客户端兼容 | 已弃用，不应作为新系统默认方案 |

SSE（Server-Sent Events，服务器发送事件）在早期 MCP 中曾是独立传输。2025 年的 Streamable HTTP 已取代旧 HTTP+SSE，2026-07-28 修订又改为以 POST 为核心的无状态请求模型。

新项目直接采用当前 Streamable HTTP 语义。需要流式响应，并不代表还要保留旧 SSE 端点。

数据层使用 JSON-RPC（JSON Remote Procedure Call，JSON 远程过程调用）表达请求、响应、通知和错误。请求 ID 只负责匹配请求与响应，不能当作用户身份或幂等键。通知不要求响应，适合传递进度或变更；错误对象则要提供稳定代码和安全消息。

传输层可以改变连接和流式方式，但工具的业务含义不能改变。同一个工具通过 STDIO 或 HTTP 调用时，应接受相同含义的输入，并返回相同含义的结果。

STDIO 最常见的问题是“进程能启动，但协议无法握手”。协议通道必须保持纯净，应用日志要写到标准错误。v2 能隔离多数运行期输出，却无法修复包装脚本、导入期输出或旧 Client / Server 造成的污染。还要注意：Host 使用的工作目录、Python 解释器和环境变量，可能与手工终端不同。

HTTP 最常见的问题是“端点能访问，但协议调用失败”。反向代理可能缓冲流、改写路径、删除请求头，或者在长工具调用完成前超时。

因此，两种传输都要做真实的协议调用检查，不能只看进程是否存在或端口是否监听。

### 10.3 2025 会话式语义与 2026-07-28 无状态规范

这是升级 MCP Client 与 Server 时最重要的版本差异：

第一次阅读先抓住三个结论：新规范不再建立跨请求的协议会话；无状态请求仍可流式返回；旧 Client 和新 Server 能否协作要靠版本矩阵与真实调用验证。表中的缓存、订阅和兼容 API 主要用于迁移与生产设计，可以在完成第 10.6 节的 HTTP 示例后再回来看。

| 主题 | 2025 风格 | 2026-07-28 核心规范 |
| --- | --- | --- |
| 初始化 | `initialize` / `initialized` 握手协商 | 新修订取消该握手，每个请求自描述 |
| 会话 | 可使用 `Mcp-Session-Id` | 新修订取消协议会话 |
| HTTP | POST 请求，并可能配合 GET 流与会话管理 | 单 POST 入口为核心，不再用 GET 长连接承担协议会话 |
| 能力信息 | 初始化阶段交换 | 请求 `_meta` 携带上下文，可选 `server/discover` |
| 路由提示 | 主要查看 JSON-RPC 内容 | 可使用 `Mcp-Method`、`Mcp-Name` 请求头帮助网关路由 |
| 多轮输入 | 依赖会话内交互 | MRTR（Multi Round-Trip Requests，多往返请求）返回 `input_required`，客户端带新输入重试 |
| 列表缓存 | 通常由客户端自行约定 | `tools/list`、`resources/list`、`prompts/list` 等结果可携带 `ttlMs` 和 `cacheScope` |
| 变更通知 | 可依赖会话的独立流 | 客户端通过 `subscriptions/listen` 建立订阅响应流 |
| Server 发起请求 | 会话中可发起 Sampling、Elicitation、Roots 等反向请求 | 核心协议不保留反向请求通道；交互输入改用 MRTR 或显式依赖解析 |
| Roots、Sampling、协议日志 | 作为核心能力使用 | 已弃用；旧协议兼容路径仍可能工作，但新设计不再建立依赖 |
| Tasks | 曾进入核心草案 | 移到扩展 |
| SSE 旧传输 | 仍常见于示例 | 正式弃用，并提供兼容窗口 |

2026-07-28 取消的是独立 `GET` 流和协议会话，并没有禁止流式返回。一次 `POST` 的响应仍可使用 `text/event-stream`，逐步发送这次请求的通知和最终结果。长期变更通知则由 `subscriptions/listen` 请求的响应流承担。

因此，“无状态”不等于“只能一次返回 JSON”。它表示 Server 不再依赖跨请求保存的协议会话。

还要区分两个变化：Roots、Sampling 和协议日志被弃用；现代核心协议同时取消了 Server 主动向 Client 发请求的反向通道。旧修订或兼容模式可能仍保留这些 API，但新代码不能依赖它们一直存在。

MCP Python SDK v2 提供 `Resolve(...)` 一类兼容入口。现代路径会把缺少的输入作为 MRTR（Multi Round-Trip Requests，多往返请求）结果返回；旧路径仍可使用会话内 Elicitation（向用户补充取值）。应用代码应使用这种可移植入口，不要自己维护两套业务流程。

旧代码仍可用于对应的兼容环境，但协议修订与 SDK 支持需要配套。`session.initialize()`、SSE URL、`Mcp-Session-Id` 和 Spring AI M7 配置都属于特定版本背景。

迁移前先确认 Host、Client、Server 和 SDK 共同支持的修订，再成组调整调用方式与配置。

迁移评审应产出一张实测矩阵：

| Host / Client | Server | 共同修订 | 握手与会话 | 流式路径 | 结果 |
| --- | --- | --- | --- | --- | --- |
| 旧 Adapter | v2 Server 兼容路由 | 旧修订 | `initialize` 与旧 session | 旧兼容方式 | 契约测试通过后保留 |
| 2026 Client | v2 Server | 2026-07-28 | 每请求自描述 | POST 响应流 | 新项目目标 |
| 2026 Client | 仅 v1 Server | 无共同修订 | 不匹配 | 不匹配 | 在入口拒绝并给出版本证据 |

“最新”或“支持 MCP”不足以说明兼容性。发布记录应固定 SDK 包版本、协商到的协议修订、传输、端点、认证方式和一组实际请求响应样本。

### 10.4 Tool Schema 与结果设计

一个高质量 MCP Tool 至少应有稳定名称、清晰标题和描述、输入 JSON Schema、可选输出 Schema，以及准确的只读、破坏性、幂等性等注解。

```python
from typing import Annotated, Literal

from mcp.server import MCPServer
from pydantic import BaseModel, Field


mcp = MCPServer("expense-service")


class ExpenseQuery(BaseModel):
    employee_id: str = Field(pattern=r"^E\d{6}$")
    category: Literal["hotel", "flight", "meal"]


@mcp.tool()
def get_expense_limit(
    employee_id: Annotated[str, Field(pattern=r"^E\d{6}$")],
    category: Literal["hotel", "flight", "meal"],
) -> dict:
    """读取员工可见的报销上限；不创建或修改报销单。"""
    validated = ExpenseQuery(employee_id=employee_id, category=category)
    return {
        "employee_id": validated.employee_id,
        "category": validated.category,
        "limit_cny": 800,
        "policy_version": "v3",
    }
```

Schema 只能减少参数误用，无法替代授权。Server 还要结合访问令牌主体和组织关系校验 `employee_id`，参数格式正确并不代表调用者有权查询任意员工。

工具结果应区分机器可处理的结构化数据和给人看的文本。错误应有稳定代码、可重试标识和安全消息；堆栈、SQL、内部路径和凭据不得原样返回模型。

一个读工具的结构化结果可以进一步统一为：

```json
{
  "status": "ok",
  "data": {
    "limit": 800,
    "currency": "CNY",
    "policy_version": "v3"
  },
  "evidence": ["travel-policy-v3#4"],
  "warnings": []
}
```

`status` 让 Host 不必从自然语言猜成功与否；`data` 给代码消费；`evidence` 支持引用与审计；`warnings` 表示非致命缺口。不存在的数据、无权限的数据和后端暂不可用应使用不同错误码，因为它们分别对应正常空结果、安全拒绝和可重试故障。

写工具还应声明将产生什么副作用、是否幂等、需要何种确认以及成功后返回哪个业务 ID。工具名要表达动作，例如 `create_expense_draft`，而不是模糊的 `handle_expense`；输入不接受让模型拼接的任意 SQL、Shell 或 URL。

### 10.5 Resources 与 Prompts 的使用场景

Resources 对上下文治理与 Tools 同样重要。资源 URI（Uniform Resource Identifier，统一资源标识符）应稳定、可授权、可缓存，并返回媒体类型和内容。大资源应分页或返回引用，不能把整个数据库导出到上下文。

Resources 的典型生命周期是：Client 列出静态资源或资源模板；Host 根据用户任务和权限选择 URI；Server 在读取时再次授权；返回内容、媒体类型与版本元数据；Host 决定是否放入模型上下文。列出某个 URI 不代表当前用户有权读取它，缓存键也必须包含主体、租户与版本。

Tool 与 Resource 的区别不在于“都能返回数据”，而在于访问意图：

| 示例 | 应建模为 | 原因 |
| --- | --- | --- |
| `expense://policy/v4/section/4` | Resource | 它是可寻址、可重复读取的制度内容 |
| `search_policy(query, filters)` | 只读 Tool | 它会根据参数执行一次检索 |
| `submit_expense(...)` | 写 Tool | 它会改变外部系统状态 |

静态内容保留为 Resource，契约更准确，也能减少模型工具列表中的噪声。

Prompts 适合暴露组织认可的模板，但 Host 在使用前仍要展示来源和参数。外部 Server 提供的 Prompt 与网页文本一样不可信，不能覆盖 Host 的系统安全规则。

Prompt 的价值是复用受版本管理的工作方法，例如事故复盘模板要求输入时间线、影响范围和证据链接。Host 可以让用户选择模板、展示将填入的参数，再把结果作为普通上下文组合；Server 返回的 Prompt 不获得更高指令优先级。模板升级还应保留版本，避免同一个名称在审计时指向不同内容。

### 10.6 Python Server：本地与远程

MCP Python SDK v2 已把 v1 的 `FastMCP` 重命名为 `MCPServer`。装饰器的基本写法保留，但导入路径和部分运行时 API 已发生破坏性变化。本地 STDIO 模式如下：

```python
from mcp.server import MCPServer


mcp = MCPServer("policy-service")


@mcp.tool()
def get_policy(topic: str) -> dict[str, str]:
    """按主题读取演示制度，只读。"""
    return {"topic": topic, "policy": "酒店上限为每晚 800 元"}


if __name__ == "__main__":
    mcp.run()
```

远程模式使用相同的工具定义，只改变启动传输：

```python
if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

默认服务地址通常是 `http://127.0.0.1:8000/mcp`，但实际端口和路径要以启动日志及当前锁定版本的文档为准。

同一个 MCP Python SDK v2 Streamable HTTP 应用可以按协议版本处理两类请求：2026-07-28 请求不做握手，也不建立协议会话；旧客户端仍可执行 `initialize` 并使用旧会话。

旧会话状态保存在单个进程内。多 Worker 部署如果还要兼容旧会话，就必须使用粘性路由；如果不需要服务端回调，也可为旧路径启用 `stateless_http=True`。

这里需要分清三种包与导入方式：

| 环境 | 导入与类名 | 适用范围 |
| --- | --- | --- |
| MCP Python SDK v2 | `from mcp.server import MCPServer` | 当前稳定主线，覆盖 2026-07-28 和旧协议兼容 |
| MCP Python SDK v1 | `from mcp.server.fastmcp import FastMCP` | 维护线，供尚未迁移的客户端和当前 LangChain MCP Adapters 使用 |
| 独立 `fastmcp` 包 | 以该项目文档为准 | 与官方 `mcp` 包不是同一个发布物，不得混用导入路径和扩展 API |

验收分为三层：`mcp dev 文件名.py` 能发现并调用工具；HTTP 客户端能访问实际 `/mcp` 路径；抓取的请求版本与预期协议修订一致。端口已监听只说明进程在运行，还不能证明工具 Schema、版本路由或多 Worker 会话正确。

本地 STDIO 验收必须从 Host 实际启动，不能只在交互终端里手工运行。记录 Host 使用的绝对解释器路径、脚本路径、工作目录、环境变量白名单和 stderr 日志。

还要分别测试路径含空格、缺少环境变量和工具抛出受控异常的情况。尤其检查包装脚本、导入期代码或旧 SDK 是否在协议接管前向 stdout 输出文本；这些文本会导致第一个协议帧无法解析。

远程 Server 应把 Web 进程、工具执行器和下游凭据隔离。慢工具配置任务级超时和取消传播；CPU（Central Processing Unit，中央处理器）或内存密集任务进入独立队列并设置资源上限，使单个工具不会阻塞整个协议服务的事件循环。

### 10.7 在 LangChain Agent 中消费 MCP 工具

本节要把两条已经学过的链路接起来：LangChain Agent 负责选择工具，MCP Server 负责真正执行工具。中间的 LangChain MCP Adapters 把 MCP Tool 转换成 LangChain 能识别的 Tool。

当前 Adapter 仍依赖 MCP Python SDK v1，而第 10.6 节的 Server 使用 v2。两个版本不能安装在同一个虚拟环境，因此让它们运行在两个进程中，再通过 HTTP 通信：

```mermaid
flowchart LR
    A[".venv-adapter：LangChain Agent"] --> B["LangChain MCP Adapters"]
    B -->|"HTTP"| C[".venv：MCP Python SDK v2 Server"]
    C --> D["get_policy 工具"]
```

现在创建 Adapter 环境：

```bash
python3 -m venv .venv-adapter
source .venv-adapter/bin/activate
python -m pip install -U \
  "langchain>=1,<2" \
  "langgraph>=1,<2" \
  "langchain-openai>=1,<2" \
  "langchain-mcp-adapters>=0.3,<0.4" \
  "mcp>=1.24,<2"
```

安装后做一次导入检查：

```bash
python -c "from langchain_mcp_adapters.client import MultiServerMCPClient; import importlib.metadata as m; print('adapter', m.version('langchain-mcp-adapters'), 'mcp', m.version('mcp'))"
```

预期能打印 Adapter 版本和 `mcp 1.x`。这只能证明依赖可以导入；Server 是否可连接，还要由后面的真实发现与调用验证。

将下面的 Client 代码保存为 `mcp_agent.py`：

```python
import asyncio
import os

from langchain.agents import create_agent
from langchain_mcp_adapters.client import MultiServerMCPClient
from langchain_openai import ChatOpenAI


async def main() -> None:
    client = MultiServerMCPClient(
        {
            "policy": {
                "transport": "http",
                "url": os.environ["POLICY_MCP_URL"],
            }
        }
    )
    tools = await client.get_tools()
    model = ChatOpenAI(
        model=os.environ["MODEL_NAME"],
        api_key=os.environ["LLM_API_KEY"],
        base_url=os.getenv("LLM_BASE_URL"),
        temperature=0,
        timeout=30,
        max_retries=2,
    )
    agent = create_agent(
        model=model,
        tools=tools,
        system_prompt="只使用当前用户已授权的工具。写操作必须先获得确认。",
    )
    result = await agent.ainvoke(
        {"messages": [{"role": "user", "content": "查询差旅制度"}]}
    )
    print(result["messages"][-1].content)


if __name__ == "__main__":
    asyncio.run(main())
```

按下面的顺序运行：

1\. 在第一个终端激活 `.venv`，启动第 10.6 节的 v2 HTTP Server。

2\. 在第二个终端激活 `.venv-adapter`，设置 `POLICY_MCP_URL=http://127.0.0.1:8000/mcp`、`MODEL_NAME`、`LLM_API_KEY` 和可选的 `LLM_BASE_URL`。

3\. 在第二个终端运行 `python mcp_agent.py`。

4\. 检查 Agent 消息中是否出现 `get_policy` 工具调用，并检查 Server 是否收到了对应请求。

最终文字回答不是唯一成功判据。完整验收包含三项证据：`get_tools()` 发现 `get_policy`；模型产生了工具调用；Server 收到了正确参数并返回结果。这样才能区分“模型碰巧答对”和“工具链路确实工作”。

这里显式创建 `ChatOpenAI`，因此 `MODEL_NAME` 保存提供商实际的模型标识，不带 `provider:` 前缀。若改用 `create_agent(model="provider:model-name")`，则要按照对应提供商集成的环境变量规则配置密钥。

`MultiServerMCPClient` 默认在每次工具调用时创建并清理一个 ClientSession。只有旧协议工具确实需要跨调用保存会话状态时，才使用 `client.session(...)`；这会同时带来连接生命周期和服务端粘性路由要求。

错误也分成两层。MCP Tool 返回 `isError=True` 时，Adapter 默认生成 `status="error"` 的 ToolMessage，让 Agent 在有限预算内修正参数。传输断开、会话失败和内容转换错误没有可靠的业务结果，因此会直接抛出异常。权限拒绝不进入模型重试循环。

这个本地示例没有实现认证，只用于验证协议和工具调用。生产中的 Server 地址应来自可信配置，不能让用户提交任意 URL，否则可能形成 SSRF（Server-Side Request Forgery，服务器端请求伪造）入口。远程 Server 还要验证短期凭据，并在返回模型前删除敏感错误细节。

### 10.8 Java 与 Spring AI 的版本迁移

如果项目还在使用 Spring AI 1.0.0-M7 和早期 WebFlux MCP Server Starter，它属于里程碑版本技术栈。迁移时不要直接照搬旧教程中的依赖和配置。

当前稳定基线是 Spring AI 2.0.0，支持 Spring Boot 4.0.x 与 4.1.x，并提供 STDIO、Streamable HTTP 和 Stateless Streamable HTTP。SSE 只用于弃用兼容。

新项目通过 `spring-ai-bom`（Bill of Materials，物料清单）统一依赖版本，再用 `spring.ai.mcp.server.protocol=STREAMABLE` 或 `STATELESS` 明确服务形态。

Spring AI 的普通模型工具使用 `@Tool`；要直接声明 MCP Server 能力，Spring AI 2.0 优先使用 `@McpTool` 与 `@McpToolParam`。两套注解服务的边界不同，不应仅因名称都含“Tool”而互换：

```java
import org.springframework.ai.mcp.annotation.McpTool;
import org.springframework.ai.mcp.annotation.McpToolParam;
import org.springframework.stereotype.Component;

@Component
public class WeatherService {
    public record WeatherResult(String city, int temperatureC, String source) {}

    @McpTool(
        name = "get_weather",
        description = "查询指定城市天气，只读",
        generateOutputSchema = true
    )
    public WeatherResult getWeather(
        @McpToolParam(description = "城市名称", required = true) String city
    ) {
        return new WeatherResult(city, 30, "demo");
    }
}
```

`@McpTool` 会根据参数生成 MCP 输入 Schema。设置 `generateOutputSchema=true` 后，它还会为非基础返回类型生成输出 Schema。

同步 Server 可以直接接收示例中的普通返回值；异步 Server 要使用与配置匹配的 Reactor 类型。

迁移时不能只改版本号。还要同时核对依赖坐标、注解包名、协议模式、端点路径、WebMVC / WebFlux 选择、认证过滤器和客户端兼容性。否则，项目可能残留旧 SSE 配置，或者工具方法根本没有被扫描到。

### 10.9 OAuth、令牌与远程授权

OAuth（Open Authorization，开放授权）2.1 为远程 HTTP 提供授权基础，MCP Server 仍要逐项验证令牌的签发者、受众、作用域、过期时间和资源绑定。PKCE（Proof Key for Code Exchange，代码交换证明密钥）可降低授权码被截获的风险。

安全底线如下：

1\. 禁止 Token Passthrough（令牌透传）。MCP Server 不能把收到的用户令牌未经受众验证直接转发给下游 API。

2\. 远程服务使用 HTTPS；本地回环地址也要防 DNS（Domain Name System，域名系统）Rebinding（重绑定）和 Host 头攻击。

3\. 令牌短期有效、最小 Scope（作用域），并与目标资源绑定。

4\. 新规范强调签发者验证，并不再推荐把动态客户端注册作为默认发现方式，应采用当前规范支持的客户端元数据机制。

5\. 高风险工具调用前向用户展示工具名、服务来源、关键参数和副作用，允许拒绝。

远程授权按下面的顺序执行：

1\. Host 从受信任配置中取得 Server 地址。

2\. Host 发现该 Server 的授权元数据。

3\. 用户在浏览器或设备流程中同意精确的 Scope。

4\. Host 取得只面向该 Server 的短期访问令牌。

5\. Server 在每次请求中验证签发者、受众、签名、期限和 Scope。

6\. Server 使用自身服务身份或受控 Token Exchange（令牌交换）访问下游。

用户令牌不是万能凭据，不能不加验证地横向转发给其他服务。

认证回答“调用者是谁”，授权回答“他能对哪个对象做什么”，确认回答“用户是否同意这一次高风险动作”。三者缺一不可。即使 OAuth 成功，`employee_id` 与登录主体不匹配时仍应被对象级授权拒绝。

日志只保留令牌指纹或 `jti`（JWT ID，JSON Web Token 标识）等可关联信息，不记录完整令牌。时钟偏差、密钥轮换、令牌撤销、Scope 缺失和受众错误都应有独立测试，并确保错误信息不会把验证细节泄露给模型。

### 10.10 MCP 调试顺序

出现“Agent 不会调用 MCP 工具”时按层排查：

1\. Server 进程是否能启动，协议接管前的标准输出是否被包装脚本或导入日志污染。

2\. Inspector 能否列出工具并直接调用。

3\. Client 与 Server 是否支持共同传输和协议修订。

4\. URL、端点路径、请求头、TLS 和代理是否正确。

5\. LangChain Adapter 是否成功把 MCP Schema 转为 Tool。

6\. 模型是否支持工具调用，工具描述是否能区分使用场景。

7\. LangGraph 是否真的执行了 ToolNode，还是在路由或授权层被拒绝。

每一层都应保留独立测试，避免直接从最终聊天界面猜问题。

排查时要沿调用链保存证据：Server 的启动版本和 stderr、Inspector 的 `tools/list` 与调用结果、网络请求的端点与协议修订、Adapter 转换后的工具、模型生成的 tool call、ToolNode 的调用 ID，以及 Server 审计结果。

从前往后比较这些证据。第一次偏离预期的位置，通常就是故障所在层，或者它收到了错误输入。

| 现象 | 首个检查点 | 典型根因 |
| --- | --- | --- |
| Inspector 看不到工具 | Server 注册与协议版本 | 装饰器未执行、启动期 stdout 污染、启动了错误解释器 |
| Inspector 可调，Agent 不调用 | Adapter Tool 与模型消息 | 工具未加载、描述重叠、模型不支持 Tool Calling |
| Agent 生成调用但 Server 无日志 | ToolNode 与 Client 传输 | 路由未连接、URL 错、认证在代理层被拒绝 |
| Server 成功但最终答案无结果 | ToolMessage 与模型第二轮 | call ID 不匹配、结果过大被裁剪、模型未再次执行 |
| 偶发重复写入 | 超时、重试与幂等记录 | 客户端重试叠加、目标服务无唯一约束 |

## 11 Agent、Workflow 与 Multi-Agent 的工程选择

### 11.1 标准 Agent 的完整控制面

当前 LangChain `create_agent` 已经提供基于 LangGraph 的工具循环。生产 Agent 在此基础上仍需补齐模型选择、动态工具过滤、提示构造、重试、结构化输出、人工审批和观测。Middleware（中间件）适合把这些横切策略组合起来。

System Prompt 只能提供行为引导，安全规则还要落在执行控制面上。常见做法分为四层：按用户权限过滤模型可见工具；用 Schema 校验工具参数；由服务端在执行前授权；高风险副作用再进入人工审批。

一个生产 Agent 的请求可以按以下控制面处理：

| 阶段 | 输入 | 主要控制 | 失败结果 |
| --- | --- | --- | --- |
| 请求进入 | 用户消息、认证上下文 | 大小限制、租户绑定、内容分类 | 拒绝请求，不调用模型 |
| 模型调用前 | 消息、可用工具 | Prompt 版本、工具按权限裁剪、上下文预算 | 降级为安全工具集 |
| 模型调用后 | AIMessage、tool calls | 输出 Schema、工具名与参数验证、循环预算 | 修复一次或受控失败 |
| 工具执行前 | 主体、对象、参数 | 服务端授权、风险分类、人工确认 | 返回明确的拒绝或中断 |
| 工具执行后 | 结果或错误 | 大小裁剪、敏感字段过滤、审计关联 | 不把内部异常直接交给模型 |
| 最终输出 | 草稿、证据、状态 | 引用检查、内容策略、业务完成条件 | 拒答、部分结果或人工升级 |

Middleware 适合实现模型前后、工具前后和动态模型选择等横切逻辑，但应保持顺序可解释。例如先认证再过滤工具，先验证参数再请求人工确认，先裁剪敏感结果再写普通追踪。两个 Middleware 都修改消息或工具集合时，要通过集成测试固定执行顺序。

控制面本身也可能失败。若审计后端短暂不可用，财务写操作究竟采用 Fail Closed（失败关闭），还是写入本地 Outbox 后继续，需要由统一的风险策略决定，而非交给每个 Agent 临时判断。

### 11.2 确定性 Workflow 优先的场景

下列任务应优先使用明确工作流，而不是开放 Agent：

1\. 财务、支付、权限变更和生产发布等高风险操作。

2\. 法规规定步骤、顺序和审批人不可由模型决定的流程。

3\. 输入输出字段固定、分类和抽取已足够解决的问题。

4\. 批量处理、成本预算和完成时间必须可预测的任务。

可以在确定图中的个别节点使用模型，例如“从票据抽取字段”，但由代码决定下一步校验、审批和提交。

以 900 元酒店报销为例，推荐流程如下：

1\. 模型从票据中抽取 `amount=900` 和 `category=hotel`。

2\. 代码验证币种和必填字段。

3\. 只读工具查询适用制度与员工职级。

4\. 规则节点计算 `over_limit=True`。

5\. 图暂停并等待人工审批。

6\. 审批通过后，幂等提交节点执行写入。

模型适合处理非结构化票据，但不能自行跳过规则，也不能自行选择审批人。

| 决策 | 由谁负责 | 原因 |
| --- | --- | --- |
| 从图片识别商户和金额 | 模型加结构化输出 | 输入非结构化，允许置信度与人工复核 |
| 选择适用制度版本 | 代码与数据库 | 依赖生效日期和组织关系，必须确定 |
| 是否超额 | 规则函数 | 可精确计算和单测 |
| 是否需要审批 | 图路由 | 组织流程要求可审计 |
| 生成解释 | 模型 | 只基于已确定结果和证据表述 |
| 创建报销单 | 受控工具 | 授权、幂等、审计在执行层保证 |

当团队争论“要不要 Agent”时，先把所有决策列入这张表。只有无法用稳定规则枚举、允许探索且错误可恢复的决策，才交给 Agent 自主选择。

### 11.3 Evaluator-Optimizer 循环

“生成—评估—优化”图适合报告、文案和代码修复，同时要定义明确的终止条件：

```mermaid
flowchart LR
    G["Generator"] --> E["Evaluator"]
    E -->|"pass"| END((END))
    E -->|"revise 且 attempts < 3"| G
    E -->|"预算耗尽"| H["人工复核 / 降级结果"]
```

Evaluator 最好输出结构化维度、证据和改进建议，而不是一句“质量不好”。模型自评会有系统性偏差，关键质量门还要结合规则、测试、外部验证器或人工抽样。

```python
from typing import Literal, TypedDict


class Evaluation(TypedDict):
    decision: Literal["pass", "revise", "escalate"]
    failed_criteria: list[str]
    evidence: list[str]
    revision_instruction: str


class OptimizeState(TypedDict, total=False):
    draft: str
    evaluation: Evaluation
    attempts: int
    max_attempts: int
    token_budget_remaining: int
```

Generator 只读取上一次明确的 `revision_instruction`，Evaluator 则对照固定 Rubric（评分准则）输出 `Evaluation`。

路由条件同时检查 decision、attempts、剩余 Token 和截止时间。任何预算耗尽都进入 `escalate`，或者返回带缺口的部分结果，不能继续递归。

如果 Generator 与 Evaluator 使用同一模型和相似 Prompt，它们可能共享盲点。代码任务应运行编译和测试，事实任务应核对引用，合规任务应使用确定规则，抽样结果再交人工标注。离线评测要比较“首稿质量”和“循环后质量”，避免花三倍成本只改善措辞。

### 11.4 Multi-Agent 什么时候值得用

Multi-Agent（多智能体）适合上下文明显隔离、工具权限不同、任务能够分解且单 Agent 工具过多的场景。例如研究 Agent 只读网络，财务 Agent 只能访问财务 MCP，Supervisor 负责拆解和聚合。

不适合的信号包括：只是想让两个相同模型“讨论一下”、任务无法独立、共享状态频繁冲突、调用成本没有上限、责任归属不清。此时多智能体只会把一次不确定调用变成多次不确定调用。

| 条件 | 单 Agent 更合适 | Multi-Agent 可能更合适 |
| --- | --- | --- |
| 上下文 | 信息高度共享 | 领域上下文可明显隔离 |
| 工具 | 少量同权限工具 | 工具很多且权限边界不同 |
| 任务依赖 | 每步强依赖前一步 | 子任务可以并行或独立验收 |
| 输出 | 一个紧密结果 | 多份结构化产物可汇总 |
| 失败处理 | 整体重试简单 | 每个子任务能独立重试、降级 |
| 成本 | 预算严格 | 额外调用有可量化收益 |

采用多智能体前做一个消融实验：在同一评测集上比较单 Agent、单 Agent 加工具分组、Multi-Agent 三种方案的成功率、P95 延迟、Token、工具误用率和可解释性。若工具分组已经解决选择困难，就没有必要增加监督者和交接协议。

### 11.5 Supervisor 与 Handoff

Supervisor（监督者）把子任务分给专门 Agent 并汇总；Handoff（交接）则把当前控制权和必要状态转给另一 Agent。跨子图交接可以通过 `Command` 指定目标与父图，传递内容以完成子任务所需的最小上下文为限，整段内部历史通常没有必要复制。

```mermaid
flowchart TB
    S["Supervisor"] --> R["Research Agent：只读检索"]
    S --> F["Finance Agent：财务制度"]
    S --> O["Operations Agent：工单工具"]
    R --> S
    F --> S
    O --> A["人工批准"] --> S
```

每个 Agent 都要明确所有者、工具白名单、最大轮数、Token 预算、超时、输出 Schema 和失败回退。Supervisor 负责协调，不因此获得所有下游权限。

Supervisor 不应只给子 Agent 一句模糊的自然语言任务，而要发送结构化任务契约：

| 契约字段 | 说明 |
| --- | --- |
| `task_id`、目标、截止时间 | 说明要完成什么、何时结束 |
| 允许的数据引用、禁止动作 | 限定上下文与权限边界 |
| 预算、输出 Schema、验收条件 | 限定成本，并定义怎样才算完成 |

子 Agent 返回 `status`、产物、证据、消耗和未解决问题。Supervisor 由此按字段汇总，不必从一段角色扮演式对话中猜任务是否完成。

Handoff 还要明确控制权。交接后原 Agent 是暂停、结束还是等待回传；新 Agent 能否再次交接；共享的 `request_id` 与各自 `run_id` 如何关联；谁有权宣布最终完成。若两个 Agent 都能更新 `final_answer`，图中需要设置唯一汇总节点和 Reducer 规则。

安全设计上，Research Agent 返回网页证据引用，不直接调用财务工具；Finance Agent 只接收经裁剪的制度问题，不查看用户全部网页历史；Operations Agent 只有创建草稿权限，正式提交仍由人工批准节点控制。上下文隔离和凭据隔离需要同时落实。

### 11.6 Agent 的终止条件

运行边界包括最大模型轮数、最大工具调用数、总耗时、总 Token、费用预算和重复调用检测。到达边界后，系统返回已经完成的步骤、未完成原因、可用证据和恢复标识，而非只抛出一个笼统错误。

“模型没有再调用工具”只能说明循环自然停下，不一定表示任务成功。终止节点应检查业务完成条件，例如 `final_answer` 已通过 Schema、必需证据存在、所有写操作有确定结果、待审批集合为空。模型输出“已提交”但 State 没有 `submission_id` 时必须判定未完成。

```json
{
  "status": "partial",
  "reason": "tool_budget_exhausted",
  "completed_steps": ["identified_policy", "checked_limit"],
  "pending_steps": ["submit_expense"],
  "evidence": ["travel-policy-v3#4"],
  "resume_id": "thread-expense-100"
}
```

重复调用检测可以对“工具名 + 规范化参数 + 关键状态版本”取哈希。如果相同调用连续出现且 State 没有新增证据，就停止并报告循环；但轮询任务应把允许重试间隔和最大等待时间纳入签名，避免把合法等待误判成死循环。

## 12 一个端到端项目骨架

### 12.1 参考架构

```mermaid
flowchart TB
    UI["Web / App"] --> API["API Gateway"]
    API --> AUTH["认证、租户、限流"]
    AUTH --> ORCH["LangGraph Orchestrator"]
    ORCH --> MODEL["Model Gateway"]
    ORCH --> RAG["Retriever / Reranker"]
    RAG --> VDB["Vector DB"]
    ORCH --> MCP1["MCP Client：Policy"]
    ORCH --> MCP2["MCP Client：Expense"]
    MCP1 --> S1["Policy MCP Server"]
    MCP2 --> S2["Expense MCP Server"]
    ORCH --> CP["Checkpoint DB"]
    ORCH --> STORE["Long-term Store"]
    ORCH --> OBS["Trace / Metrics / Audit"]
    S1 --> OBS
    S2 --> OBS
```

API Gateway 负责外部认证、请求大小和速率；Orchestrator 负责状态与策略；Model Gateway 统一模型凭据、配额和降级；MCP Server 只获得完成自身能力所需的最小下游权限；审计存储与调试追踪分开，保留策略也不同。

图中的箭头只表示数据或调用方向，不表示信任会自动传递。

API 认证用户后，要把身份转换成内部主体上下文。Orchestrator 只把当前节点需要的最少信息交给模型和 MCP。Vector DB 的过滤条件来自服务端授权结果，MCP Server 还要再次校验对象级权限。

这样即使某一层被绕过，下一层仍有机会拒绝越权请求。

最小部署可以合并 API 与 Orchestrator 进程，但逻辑边界仍要保留。只有出现独立扩缩容、不同安全域或跨团队所有权时，才把 Model Gateway、RAG 与 MCP Server 拆成网络服务。过早拆分会增加认证、重试和可观测性成本。

### 12.2 推荐目录

```text
agent-platform/
├── pyproject.toml
├── uv.lock
├── .env.example
├── src/
│   ├── api/
│   │   ├── routes.py
│   │   └── auth.py
│   ├── agent/
│   │   ├── state.py
│   │   ├── graph.py
│   │   ├── nodes.py
│   │   ├── middleware.py
│   │   └── prompts/
│   ├── rag/
│   │   ├── ingest.py
│   │   ├── retrieval.py
│   │   └── schemas.py
│   ├── tools/
│   │   ├── local.py
│   │   └── mcp_clients.py
│   ├── persistence/
│   │   ├── checkpoints.py
│   │   └── store.py
│   └── settings.py
├── mcp_servers/
│   ├── policy_server/
│   └── expense_server/
├── tests/
│   ├── unit/
│   ├── contract/
│   ├── graph/
│   ├── evals/
│   └── security/
└── deploy/
    ├── docker/
    └── kubernetes/
```

提示词应与代码一样经过版本控制和评审，但敏感 System Prompt 不能作为秘密保存机制。攻击者可能通过输出、错误和间接注入推断其内容，安全边界仍要由代码和授权系统建立。

这个目录通过限制每一层的职责来保持依赖方向清楚：

| 目录 | 只负责什么 |
| --- | --- |
| `api` | 传输协议与身份适配 |
| `agent` | State、节点和控制流 |
| `rag` | 索引、检索和证据结构 |
| `tools` | 本地工具与 MCP 能力适配，不放页面逻辑 |
| `persistence` | Checkpoint、Store 等基础设施 |
| `mcp_servers` | 独立领域能力及其部署 |

如果一个 Node 同时读取 `.env`、拼接 SQL 并返回 HTTP Response，单元测试、重放和权限评审就会纠缠在一起。

### 12.3 一条完整垂直切片

目标请求是“为员工 E000042 检查 900 元酒店票据，超额则请求审批，通过后创建报销草稿”。入口先把外部 JSON 转换为类型化命令：

```json
{
  "request_id": "req-20260812-001",
  "employee_id": "E000042",
  "expense": {
    "category": "hotel",
    "amount": 900,
    "currency": "CNY",
    "receipt_object_id": "obj-7a91"
  }
}
```

图状态只保存编排所需数据：

```python
from typing import Literal, TypedDict


class ExpenseState(TypedDict, total=False):
    schema_version: Literal[1]
    request_id: str
    tenant_id: str
    actor_user_id: str
    employee_id: str
    category: Literal["hotel", "flight", "meal"]
    amount_minor: int
    currency: str
    receipt_object_id: str
    policy_version: str
    policy_evidence_id: str
    limit_minor: int
    over_limit: bool
    approval_status: Literal["not_required", "pending", "approved", "rejected"]
    draft_id: str
    failure_code: str
```

金额使用最小货币单位整数，例如 900 元保存为 `90000` 分，避免二进制浮点误差。`actor_user_id` 表示实际操作人，`employee_id` 表示报销对象，二者不能混为一个字段。票据内容留在对象存储，State 只保存受控引用。

| 节点 | 输入 | 输出更新 | 失败路径 |
| --- | --- | --- | --- |
| `validate_request` | 入口字段、认证上下文 | 规范化金额、主体与租户 | `INVALID_INPUT` / `FORBIDDEN` |
| `extract_receipt` | 对象 ID | 结构化票据、字段置信度 | 低置信度转人工录入 |
| `load_policy` | 员工、类别、日期 | 上限、版本、证据 ID | 无权或无制度时拒绝继续 |
| `evaluate_limit` | 金额、上限 | `over_limit` | 纯函数错误进入系统失败 |
| `request_approval` | 超额事实、证据 | interrupt 负载 | 超时、拒绝或证据过期 |
| `create_draft` | 已验证载荷、审批 | `draft_id` | 用幂等键查询或恢复 |
| `build_response` | 完整 State | API 响应 | 缺不变量时返回 partial / failed |

请求的第一阶段可能返回 HTTP 202 与审批信息，而不是伪装成同步完成：

```json
{
  "status": "approval_required",
  "request_id": "req-20260812-001",
  "thread_id": "expense-thread-9d2c",
  "approval": {
    "amount": "CNY 900.00",
    "limit": "CNY 800.00",
    "evidence": "travel-policy-v3#4",
    "expires_at": "2026-08-13T10:00:00+08:00"
  }
}
```

审批 API 校验审批人、thread 归属、证据哈希和过期时间，再用 `Command(resume=...)` 恢复。最终成功响应以 State 中真实的 `draft_id` 为准：

```json
{
  "status": "completed",
  "request_id": "req-20260812-001",
  "draft_id": "draft-4815",
  "policy_evidence": ["travel-policy-v3#4"]
}
```

这条垂直切片的最小验收包括五条路径：800 元以内无需审批；900 元触发审批；拒绝后不创建草稿；恢复请求重复两次仍只有一个 `draft_id`；另一租户即使知道 thread 也无法读取或恢复。先让这五条用假模型、假 MCP 和真实 Checkpointer 通过，再接真实外部服务。

### 12.4 从原型到生产的迁移顺序

1\. 先用固定输入和假工具验证状态、路径与输出 Schema。

2\. 接入真实模型，但工具保持只读，建立回归集和成本基线。

3\. 接入真实检索，独立评测召回与生成忠实度。

4\. 把本地工具拆成 MCP Server，先做契约测试再连 Agent。

5\. 增加持久化、身份映射、人工审批和幂等写入。

6\. 做负载、故障、提示注入、越权和恢复演练后再开放写工具。

每一步都要有退出标准：

| 阶段 | 进入下一阶段前要证明什么 |
| --- | --- |
| 固定输入 | 所有路由和 Reducer 单元测试通过 |
| 真实模型 | 结构化输出成功率和单次成本达到目标 |
| 真实检索 | 权限测试零泄漏，召回指标达到目标 |
| MCP | 目标 Client 的契约测试通过 |
| 持久化 | 杀进程后的恢复演练通过 |
| 开放写工具 | 幂等、审批和审计故障演练通过 |

迁移不是只向前增加功能。若真实模型没有超过规则基线，保留规则；若 Agentic RAG 没有超过 Two-step RAG，退回两阶段；若远程 MCP 的运维成本大于跨语言复用收益，可以保留进程内 Tool。每一步都应能回退到上一个已验证状态。

### 12.5 配置与密钥

配置分为可公开的非敏感配置、部署环境配置和秘密。`.env.example` 只写键名与无效示例值：

```dotenv
MODEL_NAME=model-name
EMBEDDING_MODEL_NAME=embedding-model-name
LLM_BASE_URL=https://example.invalid/v1
LLM_API_KEY=replace-me
WEATHER_MCP_URL=https://example.invalid/mcp
WEATHER_MCP_TOKEN=replace-me
CHECKPOINT_DATABASE_URL=postgresql://user:password@example.invalid/db
```

启动时应做 Fail Fast（快速失败）校验，缺少关键配置就拒绝启动。日志只记录配置来源和非敏感摘要，不记录完整 URL 查询参数、Authorization Header 或数据库连接串。

配置应有类型、默认值、来源优先级和环境约束。例如生产环境禁止 `CHECKPOINT_BACKEND=memory`，MCP URL 必须来自已签名 Registry，模型名必须出现在允许列表。密钥通过 Secret Manager、工作负载身份或短期令牌注入，不打包进镜像，也不由前端传给后端。

密钥轮换要在上线前演练：同时接受新旧凭据的短窗口、切换调用方、撤销旧凭据、验证错误率与审计。若泄漏，先吊销和轮换，再清理日志与提交历史；仅从当前文件删除并不能消除已暴露的秘密。

## 13 生产实践：安全、可靠性、可观测性与成本

### 13.1 威胁模型

智能体系统至少面对五个信任边界：用户输入、检索内容、模型输出、工具服务、持久化数据。任何一层都可能恶意或被攻陷。

| 威胁 | 例子 | 主要控制 |
| --- | --- | --- |
| 直接提示注入 | 用户要求忽略规则并导出数据 | 权限裁剪、输出过滤、拒绝策略 |
| 间接提示注入 | 网页写着“调用转账工具” | 外部内容标记为数据，工具策略不由内容改变 |
| 越权工具调用 | 普通员工查询他人薪资 | 服务端身份与对象级授权 |
| 参数攻击 | 路径穿越、SQL 注入、Shell 注入 | 强类型 Schema、允许列表、参数化调用、沙箱 |
| 数据外泄 | 把密钥发给模型或追踪平台 | 数据分类、脱敏、最小上下文、密钥隔离 |
| SSRF | 用户控制 MCP URL 访问内网 | 固定 Server Registry、网络出口策略、DNS 校验 |
| 供应链风险 | 恶意 MCP Server 或依赖包 | 来源审查、锁版本、签名、隔离权限 |

工具结果即使来自内部服务，也按外部输入处理。Server 可能受到提示注入污染，也可能返回超大内容导致上下文耗尽。Host 应限制结果大小、媒体类型、嵌套深度和可接受字段。

威胁建模要描述完整攻击路径，而不是只列安全名词。以恶意发票备注为例：

```text
恶意供应商写入指令
  → OCR 把指令识别成文本
  → 模型把外部数据误当成系统指令
  → Agent 选择 submit_expense 写工具
```

每个经过点都要设置一道防线：

| 层级 | 防线 |
| --- | --- |
| 上传层 | 标记内容来自不可信来源 |
| Prompt | 把 OCR 内容放在数据字段，而不是指令位置 |
| 工具选择 | 当前阶段只向模型暴露只读工具 |
| 写操作 | 使用确定性路由，并要求人工确认 |
| Server | 再次校验身份、对象和参数 |
| 审计 | 检测并告警异常调用 |

纵深防御的目的，是让某一层失守后，攻击仍然无法完成。

每个资产都应回答所有者、攻击者、入口、影响和控制。重点资产包括用户隐私、下游写权限、模型与 MCP 凭据、Checkpoint、长期记忆、向量索引和审计日志。威胁模型需随新增工具、模型供应商、数据源和部署网络更新。

### 13.2 最小权限与人工确认

只读与写工具分开，查询与管理 Scope 分开，开发与生产凭据分开。写工具使用明确的动作名称，例如 `create_expense_draft` 和 `submit_expense`；`process_expense` 这类名称没有说明具体副作用，不利于权限配置与审计。

人工确认界面应显示将要执行的动作、目标系统、关键参数、预期副作用、数据来源和可撤销性。只显示“是否继续？”不足以形成知情同意。

确认需要绑定到具体的动作快照。可以对 `tool_name + canonical_arguments + evidence_version + actor + expiry` 计算签名或不可变哈希。

恢复执行前重新计算快照。只要参数或证据发生变化，旧确认就失效。否则，用户确认的是 900 元草稿，系统却可能把同一个布尔值复用于已经变成 9,000 元的请求。

权限最好按能力拆分：读制度、创建草稿、提交审批、正式付款使用不同 Scope 与服务身份。生产 Agent 不拥有数据库超级账号，MCP Server 也不继承 Host 的全部权限。Break-glass（紧急提权）流程需要独立审批、短时授权和事后复核。

### 13.3 超时、重试、熔断与背压

背压表示：下游处理速度变慢时，上游主动减速、排队或拒绝新任务，避免请求无限堆积并拖垮整个系统。熔断则是在下游连续故障时暂时停止调用，让系统快速失败或降级。

总请求预算应自上而下分配。例如 API 总超时 60 秒，模型 25 秒，检索 5 秒，单工具 10 秒，同时预留汇总和网络开销。各层若分别重试三次，一条请求会被放大成几十次调用，因此重试预算需要统一管理。

只对超时、限流和短暂不可用等可恢复错误重试；参数错误、认证失败、权限拒绝和确定性业务冲突不重试。连续故障时熔断并快速降级，恢复阶段用少量探测请求。

入口、图任务、模型、嵌入、向量库和 MCP Client 各层都要设置并发上限。批量扇出通过队列和背压调节速度，避免一条用户请求耗尽整个连接池。

总超时不只是各阶段上限相加，还要为排队、序列化、重试退避和最终响应预留预算。每个节点接收绝对 `deadline`，开始前计算剩余时间；若只剩 2 秒，就不应再启动一个通常耗时 10 秒的工具。取消信号应向模型流、子图和 MCP 请求传播，但已提交的外部副作用仍需通过业务状态查询确认结果。

熔断器按下游服务和错误类别统计，不应因为一个租户的参数错误熔断全局服务。背压策略要明确排队、拒绝、降级或采样哪一种；高优先级审批恢复不能被大批离线 Embedding 任务占满相同连接池。

### 13.4 可观测性三件套

Trace（追踪）回答一次请求经过了哪些节点；Metric（指标）回答系统整体是否健康；Log（日志）提供离散诊断事件。三者使用同一关联标识，但数据量和保留策略不同。

建议指标如下：

1\. 入口：请求量、成功率、p50 / p95 / p99 延迟、限流数。

2\. 模型：首 Token 延迟、总耗时、输入输出 Token、结构化输出失败率、实际模型。

3\. RAG：召回耗时、Top-K、空检索率、重排耗时、引用覆盖率。

4\. Tool / MCP：发现失败率、调用量、错误码、超时、重试、结果大小。

5\. LangGraph：节点耗时、循环次数、中断数、恢复成功率、检查点大小与写入耗时。

6\. 业务：解决率、审批拒绝率、人工升级率、错误副作用数和单次任务成本。

LangSmith 或 OpenTelemetry 都可用于观测，落地时还要配置脱敏、采样、租户隔离和访问审计。调试数据的采集范围服从隐私与合规要求。

一次可用 Trace 至少能串起：

```text
trace_id=tr-8f3a
request req-001
└── graph expense_assistant / thread t-100
    ├── node load_policy / span sp-11
    │   └── mcp tool get_expense_limit / call tc-77 / 83 ms
    ├── node request_approval / interrupt int-9
    └── node create_draft / idempotency req-001:create / 142 ms
```

Trace 中保留 ID、耗时、版本、状态码和脱敏后的大小，不默认保留完整票据、Prompt 或工具结果。Metric 标签不能使用 `thread_id`、用户 ID 等高基数值，否则时序数据库成本与性能会失控；这些标识属于 Trace 或受控日志字段。

告警要与用户影响和处置动作绑定。例如“5 分钟内 MCP 超时率超过 5%，且请求量大于 100”可触发降级只读模式；单纯对每个异常发消息会造成告警风暴。每个告警应链接运行手册和最近部署版本。

### 13.5 成本治理

成本来自模型输入输出、Embedding、检索、重排、工具服务、持久化和观测，不只是一次聊天的 Token。优先通过缩小上下文、缓存稳定结果、批量嵌入、选择合适模型、限制 Agent 轮数和提前终止降低成本。

缓存键要包含模型、提示版本、工具版本、权限和数据版本。不能让 A 租户命中 B 租户的答案，也不能把已经过期的制度答案长期缓存。

单次任务成本可以近似拆为：

$$
C=C_{model\_input}+C_{model\_output}+C_{embedding}+C_{rerank}+C_{tools}+C_{storage}+C_{observability}
$$

平均成本仍会掩盖失控 Agent，应同时看 P95、最大值和按终止原因分组的成本。为每个请求保存预算与实际消耗；扇出前预估子任务数，超预算则减少候选、使用小模型、返回部分结果或请求用户缩小范围。

优化要以质量约束为前提。把 Top-K 从 10 降到 3 可能省 Token，却也可能降低跨文档召回；应在固定评测集上画出质量、延迟与成本的 Pareto（帕累托）前沿，再选业务可接受点。

### 13.6 数据保留与删除

分别定义原始对话、检查点、长期记忆、模型追踪、工具审计和向量索引的保留期。删除用户数据时还要删除派生摘要、Embedding、缓存和备份索引，或记录不可立即删除的合规依据。

删除是一张数据血缘图，而不是一次数据库 `DELETE`：原消息可能生成摘要、评测样本、Embedding、缓存答案和追踪附件。每个派生物应记录源数据 ID 与删除处理器；删除任务执行后输出各存储的成功、失败和待到期备份状态，并支持重试与审计。

审计记录可能依法需要比业务内容保存更久，此时只保留证明“谁在何时执行了什么”的最小字段，并对敏感载荷做不可逆脱敏。恢复备份时必须重放在保留期内发生的删除墓碑，避免被删除的数据因灾难恢复重新出现。

## 14 测试与评测：从函数正确到业务可信

### 14.1 测试金字塔

| 层级 | 测什么 | 是否调用真实模型 |
| --- | --- | --- |
| 单元测试 | 节点、Reducer、路由、参数校验、格式化 | 否 |
| 契约测试 | Tool / MCP Schema、错误码、兼容性 | 通常否 |
| 图路径测试 | 分支、循环上限、中断、恢复、幂等 | 使用假模型或录制响应 |
| 集成测试 | 模型、向量库、检查点、真实 MCP 服务 | 选择性调用 |
| 离线评测 | 任务质量、RAG、工具选择、安全样本 | 是，固定版本与数据 |
| 线上评测 | 真实成功率、延迟、成本、人工反馈 | 是，受控发布 |

测试层级越低，运行越快、定位越准；层级越高，越接近真实环境，但成本和不稳定性也更高。金额规则可以由纯函数测试证明，没有必要依赖端到端模型调用；单元测试通过以后，协议和权限集成测试仍需单独执行。

测试夹具应固定模型响应、当前时间、随机种子、工具结果与身份上下文。真实模型评测则记录供应商、精确模型版本、参数、Prompt、工具 Schema、知识库快照和评测器版本，否则分数变化无法归因。

### 14.2 节点与路由测试

节点测试先绕过模型和网络，直接检查普通函数的输入、返回增量和路由结果。这样失败时可以确定是业务逻辑问题，而不是模型波动或外部服务问题。

```python
def test_classify_then_choose_routes_weather_to_tool():
    initial = {"question": "新加坡天气"}
    update = classify(initial)
    assert update == {"route": "tool"}
    assert choose({**initial, **update}) == "tool"


def test_expense_tool_rejects_invalid_level():
    try:
        query_policy.invoke({"topic": "expense", "employee_level": 99})
    except ValueError as exc:
        assert "1 到 10" in str(exc)
    else:
        raise AssertionError("无效等级应被拒绝")
```

Reducer 和路由还要测试不变量，而不只是一个样例：

```python
import operator


def route_expense(state: dict) -> str:
    if not state["over_limit"]:
        return "create_draft"
    if state["approval_status"] == "approved":
        return "create_draft"
    if state["approval_status"] == "rejected":
        return "reject"
    return "request_approval"


def test_evidence_reducer_preserves_both_parallel_results():
    left = ["policy#4"]
    right = ["faq#9"]
    merged = operator.add(left, right)
    assert merged == ["policy#4", "faq#9"]
    assert left == ["policy#4"]
    assert right == ["faq#9"]


def test_high_risk_route_never_skips_approval():
    state = {"over_limit": True, "approval_status": "pending"}
    assert route_expense(state) == "request_approval"
```

第二个测试应再做参数化，覆盖金额等于上限、低于上限、币种不匹配、制度缺失和审批过期。安全相关路由最好断言“禁止边永远不可达”，而不仅是“正常边能到达”。

模型输出不适合只做字符串完全相等断言。优先验证结构化字段、引用、工具序列、禁止动作和不变量；文本质量用评分规则和人工校准后的评估器补充。

针对模型节点可以录制最小的 AIMessage 序列：一次直接回答、一次合法 tool call、一次未知工具、一次重复调用。测试断言消息角色、tool call ID、参数和路由，不断言供应商内部原始对象。录制内容要脱敏并标明生成版本。

### 14.3 LangGraph 恢复测试

恢复测试通常覆盖六类情况：相同 thread 恢复成功；错误 thread 无法读取；中断前节点重跑不会重复副作用；进程重启后外部 Checkpointer 仍能恢复；Schema 版本升级能迁移旧检查点；达到循环上限后返回可诊断错误。

最关键的恢复测试要跨进程执行：

1\. 第一个进程运行到 interrupt 后退出。

2\. 测试读取 Checkpoint 和审批记录，确认暂停状态已保存。

3\. 第二个进程使用相同版本和 `thread_id` 恢复。

4\. 在外部工具已成功、Checkpoint 尚未写入的窗口再次中断网络。

5\. 第三个进程继续恢复，并通过幂等键查询真实业务对象。

6\. 最终确认下游只有一个业务对象，没有重复写入。

测试结果应同时断言 State 和外部事实。例如恢复后的 `draft_id` 非空、目标服务中该幂等键只有一条记录、审计中只有一次真实创建但可有多次查询、thread 历史显示发生过恢复。只断言最终回答包含“成功”无法发现重复提交。

### 14.4 MCP 契约测试

对每个 Server 验证工具列表、名称稳定性、输入 Schema、输出 Schema、参数边界、错误代码、授权、超时和结果大小。远程模式还要测试 TLS、令牌受众、过期、错误签发者、跨租户对象、代理超时和协议修订兼容。

Inspector 适合人工调试，但不能替代自动契约测试。服务发布前应由目标 Client SDK 实际完成发现和调用。

契约测试应保存一份经过评审的 Schema 快照，并按兼容规则比较新版本：新增可选字段通常兼容；删除字段、缩窄枚举、把可选改必填或改变错误含义通常破坏兼容。Tool 描述改变也可能改变模型选择行为，因此需要工具选择评测，不只是 JSON Schema diff。

对写 Tool 使用隔离的测试租户和可回滚下游。测试同一个幂等键调用两次、超时后查询、无 Scope、错租户对象、过期确认和超大结果；验证 Server 审计与 Client 看到的安全错误一致。

### 14.5 安全评测集

安全样本覆盖直接注入、网页间接注入、伪造工具结果、要求显示 System Prompt、请求传出凭据、越权资源标识、恶意 URL、路径穿越、超长输入、递归工具调用和审批绕过。判断是否通过时，重点检查高风险工具是否执行以及审计事件是否完整，不能只看模型有没有输出拒绝语句。

每条安全样本应包含攻击载荷、攻击入口、当前主体、可见工具、期望的禁止动作、允许的安全响应和对应审计码。评分先看真实副作用和数据访问，再看文本表达。即使模型礼貌拒绝，只要工具已经执行，该样本仍判定为失败。

安全评测要包含组合攻击。例如无权用户上传含间接指令的 PDF，并要求 Agent 连接用户提供的 MCP URL，再诱导其提交工具。单独测试注入、SSRF 和越权都通过，不代表组合路径安全。

### 14.6 线上发布策略

采用影子流量、内部用户、只读工具、小比例租户、逐步扩大权限的顺序发布。每一阶段定义成功门槛与自动回滚条件。写工具开放前需要业务所有者、安全和运维共同确认。

| 发布门 | 示例通过条件 | 自动停止条件 |
| --- | --- | --- |
| 代码与契约 | 单元、图路径、MCP 契约全绿 | 任一高风险路由或授权测试失败 |
| 离线质量 | 核心任务成功率达到基线，拒答达标 | 权限泄漏或错误副作用大于 0 |
| 影子流量 | 延迟、成本和路径与预期一致 | P95 超预算或下游负载异常 |
| 只读试点 | 用户解决率达标，审计完整 | 注入导致未授权读取 |
| 写工具小流量 | 幂等与审批成功率达标 | 重复写入、绕过审批、无法回滚 |

模型、Prompt、工具 Schema、知识库和图代码都可能独立变化，发布记录要能指出本次只改变了什么。一次只放大一个变量，失败时才能快速回退。

## 15 常见故障排查手册

### 15.1 症状到根因

| 症状 | 优先检查 | 常见根因 | 修复方向 |
| --- | --- | --- | --- |
| 模型从不调用工具 | 模型原始响应与工具 Schema | 模型不支持、描述含糊、工具未绑定 | 换支持模型，改描述，检查绑定结果 |
| 工具参数反复错误 | Schema 与示例 | 字段过多、枚举不清、提示冲突 | 缩小 Schema，提供边界，参数错误回填 |
| Agent 无限循环 | 消息序列与轮数 | 工具结果无信息、终止条件缺失 | 最大轮数、重复检测、明确失败返回 |
| LCEL 不流式 | 每个 Runnable 能力 | 中间 `RunnableLambda` 阻塞 | 调整节点位置或实现支持增量的 Runnable |
| RAG 答非所问 | Top-K 原始片段 | 切分差、模型不一致、权限过滤错误 | 先修检索，再修 Prompt |
| 向量维度错误 | 索引元数据 | 入库和查询模型不同 | 重建索引并记录模型版本 |
| Checkpoint 不恢复 | `thread_id` 与持久化后端 | 每次生成新 ID、使用内存 Saver、未提交 | 固定恢复键，使用生产 Saver |
| 恢复后重复提交 | 节点执行轨迹 | 副作用在 interrupt 前或无幂等键 | 拆节点、唯一约束、查询已有结果 |
| STDIO MCP 解析失败 | 子进程启动期标准输出 | 包装脚本、导入日志、缓冲输出或旧 SDK 污染协议 | 日志写标准错误，移除启动横幅并核对 SDK 版本 |
| `mcp.server.fastmcp` 无法导入 | `mcp` 与 Adapter 依赖版本 | v2 SDK 与 v1 示例或 Adapter 混装 | v2 改用 `MCPServer`；Adapter 单独固定 `mcp<2` |
| Streamable HTTP 404 | URL 与 Server 配置 | 端点路径、代理前缀或版本不匹配 | 用 Inspector / curl 核对部署路径 |
| MCP 鉴权 401 | `WWW-Authenticate` 与令牌 Claim | 受众、Scope、签发者或时钟错误 | 按资源元数据重新授权，校准时钟 |
| 旧 Client 无法连接新 Server | 协议日期与传输 | 初始化 / 会话语义不同 | 启用受控兼容层或成对升级 |
| Java MCP 启动失败 | BOM 与 Starter | M7 配置和 2.0 依赖混用 | 按同一版本文档重建最小工程 |
| Token 或密码出现在追踪 | 追踪输入与 Header | 全量采集、缺少脱敏 | 立即轮换秘密，清理记录，增加过滤 |

### 15.2 Runbook：RAG 答案错误

先冻结一个失败请求的身份、问题、知识库版本、检索配置和最终答案，避免排查过程中索引继续变化。随后按以下顺序检查：

1\. 直接按期望文档 ID 读取源文件与解析文本，确认事实存在且版本正确。

2\. 查该文档派生的 Chunk，确认标题、表头、数字、权限和回链没有在解析或切分中丢失。

3\. 用相同身份执行真实检索，保存 Top-K、原始分数、过滤条件和重排前后位置。

4\. 若正确证据未进入 Top-K，分别测试无过滤、关键词检索、向量检索和重排，定位是权限、Embedding 还是排序。

5\. 若正确证据已经进入，保存最终 Prompt，检查模型是否看到冲突版本、证据是否被截断、答案断言能否逐项映射到引用。

修复后用原失败样本、相邻同义问题、不可回答问题和另一个租户回归。只让原问题答对可能是过拟合；关闭权限过滤后答对则是安全倒退，不能接受。

### 15.3 Runbook：恢复后重复副作用

先暂停相关写 Tool 或切到只读模式，再按幂等键和业务对象 ID 查询真实下游，确认是否已经产生重复对象。此时直接重放 thread 可能继续扩大影响，应等实际状态核对清楚后再恢复。

然后按时间对齐四类记录：图节点的开始与结束、MCP Client 的请求与重试、Server 的幂等记录、业务数据库的提交。

先判断故障属于哪个窗口：调用前、服务端提交后但响应前，或者响应后但 Checkpoint 写入前。再检查 interrupt 是否位于副作用之后、重试是否包住整条链、目标服务是否有原子唯一约束。

修复通常包括把确认与提交拆成独立节点、使用稳定业务幂等键、增加服务端唯一约束、超时后先查询，以及把外部 ID 写回 Checkpoint。恢复演练还要覆盖“服务端已经成功，但客户端收到超时”的故障窗口。

### 15.4 Runbook：MCP Tool 可发现但 Agent 不执行

先用 Inspector 直接调用，保存 Tool 名称、描述、输入 Schema 和结构化结果；再打印 Adapter 转换后的 LangChain Tool，确认名称和字段未变化。随后用固定消息直接调用绑定 Tool 的模型，查看原始 AIMessage 是否产生 tool call。

若模型没有调用，给 Tool 描述增加明确的适用条件与不适用条件，并加入一个应调用和一个不应调用的 Few-shot 测试；若模型已经调用，继续检查 LangGraph 条件边是否到 `tools`、ToolNode 是否收到相同 call ID、结果是否作为 ToolMessage 回到第二次模型调用。

在 System Prompt 中反复强调“必须调用工具”，无法修复协议或路由故障。先验证发现、转换、模型意图、执行和结果回填五层；这些链路都正常以后，再调整 Agent 策略。

### 15.5 一条通用排查原则

始终在离故障最近的边界验证输入输出：先测普通函数，再测 Tool，再测 MCP Server，再测 Adapter，再测图节点，最后测 Agent。每跨一层只引入一个新变量。

排查记录使用“预期输入—实际输入—预期输出—实际输出—版本—关联 ID”格式。看到异常后，先定位第一次偏离预期的位置，再沿调用链验证原因。由最终症状同时推测多个环节，通常只会增加干扰。改变安全或数据边界的临时绕过不属于生产修复。

### 15.6 版本问题的最小证据集

提交问题时附上 Python / Java 版本、完整依赖锁、LangChain / LangGraph / MCP SDK 版本、Server 支持的协议修订、最小可复现代码、脱敏后的请求错误和是否经过代理。只说“最新版不能用”无法定位兼容问题。

还应注明命令实际使用的解释器路径、操作系统与架构、安装来源、启动命令、环境变量键名、锁文件是否同步，以及问题在全新虚拟环境中是否复现。MCP 网络问题附代理前后的端点和响应头；LangGraph 恢复问题附 State Schema 版本、节点名和脱敏的 Checkpoint 元数据。

## 16 版本迁移与过时写法替换

### 16.1 迁移时应保持的核心边界

版本迁移不应只替换导入路径。Prompt、结构化输出、Runnable、Retriever、Tool、State、Reducer、Checkpointer、Store 和 MCP 能力都要保持明确的输入输出与职责边界，并通过原有回归样本验证行为没有悄然变化。

迁移前先建立行为基线：固定 20 至 100 条代表性请求，记录输出 Schema、路由、工具序列、流事件、Checkpoint、延迟、Token 和错误码。对于非确定文本，比较关键字段与不变量，不要求逐字相同。没有基线时，“升级后看起来还能回答”无法证明没有回归。

兼容性至少包含五个方向：

| 方向 | 要验证的问题 |
| --- | --- |
| 源代码 API | 导入路径、类名和参数是否仍可使用 |
| 运行时行为 | 路由、错误、流式事件和默认值是否改变 |
| 持久化数据 | 新代码能否恢复暂停中的旧 thread |
| 网络协议 | 旧 Adapter 能否调用新 Server |
| 运维配置 | 代理、端点和环境变量是否仍然匹配 |

代码能够导入，只证明了第一项兼容，不能代表迁移已经完成。

### 16.2 一套可回退的迁移流程

1\. 盘点当前锁文件、解释器、模型、Prompt、图节点、State 版本、MCP 修订和部署配置，保存可复现环境。

2\. 阅读目标版本迁移指南和发布说明，列出破坏性变化、弃用项与依赖约束，不在同一次迁移中顺便重构业务。

3\. 建立新虚拟环境和最小冒烟工程，验证 Prompt、Tool Calling、结构化输出、图中断与 MCP 发现。

4\. 用适配层保持旧业务接口，在离线回归集上比较新旧行为；对差异逐项决定接受、修复或阻断。

5\. 对 Checkpoint、Store、向量索引和缓存进行双读或影子验证；禁止直接拿唯一生产副本试迁移脚本。

6\. 影子运行或小流量发布，保持旧版本可接管；监控错误码、路径、延迟、成本和安全不变量。

7\. 达到观察窗口后再停止旧写入，完成数据迁移和回滚演练，最后移除兼容层。

回退条件在发布前定义，例如结构化输出失败率增加 0.5 个百分点、旧 thread 恢复失败、出现未知 MCP 协议修订或任何越权结果。数据库和 Checkpoint 迁移尽量保持向后兼容；如果旧代码无法读取新数据，代码回滚也就失去了实际意义。

| 资产 | 迁移验证 | 回退保障 |
| --- | --- | --- |
| Prompt / 模型 | 固定评测集与工具选择差异 | 保留旧模型与 Prompt 版本开关 |
| State / Checkpoint | 每个暂停节点的旧快照恢复 | 新增字段先可选，保留旧节点处理实例 |
| MCP | 目标 Client × Server 实测矩阵 | 兼容路由或成对回退 |
| 向量索引 | 新旧索引双查与 Recall 对比 | 通过别名切回旧索引 |
| API / 流事件 | 消费者契约与录制事件回放 | 业务适配层维持旧外部格式 |

### 16.3 不应直接照搬的旧版写法

| 旧版做法 | 当前处理 |
| --- | --- |
| `langgraph.prebuilt.create_react_agent` 作为默认入口 | 新 LangChain Agent 优先使用 `langchain.agents.create_agent`；自定义底层图时再用 LangGraph API |
| 旧版 `initialize_agent`、旧 Memory 抽象 | 迁移到 `create_agent`、Middleware 与 LangGraph 持久化 |
| `langchain_community` 中的提供商模型导入 | 使用独立提供商包，例如 `langchain-openai`，以当前集成文档为准 |
| `langchain-core 0.3.x`、`langgraph 0.5.x` 等旧依赖锁定 | 维护时保留原环境；升级项目使用 v1 体系并重新跑测试 |
| MCP Python SDK v1 的 `FastMCP` | v2 使用 `from mcp.server import MCPServer`；按官方迁移指南处理类型、传输和错误语义变化 |
| 当前 LangChain MCP Adapters 与 MCP Python SDK v2 混装 | Adapter 使用独立 `mcp>=1.24,<2` 环境，通过 HTTP 连接可兼容旧协议的 v2 Server；等待 Adapter 明确支持 v2 后再合并环境 |
| MCP `initialize`、会话 ID、独立 SSE 端点 | 视为旧修订兼容知识；新系统面向 2026-07-28 无状态核心设计 |
| Spring AI 1.0.0-M7 MCP Starter | 迁移到当前稳定 Spring AI BOM 和 Streamable / Stateless 配置 |
| 代码中硬编码 Token、密码或演示 JWT | 删除并立即轮换真实秘密；改用环境注入和秘密管理 |
| FAISS 危险反序列化开关 | 只对可信、完整性可验证的自有索引使用 |
| 工具函数拥有宽泛 Shell、SQL 或文件权限 | 拆成领域动作，参数化，最小权限，必要时沙箱和审批 |

表中的“当前处理”不是机械替换命令。例如从 `FastMCP` 迁到 `MCPServer` 还要验证错误模型、协议修订、传输、并发和 Client 兼容；从旧 Memory 迁到 Checkpointer 要决定 thread 生命周期、授权和清理策略。每一行都应关联至少一个回归测试。

### 16.4 为什么“能跑”仍不等于“已迁移”

框架往往保留兼容包装，因此旧函数在新环境里可能暂时能导入。但迁移是否完成要看状态 Schema、工具错误、流式事件、检查点、传输、认证、可观测性和测试是否遵循新语义，而不是只看 Demo 返回了一句话。

完成标准应包含：源码中不再出现计划移除的 API；依赖锁无冲突和意外降级；所有外部契约有版本证据；旧数据可迁移或有明确排空策略；监控能区分新旧路径；团队运行手册与示例已经更新；回滚演练成功。若只能由发起迁移的一个人解释兼容层，知识转移也尚未完成。

## 17 面试与设计评审：用运行证据回答追问

### 17.1 从概念边界追到验证证据

面试或评审中的追问，通常是在检查一个技术判断能否落到可观察事实。前文已经完整解释各概念，本章不重复定义，而是把结论连接到应提供的运行证据和失败信号。

| 追问 | 第一层判断 | 应提供的运行证据 | 说明判断错误的信号 | 复习入口 |
| --- | --- | --- | --- | --- |
| LangChain 与 LangGraph 如何分工 | 组件组合用 LangChain；状态、分支、恢复和人工介入用 LangGraph | Runnable 输入输出、节点状态增量、路径与 Checkpoint | 一次固定调用也引入复杂 State，或长流程只保存聊天文本 | 第 3、4、8、9 章 |
| LCEL 的 `\|` 做了什么 | 构造并配置可执行的 Runnable 组合 | 每个边界的输入输出类型、流事件与 Trace | 中间 Lambda 阻塞流式，或整条含写操作的链被重试 | 第 2.2、5 章 |
| Reducer 为什么属于 State 契约 | 它规定并发更新如何合并 | 两个并发节点的更新、合并结果与不变量测试 | 结果依赖完成顺序、证据丢失或最终字段被多写 | 第 8.1～8.2、14.2 节 |
| Checkpointer、Store 与业务数据库如何区分 | 分别保存 thread 执行快照、跨 thread 记忆和业务真相 | 杀进程恢复、跨 thread 读取、业务库唯一记录 | 内存 Saver 被当作持久化，或订单事实只存在 Agent 记忆 | 第 6.1、9.1～9.2 节 |
| interrupt 后为什么可能重跑 | 恢复时含 `interrupt()` 的节点从头执行 | 中断负载、恢复轨迹、幂等键与外部对象数量 | 同一恢复产生两个业务对象，或审批值对应错误中断位置 | 第 9.3～9.4、14.3 节 |
| Function Calling 与 MCP 如何衔接 | 前者表达模型意图，后者负责跨边界发现和调用 | AIMessage tool call、MCP 请求、ToolMessage 的同一调用 ID | 模型已产生调用但 Server 无请求，或 Schema 合法却越权执行 | 第 3.3、10.4、10.7 节 |
| Tools、Resources 与 Prompts 如何区分 | 动作、可寻址内容与复用模板使用不同契约 | 能力列表、URI 读取、模板参数与各自授权记录 | 把静态大文档做成写 Tool，或把外部 Prompt 当系统规则 | 第 10.1、10.5 节 |
| 2026 MCP 无状态核心改变了什么 | 初始化握手和协议会话移除，多轮输入改用 MRTR | Client × Server 修订矩阵、请求头、POST 响应流 | 新 Client 仍依赖会话粘性，或把无状态误解为不能流式 | 第 10.2～10.3、16 章 |
| RAG 与微调如何选择 | RAG 管可更新事实，微调改善稳定行为或专门能力 | Top-K、引用、行为评测与版本化数据集 | 检索没命中却先调 Prompt，或用微调保存频繁变化的制度 | 第 7 章 |
| 如何控制 Agent 的真实副作用 | 权限、确认、幂等、预算和审计共同构成控制面 | 越权样本零执行、重复恢复单写入、审批快照签名和完整审计链 | 只靠 System Prompt，或模型文本称成功但 State 无业务 ID | 第 9.4、11.6、13、14 章 |

### 17.2 一个可复述的判断顺序

面对新的设计题，可以按同一顺序展开，避免从框架名或术语开始背诵：

1\. 先写业务完成条件与不可接受后果，例如必须有引用、不能越权、不能重复写入。

2\. 再划分确定性规则与模型判断，把金额计算、授权和提交留在可测试的代码边界。

3\. 逐段说明数据契约、状态变化和信任边界，指出 Chain、Graph、MCP 与业务数据库各自负责的部分。

4\. 展开最危险的失败窗口，包括超时后结果未知、中断恢复、并发合并、协议不兼容和证据过期。

5\. 最后给出可复现实验、指标、审计记录与回滚条件。没有运行证据的架构判断仍是假设，需要在上线门槛中继续验证。

## 18 速查表与学习验收

### 18.1 技术选择速查

| 需求 | 首选 | 原因 |
| --- | --- | --- |
| 固定提示后解析字段 | LangChain Chain + 结构化输出 | 路径固定、测试简单 |
| 文档问答 | Retriever + Two-step RAG | 可引用、可独立评测 |
| 模型在少量只读工具间选择 | `create_agent` | 标准工具循环已具备 |
| 分支、循环、暂停、恢复 | 自定义 LangGraph | 状态与控制流显式 |
| 跨进程或跨语言共享工具 | MCP | 契约与传输标准化 |
| 高风险业务操作 | 确定性图 + Human-in-the-loop | 权限、审批和审计可控 |
| 多领域且工具权限隔离 | 受约束 Multi-Agent | 上下文和权限边界清晰 |

### 18.2 上线前检查清单

1\. 依赖有锁文件，旧项目维护环境与升级验证环境分离。

2\. 模型、Prompt、Tool、Graph 和 MCP Schema 都有版本标识。

3\. 所有秘密来自秘密管理系统，仓库、日志、追踪和错误中没有凭据。

4\. thread_id、user_id、tenant_id 分离并由服务端校验。

5\. 检索实施文档级和片段级权限过滤，引用能回到原文。

6\. 写工具有最小权限、参数校验、幂等键和人工确认。

7\. 每个外部调用有超时，重试范围和总预算已明确。

8\. Agent 有最大轮数、调用数、Token、费用和时间限制。

9\. Checkpointer 使用生产后端，恢复、迁移、备份和删除经过演练。

10\. MCP Client / Server 的协议修订、传输、认证和契约经过联调。

11\. 直接与间接提示注入、越权、SSRF、路径与参数攻击已测试。

12\. 指标、日志、追踪和审计已脱敏，并配置告警与保留策略。

13\. 有离线评测基线、线上灰度门槛、回滚开关和事故负责人。

### 18.3 学习成果自测

如果能够独立完成下列任务，说明已经跨过“会跑 Demo”阶段：

1\. 用 Runnable 实现串行、并行、动态路由、重试和流式，并解释每个节点类型。

2\. 构建带引用 RAG，并分别评测检索召回与生成忠实度。

3\. 用 StateGraph 实现工具循环、条件边、Reducer、检查点和中断恢复。

4\. 写一个 MCP Python SDK v2 Server，通过 Inspector 验证，再在独立的 v1 兼容环境中通过 LangChain Adapter 验证旧协议互操作。

5\. 解释 2025 与 2026-07-28 MCP 生命周期和传输差异。

6\. 为写工具设计授权、人工确认、幂等、审计和失败恢复。

7\. 用一组固定样本比较模型、Prompt、检索和工具版本，而不是凭聊天体验判断。

## 19 官方资料与版本阅读

### 19.1 官方资料

1\. [LangChain v1 发布与迁移](https://docs.langchain.com/oss/python/releases/langchain-v1)：`create_agent`、Middleware 和旧功能迁移说明。

2\. [LangChain Agents](https://docs.langchain.com/oss/python/langchain/agents)：标准 Agent、工具和动态行为。

3\. [LangChain ChatOpenAI](https://docs.langchain.com/oss/python/integrations/chat/openai)：模型初始化、当前参数、工具调用和结构化输出能力。

4\. [LangChain Structured Output](https://docs.langchain.com/oss/python/langchain/structured-output)：Provider Strategy、Tool Strategy 与结构化结果。

5\. [LangChain Runnable Reference](https://reference.langchain.com/python/langchain-core/runnables)：Runnable、Sequence、Parallel、流式、重试与回退。

6\. [LangChain Retrieval](https://docs.langchain.com/oss/python/langchain/retrieval)：Two-step、Agentic 与 Hybrid RAG。

7\. [LangGraph Persistence](https://docs.langchain.com/oss/python/langgraph/persistence)：检查点、thread、Store 与生产后端。

8\. [LangGraph Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts)：暂停、恢复、重放与幂等要求。

9\. [LangGraph ToolNode](https://reference.langchain.com/python/langgraph.prebuilt/tool_node/ToolNode)：自定义工具节点的输入输出和默认错误策略。

10\. [LangGraph Streaming](https://docs.langchain.com/oss/python/langgraph/streaming)：底层 `stream_mode` 与 v2 事件外壳。

11\. [LangGraph Event Streaming](https://docs.langchain.com/oss/python/langgraph/event-streaming)：LangGraph 1.2 推荐的类型化投影与 v3 API。

12\. [LangChain Text Splitters](https://docs.langchain.com/oss/python/integrations/splitters/index)：结构感知切分器与集成入口。

13\. [MCP 2026-07-28 发布说明](https://blog.modelcontextprotocol.io/posts/2026-07-28/)：无状态核心、MRTR、缓存、认证与弃用项。

14\. [MCP Streamable HTTP 2026-07-28](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/streamable-http.mdx)：当前 HTTP 传输语义。

15\. [MCP Python SDK v2](https://py.sdk.modelcontextprotocol.io/)：当前稳定 SDK 的 `MCPServer`、Client、传输和旧协议兼容。

16\. [MCP Python SDK v2 变化说明](https://py.sdk.modelcontextprotocol.io/whats-new/)：稳定主线、协议时代变化、弃用能力和主要重命名。

17\. [MCP Python SDK 运行 Server](https://py.sdk.modelcontextprotocol.io/run/)：STDIO 默认行为、Streamable HTTP 参数与端点。

18\. [MCP Python SDK v1 维护线](https://py.sdk.modelcontextprotocol.io/v1/)：`FastMCP` 与当前 LangChain MCP Adapters 所需的旧主版本。

19\. [MCP Python SDK v1 到 v2 迁移指南](https://py.sdk.modelcontextprotocol.io/migration/)：类名、类型、传输、认证和错误语义的破坏性变化。

20\. [MCP Python SDK 服务旧版客户端](https://py.sdk.modelcontextprotocol.io/run/legacy-clients/)：同一 v2 Server 如何按协议日期路由现代与旧版请求，以及旧会话的多 Worker 代价。

21\. [MCP SDK 分级](https://modelcontextprotocol.io/docs/sdk)：各语言 SDK 的支持级别。

22\. [MCP Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)：远程授权基础；同时结合 2026 发布说明理解新修订。

23\. [LangChain MCP Adapters](https://github.com/langchain-ai/langchain-mcp-adapters)：MCP Tool 与 LangChain Agent 的适配；使用前核对其 `mcp` 依赖上限。

24\. [LangChain MCP Adapters 依赖元数据](https://github.com/langchain-ai/langchain-mcp-adapters/blob/main/pyproject.toml)：确认当前 Adapter 对 MCP Python SDK 主版本的约束。

25\. [Spring AI MCP Overview](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-overview.html)：Java MCP Client / Server、传输与能力概览。

26\. [Spring AI 2.0 Upgrade Notes](https://docs.spring.io/spring-ai/reference/upgrade-notes.html)：BOM、MCP Java SDK、包名和输入校验等破坏性变化。

27\. [Spring AI MCP Server Annotations](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-annotations-server.html)：`@McpTool`、参数 Schema 与同步、异步、无状态方法过滤。

28\. [Spring AI Streamable HTTP Server](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-streamable-http-server-boot-starter-docs.html)：当前 Starter 和协议配置。

### 19.2 版本阅读原则

框架文档、协议规范和 SDK README 可能处于不同发布节奏。遇到冲突时按“项目锁定版本的 API 文档—SDK 发布说明—目标协议日期—主分支示例”的顺序核对，并用最小契约测试确认实际行为。主分支示例反映当前开发方向，不能直接代表已安装的稳定版本。

一次版本结论至少附四项证据：锁文件中的精确包版本；该版本对应的发布说明或文档；运行时打印出的版本与协议修订；最小成功和失败请求。主分支 `README` 只能说明项目当前开发方向，不能证明已发布包包含同一 API。

涉及多个项目时，要建立兼容矩阵，不能用“都升级到最新”代替验证。比如 LangChain MCP Adapters 0.3.2 约束 `mcp>=1.24,<2`，而 MCP Python SDK v2 属于另一条稳定主线。

这两套依赖应放入独立环境，再用目标 Client 与 Server 的契约测试证明它们能够互操作。兼容结论还要关联精确锁文件、发布说明和实际请求样本，因为依赖上限与协议支持会继续变化。
