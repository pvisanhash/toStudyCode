# Arthas 快速入门简要手册

## 1. Arthas 是什么

Arthas 是 Alibaba 开源的 Java 诊断工具，可以在不重启应用的情况下，动态查看 JVM、线程、类加载、方法入参、返回值、异常、调用链和性能耗时。

官网:https://arthas.aliyun.com/

常见用途：

- 排查线上接口异常
- 确认 JVM 中实际加载的类和代码
- 查看方法入参、返回值、异常
- 分析慢方法、CPU 高、线程阻塞
- 临时调整日志级别

> 命令比较复杂,推荐使用arthas插件以及AI辅助

## 2. 启动方式

下载并启动：

```bash
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

启动后选择目标 Java 进程即可进入 Arthas Shell。

退出当前会话：

```bash
quit
```

停止 Arthas 服务：

```bash
stop
```

## 3. 快速查看 JVM 状态

查看实时面板：

```bash
dashboard
```

查看 JVM 信息：

```bash
jvm
```

查看内存：

```bash
memory
```

查看最忙的线程：

```bash
thread -n 5
```

查看指定线程堆栈：

```bash
thread <线程ID>
```

## 4. 查类和方法

查看 JVM 是否加载了某个类：

```bash
sc com.example.UserService
```

查看类的详细信息：

```bash
sc -d com.example.UserService
```

模糊搜索类：

```bash
sc *UserService*
```

查看类的方法：

```bash
sm com.example.UserService
```

查看方法详细签名：

```bash
sm -d com.example.UserService
```

## 5. 反编译线上代码

反编译指定类：

```bash
jad com.example.UserService
```

反编译指定方法：

```bash
jad com.example.UserService getUser
```

只输出源码：

```bash
jad --source-only com.example.UserService
```

适合排查：

- 发布版本是否正确
- 线上代码是否和本地一致
- 依赖冲突
- ClassLoader 加载异常

## 6. 查看方法入参、返回值和异常

查看入参：

```bash
watch com.example.UserService getUser 'params' -n 5 -x 2
```

查看返回值：

```bash
watch com.example.UserService getUser 'returnObj' -n 5 -x 2
```

同时查看入参、返回值、异常和耗时：

```bash
watch com.example.UserService getUser '{params, returnObj, throwExp, #cost}' -n 5 -x 3
```

只看异常调用：

```bash
watch com.example.UserService getUser '{params, throwExp}' 'throwExp != null' -n 5 -x 3
```

只看慢调用：

```bash
watch com.example.UserService getUser '{params, returnObj, #cost}' '#cost > 100' -n 5 -x 3
```

## 7. 分析方法耗时

查看方法内部调用耗时：

```bash
trace com.example.UserService getUser
```

只看耗时超过 200ms 的调用：

```bash
trace com.example.UserService getUser '#cost > 200' -n 5
```

`trace` 常用于定位慢接口里具体慢在哪个方法。

## 8. 查看方法调用来源

查看是谁调用了某个方法：

```bash
stack com.example.UserService getUser -n 5
```

带条件查看：

```bash
stack com.example.UserService getUser 'params[0] == 1001' -n 5
```

## 9. 统计方法调用情况

每 5 秒统计一次方法调用情况：

```bash
monitor -c 5 com.example.UserService getUser
```

可以看到：

- 调用次数
- 成功次数
- 失败次数
- 平均耗时
- 失败率

## 10. 记录调用现场

记录方法调用：

```bash
tt -t com.example.UserService getUser -n 5
```

查看记录列表：

```bash
tt -l
```

查看某次调用详情：

```bash
tt -i <index>
```

重新执行某次调用：

```bash
tt -i <index> -p
```

注意：`tt -p` 会重新执行方法，涉及写库、扣款、发消息、改库存等操作时不要在线上随意使用。

## 11. 查看和修改日志级别

查看 logger：

```bash
logger
```

查看指定 logger：

```bash
logger -n com.example
```

临时修改日志级别：

```bash
logger --name com.example.UserService --level DEBUG
```

排查结束后建议恢复原日志级别。

## 12. 常见排查套路

### 接口返回不对

```bash
sc -d com.example.UserService
jad com.example.UserService getUser
watch com.example.UserService getUser '{params, returnObj, throwExp}' -n 5 -x 3
```

### 接口很慢

```bash
monitor -c 5 com.example.UserService getUser
trace com.example.UserService getUser '#cost > 200' -n 5
```

### 不知道方法是谁调用的

```bash
stack com.example.UserService getUser -n 5
```

### 怀疑依赖冲突或类加载异常

```bash
sc -d com.example.SomeClass
classloader -t
jad com.example.SomeClass
```

### 偶现异常

```bash
watch com.example.UserService getUser '{params, throwExp}' 'throwExp != null' -n 5 -x 3
tt -t com.example.UserService getUser -n 10
tt -l
```

## 13. 线上使用安全原则

- 优先指定明确的类名和方法名，避免大范围匹配。
- `watch`、`trace`、`stack`、`monitor`、`tt` 尽量加 `-n` 限制次数。
- 使用条件表达式过滤数据，例如 `#cost > 100` 或 `throwExp != null`。
- `-x` 展开层级不要过大，通常 `-x 2` 或 `-x 3` 足够。
- 排查结束后执行 `reset` 清理增强类。
- 不确定影响时，不要在线上使用 `tt -p` 重放调用。

清理增强：

```bash
reset
```

停止 Arthas：

```bash
stop
```

## 14. 高频命令速记

```bash
dashboard
thread -n 5
sc -d 类名
sm -d 类名
jad 类名 方法名
watch 类名 方法名 '{params, returnObj, throwExp, #cost}' -n 5 -x 3
trace 类名 方法名 '#cost > 100' -n 5
stack 类名 方法名 -n 5
monitor -c 5 类名 方法名
reset
```

一句话记忆：

> `sc` 查类，`sm` 查方法，`jad` 看代码，`watch` 看数据，`trace` 看耗时，`stack` 看来源，`tt` 抓现场，`reset` 做清理。

- sc = search class，查类
- sm = search method，查方法
- jad = Java decompiler，反编译类
- tt = time tunnel，时间隧道
- mc = memory compiler，内存编译器
