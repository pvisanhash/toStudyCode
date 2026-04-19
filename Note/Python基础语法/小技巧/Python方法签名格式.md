
PyCharm 中方法签名的常见格式有以下几种：

---

## 1. 普通参数

```python
def greet(name: str, age: int) -> str:
```

最基础的形式，参数名 + 类型注解，`->` 后面是返回值类型。

---

## 2. 默认值参数

```python
def connect(host: str, port: int = 3306, timeout: float = 30.0)
```

`=` 后面是默认值，调用时可以不传。

---

## 3. `*args` — 可变位置参数

```python
def print_all(*values: object)
def sum_all(*numbers: int)
```

收集任意数量的位置参数，在函数内是一个 **tuple**。

---

## 4. `**kwargs` — 可变关键字参数

```python
def create_user(**kwargs: Any)
def render(template: str, **context: str)
```

收集任意数量的关键字参数，在函数内是一个 **dict**。

---

## 5. 仅限关键字参数（`*` 后面的参数）

```python
def process(data: list, *, encoding: str, errors: str = "strict")
```

`*` 之后的参数**必须用关键字方式传入**，不能按位置传。

```python
process([1,2,3], encoding="utf-8")   # 正确
process([1,2,3], "utf-8")            # 报错
```

---

## 6. 仅限位置参数（`/` 前面的参数）

```python
def pow(x: float, y: float, /, mod: int = None)
```

`/` 之前的参数**只能按位置传入**，不能用关键字。

```python
pow(2, 3)           # 正确
pow(x=2, y=3)       # 报错
```

---

## 7. 类型注解的常见写法

```python
# 可选参数（可以是 None）
def find(name: str | None = None)        # Python 3.10+
def find(name: Optional[str] = None)     # 旧写法

# 列表、字典、元组
def process(items: list[int], config: dict[str, Any])

# 联合类型（多选一）
def parse(value: int | str | float)

# 返回多个值
def divmod(a: int, b: int) -> tuple[int, int]

# 无返回值
def close() -> None

# 返回类型不确定
def load() -> Any
```

---

## 8. 完整组合示例

```python
def request(
    url: str,                    # 普通参数
    /,                           # 斜杠前：仅限位置
    method: str = "GET",         # 有默认值
    *headers: str,               # 可变位置参数
    timeout: float = 30.0,       # 仅限关键字（* 之后）
    **params: str                # 可变关键字参数
) -> Response:
```

调用顺序规则：**位置参数 → `*args` → 关键字参数 → `**kwargs`**，不能乱序。

---

## PyCharm 里快速查看的方式

|操作|快捷键|
|---|---|
|查看参数提示|`Cmd+P`|
|查看完整文档|`F1`|
|跳转到源码|`Cmd+点击`|
