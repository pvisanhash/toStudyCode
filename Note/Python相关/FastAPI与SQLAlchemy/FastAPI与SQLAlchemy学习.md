# 0 FastAPI 与 SQLAlchemy 学习笔记

> 本文面向第一次接触 FastAPI 与 SQLAlchemy 的读者。示例使用 Python 3.11+、FastAPI 的现代写法、Pydantic 2 和 SQLAlchemy 2.x。贯穿全文的业务是“任务管理 API（Application Programming Interface，应用程序编程接口）”：客户端可以新建、查询、修改和删除任务。SQLite 用于本地学习；生产章节再说明 PostgreSQL、连接池、迁移、并发和部署边界。

## 1 从一个可观察的问题开始

### 1.1 我们要解决什么问题

假设前端需要保存下面这条任务：

```json
{
  "title": "学习 FastAPI",
  "description": "完成第一个数据库接口"
}
```

后端至少要完成四件事：接收并校验数据、执行业务规则、持久化到数据库、返回稳定的 JSON（JavaScript Object Notation，JavaScript 对象表示法）结构。如果标题缺失，客户端应收到可定位到字段的错误；如果任务不存在，应返回 `404 Not Found`，而不是返回空对象或把数据库异常直接暴露出去。

这组问题对应两类工具：

1\. FastAPI 负责 HTTP（Hypertext Transfer Protocol，超文本传输协议）请求匹配、参数提取、数据校验、依赖调度、响应序列化和 OpenAPI 文档生成。

2\. SQLAlchemy 负责数据库方言适配、连接管理、SQL（Structured Query Language，结构化查询语言）表达式、对象关系映射和事务会话。

两者不会自动替你决定业务边界。例如“任务标题能否重复”“一次操作修改几张表”“何时提交事务”仍由应用代码明确表达。

### 1.2 分阶段学习路线

| 阶段 | 阅读范围 | 能力目标 | 成功判据 |
| --- | --- | --- | --- |
| 第一阶段 | 第 2～4 章 | 看懂一次请求如何进入函数并变成响应 | 浏览器能打开 `/docs`，非法参数能得到结构化错误 |
| 第二阶段 | 第 5～8 章 | 用 SQLAlchemy 2.x 完成持久化 CRUD（Create、Read、Update、Delete，增删改查） | 重启服务后任务仍存在，失败事务会回滚 |
| 第三阶段 | 第 9～12 章 | 处理关系、迁移、异步、测试和工程结构 | 能解释 Session 生命周期、N+1 查询和迁移审查 |
| 第四阶段 | 第 13～16 章 | 面向生产治理安全、容量、部署与故障 | 能计算连接上限，能从日志、指标和数据库逐层定位问题 |

第一次学习可先完成第 2、3、6 章。第 10 章异步方案和第 14 章多进程部署建立在事务、连接池和测试已经理解的前提上。

### 1.3 最小系统中的数据流

```mermaid
flowchart LR
    C["客户端：浏览器、移动应用或脚本"] -->|"HTTP 请求"| A["ASGI 服务器：Uvicorn"]
    A --> M["FastAPI 中间件"]
    M --> R["路由与依赖注入"]
    R --> V["Pydantic 校验"]
    V --> H["路径操作函数"]
    H --> S["SQLAlchemy Session"]
    S --> E["Engine、连接池与方言"]
    E --> D[("关系数据库")]
    D --> E --> S --> H
    H --> O["响应模型与 JSON 序列化"]
    O --> C
```

Uvicorn 是 ASGI（Asynchronous Server Gateway Interface，异步服务器网关接口）服务器，负责网络连接和协议层事件；FastAPI 在其上匹配路由并组织依赖；Pydantic 把外部数据转成满足类型约束的 Python 对象；SQLAlchemy Session 在一个事务上下文里与数据库交互。数据库连接失败会阻断持久化阶段，响应模型校验失败通常说明服务端返回结构违反了接口契约。

这张图省略了反向代理、多个进程、缓存和消息队列，适用于本地单进程学习环境。第 14 章给出生产拓扑及故障边界。

## 2 第一个可运行的 FastAPI 程序

### 2.1 创建环境并安装依赖

在空目录中执行：

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install "fastapi[standard]" "sqlalchemy>=2,<3" alembic pydantic-settings pytest httpx
```

Windows PowerShell 可使用 Python Launcher 创建并激活虚拟环境：

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install "fastapi[standard]" "sqlalchemy>=2,<3" alembic pydantic-settings pytest httpx
```

`fastapi[standard]` 包含常用运行依赖和 FastAPI CLI（Command-Line Interface，命令行界面）。团队项目应把解析后的版本写入锁文件，并让开发、测试和生产使用同一组依赖；本文不固定补丁版本，以免示例在安全更新后误导读者。

### 2.2 写出第一个端点

创建 `main.py`：

```python
from fastapi import FastAPI

app = FastAPI(title="任务管理 API")


@app.get("/health")
def health() -> dict[str, str]:
    return {"status": "ok"}
```

启动开发服务器：

```bash
# 使用 FastAPI 的开发服务器运行 main.py 中的 FastAPI 应用。
fastapi dev main.py
```

`app = FastAPI(...)` 创建应用对象；`@app.get("/health")` 把 `GET /health` 与下面的函数绑定；函数返回的字典会被序列化成 JSON。`def` 在这里足够，因为函数没有异步等待操作。

### 2.3 验证成功与失败

新开终端执行：

```bash
curl -i http://127.0.0.1:8000/health
```

成功时应看到 `HTTP/1.1 200 OK`，响应体为：

```json
{"status":"ok"}
```

再访问以下地址：

1\. Swagger UI：`http://127.0.0.1:8000/docs`

2\. ReDoc：`http://127.0.0.1:8000/redoc`

3\. OpenAPI JSON：`http://127.0.0.1:8000/openapi.json`

```txt
                    /docs（交互调试）
                  ↗
/openapi.json ────→ /redoc（read阅读文档）
                  ↘
                    数据源头，用于客户端生成、Postman、自动化测试
```

交互文档不是额外手写的页面。FastAPI 读取路由、类型标注和 Pydantic 模型，生成 OpenAPI 描述；Swagger UI 与 ReDoc 再消费这份描述。

启动失败时先检查终端的第一条异常。`Address already in use` 表示 8000 端口被占用，可临时执行 `fastapi dev main.py --port 8001`；`ModuleNotFoundError` 通常表示虚拟环境未激活或依赖安装在另一个 Python 解释器中；请求得到 `404` 时核对 HTTP 方法与路径是否同时匹配。

### 2.4 一次请求在运行时发生了什么

```mermaid
sequenceDiagram
    participant C as 客户端
    participant U as Uvicorn
    participant F as FastAPI
    participant H as health 函数
    C->>U: GET /health
    U->>F: ASGI HTTP 事件
    F->>F: 匹配 GET 与 /health
    F->>H: 调用路径操作函数
    H-->>F: Python 字典
    F-->>U: 状态码、响应头、JSON 字节
    U-->>C: HTTP 200
```

ASGI 定义服务器与 Python Web 应用之间的调用约定。与传统 WSGI（Web Server Gateway Interface，Web 服务器网关接口）相比，ASGI 能表达异步 HTTP、WebSocket 和应用生命周期事件。FastAPI 是应用框架，Uvicorn 是服务器；二者职责不同，部署时都存在。

## 3 用类型声明接口契约

### 3.1 路径参数与查询参数

把下面代码追加到 `main.py`：

```python
from typing import Annotated

from fastapi import FastAPI, Query

app = FastAPI(title="任务管理 API")


@app.get("/health")
def health() -> dict[str, str]:
    return {"status": "ok"}


@app.get("/tasks/{task_id}")
def read_task(
    task_id: int,
  	// 定义一个默认值为 False 的布尔查询参数 detail，用于控制是否返回详细信息
    detail: Annotated[bool, Query(description="是否返回详细信息")] = False,
) -> dict[str, int | bool]:
    return {"task_id": task_id, "detail": detail}
```

`task_id` 的名字出现在路径模板中，因此来自路径；`detail` 没出现在路径中，且是普通标量，因此来自查询字符串。请求 `/tasks/12?detail=true` 会得到 `{"task_id":12,"detail":true}`。

请求 `/tasks/abc` 时，FastAPI 在调用函数前发现 `abc` 无法转换成整数，默认返回 `422 Unprocessable Content` 以及错误位置。业务函数没有被执行。这个行为让边界校验集中在接口声明处。

`Annotated` 把 Python 类型和 FastAPI 元数据放在一起。它保留静态类型信息，也能承载 `Query`、`Path`、`Header` 或 `Depends` 等框架声明，是当前官方文档优先采用的写法。

### 3.2 请求体与 Pydantic 模型

Pydantic 模型描述输入数据的字段、类型和约束。添加：

```python
from typing import Annotated

from fastapi import FastAPI, Query, status
from pydantic import BaseModel, ConfigDict, Field

app = FastAPI(title="任务管理 API")


class TaskCreate(BaseModel):
    # 禁止请求中出现 title、description 之外的字段
    model_config = ConfigDict(extra="forbid")

    # 任务标题：必填，长度必须在 1～100 个字符之间
    title: str = Field(min_length=1, max_length=100)

    # 任务描述：可选，默认值为 None；填写时最多 500 个字符
    description: str | None = Field(default=None, max_length=500)


class TaskRead(BaseModel):
    id: int
    title: str
    description: str | None
    completed: bool


@app.get("/health")
def health() -> dict[str, str]:
    return {"status": "ok"}


@app.get("/tasks/{task_id}")
def read_task(
    task_id: int,
    detail: Annotated[bool, Query(description="是否返回详细信息")] = False,
) -> dict[str, int | bool]:
    return {"task_id": task_id, "detail": detail}

# 成功时返回 HTTP 201
@app.post("/tasks", response_model=TaskRead, status_code=status.HTTP_201_CREATED)
def create_task(payload: TaskCreate) -> TaskRead:
    return TaskRead(
        id=1,
        title=payload.title,
        description=payload.description,
        completed=False,
    )
```

发送：

```bash
curl -i -X POST http://127.0.0.1:8000/tasks \
  -H 'Content-Type: application/json' \
  -d '{"title":"学习 FastAPI","description":"完成第一个接口"}'
```

成功判据是状态码 `201 Created`，且响应含有服务端生成的 ID（Identifier，标识符）和 `completed`。如果额外发送 `{"priority":99}`，`extra="forbid"` 会把拼错或未约定字段当作错误；如果标题是空字符串，`min_length=1` 会阻止业务函数执行。

Pydantic 所称的“校验”包含解析和类型转换，保证的是处理后的模型满足声明的类型。比如非严格模式可能把字符串数字转换为整数。金额、权限标记等不允许宽松转换的字段可考虑严格类型或严格模式，并为客户端兼容性编写测试。

例如：

```python
from pydantic import BaseModel


class User(BaseModel):
    age: int


user = User(age="18")

print(user.age)        # 18
print(type(user.age))  # <class 'int'>
```

客户端传入的是字符串 `"18"`，但模型声明 `age: int`，因此 Pydantic 会尝试将它转换成整数 `18`。转换成功后，Pydantic 保证 `user.age` 是整数。

如果某些字段不希望自动转换，可以使用严格类型：

```python
from pydantic import BaseModel, StrictInt


class User(BaseModel):
    age: StrictInt
```

此时传入：

```python
User(age="18")
```

会产生校验错误，因为字符串 `"18"` 不是严格意义上的整数。

### 3.3 输入模型、数据库模型与输出模型各自负责什么

| 模型 | 数据来源 | 主要职责 | 生命周期 |
| --- | --- | --- | --- |
| 请求模型 | 不可信的客户端输入 | 校验允许字段、类型、格式和边界 | 一次请求 |
| SQLAlchemy 模型 | 数据库行与关系 | 表映射、持久化状态、关系加载 | 一个 Session 中被跟踪 |
| 响应模型 | 应用准备返回的数据 | 过滤字段、序列化、校验输出契约 | 一次响应 |

把三者全部合成一个类容易泄露 `password_hash`、内部状态或审计字段，也会让数据库结构变更直接破坏外部 API。小项目可以少写几类模型，但输入和输出边界仍应明确。

