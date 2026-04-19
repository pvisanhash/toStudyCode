---
author: aitx
title: Python核心基础课程
time: 2026-04-01 Wed
tags:
  - Python
  - 基础语法
  - 课程整理
---

# Python核心基础课程

> 课程整理说明
> - 内容来源：[飞书课程原文](https://my.feishu.cn/drive/folder/LkfIfEoarlbcmEdRLLIcmMt0nvg)
> - python基础语法在线练习网站:[python在线练习](https://hedy.org/ "python在线练习")
> - 整理方式：基于原文语义重新编排标题与内容结构，清理格式噪音
> - 用途：适合在 Obsidian 中检索、阅读与复习

## 第一节：Python入门

> 本节要点
> - 了解 Python 的定位与特点
> - 认识解释器及常见版本
> - 掌握 Python 程序的两种运行方式
> - 了解 PyCharm 的基本作用

### Python 是什么

编程语言是人与计算机交流的一种方式。Python 是一种高级语言，兼具解释型、交互性、面向对象和面向函数等特点，适合初学者入门，也适合广泛的软件开发场景。

Python 的主要特点：
- **解释型语言**：开发时不需要单独编译步骤。
- **交互式语言**：可以在 `>>>` 提示符下直接执行代码。
- **面向对象语言**：支持对象封装与类的设计。
- **面向函数语言**：适合函数式组织方式与递归计算。
- **适合初学者**：语法相对简洁，上手成本低。

### Python 解释器

#### 解释器的作用

解释器的核心作用，是把程序员编写的代码翻译成计算机能够执行的形式。计算机最终只认识底层机器指令，因此 Python 代码需要通过解释器完成执行。

#### 常见解释器

- **CPython**：官方实现，基于 C 语言开发，也是最常用的解释器。
- **IPython**：基于 CPython 的增强交互式解释器。
- **Jython**：运行在 Java 平台上。
- **IronPython**：运行在 .NET 平台上。

#### 常见版本

- Python 2.x
- Python 3.x
  - Python 3.6
  - Python 3.7
  - Python 3.8
  - Python 3.9
  - Python 3.10

#### 下载地址

<https://www.python.org/downloads/windows/>

#### 版本建议

优先使用 Python 3.x，课程中提到 3.8、3.9、3.10 都可以。

### Python 程序的运行方式

#### 交互式运行

在命令行中启动 Python 解释器，输入代码后立即得到结果。

基本流程：
- 打开终端
- 进入 Python 安装目录或直接调用 Python
- 输入 `python` 进入交互模式
- 输入代码，例如：`print("hello world")`
- 输入 `exit()` 退出

#### 文件方式运行

把代码写入 `.py` 文件后，再通过解释器执行。

基本流程：
- 编写 `.py` 文件
- 打开终端
- 进入程序所在目录
- 执行：`python 文件名`

### PyCharm 简介

PyCharm 是 Python 的集成开发环境（IDE），常见功能包括：
- 项目管理
- 智能提示
- 语法高亮
- 代码跳转
- 调试功能
- 解释器管理
- 框架和库支持

常见开发工具还包括：
- VS Code
- Jupyter
- IDEA

#### 版本说明

PyCharm 分为：
- 专业版（Professional）
- 社区版（Community）

其中社区版免费可用。

#### 下载地址

<http://www.jetbrains.com/pycharm/download/#section=windows>

## 第二节：注释和变量

> 本节要点
> - 掌握单行和多行注释
> - 理解变量的概念
> - 了解标识符命名规则与命名习惯
> - 学会用 `type()` 查看数据类型

### 注释

注释用于解释代码含义，本身不会被执行。

常见形式：
- 单行注释
- 多行注释

补充：
- 常见快捷键：`Ctrl + /`

#### 代码模板示例

```python
@Time : ${DATE} ${TIME}
@Author : 老肖老师
@Version : V1.0
@IDE : ${PRODUCT_NAME}
@Project : ${PROJECT_NAME}
```

### 变量

变量用于在程序中存储数据。可以把变量理解成“内存中的一个命名位置”：
- 变量名：相当于房间号
- 变量值：相当于房间里存放的数据

有了变量名，就可以快速找到对应的数据。

#### 变量的基本写法

- 定义变量：`变量名 = 值`
- 使用变量：直接通过变量名访问

### 标识符与命名规范

#### 标识符规则

Python 中定义名字时，需要遵守这些规则：
- 由数字、字母、下划线组成
- 不能以数字开头
- 不能使用关键字
- 严格区分大小写

#### 命名习惯

- 见名知义
- 多个单词之间用下划线分隔，例如：`your_name`

### 数据类型初识

可以用 `type()` 查看一个对象的数据类型。

## 第三节：Python 的输入、类型转换与输出格式化

> 本节要点
> - 掌握 `input()` 的基本行为
> - 学会常用类型转换函数
> - 理解 `%` 格式化与 f-string
> - 了解浮点数输出精度问题

### 用户输入：input()

`input()` 用于接收用户输入。

特点：
- 执行到 `input()` 时，程序会等待用户输入。
- 输入结果通常会保存到变量中。
- `input()` 接收到的数据默认都是字符串类型。

#### 注意

通过 `input()` 获取的数据默认都是字符串，Python 是强类型语言，如果后续要参与数值运算，需要先做类型转换。

### 类型转换

#### 常用类型转换函数

- `int()`
- `float()`
- `str()`
- `list()`
- `tuple()`
- `eval()`

### 输出格式化

#### 使用 `%` 占位符

常见格式控制：
- `%s`：输出字符串或数字
- `%06d`：整数总宽度为 6，不足补 0
- `%.2f`：保留两位小数

#### 使用 f-string

也可以写成：
```python
f'{表达式}'
```

#### 精度说明

浮点数在计算机内部以二进制形式存储，部分小数无法精确表示，因此在格式化输出时可能出现精度误差。

## 第四节：Python 中的运算符

> 本节要点
> - 认识常见运算符分类
> - 掌握算术、赋值、比较、逻辑运算符
> - 理解复合赋值的执行顺序
> - 学会用综合案例做简单计算

### 运算符分类

常见运算符包括：
- 算术运算符
- 赋值运算符
- 复合赋值运算符
- 比较运算符
- 逻辑运算符

### 算术运算符

常见示例：

```python
print(2**3)      # 2 的立方
print(16**0.5)   # 16 的平方根
print(27**(1/3)) # 27 的立方根
print(pow(2, 3))
print(pow(6, 1/3))
```

### 赋值运算符

用于把值赋给变量。

### 复合赋值运算符

常见如 `+=`、`-=`、`*=`、`/=` 等。

#### 注意

复合赋值通常遵循“先计算，后赋值”的过程。

### 比较运算符

比较运算符也叫关系运算符，常用于条件判断。

### 逻辑运算符

- 与：`and`
- 或：`or`
- 非：`not`

### 综合案例：BMI 计算

输入身高和体重，计算 BMI 指数：

```python
h = float(input('请输入身高(单位:米)\t'))
w = float(input('请输入体重(单位:千克)\t'))
bmi = w / h ** 2
print(f'你的身高是:{h}米，', end='')
print(f'你的体重是:{w}千克，', end='')
print('我的BMI体质指数是: %.2f' % bmi, end='\n')
```

### 补充：转义字符与 print 结束符

#### 常见转义字符

- `\n`：换行
- `\t`：制表符

#### print 的结束符

`print()` 默认带有 `end="\n"`，所以输出后会自动换行；可以通过修改 `end` 参数改变结尾行为。

## 第五节：流程控制语句

> 本节要点
> - 理解条件语句与循环语句
> - 掌握 if / elif / else 的基本结构
> - 学会 while、for、range、break、continue、else 的使用
> - 通过案例理解流程控制

### 流程控制的两大类

- 条件语句
- 循环语句

### 条件语句

条件语句让程序根据不同条件执行不同分支。

结构特点：
- `if` 必须有
- `elif` 可以有 0 个或多个
- `else` 可以没有，也只能出现 0 个或 1 个

基本形式：

```python
if 条件1:
    代码
elif 条件2:
    代码
else:
    代码
```

#### if 嵌套

```python
if 条件1:
    代码
    if 条件2:
        代码
```

#### 三目运算

三目运算适合简洁赋值：

```python
值1 if 条件 else 值2
```

### 循环语句

循环用于重复执行一段代码，常见形式有：
- `while`
- `for`

#### range() 函数

`range()` 用来生成整数序列。

口诀：**包头不包尾**。

#### break 与 continue

- `break`：直接结束当前循环
- `continue`：跳过本次循环，继续下一次

#### 注意

在嵌套循环中，`break` 和 `continue` 默认只作用于最内层循环。

#### 循环中的 else

循环和 `else` 搭配时：
- 正常结束循环，会执行 `else`
- 如果是 `break` 跳出，则不执行 `else`

### 综合案例：猜数游戏

课程中给出了猜数游戏作为流程控制综合练习。

## 第六节：Python 中的序列

> 本节要点
> - 理解序列的概念
> - 掌握字符串、列表、元组的常见操作
> - 了解序列的通用操作
> - 学会切片、成员判断与常用内置函数

### 序列概览

Python 中常见的序列包括：
- 字符串
- 列表
- 元组

### 字符串

#### 定义

字符串是 Python 中最常见的数据类型之一，可以使用单引号、双引号、三引号创建。

```python
var1 = 'Hello World!'
var2 = "Python \t Runoob"
var3 = '''sdfsd
           sdfsd'''
```

#### 下标（索引）

下标也叫索引，用于通过编号访问序列中的元素。

特点：
- 正向从 `0` 开始
- 反向从 `-1` 开始

#### 切片

切片用于截取序列的一部分，基本形式：

```python
序列[开始:结束:步长]
```

口诀：**包头不包尾**。

示例：

```python
name = "abcdefg"
print(name[2:5:1])
print(name[2:5])
print(name[:5])
print(name[1:])
print(name[:])
print(name[::2])
print(name[:-1])
print(name[-4:-1])
print(name[::-1])
```

#### 常用字符串函数

课程原文提到字符串也有对应函数，可结合官方文档继续学习。

### 列表（List）

#### 定义

列表是可变序列，可以存储多个不同类型的数据。

```python
[数据1, 数据2, 数据3, 数据4]
```

#### 常见操作

- 下标和切片：查找、修改、截取
- `index()`：返回指定元素的下标
- `count()`：统计元素出现次数
- `len()`：获取长度
- `append()`：末尾追加一个元素
- `extend()`：末尾追加一个序列中的多个元素
- `insert()`：指定位置插入元素
- `pop()`：删除并返回指定位置元素
- `del`：删除元素
- `remove()`：删除首个匹配元素
- `reverse()`：逆序
- `sort()`：排序
- `for`：遍历

### 元组（Tuple）

#### 定义

元组是不可变序列，一旦创建，不能修改元素内容。

```python
元组名 = ()
元组名 = (20,)
元组名 = (1, 2, 3)
元组名 = 100, 200, 300
```

#### 常见操作

- 下标和切片
- `index()`
- `count()`
- `len()`
- `for` 遍历

### 序列的通用操作

#### 数学运算

- `+`：拼接两个序列
- `+=`：拼接并重新绑定
- `*`：重复元素
- `*=`：重复并重新绑定
- 比较运算：`< <= > >= == !=`

#### 成员判断

- `数据 in 序列`
- `数据 not in 序列`

#### 下标与切片

字符串、列表、元组都支持下标与切片。

#### 常用内置函数

- `len(x)`：长度
- `max(x)`：最大值
- `min(x)`：最小值
- `sum(x)`：求和（元素需为数值）

## 第七节：Python 的函数

> 本节要点
> - 理解函数的定义与调用
> - 掌握参数的常见类型
> - 理解返回值与变量作用域
> - 学会总结函数的核心使用方式

### 函数的定义与调用

函数是为了实现某个功能而组织起来、可重复使用的代码段。

#### 定义函数

```python
def 函数名(参数):
    代码1
    代码2
```

#### 调用函数

```python
函数名(参数)
```

#### 注意

- 参数可以有，也可以没有
- Python 中函数必须先定义后使用

### 函数参数

#### 位置参数（必要传参）

调用函数时，按参数顺序传值，顺序和个数都必须一致。

#### 关键字参数

通过 `键=值` 形式传参，可读性更高。

注意：如果同时使用位置参数和关键字参数，位置参数必须放在关键字参数前面。

#### 默认参数

定义函数时为参数提供默认值；调用时可以不传这个参数。

#### 不定长参数

适用于参数个数不固定的场景。

类型包括：
- 不定长普通参数
- 不定长关键字参数

### 返回值

`return` 用于返回函数结果，并结束函数执行。

补充：
- 默认返回值为 `None`
- `return a, b` 实际上默认返回一个元组
- 也可以返回列表、元组、字典等对象

### 局部变量与全局变量

#### 局部变量

在函数内部定义，只能在函数内部使用。

#### 全局变量

定义在函数外部，在整个文件范围内可用。

如果函数内部要修改全局变量，需要使用 `global`。

### 总结

函数的核心价值：
- 抽离重复逻辑
- 提高代码复用性
- 让代码结构更清晰

常见知识点：
- 定义函数
- 调用函数
- 参数类型
- 返回值
- 变量作用域

#### 易错题

```python
def test(a, lst1=[1, 2]):
    if a not in lst1:
        lst1.append(a)
    return lst1
```

多次调用时，要注意默认参数是可变对象时带来的影响。

## 第八节：Python 的内置容器

> 本节要点
> - 理解容器的概念
> - 掌握字典的结构与常见操作
> - 了解集合的特点
> - 对常见内置容器做整体对比

### 容器的概念

容器是能够包含其他对象的数据结构。

常见容器主要包括：
- 序列：如列表、元组等
- 映射：如字典
- 集合：也属于常见容器类型

### 字典（dict）

#### 字典的特点

- 使用大括号 `{}`
- 数据以键值对形式保存
- 键必须唯一
- 键和值之间用冒号分隔

#### 定义字典

```python
dict1 = {'name': '老肖', 'age': 40}
dict2 = {}
dict3 = dict([('name', 'zs'), ('age', 44)])
dict4 = dict(name='zs', age=44)
```

#### 常见操作

- 增 / 改 / 删：
  ```python
  字典[key] = 值
  del d[key]
  ```
- 查找
- `len(dict)`：项数
- `d[k]`：通过键取值
- `k in d`：判断键是否存在

常见方法：
- `clear()`
- `fromkeys()`
- `get()`
- `items()`
- `keys()`
- `values()`
- `pop()`

### 集合（set）

集合是由一系列不重复元素组成的可变散列容器。

#### 创建方式

- `{}`
- `set()`

注意：空集合必须用 `set()`，因为 `{}` 表示空字典。

#### 特点

- 自动去重
- 元素无序
- 不支持下标访问

### 容器总结

- `str`：不可变，序列
- `list`：可变，序列
- `tuple`：不可变，序列
- `dict`：可变，映射 / 散列结构
- `set`：可变，集合 / 散列结构

#### 可变与不可变

- 不可变：如字符串、数字、元组
- 可变：如列表、字典、集合

## 第九节：递归函数和高阶函数

> 本节要点
> - 理解递归函数的特点和适用场景
> - 学会 lambda 匿名函数的基本写法
> - 理解高阶函数的定义
> - 认识 map、reduce、filter、sorted 等常见内置高阶函数

### 递归函数

递归函数是指：在函数内部调用自身，并且必须具备退出条件。

#### 适用场景

- 遍历多层文件夹
- 算法实现，例如快速排序

### 匿名函数 lambda

如果一个函数只有一句表达式，并且需要快速返回结果，可以使用 `lambda`。

基本形式：

```python
lambda 参数列表: 表达式
```

#### 注意事项

- 参数可有可无
- 可以接收任意数量参数
- 只能返回一个表达式的结果
- 直接打印 lambda，得到的是函数对象地址

#### 示例：基础用法

```python
fn1 = lambda a, b: a + b
print(fn1(1, 2))
```

#### 示例：条件判断

```python
fn1 = lambda a, b: a if a > b else b
print(fn1(1000, 500))
```

#### 示例：与排序结合

```python
students = [
    {'name': 'TOM', 'age': 20},
    {'name': 'ROSE', 'age': 19},
    {'name': 'Jack', 'age': 22}
]

students.sort(key=lambda x: x['name'])
students.sort(key=lambda x: x['name'], reverse=True)
students.sort(key=lambda x: x['age'])
```

### 高阶函数

把函数作为参数传入，或者把函数作为返回值返回，这类函数就叫高阶函数。

#### 函数作为参数

```python
def add_num(a, b):
    return abs(a) + abs(b)

def sum_num(a, b, f):
    return f(a) + f(b)
```

#### 函数作为返回值

```python
def sum_fun_b(*args):
    def sum_a():
        a = 0
        for n in args:
            a = a + n
        return a
    return sum_a
```

### Python 内置高阶函数

#### map()

把序列中的每个元素依次交给函数处理，返回迭代器。

```python
list1 = [1, 2, 3, 4, 5]

def func(x):
    return x ** 2

result = map(func, list1)
print(list(result))
```

#### reduce()

常用于累加、累乘等聚合运算，返回单个结果值。

```python
from functools import reduce
list_a = [1, 2, 3, 4, 5]

def fun_b(x, y):
    return x + y

print(reduce(fun_b, list_a))
```

#### filter()

按条件筛选元素，保留返回 `True` 的项。

```python
def not_odd(num):
    return num % 2 == 0

newlist = filter(not_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(list(newlist))
```

#### sorted()

用于排序，可结合 `key` 自定义排序规则。

```python
sorted([36, 5, -12, 9, -21])
sorted([36, 5, -12, 9, -21], key=abs)
sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
```

补充：字符串排序默认按照 ASCII / Unicode 规则比较，因此大小写会影响结果。

## 第十节：文件和目录操作

> 本节要点
> - 理解 I/O 流的概念
> - 掌握文件的读写、指针和关闭操作
> - 学会用 `with` 管理文件资源
> - 了解 `os` 模块的常见文件与目录操作

### I/O 流的概念

流（Stream）可以理解为数据从源头流向目标的通道。

#### 文件流

源或者目标是文件时，就属于文件流。

### 文件的基本操作

#### 打开文件

```python
文件对象 = open(目标文件, 访问模式)
```

#### 读操作

- `read()`
- `readlines()`
- `readline()`

#### 写操作

- `write()`

#### 指针操作

- `seek(偏移量, 起始位置)`
- `tell()`

#### 关闭文件

- `close()`

### with 语句

`with` 适合管理文件、数据库连接、socket 等资源，可在代码结束后自动关闭资源。

```python
with open(file, "w") as f:
    f.write("hello python")
```

### 文件与目录操作

在 Python 中，文件与目录操作常借助 `os` 模块完成。

#### 导入模块

```python
import os
```

#### 常见能力

- 获取系统信息：`os.name`、`os.sep`
- 获取与切换目录：`os.getcwd()`、`os.chdir()`
- 获取环境变量：`os.environ`、`os.getenv()`
- 查看目录内容：`os.listdir()`
- 创建 / 删除目录：`os.mkdir()`、`os.makedirs()`、`os.rmdir()`
- 删除 / 重命名文件：`os.remove()`、`os.rename()`
- 路径处理：`abspath()`、`split()`、`exists()`、`join()`、`dirname()`、`getsize()` 等

### 综合案例

课程给出了“递归拷贝目录中所有 `.py` 文件到指定目录”的案例，属于文件操作与目录遍历的综合练习。

## 第十一节：Python 面向对象编程

> 本节要点
> - 理解类和对象的关系
> - 学会定义类、创建对象、使用 self
> - 掌握魔术方法、类属性、实例属性
> - 理解类方法与静态方法的区别

### 类与对象

面向对象是一种抽象化的编程思想，可以概括为“一切皆对象”。

- **类**：具有相同特征和行为的一类事物的抽象
- **对象**：根据类创建出来的具体实例

### 定义类与创建对象

#### 定义类

```python
class 类名():
    属性1
    属性2
    函数1
    函数2
```

注意：类名要满足标识符规则，并遵循大驼峰命名习惯。

#### 创建对象

```python
对象名 = 类名()
```

创建对象也叫实例化对象。

### self 的含义

`self` 表示当前对象本身。

### 访问对象的属性和方法

#### 类外访问

- `对象名.属性名`
- `对象名.函数名`

#### 类内访问

- `self.属性名`
- `self.函数名`

### 魔术方法

Python 中形如 `__xx__()` 的方法，通常被称为魔术方法。

#### `__init__`

对象初始化方法，创建对象时自动调用。

#### `__str__`

用于控制 `print(对象)` 时显示的内容。

#### `__del__`

对象被删除时可能触发。

### 类属性与实例属性

- **类属性**：类本身拥有，所有实例共享
- **实例属性**：每个对象独立拥有

#### 类属性示例

```python
class Person(object):
    type_name = '人类'
```

### 类方法与静态方法

#### 类方法

使用 `@classmethod` 修饰，第一个参数通常写作 `cls`。

#### 静态方法

使用 `@staticmethod` 修饰，不依赖实例对象，也不依赖类对象。

#### 适用场景

当方法中既不需要实例信息，也不需要类信息时，可以定义为静态方法。

## 第十二节：继承与重写

> 本节要点
> - 理解继承的基本含义
> - 掌握单继承与多继承
> - 理解方法重写与 `super()` 的作用
> - 了解私有属性与面向对象三大特性

### 继承

继承表示类与类之间的所属关系，子类可以继承父类的属性和方法。

在 Python 中，所有类默认继承 `object`。

### 单继承

```python
class Fruit:
    color = '绿色'

class Apple(Fruit):
    color = '红色'
```

单继承的核心价值在于代码复用。

### 方法重写

当父类的方法不完全适合子类时，可以在子类中重新定义同名方法，这就是重写。

#### super() 的作用

`super()` 用于在子类中调用父类的方法，有助于在扩展原有逻辑时复用父类实现。

#### 扩展父类方法的基本思路

- 在子类中重写父类方法
- 在需要的位置调用 `super().父类方法`
- 再补充子类自己的业务逻辑

### 多继承

Python 支持多继承。

当查找方法时，会按照方法解析顺序（MRO）查找。

```python
类名.__mro__
```

### 私有属性和私有方法

通过在属性名或方法名前加双下划线 `__`，可以形成私有访问约束。

通常会配合：
- `get_xx()`：获取属性
- `set_xx()`：修改属性

### 面向对象三大特性

- 封装
- 继承
- 多态

## 第十三节：模块与包

> 本节要点
> - 理解模块与包的概念
> - 学会导入模块的常见方式
> - 了解自定义模块与 `__all__`
> - 学会安装第三方库

### 模块

模块就是一个以 `.py` 结尾的 Python 文件，模块中可以包含函数、类、变量以及可执行代码。

### 导入模块的常见方式

- `import 模块名`
- `from 模块名 import 函数名`
- `from 模块名 import *`
- `import 模块名 as 别名`
- `from 模块名 import 函数名 as 别名`

### 自定义模块

每个 Python 文件都可以视作一个模块，模块名就是文件名。

#### 注意

自定义模块名必须符合标识符命名规则。

#### 定义模块

```python
def test1(a, b):
    print(a + b)
```

#### 测试模块

如果测试代码直接写在模块文件里，那么其他文件导入时也会一起执行。

#### 正确写法

```python
if __name__ == '__main__':
    test1(10, 10)
```

#### 调用模块

```python
import my_module1
my_module1.test1(1, 1)
```

#### 导入注意事项

- 同名功能以后导入的为准
- Python 会按搜索路径查找模块
- 自己的文件名不要与已有模块重名

#### __all__

当使用 `from xxx import *` 时，`__all__` 可控制允许导入的名称。

### 包

包体现了模块的结构化管理方式，本质上是多个相关模块的组合。

通常包目录中会有 `__init__.py` 文件，用于控制包的导入行为。

#### 导入包中的模块

```python
import 包名.模块名
from 包名.模块名 import 目标
```

### 安装第三方库

#### 常用命令

```bash
pip install 库名字
pip install 库名字==1.0.4
pip install opencv-python -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

#### 常见镜像源

- 中科大：<https://pypi.mirrors.ustc.edu.cn/simple/>
- 豆瓣：<https://pypi.douban.com/simple/>
- 清华：<https://pypi.tuna.tsinghua.edu.cn/simple/>
- 阿里云：<http://mirrors.aliyun.com/pypi/simple/>

补充：
- `pip list`：查看已安装库
- `pip list -o`：查看可升级版本

## 第十四节：异常与 Debug 调试

> 本节要点
> - 理解异常的含义
> - 掌握 try / except / else / finally
> - 学会使用 raise 和自定义异常
> - 了解 PyCharm 中的基本调试操作

### 异常

程序运行期间检测到的错误，称为异常。异常不一定导致严重后果，但如果没有处理，可能会导致程序中断。

常见异常示例：
- `ZeroDivisionError`
- `NameError`
- `TypeError`

### 捕获异常

基本结构：

```python
try:
    可能发生错误的代码
except 异常类型1:
    处理代码
except 异常类型2:
    处理代码
```

#### 工作流程

- 先执行 `try` 中代码
- 如果没有异常，跳过 `except`
- 如果有异常且匹配成功，就执行对应 `except`
- 如果没有匹配处理器，异常继续向外抛出

### 强制抛出异常：raise

`raise` 可以主动触发指定异常。

### else 与 finally

- `else`：没有异常时执行
- `finally`：无论是否异常都会执行，通常用于资源清理

### 自定义异常

自定义异常一般继承 `Exception`。

示例：

```python
class TooShortInputError(Exception):
    ...
```

### Debug 调试

PyCharm 调试时，断点会让程序执行到指定位置后暂停，方便观察变量状态和执行流程。

#### 常见操作

- 显示执行位置
- `step over (F8)`
- `step into (F7)`
- `step into my code`
- `force step into`
- `step out`
- `run to cursor`

## 第十五节：企业员工管理系统

> 本节要点
> - 理解项目需求与功能范围
> - 分析系统中的核心对象
> - 了解员工类与管理系统类的职责
> - 阅读项目代码结构

### 项目需求

项目目标：使用面向对象编程思想完成员工管理系统。

#### 功能要求

- 员工数据存储在文件中
- 员工信息包括：姓名、年龄、性别、手机号、是否离职
- 系统功能包括：
  - 添加员工
  - 删除员工
  - 修改员工信息
  - 查询员工信息
  - 显示所有员工
  - 保存员工信息
  - 退出系统

### 项目结构分析

系统中的核心对象：
- 员工：对应 `Employee` 类
- 员工管理系统：对应 `EmployeeManagerSystem` 类

### 员工对象设计

#### 员工的特征

- 姓名
- 年龄
- 性别
- 手机号码
- 是否离职

#### 员工的行为

- 输出员工整体信息
- 转换为字典结构

### 员工管理系统设计

#### 主要行为

- 加载和读取员工数据文件
- 显示系统欢迎界面
- 添加员工
- 删除员工
- 修改员工信息
- 查询员工信息
- 展示全部员工
- 保存员工信息
- 退出系统
- 程序入口控制

### 代码实现

#### employee.py

```python
class Employee(object):
    """员工类"""

    def __init__(self, name, gender, age, mobile_number, is_leave=0):
        self.name = name
        self.age = age
        self.gender = gender
        self.mobile_number = mobile_number
        self.is_leave = False if is_leave == 0 else True

    def __str__(self):
        msg = '离职' if self.is_leave else '在职'
        return f'{self.name}\t{self.age}\t{self.gender}\t{self.mobile_number}\t{msg}'
```

#### employee_manager_system.py

```python
from employee import Employee
import os

class EmployeeManagerSystem(object):
    def __init__(self):
        self.employee_data_file = 'employee.data'
        self.employee_data_file_backup = 'employee.data.backup'
        self.employee_list = []
        self.save_flag = True

    def main(self):
        self.load_employee()
        while True:
            self.show_hello()
            menu_number = int(input('请输入你需要的功能编号:'))
            if menu_number == 7:
                self.go_out()
                break
            elif menu_number == 1:
                self.add_employee()
            elif menu_number == 2:
                self.del_employee()
            elif menu_number == 3:
                self.update_employee()
            elif menu_number == 4:
                self.search_employee()
            elif menu_number == 5:
                self.show_all_employee()
            elif menu_number == 6:
                self.save_employee()
```

#### 功能要点总结

- 通过文件保存员工数据
- 通过对象列表管理员工信息
- 支持增删改查与保存
- 退出前可根据状态自动保存