`response_model` 会限制实际输出字段。即使内部对象含有额外属性，未出现在响应模型中的字段也不会进入 JSON；这既是接口稳定性措施，也是避免敏感字段泄漏的重要防线。官方说明见 [FastAPI 响应模型](https://fastapi.tiangolo.com/tutorial/response-model/) 与 [Pydantic 模型](https://docs.pydantic.dev/latest/concepts/models/)。

### 3.4 参数来源速查

| 声明方式 | 默认来源 | 典型用途 | 常见错误 |
| --- | --- | --- | --- |
| 路径模板中的标量参数 | Path | 资源标识，如 `/tasks/{task_id}` | 把可选筛选条件放进路径 |
| 路径外的标量参数 | Query | 分页、排序、筛选 | `limit` 没设上限导致大查询 |
| Pydantic 模型参数 | Body | JSON 请求体 | 用 `GET` 请求体承载常规查询 |
| `Header()` | Header | 追踪 ID、条件请求 | 自制认证头而忽略标准方案 |
| `Cookie()` | Cookie | 浏览器会话 | 缺少 `Secure`、`HttpOnly`、`SameSite` 策略 |
| `Form()` 与 `File()` | 表单或上传 | OAuth2 表单、文件上传 | 把大文件一次性读入内存 |

HTTP `GET` 请求体在规范和中间代理中的兼容性不稳定，常规查询应使用路径与查询参数。创建通常用 `POST`，完整替换常用 `PUT`，局部更新常用 `PATCH`，删除用 `DELETE`。

## 4 路由、依赖与错误边界

### 4.1 APIRouter 拆分业务模块

当所有端点都写在 `main.py` 中，导入关系、测试和权限配置会逐渐混乱。`APIRouter` 可以把同一资源的路由组合起来：

```python
from fastapi import APIRouter, FastAPI

tasks_router = APIRouter(prefix="/tasks", tags=["tasks"])


@tasks_router.get("")
def list_tasks() -> list[dict[str, object]]:
    return []


app = FastAPI(title="任务管理 API")
app.include_router(tasks_router)
```

`prefix` 统一添加路径前缀，`tags` 用于文档分组。大型项目通常按业务能力拆分路由，如任务、用户、认证；按 HTTP 方法把所有 `GET` 放一个文件会把同一资源的规则拆散。

### 4.2 依赖注入如何工作

依赖注入让路径操作函数声明“需要什么”，FastAPI 负责解析依赖树、调用提供者并把结果传入。数据库 Session、当前用户、权限校验和配置读取都是常见依赖。

```python
from typing import Annotated

from fastapi import Depends, Header, HTTPException, status


def require_request_id(
    # 从请求头读取 X-Request-Id；没有提供时值为 None
    x_request_id: Annotated[str | None, Header()] = None,
) -> str:
    # 如果请求头不存在，立即返回 HTTP 400 错误
    if x_request_id is None:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="X-Request-Id header is required",
        )

    # 请求头存在时，将它返回给路径操作函数
    return x_request_id


# 定义一个可重复使用的类型别名：
# 使用该类型的参数会自动调用 require_request_id
RequestId = Annotated[str, Depends(require_request_id)]
```

路径函数使用 `request_id: RequestId` 时，FastAPI 会先调用 `require_request_id`。依赖自身也可以依赖其他依赖，由此形成有向树。同一个请求中，默认会缓存同一个依赖的结果，避免重复创建 Session 或重复解析用户；确实需要每次重新执行时才考虑 `use_cache=False`。

> 解释：
>
> 同一次请求中，如果多个地方使用同一个依赖，FastAPI 默认只执行一次，然后复用结果，避免重复创建数据库 Session。
>
> 设置 `Depends(get_data, use_cache=False)` 后，每次使用都会重新执行依赖，一般仅在确实需要不同结果时使用

### 4.3 带 yield 的资源生命周期

数据库 Session 需要“请求前创建、请求后关闭”。带 `yield` 的依赖能表达这个边界：

```python
from collections.abc import Generator


def get_resource() -> Generator[
    str,   # yield 产生的值的类型
    None,  # 外部通过 send() 传入生成器的值的类型；None 表示不接收
    None,  # 生成器结束时 return 的值的类型；None 表示不返回结果
]:
    # 创建资源
    resource = "opened"

    try:
        # 暂停函数并把资源交给调用者
        yield resource
    finally:
        # 使用结束或发生异常时都会执行
        print("closed")
```

`yield` 的代码在路径函数操作执行前运行，产出的值被注入；`finally` 中的清理即使遇到异常也会执行。当前 FastAPI 支持依赖作用域：默认请求作用域的退出逻辑在响应发送后执行；`Depends(..., scope="function")` 可让清理发生在路径操作返回后、响应发送前。流式响应需要资源在迭代期间仍然可用，不能过早关闭。详细时间线见 [FastAPI 带 yield 的依赖](https://fastapi.tiangolo.com/tutorial/dependencies/dependencies-with-yield/)。

如果依赖捕获异常却不重新抛出，框架可能无法按预期观察到失败。除非明确转换成另一个异常，清理代码中的 `except` 通常应使用裸 `raise` 继续传播原异常。

这段话在说明：使用 `yield` 的 FastAPI 依赖既能提供资源，也能保证资源最后被清理。

```
def get_session():
    session = create_session()  # ① 路径函数执行前：创建资源

    try:
        yield session           # ② 把资源注入路径函数
    finally:
        session.close()         # ④ 请求结束后：关闭资源
```

路径函数位于第 ③ 步：

```
@app.get("/tasks")
def list_tasks(session=Depends(get_session)):
    return query_tasks(session)  # ③ 使用资源
```

默认执行顺序是：

```
创建 Session
    ↓
yield Session
    ↓
执行路径函数
    ↓
生成并发送响应
    ↓
执行 finally，关闭 Session
```

如果使用：

```
Depends(get_session, scope="function")
```

执行顺序会变成：

```
创建 Session
    ↓
执行路径函数
    ↓
关闭 Session
    ↓
发送响应
```

区别在于资源关闭时间：

- `scope="request"`：响应发送后关闭，是 `yield` 依赖的默认作用域。
- `scope="function"`：路径函数返回后、响应发送前关闭。

流式响应会分批读取和发送数据：

```
return StreamingResponse(generate_rows(session))
```

如果 Session 在数据迭代完成前关闭，后续数据就无法查询，因此流式响应通常需要让资源保持到响应发送完成。

异常部分的意思是：依赖捕获异常后，如果既不处理也不重新抛出，FastAPI 可能会误以为执行正常。

错误写法：

```
def get_session():
    session = create_session()

    try:
        yield session
    except Exception:
        session.rollback()
        # 异常被吞掉了
    finally:
        session.close()
```

推荐写法：

```
def get_session():
    session = create_session()

    try:
        yield session
    except Exception:
        session.rollback()
        raise  # 继续抛出刚才捕获的原异常
    finally:
        session.close()
```

这里的裸 `raise` 表示重新抛出当前捕获的原异常，让 FastAPI 能继续执行异常处理并返回正确的错误响应。

### 4.4 把错误映射成稳定的 HTTP 语义

| 场景 | 建议状态码 | 由谁发现 | 响应原则 |
| --- | --- | --- | --- |
| 请求字段不满足约束 | 422 | FastAPI/Pydantic | 给出字段位置和原因 |
| 身份凭证缺失或无效 | 401 | 认证依赖 | 搭配合适的 `WWW-Authenticate` |
| 已认证但权限不足 | 403 | 授权逻辑 | 不泄露不必要的资源信息 |
| 任务不存在 | 404 | 查询结果判断 | 返回稳定的业务错误结构 |
| 唯一键冲突 | 409 | 数据库约束与异常映射 | 回滚事务，不暴露 SQL |
| 未处理的程序错误 | 500 | 全局异常处理 | 对外隐藏内部细节，对内记录堆栈和请求 ID |

`HTTPException` 适合在接口或依赖边界表达可预期的 HTTP 错误。领域层也可以抛出不依赖 Web 框架的业务异常，再由全局异常处理器映射成 HTTP 响应。数据库连接串、SQL 参数、文件路径和堆栈不应直接返回给客户端。

这段话的意思是：不同层负责不同类型的错误，并且不能把服务器内部信息泄露给客户端。

例如，在接口层可以直接使用 `HTTPException`：

```python
from fastapi import HTTPException, status


@app.get("/tasks/{task_id}")
def get_task(task_id: int):
    task = find_task(task_id)

    if task is None:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Task not found",
        )

    return task
```

这里的“任务不存在”是可预期情况，因此返回 `404 Not Found` 很合适。

业务层通常不应该依赖 FastAPI，可以定义普通业务异常：

```python
class TaskNotFoundError(Exception):
    pass


def find_task_service(task_id: int):
    task = find_task(task_id)

    if task is None:
        raise TaskNotFoundError("Task not found")

    return task
```

然后由 FastAPI 的全局异常处理器转换成 HTTP 响应：

```python
from fastapi import Request
from fastapi.responses import JSONResponse


@app.exception_handler(TaskNotFoundError)
def handle_task_not_found(
    request: Request,
    exc: TaskNotFoundError,
) -> JSONResponse:
    return JSONResponse(
        status_code=404,
        content={"detail": str(exc)},
    )
```

这样，业务层只表达“任务不存在”，接口层负责决定它对应 HTTP `404`。同一个业务层以后也可以被命令行程序、定时任务或消息消费者复用。

发生未知错误时，不应这样返回：

```json
{
  "detail": "postgresql://user:password@10.0.0.5/database",
  "sql": "SELECT * FROM users WHERE ...",
  "file": "/app/services/task.py",
  "traceback": "完整异常堆栈"
}
```

这些信息可能暴露数据库密码、服务器目录和程序结构。正确做法是：

- 客户端收到简洁、安全的错误信息和请求 ID。
- 服务端日志记录完整异常和堆栈，供开发人员排查。

例如客户端只收到：

```json
{
  "detail": "Internal server error",
  "request_id": "req-123"
}
```

### 4.5 中间件、依赖和路径函数的边界

中间件包围整个请求响应过程，适合请求 ID、访问日志、耗时、CORS（Cross-Origin Resource Sharing，跨源资源共享）和统一安全头。依赖能读取已解析的路径参数并参与 OpenAPI，适合认证、授权和 Session。路径函数负责具体用例。把所有业务都放在中间件会失去路由上下文，把每个访问日志都写成依赖则会重复配置。

这段话是在说明：中间件、依赖和路径函数位于不同层次，适合承担不同职责。

请求处理顺序大致如下：

```txt
客户端请求
    ↓
中间件
    ↓
依赖
    ↓
路径函数
    ↓
依赖清理
    ↓
中间件
    ↓
客户端响应
```

**中间件：处理所有请求的公共逻辑**

中间件会包围整个请求和响应过程，因此适合记录请求耗时、添加请求 ID、处理 CORS 和设置安全响应头。

```python
import time
import uuid

from fastapi import FastAPI, Request

app = FastAPI()


@app.middleware("http")
async def request_context(request: Request, call_next):
    request_id = str(uuid.uuid4())
    start_time = time.perf_counter()

    response = await call_next(request)

    elapsed = time.perf_counter() - start_time
    response.headers["X-Request-Id"] = request_id
    response.headers["X-Process-Time"] = f"{elapsed:.4f}"

    return response
```

因为每个请求都会经过中间件，所以访问日志不必在每个接口重复编写。

**依赖：为特定接口提供资源或执行检查**

依赖可以读取路径参数、请求头和查询参数，适合认证、授权及创建数据库 Session。

```python
from typing import Annotated

from fastapi import Depends, HTTPException


def check_task_permission(task_id: int):
    if task_id != 1:
        raise HTTPException(status_code=403, detail="Permission denied")

    return task_id


@app.get("/tasks/{task_id}")
def read_task(
    task_id: Annotated[int, Depends(check_task_permission)],
):
    return {"task_id": task_id}
```

依赖中的参数、认证要求等信息可以进入 OpenAPI 文档。中间件通常不了解最终匹配到的路径模板和已解析的 `task_id`，因此不适合承担这种资源级权限判断。

**路径函数：执行具体业务用例**

路径函数负责当前接口要完成的事情：

```python
@app.post("/tasks")
def create_task():
    # 校验业务规则
    # 创建任务
    # 提交数据库事务
    return {"message": "Task created"}
```

例如“创建任务”“查询订单”“修改用户资料”都属于具体业务用例。

因此可以这样划分：

| 位置     | 适合处理的内容                                    |
| -------- | ------------------------------------------------- |
| 中间件   | 所有请求都需要的日志、耗时、请求 ID、CORS、安全头 |
| 依赖     | Session、当前用户、认证、授权、路径参数相关检查   |
| 路径函数 | 创建任务、查询订单等具体业务操作                  |

“把所有业务放进中间件”会使中间件承担它不了解的路由业务；“把访问日志写成依赖”则需要给大量路由重复添加依赖，还可能漏掉未配置该依赖的接口。

## 5 SQLAlchemy 2.x 的最小持久化闭环

### 5.1 ORM 解决了什么问题

ORM（Object-Relational Mapping，对象关系映射）把表、列和关系映射成 Python 类与属性，让应用以对象方式组织持久化逻辑。SQLAlchemy 同时提供 Core 层：用 Python 表达 SQL 结构、参数绑定和结果处理。SQLAlchemy 2.x 的 ORM 查询也统一使用 `select()` 等 Core 风格构造。

ORM 不会消除 SQL。索引是否命中、连接数是否合理、事务锁了哪些行，最终仍由数据库执行计划和隔离级别决定。学习 ORM 时应能查看它生成的 SQL，并理解关键查询对应的关系模型。



可以把 ORM 理解成 Python 对象与关系数据库之间的“转换层”。

假设数据库中有一张任务表：

```mysql
CREATE TABLE tasks (
    id INTEGER PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    completed BOOLEAN NOT NULL
);
```

使用 SQLAlchemy ORM 时，可以把它映射成 Python 类：

```python
class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100))
    completed: Mapped[bool] = mapped_column(default=False)
```

对应关系如下：

| 数据库           | Python             |
| ---------------- | ------------------ |
| `tasks` 表       | `Task` 类          |
| 一条记录         | 一个 `Task` 对象   |
| `title` 列       | `task.title` 属性  |
| 表之间的外键关系 | 对象之间的属性关系 |

于是，业务代码可以操作对象：

```python
task = Task(title="学习 FastAPI")
session.add(task)
session.commit()
```

SQLAlchemy 会在底层生成类似的 SQL：

```mysql
INSERT INTO tasks (title, completed)
VALUES ('学习 FastAPI', false);
```

查询也是一样：

```python
statement = select(Task).where(Task.completed.is_(False))
tasks = session.scalars(statement).all()
```

底层大致对应：

```mysql
SELECT id, title, completed
FROM tasks
WHERE completed IS FALSE;
```

这里的 `select()` 来自 SQLAlchemy Core。Core 是更接近 SQL 的表达层，负责构造 SQL、绑定参数、适配不同数据库和处理查询结果；ORM 则在 Core 之上增加类映射、对象状态管理、关系加载和 Session 工作单元等能力。

可以把两层关系理解为：

```txt
Python 业务对象
      ↓
SQLAlchemy ORM
      ↓
SQLAlchemy Core
      ↓
数据库驱动
      ↓
关系数据库
```

“ORM 不会消除 SQL”指的是：开发者虽然操作 Python 对象，但数据库最终执行的仍然是 SQL。

例如下面的代码看起来只是读取对象属性：

```python
for user in users:
    print(user.tasks)
```

如果 `tasks` 使用延迟加载，它可能产生：

```mysql
SELECT * FROM users;

SELECT * FROM tasks WHERE user_id = 1;
SELECT * FROM tasks WHERE user_id = 2;
SELECT * FROM tasks WHERE user_id = 3;
```

查询 100 个用户时可能执行 101 条 SQL，这就是 N+1 查询问题。ORM 让代码更易组织，却不会自动保证 SQL 高效。

同样，下面这些问题最终都发生在数据库层：

1. `WHERE completed = false` 是否能使用索引。
2. 一个请求执行了多少条 SQL。
3. 事务是否长时间持有行锁。
4. 连接池是否已经耗尽。
5. 查询是否扫描了整张表。
6. 并发修改是否发生覆盖或冲突。

因此，学习 SQLAlchemy ORM 需要同时掌握两种视角：

```txt
对象视角：Session 中有哪些对象，它们处于什么状态？
数据库视角：实际执行了什么 SQL，事务和锁发生了什么？
```

开发时可以临时启用 SQL 日志：

```python
engine = create_engine(
    "sqlite:///./tasks.db",
    echo=True,
)
```

运行查询后，终端会打印 SQL。生产环境通常使用日志、慢查询记录和 `EXPLAIN` 执行计划进行分析，而不是长期启用 `echo=True`。

简而言之：ORM 帮助你用 Python 对象组织数据库操作；Core 帮助 ORM 构造并执行 SQL；数据库仍然负责索引、事务、锁和查询计划。熟练使用 ORM 的标志，是既能读懂 Python 对象操作，也能判断它最终会产生怎样的 SQL。

### 5.2 Engine、连接池、方言与 Session

```mermaid
flowchart LR
    A["应用代码"] --> S["Session：对象状态与事务边界"]
    S --> E["Engine：连接入口"]
    E --> P["Pool：复用物理连接"]
    E --> DI["Dialect：生成特定数据库 SQL"]
    P --> DBAPI["DBAPI 驱动"]
    DI --> DBAPI
    DBAPI --> DB[("SQLite 或 PostgreSQL")]
```

Engine 是应用级、通常长期复用的数据库入口，内部协调连接池与数据库方言。DBAPI（Database API，数据库应用程序编程接口）驱动负责真正的网络或文件通信。Session 是有状态的工作单元，跟踪 ORM 对象并代表一个逻辑事务；它不是连接池，也不适合做全局单例。

一个 Session 或 AsyncSession 不能被多个线程或异步任务并发共享。常见边界是“每个请求一个 Session，每个并发任务一个 Session”。官方对这一约束的解释见 [SQLAlchemy Session 基础](https://docs.sqlalchemy.org/en/20/orm/session_basics.html)。

可以把 SQLAlchemy 访问数据库的过程理解为“公司、车队、司机和一次配送任务”。

```
应用
  ↓
Session：组织一次业务操作
  ↓
Engine：数据库总入口
  ↓
连接池：管理可复用连接
  ↓
Dialect：把操作转换成对应数据库的 SQL
  ↓
DBAPI 驱动：真正发送 SQL、接收结果
  ↓
数据库
```

**Engine：应用长期复用的数据库入口**

Engine 可以理解为应用访问某个数据库的“基础设施总入口”。

```
from sqlalchemy import create_engine

engine = create_engine(
    "postgresql+psycopg://app:password@localhost/task_db",
    pool_pre_ping=True,
)
```

Engine 创建后主要掌握以下信息：

1. 使用哪种数据库，例如 PostgreSQL、MySQL 或 SQLite。
2. 使用哪个 DBAPI 驱动。
3. 数据库地址和连接参数。
4. 怎样管理连接池。
5. 怎样生成符合目标数据库语法的 SQL。

Engine 通常在应用启动时创建一次，之后由所有请求共同使用：

```
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(bind=engine)
```

不要在每个请求中重新创建 Engine：

```
# 不推荐
def handle_request():
    engine = create_engine(DATABASE_URL)
```

反复创建 Engine 会导致连接池无法有效复用，还可能建立过多数据库连接。

需要区分的是：Engine 可以被并发共享，因为它的职责是管理连接资源；Session 不能被多个并发执行流共享。

**连接池：复用真实数据库连接**

建立数据库连接通常需要网络握手、认证和初始化，成本高于从连接池中借用一个已有连接。

连接池会维护若干数据库连接：

```
连接池
├── 连接 1：空闲
├── 连接 2：正在被请求 A 使用
├── 连接 3：正在被请求 B 使用
└── 连接 4：空闲
```

当 Session 第一次需要执行 SQL 时，大致发生以下过程：

1. Session 向 Engine 请求连接。
2. Engine 从连接池取出一个可用连接。
3. Session 在连接上开启事务并执行 SQL。
4. Session 关闭后，连接通常被归还连接池。
5. 后续请求可以继续复用这个连接。

“关闭 Session”通常不表示关闭数据库服务器上的物理连接，而是释放 Session 占用的资源，将连接归还连接池。

**Dialect：处理不同数据库的语法差异**

Dialect 的全称是数据库“方言”。不同数据库都支持 SQL，但部分语法、类型和功能并不相同。

例如分页、自动增长主键、JSON 类型和返回插入结果的语法可能存在差异。SQLAlchemy 根据连接地址选择方言：

```
postgresql+psycopg://...
↑          ↑
数据库方言  DBAPI 驱动
```

再如：

```
mysql+pymysql://...
sqlite+pysqlite:///tasks.db
```

应用写的是 SQLAlchemy 表达式：

```
statement = select(Task).limit(10)
```

Dialect 负责将它编译成适合目标数据库的 SQL。

这不代表应用可以不经测试就任意切换数据库。数据类型、锁、事务隔离和功能特性仍有差异，因此生产测试应使用与生产环境相同类型的数据库。

**DBAPI 驱动：真正与数据库通信**

DBAPI（Database API，数据库应用程序编程接口）是 Python 数据库驱动遵循的一套接口规范。

常见驱动包括：

| 数据库     | 同步驱动示例     | 异步驱动示例 |
| ---------- | ---------------- | ------------ |
| PostgreSQL | psycopg          | asyncpg      |
| MySQL      | PyMySQL          | asyncmy      |
| SQLite     | sqlite3/pysqlite | aiosqlite    |

SQLAlchemy 自己不会直接通过网络协议连接所有数据库。真正执行以下操作的是驱动：

1. 创建数据库连接。
2. 发送带参数的 SQL。
3. 接收数据库返回的行。
4. 提交或回滚事务。
5. 报告网络、约束或语法错误。

SQLAlchemy 位于业务代码与驱动之间，提供统一的 SQL 构造、对象映射和事务管理方式。

**Session：组织一次逻辑事务**

Session 可以理解为一次业务操作的“工作台”。

```
with SessionLocal() as session:
    task = Task(title="学习 SQLAlchemy")
    session.add(task)
    session.commit()
```

它主要负责三件事。

**跟踪 ORM 对象**

Session 会记录对象的状态：

```
task = Task(title="学习 SQLAlchemy")
session.add(task)

task.completed = True
```

Session 知道：

1. `task` 是新增对象。
2. `title` 需要写入数据库。
3. `completed` 被修改。
4. flush 时应该生成 `INSERT` 或 `UPDATE`。

**维护身份映射**

在同一个 Session 中，多次通过相同主键取得记录，通常会得到同一个 Python 对象：

```
task1 = session.get(Task, 1)
task2 = session.get(Task, 1)

assert task1 is task2
```

Session 内部使用 identity map（身份映射），按“模型类型和主键”保存已经加载的对象。

因此，Session 不是无状态的 SQL 工具。它记住加载过哪些对象、哪些属性发生变化、当前事务处于什么阶段。

**管理逻辑事务**

Session 代表一个按顺序执行的逻辑事务：

```
try:
    order = Order(...)
    session.add(order)

    inventory.quantity -= 1

    session.commit()
except Exception:
    session.rollback()
    raise
```

订单写入和库存扣减应该一起成功或一起失败。Session 会在同一事务中按顺序执行这些操作。

这里的“一个逻辑事务”并不表示 Session 创建时立刻占用连接。Session 通常在第一次查询或写入时才从 Engine 获取连接并开始数据库事务。

**为什么 Session 不是连接池**

两者管理的对象和生命周期不同：

| 对比项           | Session              | 连接池               |
| ---------------- | -------------------- | -------------------- |
| 管理内容         | ORM 对象、变化和事务 | 数据库连接           |
| 是否记录业务状态 | 是                   | 否                   |
| 生命周期         | 通常一次请求或任务   | 通常与 Engine 一样长 |
| 是否适合全局共享 | 否                   | 由 Engine 安全协调   |
| 结束后的动作     | 提交/回滚、关闭      | 回收和复用连接       |

一个 Session 在某一时刻通常只使用一个数据库连接，但它并不是这个连接本身。Session 关闭后，连接通常会被归还池中供别的 Session 使用。

**为什么 Session 不能并发共享**

Session 是可变、有状态的对象。它内部同时保存：

1. 当前事务。
2. 当前使用的连接。
3. 待新增、待更新和待删除的对象。
4. identity map。
5. flush 过程中的中间状态。
6. 事务成功、失败或回滚状态。

假设两个请求共享同一个全局 Session：

```
session = SessionLocal()


def request_a():
    task_a = Task(title="A")
    session.add(task_a)
    session.commit()


def request_b():
    task_b = Task(title="B")
    session.add(task_b)
    session.rollback()
```

如果两者并发执行，可能出现这样的时间线：

```
请求 A：添加 task_a
请求 B：添加 task_b
请求 B：rollback
请求 A：commit
```

此时 `rollback()` 和 `commit()` 操作的是同一个 Session 与事务。请求 B 的回滚可能影响请求 A；请求 A 的提交也可能意外提交请求 B 加入的对象。

还可能发生：

1. 一个执行流正在 flush，另一个又调用 commit。
2. 一个执行流关闭 Session，另一个仍在读取对象。
3. 两个执行流同时修改 identity map。
4. 一个 SQL 失败后 Session 等待回滚，另一个仍试图查询。
5. 多条 SQL 在同一连接和事务上以无法预测的业务顺序交错。

因此，核心规则是：

```
一个并发执行流，对应一个 Session。
```

对于同步代码：

```
每个线程一个 Session
```

对于异步代码：

```
每个 asyncio Task 一个 AsyncSession
```

**FastAPI 中为什么通常“每个请求一个 Session”**

每个 HTTP 请求通常代表一次独立业务操作，因此适合作为 Session 和事务的边界：

```
from collections.abc import Generator
from typing import Annotated

from fastapi import Depends
from sqlalchemy.orm import Session


def get_session() -> Generator[Session, None, None]:
    with SessionLocal() as session:
        yield session


SessionDep = Annotated[Session, Depends(get_session)]
```

路径函数接收当前请求专属的 Session：

```
@app.post("/tasks")
def create_task(payload: TaskCreate, session: SessionDep):
    task = Task(**payload.model_dump())
    session.add(task)
    session.commit()
    session.refresh(task)
    return task
```

执行过程是：

```
请求到达
  ↓
创建 Session A
  ↓
将 Session A 注入路径函数
  ↓
执行查询、写入和事务提交
  ↓
生成响应
  ↓
关闭 Session A
```

另一个请求会得到 Session B：

```
请求 A → Session A → 事务 A
请求 B → Session B → 事务 B
请求 C → Session C → 事务 C
```

不同 Session 可以通过 Engine 的连接池安全地借用不同连接，业务状态不会混在一起。

**异步任务也要各自创建 AsyncSession**

下面的共享方式存在问题：

```
async def load_data(session: AsyncSession):
    await asyncio.gather(
        load_users(session),
        load_tasks(session),
    )
```

`load_users()` 和 `load_tasks()` 会并发使用同一个 AsyncSession。即使它们都是查询，也可能在同一连接、事务和对象状态管理器上发生冲突。

如果操作确实可以独立并行，应给每个任务独立 Session：

```
async def load_users() -> list[User]:
    async with AsyncSessionLocal() as session:
        result = await session.scalars(select(User))
        return list(result)


async def load_tasks() -> list[Task]:
    async with AsyncSessionLocal() as session:
        result = await session.scalars(select(Task))
        return list(result)


users, tasks = await asyncio.gather(
    load_users(),
    load_tasks(),
)
```

但这种写法也意味着两个任务处于两个独立事务中，不能再声称它们拥有共同的原子性。如果两项操作必须一起提交，就应该在一个 AsyncSession 中按顺序执行。

**最后形成的心智模型**

```
Engine
├── 应用级长期复用
├── 持有数据库配置
├── 协调方言和连接池
└── 可以被多个请求并发使用

Session / AsyncSession
├── 请求级或任务级
├── 保存 ORM 对象状态
├── 代表一个逻辑事务
├── 使用 Engine 获取连接
└── 不能被并发共享

连接池
├── Engine 的组成部分
├── 长期维护和复用连接
└── Session 关闭后回收连接

Dialect
├── 处理不同数据库的 SQL 差异
└── 配合驱动编译和执行语句

DBAPI 驱动
├── 建立真实连接
├── 发送 SQL
├── 接收结果
└── 执行提交与回滚
```

最关键的区别是：Engine 管理数据库基础设施，Session 管理一次有状态的业务对话。Engine 适合全局复用；Session 应限制在单个请求或单个并发任务中。

### 5.3 声明模型并创建表

下面先在一个独立脚本中完成数据库闭环。创建 `sqlalchemy_demo.py`：

```python
from datetime import datetime, timezone

from sqlalchemy import DateTime, String, create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column

# 所有数据库模型都要继承 Base
class Base(DeclarativeBase):
    pass


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100), unique=True, index=True)
    description: Mapped[str | None] = mapped_column(String(500), nullable=True)
    completed: Mapped[bool] = mapped_column(default=False)
    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True),
        default=lambda: datetime.now(timezone.utc),
    )

# SQL链接格式是：数据库类型+驱动://用户名:密码@主机:端口/数据库名
engine = create_engine("sqlite:///./demo.db", echo=True)
Base.metadata.create_all(engine)

with Session(engine) as session:
    task = Task(title="学习 SQLAlchemy", description="观察生成的 SQL")
    session.add(task)
    session.commit()
    # 刷新task对象，重新从数据库读取，获得数据库生成的 ID 等值
    session.refresh(task)
    print(f"created id={task.id}")

with Session(engine) as session:
    statement = select(Task).where(Task.completed.is_(False))
    tasks = session.scalars(statement).all()
    print([(task.id, task.title) for task in tasks])
```

运行：

```bash
python sqlalchemy_demo.py
```

成功判据有三个：当前目录出现 `demo.db`；日志包含 `CREATE TABLE`、`INSERT` 和 `SELECT`；终端输出数据库生成的任务 ID。第二次运行会因为标题唯一约束触发 `IntegrityError`，这恰好说明唯一性应由数据库最终保证，应用还要捕获、回滚并映射成 `409`。

`Base.metadata.create_all()` 会创建尚不存在的表，不会把已经存在的表安全演进到新结构，因此只适合教学、临时脚本和部分测试。正式演进使用第 9 章的 Alembic。

### 5.4 对象状态与事务时间线

```mermaid
stateDiagram-v2
    [*] --> Transient: Task(...)
    Transient --> Pending: session.add()
    Pending --> Persistent: flush() 或 commit() 发出 INSERT
    Persistent --> Expired: commit() 后默认过期
    Persistent --> Detached: close() 或 expunge()
    Expired --> Persistent: 再访问时刷新
    Detached --> Persistent: 重新关联到 Session
```

新建但未加入 Session 的对象是 transient（临时）状态；`add()` 后进入 pending（待持久化）；`flush()` 把待处理变化发送给数据库，对象获得主键后进入 persistent（持久）状态，但事务尚未提交；`commit()` 提交数据库事务；`refresh()` 重新读取当前行；Session 关闭后对象可能变成 detached（游离）状态。

`flush` 与 `commit` 的区别决定了跨表写入方式。可以先 `flush()` 获得父记录主键，再写子记录，全部成功后只 `commit()` 一次；任一步失败则 `rollback()` 整个事务。每写一行就提交会破坏用例的原子性。





这张状态图描述了 SQLAlchemy ORM（Object-Relational Mapping，对象关系映射）对象从创建、加入 Session、写入数据库到离开 Session 的生命周期。

**Transient：临时状态**

```python
task = Task(title="学习 SQLAlchemy")
```

此时，`task` 只是一个普通的 Python 对象：

1\. 尚未加入 Session。

2\. 尚未执行 `INSERT`。

3\. 数据库中还没有这条记录。

4\. SQLAlchemy 不会自动保存它。

```python
print(task.id)  # None
```

这种状态称为 transient（临时状态）。

**Pending：待持久化状态**

```python
session.add(task)
```

执行 `add()` 后，对象进入 pending（待持久化）状态。

SQLAlchemy 已经知道需要保存该对象，但通常还没有执行 `INSERT`：

```python
session.add(task)

print(task.id)  # 通常仍然是 None
```

可以把 Session 理解为一张待办清单，`add()` 只是把对象登记到待办清单中。

**Persistent：持久化状态**

```python
session.flush()
```

`flush()` 会把 Session 中积累的变化发送给数据库，例如：

```sql
INSERT INTO tasks (title, completed)
VALUES ('学习 SQLAlchemy', false);
```

数据库生成主键后，SQLAlchemy 会把主键写回对象：

```python
session.add(task)
session.flush()

print(task.id)  # 例如 1
```

此时，`task` 进入 persistent（持久化状态）：

1\. `INSERT` 已经执行。

2\. 对象已经获得数据库主键。

3\. Session 正在跟踪这个对象。

4\. 当前事务还没有提交。

虽然数据库已经执行了 `INSERT`，但其他事务通常还看不到这条数据。如果当前事务回滚，这条记录仍会被撤销。

**flush 与 commit 的区别**

`flush()` 把内存中的变化同步到数据库，但不会提交事务：

```python
task = Task(title="学习 SQLAlchemy")

session.add(task)
session.flush()

print(task.id)  # 已经获得主键

session.rollback()
```

执行 `rollback()` 后，这条数据不会正式保存在数据库中。

`commit()` 通常包含两个动作：

```text
先自动执行 flush()
再执行数据库 COMMIT
```

因此，即使没有手动调用 `flush()`，下面的代码也会执行 `INSERT`：

```python
session.add(task)
session.commit()
```

可以这样记忆：

```text
flush：把 SQL 发送给数据库，但事务还可以回滚
commit：提交整个事务，使数据正式生效
```

**为什么需要先 flush 再 commit**

假设需要先创建用户，再使用数据库生成的用户 ID 创建任务：

```python
try:
    user = User(username="张三")
    session.add(user)

    # 执行 INSERT，获得数据库生成的 user.id
    session.flush()

    task = Task(
        title="学习 SQLAlchemy",
        owner_id=user.id,
    )
    session.add(task)

    # 用户和任务一起提交
    session.commit()
except Exception:
    # 任一步失败，都撤销当前事务的全部修改
    session.rollback()
    raise
```

执行过程如下：

```text
创建 User 对象
    ↓
session.add(user)
    ↓
session.flush()
    ↓
数据库执行 INSERT
    ↓
获得 user.id
    ↓
使用 user.id 创建 Task
    ↓
session.commit()
    ↓
用户和任务一起提交
```

如果创建任务失败，`rollback()` 会同时撤销用户和任务的数据库操作，避免产生不完整数据。

**Expired：过期状态**

默认情况下，执行：

```python
session.commit()
```

SQLAlchemy 会把 Session 中对象的属性标记为 expired（过期）。

过期不表示对象被删除，而是表示对象中的值可能已经不是数据库的最新值。下次访问属性时，SQLAlchemy 会重新查询数据库：

```python
session.commit()

print(task.title)  # 可能触发 SELECT
```

如果 Session 工厂使用以下配置：

```python
SessionLocal = sessionmaker(
    bind=engine,
    expire_on_commit=False,
)
```

那么 `commit()` 后不会自动将对象标记为过期。

因此，下面的状态变化只适用于默认配置 `expire_on_commit=True`：

```text
Persistent → Expired
```

**refresh：立即重新读取数据库**

```python
session.refresh(task)
```

`refresh()` 会立即执行查询，从数据库重新加载对象：

```sql
SELECT *
FROM tasks
WHERE id = 1;
```

它通常用于取得数据库生成的主键、时间或默认值：

```python
session.add(task)
session.commit()
session.refresh(task)

print(task.id)
print(task.created_at)
```

`expired` 与 `refresh()` 的区别是：

```text
expired：先标记过期，访问属性时再自动查询
refresh：立即查询数据库并更新对象
```

**Detached：游离状态**

Session 关闭后：

```python
session.close()
```

原来由 Session 管理的对象会变成 detached（游离状态）。

此时：

1\. Python 对象仍然存在。

2\. 数据库记录仍然存在。

3\. 对象不再受 Session 跟踪。

4\. 对象无法正常触发需要访问数据库的延迟加载。

例如：

```python
with Session(engine) as session:
    task = session.get(Task, 1)

# Session 已关闭
print(task.title)
```

如果 `title` 已经加载，通常仍然可以读取。

如果访问尚未加载的关联属性：

```python
print(task.owner)
```

可能出现：

```text
DetachedInstanceError
```

原因是 SQLAlchemy 需要查询数据库，但该对象已经没有可用的 Session。

**重新关联游离对象**

可以使用 `merge()` 把游离对象的数据合并到当前 Session：

```python
with Session(engine) as session:
    managed_task = session.merge(task)
    print(managed_task.title)
```

`merge()` 返回受当前 Session 管理的对象。后续通常应使用返回的 `managed_task`，而不是原来的 `task`。

**完整状态变化**

```python
task = Task(title="学习 SQLAlchemy")  # Transient

session.add(task)                     # Pending

session.flush()                       # Persistent，已执行 INSERT
print(task.id)                        # 已获得主键

session.commit()                      # 提交事务，默认进入 Expired

session.refresh(task)                 # 重新读取数据库

session.close()                       # Detached
```

完整流程可以表示为：

```text
创建对象
    ↓ Transient
加入 Session
    ↓ Pending
执行 flush
    ↓ Persistent
执行 commit
    ↓ 默认进入 Expired
访问属性或执行 refresh
    ↓ Persistent
关闭 Session
    ↓ Detached
```

常用操作的含义：

| 操作         | 作用                               |
| ------------ | ---------------------------------- |
| `add()`      | 将对象登记为待保存状态             |
| `flush()`    | 执行 SQL，但不提交事务             |
| `commit()`   | 提交整个事务                       |
| `refresh()`  | 立即从数据库重新读取对象           |
| `rollback()` | 撤销当前未提交事务                 |
| `close()`    | 关闭 Session，对象可能变成游离状态 |

### 5.5 CRUD 的 SQLAlchemy 2.x 写法

```python
from sqlalchemy import select
from sqlalchemy.orm import Session


def create_task(session: Session, title: str) -> Task:
    task = Task(title=title)
    session.add(task)
    session.flush()
    return task


def get_task(session: Session, task_id: int) -> Task | None:
    return session.get(Task, task_id)


def list_tasks(session: Session, offset: int, limit: int) -> list[Task]:
    statement = select(Task).order_by(Task.id).offset(offset).limit(limit)
    return list(session.scalars(statement))


def complete_task(session: Session, task: Task) -> None:
    task.completed = True


def delete_task(session: Session, task: Task) -> None:
    session.delete(task)
```

`session.get()` 针对主键查询，并可利用 Session 的 identity map（身份映射）；`select()` 表达一般查询；`session.scalars()` 直接取得 ORM 实体序列。修改已跟踪对象的属性后，工作单元会在 flush 时生成 `UPDATE`。删除操作同理，真正的 `DELETE` 通常在 flush 或 commit 时发出。

函数故意不在内部提交，使调用者能把多次写入组合成一个业务事务。应用服务或路径操作在整个用例成功后提交；捕获数据库异常时先回滚，再继续使用同一个 Session。

5.5 展示的是 SQLAlchemy 2.x 中常见的 CRUD（Create、Read、Update、Delete，增删改查）写法。

```python
from sqlalchemy import select
from sqlalchemy.orm import Session


def create_task(session: Session, title: str) -> Task:
    task = Task(title=title)
    session.add(task)
    session.flush()
    return task


def get_task(session: Session, task_id: int) -> Task | None:
    return session.get(Task, task_id)


def list_tasks(session: Session, offset: int, limit: int) -> list[Task]:
    statement = select(Task).order_by(Task.id).offset(offset).limit(limit)
    return list(session.scalars(statement))


def complete_task(session: Session, task: Task) -> None:
    task.completed = True


def delete_task(session: Session, task: Task) -> None:
    session.delete(task)
```

`Task` 是前面定义的 ORM（Object-Relational Mapping，对象关系映射）模型，`session` 是当前数据库会话。

**新增任务**

```python
def create_task(session: Session, title: str) -> Task:
    task = Task(title=title)
    session.add(task)
    session.flush()
    return task
```

`Task(title=title)` 创建一个 Python 对象：

```python
task = Task(title="学习 SQLAlchemy")
```

此时对象只存在于内存中，数据库还没有对应记录。

`session.add(task)` 将对象加入 Session：

```python
session.add(task)
```

此时 SQLAlchemy 知道需要保存该对象，但通常还没有执行 `INSERT`。

`session.flush()` 将待处理的变化发送给数据库：

```python
session.flush()
```

大致会执行：

```sql
INSERT INTO tasks (title, completed)
VALUES ('学习 SQLAlchemy', false);
```

执行 `flush()` 后，通常可以获得数据库生成的主键：

```python
print(task.id)  # 例如 1
```

但 `flush()` 不会提交事务。调用者仍需执行 `commit()`：

```python
try:
    task = create_task(session, "学习 SQLAlchemy")
    session.commit()
except Exception:
    session.rollback()
    raise
```

执行过程如下：

```text
创建 Task 对象
    ↓
session.add()
    ↓
进入待保存状态
    ↓
session.flush()
    ↓
执行 INSERT 并获得主键
    ↓
session.commit()
    ↓
正式提交事务
```

**根据主键查询任务**

```python
def get_task(session: Session, task_id: int) -> Task | None:
    return session.get(Task, task_id)
```

`session.get()` 专门用于根据主键查询：

```python
task = session.get(Task, 1)
```

大致对应：

```sql
SELECT *
FROM tasks
WHERE id = 1;
```

返回值存在两种情况：

```text
找到记录：返回 Task 对象
没有找到：返回 None
```

因此返回类型声明为：

```python
Task | None
```

调用时需要判断任务是否存在：

```python
task = get_task(session, 1)

if task is None:
    print("任务不存在")
else:
    print(task.title)
```

`session.get()` 还会检查 Session 的 identity map（身份映射）。

如果当前 Session 已经加载过这个主键对应的对象，SQLAlchemy 可能直接返回已有的 Python 对象，而不再次查询数据库。

**分页查询任务**

```python
def list_tasks(
    session: Session,
    offset: int,
    limit: int,
) -> list[Task]:
    statement = (
        select(Task)
        .order_by(Task.id)
        .offset(offset)
        .limit(limit)
    )
    return list(session.scalars(statement))
```

查询语句可以拆开理解：

```python
select(Task)       # 查询 Task 对应的记录
order_by(Task.id)  # 按任务 ID 排序
offset(offset)     # 跳过指定数量的记录
limit(limit)       # 限制最多返回多少条记录
```

例如：

```python
tasks = list_tasks(
    session=session,
    offset=20,
    limit=10,
)
```

大致对应：

```sql
SELECT *
FROM tasks
ORDER BY id
LIMIT 10 OFFSET 20;
```

它表示跳过前 20 条记录，再读取最多 10 条记录。

分页查询需要稳定排序。如果没有：

```python
order_by(Task.id)
```

数据库不保证每次都按照相同顺序返回记录，可能导致分页结果重复或遗漏。

`session.scalars(statement)` 负责执行查询，并从每一行中提取 `Task` 对象：

```python
result = session.scalars(statement)
```

再通过 `list()` 转换成列表：

```python
tasks = list(result)
```

结果类似：

```python
[
    Task(id=21, title="任务 21"),
    Task(id=22, title="任务 22"),
]
```

也可以使用：

```python
tasks = session.scalars(statement).all()
```

**修改任务**

```python
def complete_task(session: Session, task: Task) -> None:
    task.completed = True
```

对于已被 Session 管理的对象，直接修改属性即可：

```python
task.completed = True
```

SQLAlchemy 会记录属性变化，在下一次 `flush()` 或 `commit()` 时生成 `UPDATE`：

```sql
UPDATE tasks
SET completed = true
WHERE id = 1;
```

完整调用方式：

```python
task = get_task(session, 1)

if task is not None:
    complete_task(session, task)
    session.commit()
```

如果提交失败，应回滚事务：

```python
try:
    complete_task(session, task)
    session.commit()
except Exception:
    session.rollback()
    raise
```

**删除任务**

```python
def delete_task(session: Session, task: Task) -> None:
    session.delete(task)
```

`session.delete(task)` 将对象标记为待删除状态。

真正的 `DELETE` 通常在 `flush()` 或 `commit()` 时执行：

```sql
DELETE FROM tasks
WHERE id = 1;
```

完整调用方式：

```python
task = get_task(session, 1)

if task is not None:
    delete_task(session, task)
    session.commit()
```

如果删除过程失败，可以回滚：

```python
try:
    delete_task(session, task)
    session.commit()
except Exception:
    session.rollback()
    raise
```

**为什么这些函数内部没有 commit**

这些函数故意不调用 `commit()`，目的是让上层代码决定事务边界。

例如，需要同时创建两个任务：

```python
try:
    first_task = create_task(session, "学习 FastAPI")
    second_task = create_task(session, "学习 SQLAlchemy")

    session.commit()
except Exception:
    session.rollback()
    raise
```

执行结果有两种：

```text
两个任务都创建成功
    ↓
commit()
    ↓
两个任务一起提交

第二个任务创建失败
    ↓
rollback()
    ↓
两个任务都不保存
```

如果 `create_task()` 每次都执行 `commit()`，第一个任务可能已经提交，而第二个任务创建失败，最终产生不完整的数据。

各个操作的对应关系如下：

| CRUD 操作 | SQLAlchemy 写法                   | 对应 SQL 或事务操作       |
| --------- | --------------------------------- | ------------------------- |
| 新增      | `session.add()`                   | `INSERT`                  |
| 主键查询  | `session.get()`                   | `SELECT ... WHERE id = ?` |
| 条件查询  | `select()` 与 `session.scalars()` | `SELECT`                  |
| 修改      | 修改 ORM 对象属性                 | `UPDATE`                  |
| 删除      | `session.delete()`                | `DELETE`                  |
| 同步变化  | `session.flush()`                 | 执行待处理的 SQL          |
| 正式提交  | `session.commit()`                | `COMMIT`                  |
| 撤销修改  | `session.rollback()`              | `ROLLBACK`                |

可以简化记忆为：

```text
add()：登记新增对象
get()：根据主键查询
select()：构造查询条件
修改属性：登记更新
delete()：登记删除
flush()：执行 SQL，但不提交
commit()：提交整个事务
rollback()：撤销当前事务
```

## 6 组装可运行的任务管理 API

### 6.1 工程目录与依赖方向

把教学代码整理成下面的结构：

```text
.
├── app
│   ├── __init__.py
│   ├── bootstrap.py
│   ├── database.py
│   ├── dependencies.py
│   ├── main.py
│   ├── models.py
│   ├── routers
│   │   ├── __init__.py
│   │   └── tasks.py
│   └── schemas.py
└── tests
    └── test_tasks.py
```

`routers` 处理 HTTP 适配，`schemas` 定义输入输出契约，`models` 定义持久化映射，`database` 创建 Engine 与 Session 工厂，`dependencies` 管理请求级资源。规模变大后可以增加 service 层承载业务用例、repository 层封装复杂持久化查询；若当前只有简单 CRUD，过早增加转发层只会提高导航成本。

### 6.2 集中管理配置和数据库入口

`app/database.py`：

```python
from functools import lru_cache

from pydantic_settings import BaseSettings, SettingsConfigDict
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, sessionmaker


class Settings(BaseSettings):
    model_config = SettingsConfigDict(
        env_file=".env",
        env_prefix="APP_",
      	# 忽略配置类中没有声明的额外字段
        extra="ignore",
    )

    database_url: str = "sqlite:///./tasks.db"


@lru_cache
def get_settings() -> Settings:
    return Settings()


class Base(DeclarativeBase):
    pass


settings = get_settings()
sqlite_connect_args = (
  	# 检查连接只能由创建它的线程使用
    {"check_same_thread": False}
    if settings.database_url.startswith("sqlite")
    else {}
)
engine = create_engine(
    settings.database_url,
    connect_args=sqlite_connect_args,
    pool_pre_ping=True,
)
SessionLocal = sessionmaker(
    bind=engine,
    autoflush=False,
    expire_on_commit=False,
)
```

环境变量 `APP_DATABASE_URL` 可以覆盖默认值，例如生产使用 PostgreSQL 时不必修改代码。URL（Uniform Resource Locator，统一资源定位符）在这里描述数据库方言、驱动和连接位置。`.env` 适合本地开发，生产密钥通常由部署平台的 Secret 机制注入；`.env` 不应提交真实口令。

SQLite 驱动默认存在同线程检查。FastAPI 的同步依赖和同步路径函数可能由线程池调度，因此示例显式设置 `check_same_thread=False`；这只放宽驱动检查，不会让同一个 Session 变得线程安全。每个请求仍使用独立 Session。

`pool_pre_ping=True` 在从池中取出连接时检查可用性，有助于识别服务端已经断开的陈旧连接，但会增加一次探测成本，且无法挽救执行中断开的事务。连接池参数需要结合数据库连接上限与实例数计算，第 14.3 节会展开。

### 6.3 定义 SQLAlchemy 模型

`app/models.py`：

```python
from datetime import datetime, timezone

from sqlalchemy import DateTime, String
from sqlalchemy.orm import Mapped, mapped_column

from app.database import Base


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(
        String(100),
        unique=True,
        index=True,
    )
    description: Mapped[str | None] = mapped_column(
        String(500),
        nullable=True,
    )
    completed: Mapped[bool] = mapped_column(default=False)
    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True),
        default=lambda: datetime.now(timezone.utc),
    )
```

Python 类型标注表达应用期望，数据库列约束承担最终的数据完整性。`unique=True` 防止并发请求绕过“先查再写”的应用检查；`nullable=True` 明确描述可空语义；时间使用带时区的 UTC（Coordinated Universal Time，协调世界时），展示时再转换到用户时区。

### 6.4 分离 创建、局部更新和读取模型

`app/schemas.py`：

```python
from datetime import datetime
from typing import Self

from pydantic import BaseModel, ConfigDict, Field, model_validator


class TaskCreate(BaseModel):
    model_config = ConfigDict(extra="forbid")

    title: str = Field(min_length=1, max_length=100)
    description: str | None = Field(default=None, max_length=500)


class TaskUpdate(BaseModel):
    model_config = ConfigDict(extra="forbid")

    title: str | None = Field(default=None, min_length=1, max_length=100)
    description: str | None = Field(default=None, max_length=500)
    completed: bool | None = None

    # title 和 completed 可以不传，但不能显式传 null
    @model_validator(mode="after")
    def reject_null_for_required_columns(self) -> Self:
        for field_name in ("title", "completed"):
            if (
                 # self.model_fields_set 保存的是“客户端实际传入了哪些字段”
                field_name in self.model_fields_set
                and getattr(self, field_name) is None
            ):
                raise ValueError(f"{field_name} cannot be null")
        return self


class TaskRead(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: int
    title: str
    description: str | None
    completed: bool
    created_at: datetime
```

局部更新必须区分三个状态：“字段未提供”表示保持原值；`description: null` 表示清空描述；`title: null` 与数据库非空约束冲突，应在请求边界拒绝。`model_fields_set` 记录客户端实际提供的字段，后面的 `exclude_unset=True` 据此生成变更集。

`from_attributes=True` 允许 Pydantic 从 SQLAlchemy 对象属性读取值，这是 Pydantic 2 替代旧式 `orm_mode` 的配置。它只负责取值和序列化，不会自动解决已关闭 Session 上的延迟加载关系。



这两段分别解决两个问题：PATCH 局部更新如何判断“用户想改什么”，以及如何把 SQLAlchemy 对象转换成接口响应。

**1. “未提供”和 `null` 不是一回事**

假设数据库中已有任务：

```json
{
  "title": "学习 FastAPI",
  "description": "阅读官方文档",
  "completed": false
}
```

客户端发送 PATCH 请求时，可能出现以下情况：

| 请求体                  | 客户端意图                   |
| ----------------------- | ---------------------------- |
| `{}`                    | 什么都不修改                 |
| `{"description": null}` | 清空描述                     |
| `{"title": null}`       | 把标题设为空，但数据库不允许 |
| `{"completed": false}`  | 明确把完成状态改为 `false`   |

其中最容易混淆的是：

```json
{}
```

和：

```json
{"description": null}
```

两者进入 Pydantic 模型后，`description` 的值都可能表现为 `None`，但客户端意图完全不同。

**2. `model_fields_set` 记录客户端实际提交了哪些字段**

例如：

```python
empty_update = TaskUpdate()
print(empty_update.description)       # None
print(empty_update.model_fields_set)  # set()
```

虽然 `description` 的值是 `None`，但 `model_fields_set` 是空集合，说明客户端没有提供这个字段。

再看显式传入 `null`：

```python
clear_description = TaskUpdate(description=None)

print(clear_description.description)       # None
print(clear_description.model_fields_set)  # {"description"}
```

这一次 `model_fields_set` 中包含 `description`，说明客户端明确要求修改它。

因此可以得到下面的判断：

```text
值是 None + 不在 model_fields_set 中 = 字段未提供
值是 None + 在 model_fields_set 中   = 客户端显式提交 null
```

**3. `exclude_unset=True` 只生成客户端提供的字段**

局部更新代码通常写成：

```python
changes = payload.model_dump(exclude_unset=True)
```

不同请求得到的结果如下：

```python
TaskUpdate().model_dump(exclude_unset=True)
# {}

TaskUpdate(description=None).model_dump(exclude_unset=True)
# {"description": None}

TaskUpdate(completed=False).model_dump(exclude_unset=True)
# {"completed": False}
```

然后只更新这些字段：

```python
for field_name, value in changes.items():
    setattr(task, field_name, value)
```

所以：

- 没有提供的字段保持数据库原值。
- 显式提交的 `null` 会被写成数据库 `NULL`。
- `false`、`0` 和空字符串不会因为是假值而被漏掉。

这里不能改用：

```python
payload.model_dump(exclude_none=True)
```

因为它会把 `description=None` 删除，导致客户端无法清空描述。

**4. 为什么 `title: null` 要单独拒绝**

数据库模型通常这样声明标题：

```python
title: Mapped[str] = mapped_column(nullable=False)
```

这表示数据库列不接受 `NULL`。

但用于 PATCH 的 Pydantic 字段可能写成：

```python
title: str | None = None
```

这样才能用默认 `None` 表示“未提供”，同时却也会允许客户端显式发送 `null`。因此示例通过 `model_fields_set` 进一步区分：

```python
@model_validator(mode="after")
def reject_null_for_required_columns(self) -> Self:
    if "title" in self.model_fields_set and self.title is None:
        raise ValueError("title cannot be null")
    return self
```

对应行为是：

```text
{}                  → title 未提供，允许，保持原值
{"title": "新标题"} → 修改标题
{"title": null}     → 客户端明确提交空值，拒绝
```

这样可以在请求进入数据库之前返回校验错误，而不是等数据库抛出非空约束异常。

---

**5. `from_attributes=True` 解决什么问题**

Pydantic 通常从字典读取数据：

```python
TaskRead.model_validate(
    {
        "id": 1,
        "title": "学习 FastAPI",
        "completed": False,
    }
)
```

但是 SQLAlchemy 查询返回的是 ORM（Object-Relational Mapping，对象关系映射）对象：

```python
task = session.get(Task, 1)

print(task.id)
print(task.title)
print(task.completed)
```

这些数据位于对象属性中，不是字典键。配置：

```python
class TaskRead(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: int
    title: str
    completed: bool
```

之后，Pydantic 可以读取 SQLAlchemy 对象的同名属性：

```python
response = TaskRead.model_validate(task)
```

它的读取过程类似：

```python
response.id = task.id
response.title = task.title
response.completed = task.completed
```

在 FastAPI 中设置了响应模型后，这个转换通常由框架完成：

```python
@router.get("/{task_id}", response_model=TaskRead)
def read_task(task_id: int, session: SessionDep) -> Task:
    return session.get(Task, task_id)
```

路径函数返回 SQLAlchemy 的 `Task`，FastAPI 再通过 `TaskRead` 读取并过滤字段。

**6. 为什么它解决不了 Session 已关闭后的延迟加载**

假设 `Task` 有一个关联属性：

```python
class Task(Base):
    owner: Mapped[User] = relationship()
```

SQLAlchemy 可能不会在查询任务时立即查询 `owner`。只有代码第一次访问：

```python
task.owner
```

它才向数据库发送额外的 SQL，这叫 lazy loading（延迟加载）。

`from_attributes=True` 在转换响应时会读取属性：

```python
owner = task.owner
```

如果此时 Session 已经关闭，SQLAlchemy 无法再查询数据库，可能抛出 `DetachedInstanceError`。异步环境中，未显式等待的延迟查询还可能产生 `MissingGreenlet`。

`from_attributes=True` 的能力边界是：

```text
读取SQLAlchemy对象属性并构建 Pydantic 模型
```

它不负责：

```text
维持数据库 Session
自动加载关联数据
解决 N+1 查询
处理异步数据库 I/O
```

需要返回关联数据时，应在 Session 有效期间显式加载：

```python
statement = (
    select(Task)
    .options(selectinload(Task.owner))
    .where(Task.id == task_id)
)

task = session.scalar(statement)
```

这样 Pydantic 读取 `task.owner` 时，相关数据已经加载完成。

**7. 完整执行过程**

一次 PATCH 请求的处理链可以概括为：

```text
客户端 JSON
    ↓
TaskUpdate 校验
    ↓
model_fields_set 判断实际提供的字段
    ↓
model_dump(exclude_unset=True) 生成变更集
    ↓
setattr 修改 SQLAlchemy 对象
    ↓
Session 提交事务
    ↓
TaskRead 通过 from_attributes=True 读取对象属性
    ↓
返回 JSON
```

简化理解：

- `model_fields_set` 和 `exclude_unset=True` 负责“只修改客户端明确提交的字段”。
- `from_attributes=True` 负责“把 SQLAlchemy 对象转换成响应模型”。
- Session 和加载策略负责“确保转换时需要的数据已经存在”。

### 6.5 创建请求级 Session 依赖

`app/dependencies.py`：

```python
from collections.abc import Generator
from typing import Annotated

from fastapi import Depends
from sqlalchemy.orm import Session

from app.database import SessionLocal


def get_session() -> Generator[Session, None, None]:
    with SessionLocal() as session:
        yield session


SessionDep = Annotated[Session, Depends(get_session)]
```

Session 工厂是应用级对象，Session 实例是请求级对象。上下文退出会关闭 Session，并把连接归还池中；关闭不等于提交。提交和回滚留在业务操作可见的位置，便于判断一个用例的原子边界。

这段代码的作用是：为每一次 HTTP 请求创建一个独立的 SQLAlchemy `Session`，交给接口使用，并在请求结束后自动关闭。

```python
from collections.abc import Generator
from typing import Annotated

from fastapi import Depends
from sqlalchemy.orm import Session

from app.database import SessionLocal


def get_session() -> Generator[Session, None, None]:
    with SessionLocal() as session:
        yield session


SessionDep = Annotated[Session, Depends(get_session)]
```

**`SessionLocal` 和 `Session` 的区别**

假设 `app/database.py` 中已经定义：

```python
SessionLocal = sessionmaker(bind=engine)
```

| 对象           | 含义                               | 生命周期 |
| -------------- | ---------------------------------- | -------- |
| `engine`       | 数据库连接入口，内部通常管理连接池 | 应用级   |
| `SessionLocal` | Session 工厂，用于创建 Session     | 应用级   |
| `session`      | 一次具体的数据库会话               | 请求级   |

调用 `SessionLocal()` 才会创建一个具体的 Session：

```python
session = SessionLocal()
```

可以把 `SessionLocal` 理解成会话工厂，把 `session` 理解成本次请求使用的数据库操作上下文。

**`Generator[Session, None, None]` 的含义**

```python
def get_session() -> Generator[Session, None, None]:
```

`get_session()` 使用了 `yield`，因此它是生成器函数。

`Generator` 的三个类型参数依次表示：

```python
Generator[产出的值, 接收的值, 最终返回值]
```

因此：

```python
Generator[Session, None, None]
```

表示：

1. 生成器通过 `yield` 产出一个 `Session`。
2. 外部不会通过 `send()` 向生成器传值。
3. 生成器结束时不返回额外结果。

这个类型标注主要用于编辑器提示和静态类型检查，不会改变运行逻辑。

**`with SessionLocal()` 的作用**

```python
with SessionLocal() as session:
    yield session
```

`Session` 是上下文管理器。进入 `with` 时获得 Session，退出 `with` 时自动关闭 Session。

它近似等价于：

```python
def get_session():
    session = SessionLocal()
    try:
        yield session
    finally:
        session.close()
```

即使接口执行过程中发生异常，`finally` 中的清理逻辑仍然会执行，从而避免 Session 和数据库连接长期占用。

**为什么使用 `yield` 而不是 `return`**

如果写成：

```python
def get_session() -> Session:
    return SessionLocal()
```

FastAPI 可以获得 Session，但是依赖函数没有机会在请求结束后执行清理逻辑。

使用 `yield` 后，依赖函数被分成两个阶段：

```python
def get_session():
    with SessionLocal() as session:
        # 请求处理前
        yield session
        # 请求处理后
```

运行过程如下：

```text
收到请求
    ↓
调用 get_session()
    ↓
创建 Session
    ↓
yield session
    ↓
将 Session 注入路径操作函数
    ↓
路径操作函数访问数据库
    ↓
路径操作函数执行结束
    ↓
恢复 get_session()
    ↓
退出 with
    ↓
关闭 Session并归还连接
```

**`Depends(get_session)` 的作用**

```python
Depends(get_session)
```

这是 FastAPI 的依赖注入声明，含义是：

> 当前参数需要一个数据库 Session，请调用 `get_session` 获取。

这里传入的是函数本身：

```python
Depends(get_session)
```

不能提前调用函数：

```python
Depends(get_session())  # 错误写法
```

FastAPI 会在处理请求时调用 `get_session`，并管理 `yield` 前后的生命周期。

**`Annotated` 的作用**

```python
SessionDep = Annotated[Session, Depends(get_session)]
```

`Annotated` 同时保存两类信息：

1. `Session`：告诉编辑器和类型检查器，该参数是 SQLAlchemy Session。
2. `Depends(get_session)`：告诉 FastAPI，该参数通过依赖注入获得。

定义 `SessionDep` 后，接口可以写成：

```python
@router.get("/tasks")
def list_tasks(session: SessionDep):
    return session.query(Task).all()
```

它等价于：

```python
@router.get("/tasks")
def list_tasks(
    session: Session = Depends(get_session),
):
    return session.query(Task).all()
```

使用 `SessionDep` 可以减少重复代码，同时保留完整的类型提示。

**一次请求中的执行过程**

例如：

```python
@router.post("/tasks")
def create_task(
    payload: TaskCreate,
    session: SessionDep,
):
    task = Task(title=payload.title)
    session.add(task)
    session.commit()
    session.refresh(task)
    return task
```

实际执行顺序如下：

```mermaid
sequenceDiagram
    participant Client as 客户端
    participant FastAPI
    participant Dependency as get_session
    participant API as create_task
    participant DB as 数据库

    Client->>FastAPI: POST /tasks
    FastAPI->>Dependency: 调用 get_session()
    Dependency->>Dependency: 创建 Session
    Dependency-->>FastAPI: yield session
    FastAPI->>API: 注入 session
    API->>DB: INSERT
    API->>DB: COMMIT
    API-->>FastAPI: 返回 Task
    FastAPI->>Dependency: 恢复生成器
    Dependency->>Dependency: 退出 with，关闭 Session
    FastAPI-->>Client: 返回 HTTP 响应
```

每个并发请求都会获得独立的 Session：

```text
请求 A → Session A
请求 B → Session B
请求 C → Session C
```

SQLAlchemy Session 是有状态的事务对象，不应该让多个并发请求共享同一个全局 Session。

**关闭 Session 不等于提交事务**

下面的代码不会可靠地保存数据：

```python
def create_task(session: SessionDep):
    session.add(Task(title="学习 FastAPI"))
    # 没有调用 session.commit()
```

常见的写入流程是：

```python
session.add(task)
session.commit()
session.refresh(task)
```

| 操作         | 作用                                    |
| ------------ | --------------------------------------- |
| `add()`      | 将对象加入 Session 的待处理集合         |
| `flush()`    | 将 SQL 发送给数据库，但通常尚未提交事务 |
| `commit()`   | 提交当前事务                            |
| `refresh()`  | 从数据库重新读取对象                    |
| `rollback()` | 撤销当前未提交的事务                    |
| `close()`    | 释放 Session 持有的连接和其他资源       |

如果没有提交，Session 关闭时，未完成的事务通常会被回滚，数据库连接随后归还连接池。

**为什么通常不在依赖中自动提交**

可以把提交写进依赖：

```python
def get_session():
    with SessionLocal() as session:
        try:
            yield session
            session.commit()
        except Exception:
            session.rollback()
            raise
```

这种写法可以统一事务处理，但会把提交行为隐藏在依赖清理阶段。路径操作函数返回时，数据库提交可能还没有成功；如果提交失败，错误发生的位置也不够直观。

需要明确业务事务边界时，可以让依赖只管理 Session 生命周期：

```python
def get_session():
    with SessionLocal() as session:
        yield session
```

然后在完整业务操作中显式处理提交与回滚：

```python
try:
    session.add(task)
    session.commit()
except Exception:
    session.rollback()
    raise
```

这样可以清楚地判断哪些数据库操作属于同一个事务。例如“创建订单并扣减库存”应该全部成功后只提交一次，其中任何一步失败都应回滚。

**核心关系**

```text
Engine
  ↓
SessionLocal（Session 工厂）
  ↓
get_session（生命周期管理）
  ↓
yield session
  ↓
Depends（FastAPI 依赖注入）
  ↓
路径操作函数使用 Session
  ↓
关闭 Session并归还连接
```

可以记住以下原则：

1. 应用长期复用 `Engine` 和 `SessionLocal`。
2. 每个请求单独创建一个 Session。
3. 业务代码显式执行 `commit()` 或 `rollback()`。
4. 请求结束后自动执行 `close()`。
5. Session 不应在多个线程、请求或异步任务之间并发共享。

### 6.6 实现完整 CRUD 路由

`app/routers/tasks.py`：

```python
from fastapi import APIRouter, HTTPException, Query, Response, status
from sqlalchemy import select
from sqlalchemy.exc import IntegrityError

from app.dependencies import SessionDep
from app.models import Task
from app.schemas import TaskCreate, TaskRead, TaskUpdate

router = APIRouter(prefix="/tasks", tags=["tasks"])


def get_task_or_404(task_id: int, session: SessionDep) -> Task:
    task = session.get(Task, task_id)
    if task is None:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Task not found",
        )
    return task


@router.post(
    "",
    response_model=TaskRead,
    status_code=status.HTTP_201_CREATED,
)
def create_task(payload: TaskCreate, session: SessionDep) -> Task:
    task = Task(**payload.model_dump())
    session.add(task)
    try:
        session.commit()
    except IntegrityError as exc:
        session.rollback()
        raise HTTPException(
            status_code=status.HTTP_409_CONFLICT,
            detail="Task title already exists",
        ) from exc
    session.refresh(task)
    return task


@router.get("", response_model=list[TaskRead])
def list_tasks(
    session: SessionDep,
    offset: int = Query(default=0, ge=0),
    limit: int = Query(default=20, ge=1, le=100),
) -> list[Task]:
    statement = select(Task).order_by(Task.id).offset(offset).limit(limit)
    return list(session.scalars(statement))


@router.get("/{task_id}", response_model=TaskRead)
def read_task(task_id: int, session: SessionDep) -> Task:
    return get_task_or_404(task_id, session)


@router.patch("/{task_id}", response_model=TaskRead)
def update_task(
    task_id: int,
    payload: TaskUpdate,
    session: SessionDep,
) -> Task:
    task = get_task_or_404(task_id, session)
    changes = payload.model_dump(exclude_unset=True)
    for field_name, value in changes.items():
        setattr(task, field_name, value)

    try:
        session.commit()
    except IntegrityError as exc:
        session.rollback()
        raise HTTPException(
            status_code=status.HTTP_409_CONFLICT,
            detail="Task title already exists",
        ) from exc
    session.refresh(task)
    return task


@router.delete(
    "/{task_id}",
    status_code=status.HTTP_204_NO_CONTENT,
)
def delete_task(task_id: int, session: SessionDep) -> Response:
    task = get_task_or_404(task_id, session)
    session.delete(task)
    session.commit()
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```

路由使用同步 `def`，因为示例采用同步 SQLite 驱动和同步 Session。FastAPI 会在线程池中执行它调用的同步路径函数。`limit` 上限防止一次请求无界加载数据；唯一约束异常先回滚再转换为 `409`；删除成功返回 `204 No Content`，响应体应为空。

在更复杂的系统中，不应把所有 `IntegrityError` 都解释成标题冲突。可以检查约束名或在数据访问层转换成更精确的领域异常，否则外键失败、非空失败也会被错误地报告为重复标题。

### 6.7 注册路由并初始化学习数据库

`app/main.py`：

```python
from fastapi import FastAPI

from app.routers.tasks import router as tasks_router

app = FastAPI(title="任务管理 API", version="1.0.0")
app.include_router(tasks_router)


@app.get("/health", tags=["system"])
def health() -> dict[str, str]:
    return {"status": "ok"}
```

`app/bootstrap.py`：

```python
from app import models
from app.database import Base, engine


def main() -> None:
    # 导入 models 后，Task 才会登记到 Base.metadata。
    _ = models.Task
    Base.metadata.create_all(bind=engine)


if __name__ == "__main__":
    main()
```

创建空的 `app/__init__.py`、`app/routers/__init__.py` 后执行：

```bash
python -m app.bootstrap
fastapi dev --entrypoint app.main:app
```

`bootstrap` 是学习阶段的一次性建表入口。第 9 章接入 Alembic 后，部署流程应在应用启动前执行受审查的迁移，而不是让每个 Web 进程并发调用 `create_all()`。

### 6.8 用接口完成闭环验证

按顺序执行：

```bash
curl -i -X POST http://127.0.0.1:8000/tasks \
  -H 'Content-Type: application/json' \
  -d '{"title":"完成 FastAPI 笔记","description":"验证数据库闭环"}'

curl -i 'http://127.0.0.1:8000/tasks?offset=0&limit=20'

curl -i -X PATCH http://127.0.0.1:8000/tasks/1 \
  -H 'Content-Type: application/json' \
  -d '{"completed":true}'

curl -i -X DELETE http://127.0.0.1:8000/tasks/1
```

验证时观察四类结果：创建返回 `201` 和 ID；列表能读到同一条记录；局部更新只改变 `completed`；删除返回 `204` 且随后查询得到 `404`。重启服务后未删除的数据仍存在，证明数据来自 `tasks.db` 而非进程内列表。

## 7 事务、Session 与失败恢复

### 7.1 一个请求中的事务时间线

```mermaid
sequenceDiagram
    participant C as 客户端
    participant F as FastAPI
    participant D as Session 依赖
    participant H as 路径操作
    participant DB as 数据库
    C->>F: POST /tasks
    F->>D: 创建 Session
    D-->>H: 注入 Session
    H->>DB: INSERT（flush 可能隐式发生）
    alt 全部成功
        H->>DB: COMMIT
        DB-->>H: 提交成功
        H-->>C: 201
    else 唯一约束冲突
        DB-->>H: IntegrityError
        H->>DB: ROLLBACK
        H-->>C: 409
    end
    D->>D: 关闭 Session，归还连接
```

事务保证一组数据库变化要么全部提交，要么全部回滚。Session 在需要执行 SQL 时从 Engine 取得连接并开启事务；`commit()` 或 `rollback()` 结束事务；`close()` 释放 Session 占有的资源。没有异常不等于业务成功，调用者还应检查受影响行、约束结果和提交结果。

### 7.2 flush、commit、refresh 和 rollback

| 操作 | 发生什么 | 数据是否对其他事务可见 | 典型用途 |
| --- | --- | --- | --- |
| `flush()` | 把待处理 SQL 发给数据库 | 通常尚不可见，取决于隔离级别 | 获取主键、提前触发约束 |
| `commit()` | flush 后提交当前事务 | 提交后可按隔离规则观察 | 完成业务用例 |
| `refresh(obj)` | 从数据库重新读取对象 | 不改变提交状态 | 取得数据库默认值或最新行 |
| `rollback()` | 撤销当前未提交事务 | 未提交变化不再生效 | 从 SQL/约束错误恢复 |

flush 失败后，数据库事务和 Session 都处在失败状态；即使数据库已经回滚了底层操作，应用仍应显式调用 `session.rollback()` 重置 Session，再决定继续还是结束请求。

### 7.3 事务边界应贴近业务用例

“创建订单并扣减库存”应处在一个事务中；如果库存不足，订单也不应留下。把 commit 隐藏在每个 repository 方法里会导致前半段已经提交、后半段失败。更合适的结构是 repository 只查询和修改 Session 中的对象，service 在一个用例末尾统一提交。

跨数据库、消息队列和第三方 API 的操作无法依靠单个本地事务自动获得原子性。常见做法是事务内写业务数据和 outbox（事务消息表），提交后由独立发布器投递事件；消费端设计幂等。不要在持有数据库锁时等待慢速第三方网络调用。

### 7.4 隔离、并发与数据库约束

“先查询标题不存在，再插入”在两个并发请求下会竞态：两边都可能查到不存在。数据库唯一约束才是最终仲裁者，应用捕获其中一个事务的冲突。类似地，余额扣减、库存扣减需要原子 `UPDATE`、悲观锁或乐观版本列等并发控制，不能只依赖 Python 条件判断。

事务隔离级别越强，通常越容易获得一致快照，也可能增加锁等待或重试。选择应基于实际异常：脏读、不可重复读、幻读或写冲突。修改默认隔离级别前，先用并发测试复现业务问题并观察数据库锁。

## 8 关系映射、加载策略与分页

### 8.1 一对多关系的两个层次

假设一个用户拥有多个任务。数据库层由 `tasks.owner_id` 外键保证引用关系，ORM 层由 `relationship()` 提供对象导航：

```python
from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    username: Mapped[str] = mapped_column(String(50), unique=True)
    tasks: Mapped[list["Task"]] = relationship(back_populates="owner")


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(100))
    owner_id: Mapped[int] = mapped_column(ForeignKey("users.id"), index=True)
    owner: Mapped[User] = relationship(back_populates="tasks")
```

`ForeignKey` 决定表之间如何引用；`relationship` 不新增数据库列，它描述 ORM 如何加载并同步对象关系。`back_populates` 显式连接两侧属性，比动态生成的旧式 `backref` 更利于类型检查和阅读。官方关系模式见 [SQLAlchemy Relationship Configuration](https://docs.sqlalchemy.org/en/20/orm/relationships.html)。

### 8.2 一对一、多对多与自关联的判断

| 业务关系 | 数据库表达 | ORM 重点 | 代表场景 |
| --- | --- | --- | --- |
| 一对一 | 外键加唯一约束 | 标量关系、数据库唯一性 | 用户与个人资料 |
| 一对多 | 多的一侧保存外键 | 父集合与子标量双向关系 | 用户与任务 |
| 多对多 | 中间关联表保存两侧外键 | `secondary` 或关联对象 | 任务与标签 |
| 自关联树 | 表内 `parent_id` 指向同表主键 | `remote_side` 与递归边界 | 分类目录 |

#### 8.2.1 一对一同时需要 ORM 标量语义与数据库唯一性

```python
from __future__ import annotations

from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    profile: Mapped[Profile | None] = relationship(
        back_populates="user",
        cascade="all, delete-orphan",
    )


class Profile(Base):
    __tablename__ = "profiles"

    id: Mapped[int] = mapped_column(primary_key=True)
    nickname: Mapped[str] = mapped_column(String(50))
    user_id: Mapped[int] = mapped_column(
        ForeignKey("users.id", ondelete="CASCADE"),
        unique=True,
    )
    user: Mapped[User] = relationship(back_populates="profile")
```

类型标注让 `User.profile` 是单个对象；`profiles.user_id` 的唯一约束才从数据库层阻止一个用户对应多条资料。`cascade="all, delete-orphan"` 表示资料离开唯一父对象时由 ORM 删除，是否符合业务要逐项判断。数据库 `ON DELETE CASCADE` 与 ORM cascade 是两个层次，批量 SQL 和数据库外部写入尤其需要数据库约束兜底。

#### 8.2.2 多对多使用关联表，带业务字段时使用关联对象

```python
from __future__ import annotations

from sqlalchemy import Column, ForeignKey, Table
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database import Base

task_tags = Table(
    "task_tags",
    Base.metadata,
    Column("task_id", ForeignKey("tasks.id"), primary_key=True),
    Column("tag_id", ForeignKey("tags.id"), primary_key=True),
)


class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    tags: Mapped[list[Tag]] = relationship(
        secondary=task_tags,
        back_populates="tasks",
    )


class Tag(Base):
    __tablename__ = "tags"

    id: Mapped[int] = mapped_column(primary_key=True)
    tasks: Mapped[list[Task]] = relationship(
        secondary=task_tags,
        back_populates="tags",
    )
```

复合主键防止同一任务重复关联同一标签。若关联还包含 `assigned_at`、`role`、`sort_order` 等字段，中间记录已经具有业务含义，应映射成独立关联对象，让这些字段参与校验、查询和生命周期管理。

#### 8.2.3 自关联用 remote_side 指明远端主键

```python
from __future__ import annotations

from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database import Base


class Category(Base):
    __tablename__ = "categories"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    parent_id: Mapped[int | None] = mapped_column(
        ForeignKey("categories.id"),
        nullable=True,
    )
    parent: Mapped[Category | None] = relationship(
        back_populates="children",
        remote_side=lambda: [Category.id],
    )
    children: Mapped[list[Category]] = relationship(back_populates="parent")
```

`parent_id` 为空表示根节点；`remote_side` 告诉 SQLAlchemy 在同一张表的连接中，`Category.id` 是父侧。这个邻接表模型适合普通层级。需要高频查询整棵子树时，还要评估递归 CTE（Common Table Expression，公共表表达式）、物化路径或闭包表，并限制递归深度以防循环数据拖垮请求。

### 8.3 N+1 查询是如何产生的

查询 100 个用户得到 1 条 SQL，循环访问每个 `user.tasks` 又各发 1 条 SQL，总计 101 条，这就是典型 N+1。它在本地小数据上不明显，在生产会放大数据库往返延迟。

```python
from sqlalchemy import select
from sqlalchemy.orm import selectinload

statement = (
    select(User)
    .options(selectinload(User.tasks))
    .order_by(User.id)
)
users = session.scalars(statement).all()
```

`selectinload` 通常先查父对象，再用父主键集合查询所有子对象，适合集合关系；`joinedload` 使用联接一次取回，适合多对一或结果不会严重膨胀的场景。选择依据是关系基数、分页方式、网络往返和结果集大小，不能把所有关系都改成 eager（急加载）。

排查 N+1 时打开开发环境 SQL 日志或使用查询计数测试，确认同形 SQL 是否重复出现。响应序列化会访问关系属性，因此问题也可能在路径函数返回后才暴露；Session 已关闭时则可能出现 detached 或异步隐式 I/O 错误。

### 8.4 offset 分页与游标分页

`offset/limit` 易于实现，也允许跳到指定页；偏移很大时数据库仍需扫描或跳过大量记录，并发插入还会造成重复或遗漏。稳定的大数据列表通常使用 keyset/cursor（键集/游标）分页：

```python
from sqlalchemy import select

statement = (
    select(Task)
    .where(Task.id > last_id)
    .order_by(Task.id)
    .limit(page_size)
)
```

游标字段要有稳定、唯一的排序。按 `created_at` 排序时可组合 `(created_at, id)` 处理同一时间戳。API 不应直接相信客户端传入任意列名作为排序表达式，应从允许列表映射到 SQLAlchemy 列，避免注入和不可控的慢查询。

### 8.5 联接与聚合查询要选对结果读取方式

统计每个用户的任务数时，查询返回的是“用户名加计数”，不再是单个 ORM 实体：

```python
from sqlalchemy import func, select

statement = (
    select(User.username, func.count(Task.id).label("task_count"))
    .outerjoin(User.tasks)
    .group_by(User.id, User.username)
    .order_by(User.id)
)
rows = session.execute(statement).all()
for username, task_count in rows:
    print(username, task_count)
```

`session.scalars()` 只提取每行第一列，适合 `select(Task)` 这类单实体结果；多列、聚合或实体与标量混合时使用 `session.execute()` 读取 Row。`outerjoin` 保留没有任务的用户，`count(Task.id)` 对这些用户返回 0。生产查询还要用目标数据库的执行计划验证联接顺序、索引和聚合成本。

## 9 用 Alembic 演进数据库结构

### 9.1 迁移与 create_all 的边界

代码从 `Task(title)` 演进到 `Task(title, priority)` 时，已有数据库需要可重复、可审查的变更历史。Alembic 保存每次 schema（模式）变化的 revision（修订），按依赖顺序升级或降级。`create_all()` 只创建缺失对象，不会可靠地重命名列、回填数据或删除旧约束。

初始化：

```bash
alembic init migrations
```

在 `migrations/env.py` 中让 Alembic 看到模型元数据：

```python
from app import models
from app.database import Base

_ = models.Task
target_metadata = Base.metadata
```

数据库 URL 可以从应用配置或部署环境读取，真实密码不应写进版本库中的 `alembic.ini`。

### 9.2 生成、审查和执行迁移

模型变更后执行：

```bash
alembic revision --autogenerate -m "add task priority"
alembic upgrade head
alembic current
alembic history
```

`--autogenerate` 比较目标元数据和当前数据库，生成候选脚本；候选脚本必须人工审查。官方文档明确说明自动生成并不能识别所有变更，重命名常被误判为“删除旧列并新增新列”，直接执行会丢数据。参考 [Alembic 自动生成迁移](https://alembic.sqlalchemy.org/en/latest/autogenerate.html)。

审查至少覆盖：列类型与长度、可空性、默认值是在 Python 端还是数据库端、索引和唯一约束名称、外键删除策略、数据回填、升级与降级是否可执行、预计锁表时间。

### 9.3 非空列的安全上线顺序

给大表添加非空 `priority` 列时，直接执行“新增非空列且无默认值”会因历史行不满足约束而失败。兼容新旧应用的 expand/contract（扩展/收缩）过程通常是：

1\. 先新增可空列，旧代码仍可运行。

2\. 部署能同时读取旧状态、写入新字段的新代码。

3\. 分批回填历史数据，限制批量和锁持续时间。

4\. 验证不存在空值，再添加非空约束。

5\. 所有实例不再依赖旧列后，另一个发布周期再删除旧结构。

迁移通常作为发布阶段的独立作业执行一次。多个 Web worker 同时启动迁移会竞争锁，也会把数据库变更与健康检查混在一起。

### 9.4 降级不一定等于恢复

删除列后再 downgrade 无法凭空找回列中的原数据。高风险迁移的恢复方案可能是前滚修复、备份恢复或双写切换，而非机械执行 `alembic downgrade -1`。上线前应在接近生产的数据量上演练耗时、锁行为和回滚路径。

## 10 正确选择同步与异步

### 10.1 async def 并不会自动让代码变快

异步适合大量时间花在可等待 I/O（Input/Output，输入/输出）上的请求，例如等待数据库、HTTP 服务或消息系统。事件循环在一个协程等待时运行其他协程，提高同一进程处理并发等待的能力。CPU（Central Processing Unit，中央处理器）密集计算仍会占住执行线程，需要进程池、任务队列或独立计算服务。

选择规则可以落到代码调用链：

1\. 库提供可 `await` 的异步 API 时，路径函数使用 `async def` 并逐层 `await`。

2\. 使用同步阻塞库时，路径函数可写普通 `def`，FastAPI 会把它调度到线程池。

3\. 在 `async def` 中直接调用同步数据库驱动会阻塞事件循环。FastAPI 只会自动调度它直接调用的同步路径函数和依赖，不会自动搬移你在异步函数中直接调用的普通辅助函数。

官方说明见 [FastAPI 并发与 async/await](https://fastapi.tiangolo.com/async/)。

### 10.2 SQLAlchemy AsyncSession 的最小结构

生产使用 PostgreSQL 与异步驱动时，可采用：

```python
from collections.abc import AsyncIterator

from sqlalchemy import select
from sqlalchemy.ext.asyncio import (
    AsyncSession,
    async_sessionmaker,
    create_async_engine,
)

database_url = "postgresql+asyncpg://app:password@db/app"
async_engine = create_async_engine(database_url, pool_pre_ping=True)
AsyncSessionLocal = async_sessionmaker(
    async_engine,
    expire_on_commit=False,
)


async def get_async_session() -> AsyncIterator[AsyncSession]:
    async with AsyncSessionLocal() as session:
        yield session


async def load_tasks(session: AsyncSession) -> list[Task]:
    result = await session.scalars(select(Task).order_by(Task.id))
    return list(result)
```

连接 URL 中的 `asyncpg` 是异步 PostgreSQL 驱动；`create_async_engine`、`async_sessionmaker` 与 `AsyncSession` 必须成套使用。数据库操作通过 `await` 让出控制权。密码示例仅表示 URL 结构，真实值由 Secret 注入。

### 10.3 AsyncSession 仍然是有状态事务对象

AsyncSession 不是并发安全容器。一次请求中的一个顺序调用链可以共享它；`asyncio.gather()` 启动的多个并发任务不能同时操作同一个 AsyncSession。需要真正并行的数据库任务时，每个任务创建自己的 Session，并重新考虑这些事务是否还属于同一个原子业务用例。

异步模式还应避免未显式 `await` 的隐式 I/O。访问延迟加载关系可能尝试发起查询，导致 `MissingGreenlet` 等错误。查询时使用 `selectinload` 等加载策略，让所需关系在 Session 有效期间显式加载。官方完整边界见 [SQLAlchemy asyncio 扩展](https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html)。

### 10.4 同步还是异步的验证方法

不要根据框架名称决定异步化。先确认驱动是否异步、调用链是否真正 `await`、慢点是数据库等待还是 CPU 计算，再进行有并发度和延迟分位数的压测。观察事件循环阻塞、线程池排队、连接池等待、数据库 CPU 与慢 SQL，才能知道瓶颈是否被移动而非解决。

## 11 用测试固定接口和数据库行为

### 11.1 测试要证明什么

只断言路径函数返回值，无法证明路由注册、参数校验、依赖注入、事务提交和响应序列化能协同工作。FastAPI 的 `TestClient` 基于 HTTPX，可以在 pytest 中像调用 HTTP 服务一样测试应用，而无需监听真实端口。

一个可靠的基础测试至少覆盖：

1\. 正常创建、查询、更新和删除的状态码与响应体。

2\. 缺字段、非法类型、越界分页和显式空值的校验结果。

3\. 不存在资源的 `404` 与唯一约束冲突的 `409`。

4\. 失败写入已经回滚，后续请求仍能使用数据库。

5\. 响应中没有内部字段，关系加载不会在序列化阶段意外查询。

### 11.2 用依赖覆盖隔离测试数据库

`tests/test_tasks.py`：

```python
from collections.abc import Generator

import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import Session, sessionmaker
from sqlalchemy.pool import StaticPool

from app.database import Base
from app.dependencies import get_session
from app.main import app

test_engine = create_engine(
    "sqlite://",
    connect_args={"check_same_thread": False},
    poolclass=StaticPool,
)
TestingSessionLocal = sessionmaker(
    bind=test_engine,
    expire_on_commit=False,
)


@pytest.fixture()
def client() -> Generator[TestClient, None, None]:
    Base.metadata.create_all(bind=test_engine)

    def override_get_session() -> Generator[Session, None, None]:
        with TestingSessionLocal() as session:
            yield session

    app.dependency_overrides[get_session] = override_get_session
    with TestClient(app) as test_client:
        yield test_client
    app.dependency_overrides.clear()
    Base.metadata.drop_all(bind=test_engine)


def test_task_lifecycle(client: TestClient) -> None:
    created = client.post(
        "/tasks",
        json={"title": "写集成测试", "description": "验证完整请求链"},
    )
    assert created.status_code == 201
    task_id = created.json()["id"]

    listed = client.get("/tasks", params={"offset": 0, "limit": 20})
    assert listed.status_code == 200
    assert [item["id"] for item in listed.json()] == [task_id]

    updated = client.patch(
        f"/tasks/{task_id}",
        json={"completed": True},
    )
    assert updated.status_code == 200
    assert updated.json()["completed"] is True

    deleted = client.delete(f"/tasks/{task_id}")
    assert deleted.status_code == 204
    assert client.get(f"/tasks/{task_id}").status_code == 404


def test_duplicate_title_returns_conflict(client: TestClient) -> None:
    payload = {"title": "唯一标题"}
    assert client.post("/tasks", json=payload).status_code == 201
    assert client.post("/tasks", json=payload).status_code == 409
```

内存 SQLite 数据库通常按连接存在，`StaticPool` 让测试重用同一连接，否则建表连接和请求连接可能看见不同数据库。依赖覆盖把应用的 Session 来源替换成测试工厂，不需要在路由中写测试分支。运行：

```bash
pytest -q
```

成功判据是测试全绿，并且项目目录不生成测试用的持久化数据库文件。官方入口见 [FastAPI 测试](https://fastapi.tiangolo.com/tutorial/testing/)。

### 11.3 SQLite 测试能证明什么，不能证明什么

SQLite 测试可以证明路由、模型校验、依赖覆盖和大部分 ORM 用法能连通；它不能充分证明 PostgreSQL 的列类型、约束、事务隔离、锁、JSON 操作、时区、并发和迁移行为。关键持久化逻辑应再用与生产同类、同主版本的临时数据库做集成测试，可由容器或 CI（Continuous Integration，持续集成）服务提供。

测试数据库的建表方式也有层次：快速单元/接口测试可使用 `metadata.create_all()`；迁移测试应从空库执行 `alembic upgrade head`；升级兼容测试还应从上一生产 revision 和代表性数据开始迁移。

### 11.4 异步测试与生命周期

测试需要直接调用异步数据库函数时，可以使用 pytest 的异步支持和 HTTPX `AsyncClient`。如果应用通过 lifespan 初始化资源，使用支持 ASGI lifespan 的测试上下文，确保启动和关闭逻辑真的执行。仅实例化客户端却不进入上下文，可能漏测资源初始化。

每个测试应拥有可解释的隔离方式：重建 schema、事务回滚或独立数据库。把测试顺序当作前提会造成单独运行成功、全量运行失败。

## 12 让项目可维护：生命周期、分层与核心 API

### 12.1 用 lifespan 管理应用级资源

数据库 Session 属于请求级资源；HTTP 客户端、模型文件、连接 Engine 等昂贵且可复用的对象属于应用级资源。FastAPI 推荐用 lifespan 管理启动前创建和关闭时释放：

```python
from contextlib import asynccontextmanager
from collections.abc import AsyncIterator

import httpx
from fastapi import FastAPI


@asynccontextmanager
async def lifespan(app: FastAPI) -> AsyncIterator[None]:
    app.state.http_client = httpx.AsyncClient(timeout=5.0)
    yield
    await app.state.http_client.aclose()


app = FastAPI(lifespan=lifespan)
```

`yield` 之前在应用接受请求前执行，之后在应用关闭时执行。若传入 `lifespan`，旧式 `startup`、`shutdown` 事件处理器不再与它混用。参考 [FastAPI Lifespan Events](https://fastapi.tiangolo.com/advanced/events/)。

应用启动不适合执行耗时、可能争锁且需要全局只运行一次的数据库迁移。迁移是部署阶段作业；每个应用实例的 lifespan 则都会执行。

### 12.2 路由、服务与数据访问的依赖方向

```mermaid
flowchart LR
    R["Router：HTTP 输入输出"] --> SV["Service：业务用例与事务决策"]
    SV --> RP["Repository：查询与持久化表达"]
    RP --> SA["SQLAlchemy Session"]
    SV --> DM["领域规则或领域对象"]
    R --> SC["Pydantic Schema"]
```

Router 把 HTTP 状态与业务输入互相转换；Service 协调一个完整用例和事务；Repository 集中复杂查询；领域规则描述与传输协议无关的约束。箭头表示上层依赖下层接口。小型 CRUD 可以让 Router 直接使用 Session；当同一用例被定时任务、消息消费者和 HTTP 共同调用，Service 层会明显降低重复。

避免让 SQLAlchemy 对象在 Session 关闭后穿过多个异步边界。服务返回已加载实体或专门的数据对象，Router 再转换为响应模型；需要关系时在查询阶段显式加载。

### 12.3 BackgroundTasks 的可靠性边界

FastAPI `BackgroundTasks` 在响应之后、同一应用进程中执行，适合短小且即使进程退出可重试或可丢失的工作，例如非关键审计补充。它没有持久队列、跨进程调度和可靠重试保证。

发送付款、生成大型报表、可靠邮件、长时间机器学习推理等任务更适合外部任务队列。生产者在数据库事务内记录 outbox，发布器投递，消费者以业务幂等键处理重复消息。接口返回 `202 Accepted` 时还应提供任务 ID 和状态查询入口。

### 12.4 FastAPI 核心 API 卡片

| API | 用途与返回 | 适用边界 | 失败入口 |
| --- | --- | --- | --- |
| `FastAPI()` | 创建 ASGI 应用 | 应用级单例或工厂产物 | 生命周期初始化错误 |
| `APIRouter()` | 组合路由、前缀、标签和依赖 | 按业务能力拆分 | 前缀重复、注册遗漏 |
| `Depends(provider)` | 解析并注入依赖结果 | Session、用户、配置、权限 | 依赖异常会中断下游调用 |
| `HTTPException` | 提前生成预期 HTTP 错误 | 接口适配层与权限边界 | 不应用于隐藏未知程序错误 |
| `response_model` | 校验、过滤和序列化输出 | 稳定接口契约与防泄漏 | 返回结构错误可能变成 500 |
| `status` | 使用具名 HTTP 状态常量 | 提高语义可读性 | 状态码与响应体语义不一致 |

### 12.5 SQLAlchemy 核心 API 卡片

| API | 用途与返回 | 生命周期/并发 | 关键边界 |
| --- | --- | --- | --- |
| `create_engine()` | 创建同步 Engine | 应用级复用，内部有连接池 | 不要每请求创建 Engine |
| `sessionmaker()` | 创建 Session 工厂 | 应用级复用 | 工厂不是事务本身 |
| `Session` | 跟踪对象并管理逻辑事务 | 每请求/每任务一个，不并发共享 | 异常后回滚，结束时关闭 |
| `select()` | 构造参数化 SELECT | 可组合、尚未执行 | 动态排序需允许列表 |
| `session.scalars()` | 执行语句并提取首列实体 | 返回 ScalarResult | `.all()` 会把结果装入内存 |
| `Mapped`/`mapped_column` | 声明 2.x 类型化映射 | 类定义阶段 | Python 默认与数据库默认不同 |
| `relationship()` | 描述 ORM 对象关系 | 加载发生在 Session 中 | N+1、级联和延迟加载 |

官方的系统学习顺序可从 [SQLAlchemy Unified Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/) 开始，再进入 ORM 查询、Session 和关系章节。

## 13 安全、配置与可观测性

### 13.1 认证与授权是两步判断

认证回答“请求者是谁”，授权回答“这个身份能否操作该资源”。OAuth 2.0（Open Authorization 2.0，开放授权 2.0）定义授权流程；JWT（JSON Web Token，JSON Web 令牌）是一种可承载声明的令牌格式，二者不是同一个概念。

一个访问令牌处理链通常包括：从标准 `Authorization: Bearer ...` 头取令牌，验证签名、算法、过期时间、签发者和受众，解析用户身份，再检查该用户是否拥有当前任务。JWT 内容通常只是编码并签名，不等于加密，不能放密码或不希望客户端看见的秘密。

密码应使用专用密码哈希算法及库，例如 Argon2 或 bcrypt，并保存哈希而非明文。登录错误信息不宜区分“用户不存在”和“密码错误”，以减少账号枚举。完整实现应从 [FastAPI Security 教程](https://fastapi.tiangolo.com/tutorial/security/) 的当前示例开始，并根据组织身份系统选择 OAuth2/OIDC（OpenID Connect，开放身份连接）方案。

### 13.2 常见 Web 安全边界

1\. CORS 不是服务端鉴权。它约束浏览器脚本的跨源访问；服务端、脚本和恶意客户端不受浏览器同源策略保护。允许携带凭证时应列出精确来源，不使用通配符。

2\. SQLAlchemy 的表达式 API 会绑定参数，能降低 SQL 注入风险；把用户输入拼进 `text()`、列名、排序方向或表名仍然危险。动态结构通过允许列表映射。

3\. 响应模型过滤敏感字段，但日志、异常和追踪也可能泄漏令牌、Cookie、数据库 URL 和个人数据。记录前做字段级脱敏。

4\. 上传接口限制内容类型、单文件与总大小，把内容流式写入受控存储；客户端文件名不能直接决定服务端路径。

5\. 反向代理层通常承担 HTTPS（Hypertext Transfer Protocol Secure，安全超文本传输协议）、请求体上限和基础限流，应用仍需执行细粒度资源授权与业务配额。

### 13.3 配置与 Secret

Pydantic Settings 可以从环境变量、dotenv 和 Secret 文件等来源构建类型化配置。启动时校验配置，让缺失数据库 URL、非法超时或错误环境名尽早失败。参考 [Pydantic Settings Management](https://docs.pydantic.dev/latest/concepts/pydantic_settings/)。

配置可分为非敏感行为参数和 Secret。前者包括日志级别、超时、分页上限；后者包括数据库密码、签名密钥和第三方令牌。Secret 由部署平台注入并制定轮换策略，代码库只保存变量名和示例占位符。调试输出 `settings` 时应确认敏感字段不会被打印。

### 13.4 日志、指标和追踪分别回答什么

| 信号 | 回答的问题 | FastAPI/数据库重点 |
| --- | --- | --- |
| 结构化日志 | 这次失败具体发生了什么 | 请求 ID、路由模板、状态码、异常类型，不记录 Secret |
| 指标 | 系统整体是否变慢或出错 | 请求速率、错误率、P50/P95/P99 延迟、连接池等待、慢查询 |
| 分布式追踪 | 时间消耗在哪个跨服务步骤 | 入口 Span、数据库 Span、外部 HTTP Span、任务关联 |

访问日志使用路由模板 `/tasks/{task_id}` 作为标签，而不是把每个具体 ID 作为指标标签，否则会产生高基数。错误日志保留堆栈并关联请求 ID；返回客户端的错误只含稳定错误码和安全描述。

### 13.5 存活、就绪与启动检查

Liveness（存活）表示进程是否还能工作，通常不依赖数据库，避免数据库短暂故障导致所有实例被反复重启。Readiness（就绪）表示实例是否适合接收流量，可以检查关键依赖，但必须设置短超时并防止健康检查本身压垮数据库。Startup（启动）检查用于告诉编排系统初始化是否完成。

`/health` 返回 200 只能证明路径函数可运行；要证明业务可用，还需用合成请求、数据库指标和错误率观测。健康接口不应泄漏依赖地址、版本漏洞信息或异常堆栈。

## 14 生产部署、进程模型与容量

### 14.1 生产请求拓扑

```mermaid
flowchart TB
    C["客户端"] --> LB["负载均衡/反向代理：TLS、限流、路由"]
    subgraph I1["应用实例 A：独立故障域"]
        W11["Uvicorn Worker 1"] --> P11["连接池 1"]
        W12["Uvicorn Worker 2"] --> P12["连接池 2"]
    end
    subgraph I2["应用实例 B：独立故障域"]
        W21["Uvicorn Worker 1"] --> P21["连接池 1"]
        W22["Uvicorn Worker 2"] --> P22["连接池 2"]
    end
    LB --> W11
    LB --> W12
    LB --> W21
    LB --> W22
    P11 --> DB[("数据库主节点")]
    P12 --> DB
    P21 --> DB
    P22 --> DB
    MJ["一次性迁移作业"] --> DB
    DB -."复制与故障转移".-> ST[("备用/副本节点")]
    W11 --> OT["日志、指标、追踪采集"]
    W12 --> OT
    W21 --> OT
    W22 --> OT
```

负载均衡器终止 TLS（Transport Layer Security，传输层安全）并把流量分发到实例。每个 worker 是独立进程，拥有独立内存、事件循环和连接池；实例 A 故障时，负载均衡应停止向它发流量。数据库主节点是共享依赖，其故障会同时影响所有写请求；副本是否能接管取决于数据库系统和应用连接策略，图中的虚线不代表自动完成。

迁移作业与 Web worker 分开，只执行一次并在兼容窗口内完成。遥测采集不应阻塞主请求；采集后端故障时要有缓冲、丢弃或降级策略。

### 14.2 开发服务器与生产服务器

`fastapi dev` 提供自动重载，适合开发；生产运行要考虑进程退出重启、优雅关闭、监听地址、代理头、HTTPS、资源限制和监控。可以使用 FastAPI CLI 或 Uvicorn 的生产启动方式，并由 systemd、容器平台或进程管理器负责生命周期。

多 worker 能利用多核并隔离单进程故障，但内存和连接池也按进程复制。在容器编排环境中，经常采用每容器一个 worker、横向扩容容器；在单机部署中也可能每实例多个 worker。选择取决于平台健康管理、CPU 配额、内存和连接预算，而非固定口号。参考 [FastAPI Server Workers](https://fastapi.tiangolo.com/deployment/server-workers/) 与 [FastAPI 容器部署](https://fastapi.tiangolo.com/deployment/docker/)。

### 14.3 连接池容量计算

假设每个 worker 的 `pool_size=5`、`max_overflow=10`，单个 worker 峰值最多可能占用 15 个连接。2 个实例、每实例 4 个 worker 时，理论峰值是：

```text
2 × 4 × (5 + 10) = 120 个连接
```

还要给迁移、管理、监控和其他服务预留连接，结果不能超过数据库 `max_connections` 及数据库实际可承受并发。扩大连接池不一定提高吞吐；过多并发 SQL 会增加 CPU 争用、缓存抖动和锁等待。

`pool_timeout` 到期出现的错误表示请求等待不到连接。排查顺序是确认 Session 是否关闭、事务是否过长、SQL 是否变慢、流量是否突增，再判断池是否太小。连接泄漏或慢查询未解决前盲目扩池只会推迟故障。

### 14.4 超时、重试与幂等

入口、应用到数据库、应用到外部 HTTP 都要有超时，且下游超时应短于上游总预算，给错误处理留出时间。只对明确的瞬态错误重试，并使用指数退避和随机抖动；数据库约束错误、参数错误不会因重试变好。

创建类请求被代理重试时可能重复写入。支付、下单等接口可接收 Idempotency-Key（幂等键），在数据库中用唯一约束记录请求与结果。幂等不能只存进程内字典，因为多 worker 不共享内存，重启也会丢失。

### 14.5 优雅关闭与后台任务

发布时先停止接收新流量，再等待进行中的请求到达截止时间，最后关闭 HTTP 客户端、数据库 Engine 和其他资源。超过宽限期的请求会被终止，因此长请求和后台任务需要可重试设计。进程内 `BackgroundTasks` 可能在实例终止时丢失，关键任务使用持久队列。

### 14.6 上线检查表

1\. API 契约：OpenAPI 变更已审查，状态码、空值和分页兼容旧客户端。

2\. 数据库：迁移脚本已人工审查，在代表性数据量上验证耗时、锁与恢复方案。

3\. 事务：多表用例只有一个明确提交点，失败路径执行回滚，外部调用不长期占锁。

4\. 容量：实例数、worker 数和连接池参数的乘积在数据库预算内。

5\. 安全：Secret 来自受控注入，认证与资源授权都有测试，CORS 与代理信任范围精确。

6\. 性能：列表有上限和稳定排序，热点查询有执行计划证据，关系加载不存在 N+1。

7\. 可观测性：日志可关联请求，延迟与错误率有告警，健康检查不会泄漏内部信息。

8\. 发布：迁移与应用启动分离，支持优雅关闭，回滚或前滚步骤已演练。

## 15 从现象开始排查常见故障

### 15.1 请求与校验问题

| 现象 | 第一检查点 | 常见机制原因 | 修复与验证 |
| --- | --- | --- | --- |
| 返回 404 | 方法、完整路径、路由前缀 | Router 未注册或路径不匹配 | 查看 `/openapi.json` 是否存在该操作 |
| 返回 405 | HTTP 方法 | 路径存在但操作不同 | 对照 OpenAPI 使用正确方法 |
| 返回 422 | 响应 `detail.loc` | 字段来源、类型或约束错误 | 用最小 JSON 重放，核对 Content-Type |
| 返回 500 且提示响应校验 | 返回值与响应模型 | 缺字段、错误类型、关系未加载 | 单测断言响应结构，检查服务端日志 |
| `/docs` 资源加载慢 | 浏览器网络请求 | CDN（Content Delivery Network，内容分发网络）不可达或代理限制 | 按官方方式自托管文档静态资源，不关闭 OpenAPI 来掩盖问题 |

### 15.2 Session、连接和异步问题

| 现象 | 第一检查点 | 常见机制原因 | 修复与验证 |
| --- | --- | --- | --- |
| `PendingRollbackError` | 前一条数据库异常 | 失败后未 `rollback()` | 异常路径回滚，再执行后续查询测试 |
| `DetachedInstanceError` | Session 何时关闭 | 响应序列化访问未加载关系 | 查询阶段 eager load，或在关闭前转响应模型 |
| `MissingGreenlet` | 异步代码中的属性访问 | AsyncSession 触发隐式延迟加载 | 使用 `selectinload` 并显式 await 查询 |
| `QueuePool timeout` | 池占用与事务时长 | Session 泄漏、慢 SQL、流量过载 | 记录池等待，修复关闭与慢查询后压测 |
| SQLite `database is locked` | 并发写和事务时长 | SQLite 写并发能力有限 | 缩短事务；生产并发写使用合适的服务型数据库 |
| 连接偶发断开 | 数据库和网络日志 | 池中陈旧连接或中途网络故障 | `pool_pre_ping`、合理重试只覆盖安全的瞬态操作 |

### 15.3 查询与迁移问题

| 现象 | 第一检查点 | 常见机制原因 | 修复与验证 |
| --- | --- | --- | --- |
| 列表越来越慢 | SQL 与执行计划 | 无索引、大 offset、返回无上限 | 加合适索引或游标分页，对比 P95 |
| SQL 数量随结果数增长 | 同形 SQL 日志 | N+1 延迟加载 | `selectinload`/`joinedload`，加入查询数测试 |
| Alembic 生成空迁移 | `target_metadata` | 模型未导入或 Base 不一致 | 打印/检查 metadata 表集合后重新生成 |
| 自动迁移显示删列加列 | 实际意图是否重命名 | autogenerate 无法推断重命名 | 手工改成 rename，并在数据副本演练 |
| 本地成功、部署失败 | 驱动、URL、迁移 revision | SQLite 与生产数据库差异或环境变量缺失 | 启动时打印非敏感配置摘要，CI 使用生产同类数据库 |

故障排查先建立时间线：请求何时进入、在哪个依赖取得资源、第一条异常在哪里、事务是否回滚、连接是否归还。后续连锁异常往往只是第一个错误的结果，优先处理最早出现且最接近根因的证据。

## 16 面试复盘、项目落地与资料入口

### 16.1 核心概念的递进式复盘

| 主题 | 回答应包含的机制证据 | 可继续推导的问题 |
| --- | --- | --- |
| FastAPI 为什么性能好 | ASGI、事件循环、Starlette/Pydantic、I/O 并发；性能仍受业务与数据库限制 | 同步驱动放进 `async def` 会怎样 |
| `def` 与 `async def` | FastAPI 调度边界、线程池、可等待 I/O、CPU 密集任务 | 线程池耗尽如何观察 |
| 依赖注入 | 依赖树、请求内缓存、`yield` 清理与作用域 | 流式响应何时关闭 Session |
| Pydantic 与 ORM 模型 | 外部契约、数据库映射、生命周期和敏感字段边界 | `from_attributes` 为何不解决延迟加载 |
| Session 是什么 | 工作单元、identity map、逻辑事务、每请求一个 | 为什么不能跨线程/任务共享 |
| flush 与 commit | 发 SQL 与提交事务的差异、主键获取、回滚 | 两张表写入如何保持原子性 |
| 唯一性检查 | 应用预检查改善体验，数据库约束最终仲裁 | 并发冲突如何映射成 409 |
| N+1 查询 | 延迟加载触发、SQL 数量证据、eager load 取舍 | joinedload 为何会放大结果集 |
| Alembic 自动生成 | metadata 对比、候选脚本、人工审查和数据迁移 | 非空列如何零停机添加 |
| 多 worker 部署 | 进程隔离、内存与连接池复制、总连接公式 | 内存状态和 BackgroundTasks 有何风险 |

面试回答的质量来自边界和证据：能描述一条请求时间线、给出失败场景、说明如何验证，比罗列框架特性更能体现实际理解。

### 16.2 从笔记落地为个人项目

可以按下面顺序演进任务 API，每一步都保留可观察判据：

1\. 完成第 6 章同步 SQLite 版本，确保 CRUD、404、409 和 422 有测试。

2\. 增加 User 与 Task 一对多关系，用 `selectinload` 返回用户任务，并用 SQL 计数证明没有 N+1。

3\. 接入 Alembic，从空库和旧 revision 两条路径执行 `upgrade head`。

4\. 把数据库切换为 PostgreSQL，在测试中验证唯一约束、时区、并发写和连接超时。

5\. 加入认证和资源级授权，证明用户不能读取或修改他人的任务。

6\. 增加结构化访问日志、请求 ID、延迟指标和就绪检查，通过一次故障注入验证告警。

7\. 容器化部署，按第 14.3 节计算连接预算，执行并发压测并记录 P95/P99。

完成第 4 步，已经具备常规数据库 API 的基础闭环；完成第 7 步，才能开始以生产约束评估它。

### 16.3 官方资料与课程参考

1\. [FastAPI 官方学习入口](https://fastapi.tiangolo.com/learn/)：教程、高级指南、部署与 How-to 总目录。

2\. [FastAPI First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/)：应用对象、路径操作、开发命令与自动文档。

3\. [FastAPI Dependencies](https://fastapi.tiangolo.com/tutorial/dependencies/)：依赖树、子依赖和 OpenAPI 集成。

4\. [FastAPI Bigger Applications](https://fastapi.tiangolo.com/tutorial/bigger-applications/)：多文件与 APIRouter 组织。

5\. [FastAPI Testing](https://fastapi.tiangolo.com/tutorial/testing/)：TestClient 与 pytest。

6\. [SQLAlchemy 2.0 官方文档](https://docs.sqlalchemy.org/en/20/)：当前 2.x 教程、ORM、Core、Session、关系与 asyncio。

7\. [SQLAlchemy ORM Quick Start](https://docs.sqlalchemy.org/en/20/orm/quickstart.html)：类型化声明、Engine、Session 和查询的最短入口。

8\. [SQLAlchemy Session Basics](https://docs.sqlalchemy.org/en/20/orm/session_basics.html)：事务、对象状态与并发安全边界。

9\. [SQLAlchemy Engine Configuration](https://docs.sqlalchemy.org/en/20/core/engines.html)：URL、方言和连接池参数。

10\. [Alembic 官方教程](https://alembic.sqlalchemy.org/en/latest/tutorial.html)：迁移环境、revision 与执行流程。

11\. [Pydantic Models](https://docs.pydantic.dev/latest/concepts/models/)：校验、序列化、`from_attributes` 与模型配置。

12\. [课程参考：速度最快的异步框架 FastAPI](https://www.mashibing.com/study?courseNo=2553&sectionNo=103706&courseVersionId=3432)：可用作路由、SQLAlchemy 2.0、关系与 CRUD 的视频练习顺序；版本敏感结论以官方文档为准。

### 16.4 复习自测与成功判据

1\. 不看代码画出客户端、Uvicorn、FastAPI、依赖、Session、Engine 和数据库的数据流，并指出每层失败会出现在哪里。

2\. 构造缺少 `title`、标题为空、额外字段、`title: null` 四个请求，解释为何结果不同并用测试固定。

3\. 在一次事务中创建用户和两个任务，先 `flush()` 获取用户 ID，制造第二条任务失败，验证三条记录均未提交。

4\. 制造一个 N+1 查询，记录 SQL 数量，再用 `selectinload` 修复并比较。

5\. 给任务表增加非空优先级，写出兼容新旧版本的迁移步骤，并在旧数据库副本上执行。

6\. 把同步 Session 错误地放进 `async def` 后进行并发压测，再改成同步路径或完整异步栈，对比事件循环阻塞和延迟。

7\. 给定实例数、worker 数、`pool_size` 与 `max_overflow`，算出理论最大连接数，并验证数据库仍有运维预留。

8\. 模拟数据库不可用，确认 readiness 停止接流量、liveness 不引发重启风暴、日志和指标能关联同一故障。

如果这些任务都能用代码、SQL 日志、测试结果或指标证明，而不是只口头复述，就已经建立了从学习示例到生产排查的完整主线。

### 16.5 继续学习的边界

本文没有展开 WebSocket、Server-Sent Events（服务器发送事件）、GraphQL、分布式事务协议和 SQLAlchemy 内部事件系统。需要双向长连接时从 FastAPI WebSocket 文档进入；需要跨服务可靠消息时先掌握 outbox、幂等和消息队列；需要数据库级深度优化时学习目标数据库的执行计划、索引、锁与隔离级别。它们建立在本文的请求生命周期、事务边界和可观测性基础上，不影响先完成常规 HTTP 数据 API。
