# TransmittableThreadLocal

## 一、TransmittableThreadLocal介绍

TransmittableThreadLocal（TTL）是阿里巴巴开源的一个线程池场景下的ThreadLocal，他实现的效果是将 **提交任务的线程** 中的ThreadLocal里的数据传输到 **执行任务的线程** 里。TransmittableThreadLocal本质是基于InheritableThreadLocal去实现的。

* ThreadLocal只能再当前线程之间传递数据。
* InheritableThreadLocal可以实现在父子线程之间传递数据。
* TransmittableThreadLocal可以满足上述两个套路，同时可以完成线程池中线程的数据传递。

官方地址：[https://github.com/alibaba/transmittable-thread-local](https://github.com/alibaba/transmittable-thread-local)

官方介绍：

`ThreadLocal`的需求场景即 `TransmittableThreadLocal`的潜在需求场景，如果你的业务需要 **『在使用线程池等会池化复用线程的执行组件情况下传递 `ThreadLocal`值』** 则是 `TransmittableThreadLocal`目标场景。

下面是几个典型场景例子。

1. 分布式跟踪系统 或 全链路压测（即链路打标）
2. 日志收集记录系统上下文
3. `Request`级 `Cache`
4. 应用容器或上层框架跨应用代码给下层 `SDK`传递信息

## 二、TransmittableThreadLocal基本应用

TTL的应用是非常简单的，就两个操作，写，读。本质依然是业务线程正常使用TTL去存储数据，然后执行异步任务的线程可以直接基于TTL获取业务线程存储的数据。

优先导入对应的依赖

```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>transmittable-thread-local</artifactId>
        <version>2.14.5</version>
    </dependency>
</dependencies>
```

使用就非常的简单，跟使用TL，ITL几乎没区别。

不过需要在提交任务给线程池时，将任务包装好

* 主动直接 **包装任务**
* 主动包装线程池，包装后的线程池会帮你 **包装任务** 。

```java
static ThreadLocal<String> tl = new ThreadLocal<>();

static InheritableThreadLocal itl = new InheritableThreadLocal();

static TransmittableThreadLocal ttl = new TransmittableThreadLocal();



public static void main(String[] args) throws InterruptedException {
    ExecutorService executor = Executors.newFixedThreadPool(1);
    ExecutorService ttlExecutor = TtlExecutors.getTtlExecutorService(executor);

    new Thread(() -> {
        executor.execute(() -> {
            System.out.println("初始化线程池里的工作线程");
        });
    }).start();
    Thread.sleep(1);


    tl.set("tl");
    itl.set("itl");
    ttl.set("ttl");
    // 当前线程
    System.out.println("当前线程tl：" + tl.get());
    System.out.println("当前线程itl：" + itl.get());
    System.out.println("当前线程ttl：" + ttl.get());
    // 子线程
    Thread t = new Thread(() -> {
        System.out.println("子线程：" + tl.get());
        System.out.println("子线程：" + itl.get());
        System.out.println("子线程：" + ttl.get());

    });
    t.start();

    // 线程池线程
    // TTL传递数据，需要对任务做一层包装
    executor.execute(TtlRunnable.get(() -> {
        System.out.println("工作线程：" + tl.get());
        System.out.println("工作线程：" + itl.get());
        System.out.println("包装任务：工作线程：" + ttl.get());
    }));
    ttlExecutor.execute(() -> {
        System.out.println("工作线程：" + tl.get());
        System.out.println("工作线程：" + itl.get());
        // 包装线池的本质，依然是包装任务。
        System.out.println("包装线程池：工作线程：" + ttl.get());
    });
}
```

## 三、TransmittableThreadLocal底层源码

点到TransmittableThreadLocal内部后，发现他继承了InheritableThreadLocal。

同时InheritableThreadLocal继承了ThreadLocal，咱一步一步来。

### 3.1 ThreadLocal 底层（常识）

ThreadLocal本质其实就是一个key。正常存储数据的是Thread中的一个Map。

```java
/* Thread中真正存储数据的位置 */
ThreadLocal.ThreadLocalMap threadLocals = null;
```

进到ThreadLocalMap内部，可以发现他就是一个Entry数组，ThreadLocal是作为这个Entry的key存在，存储的数据是正常的value

```java
static class ThreadLocalMap {

   static class Entry extends WeakReference<ThreadLocal<?>> {
       Object value;

       Entry(ThreadLocal<?> k, Object v) {
           super(k);
           value = v;
       }
   }
   // 省略部分代码
}
```

### 3.2  InheritableThreadLocal 底层

实现父子线程之间的数据传递。

父线程在创建子线程的时候，将父线程里的itl中的数据复制到子线程的itl里。

在创建Thread时，必然要走new Thread的过程，在newThread中，存在一个init方法的初始化过程

```java
private void init(ThreadGroup g, Runnable target, String name,
                 long stackSize, AccessControlContext acc,
                 boolean inheritThreadLocals) {
    // 省略部分代码
    // inheritThreadLocals 默认就是true
    // parent.inheritableThreadLocals != null 确认父线程是否向itl中存放了数据
    if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        // 到这，说明父线程的itl有数据，需要复制到子线程的itl中
        this.inheritableThreadLocals =
            // 这部分本质就是把父线程里的itl的Map数据遍历，扔到子线程的itl里的Map中
            ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
    // 省略部分代码
}
```

### 3.3 TransmittableThreadLocal底层

TTL的源码很乱，因为他依然再更新（虽然更新不频繁），要提供很多新功能。。。

#### 3.3.1 TTL存储结构

基于TTL的set、get、remove方法可以直接进入到他的读写删除的操作逻辑。

除了针对父类功能的提供之外，自己本质是将数据写入到了holder的属性中。

**holder** 类型是itl，但是本质存储数据的依然是一个 **HashMap** 。

TTL的数据传递，不走ThreadLocal以及InheritableThreadLocal的Map，他自己搞了一个Map。

```java
private static final InheritableThreadLocal<WeakHashMap<TransmittableThreadLocal<Object>, ?>> holder =
        new InheritableThreadLocal<WeakHashMap<TransmittableThreadLocal<Object>, ?>>() {
            // 省略初始化过程
        };

// 写入数据
private void addThisToHolder() {
    // 拿到holder里的HashMap，将当前数据写入
    if (!holder.get().containsKey(this)) {
        holder.get().put((TransmittableThreadLocal<Object>) this, null); // WeakHashMap supports null value.
    }
}
```

#### 3.3.2 任务增强底层

直接看任务增强底层，因为增强线程池本质也是在对任务增强。

直接看到new TtlRunnable的过程，内部有两处核心

* capture方法，捕获了当前线程TTL中的数据，存储到了TtlRunnable
* 将runnable任务正常存储到TtlRunnable

```java
private final AtomicReference<Object> capturedRef;
private final Runnable runnable;
private final boolean releaseTtlValueReferenceAfterRun;

private TtlRunnable(@NonNull Runnable runnable, boolean releaseTtlValueReferenceAfterRun) {
    // 捕获当前线程的TTL中的数据。
    this.capturedRef = new AtomicReference<>(capture());
    // 任务的赋值。
    this.runnable = runnable;
    this.releaseTtlValueReferenceAfterRun = releaseTtlValueReferenceAfterRun;
}
```

#### 3.3.3 capture捕获

在对任务增强时，第一步就是走了capture捕获当前线程的TTL中的数据。

直接在TtlRunnable进去到capture方法时，这里的方法是宏观的处理，里面包含了TTL数据的捕获。

他希望其他的工具想基于TTL传递数据时，可以自己去继承Transmitter类，重写各种方法。

在当前的TTL中，只有一个ttlTransmittee的重写

真正要看TTL数据捕获的过程，要看ttlTransmittee内部的capture方法。

```java
// 方便查看，省略了一堆泛型~~~~
private static final Transmittee ttlTransmittee = new Transmittee(){
    // 这里重写了capture，replay，restore，clear。
}
```

在ttlTransmitte中的capture方法，就是将当前线程中holder的数据封装成一个新的HashMap返回

```java
public HashMap<TransmittableThreadLocal<Object>, Object> capture() {
    final HashMap<TransmittableThreadLocal<Object>, Object> ttl2Value = newHashMap(holder.get().size());
    for (TransmittableThreadLocal<Object> threadLocal : holder.get().keySet()) {
        // copyValue做的是浅拷贝，如果传递引用数据类型，子线程改了，父线程也一起动。
        ttl2Value.put(threadLocal, threadLocal.copyValue());
    }
    return ttl2Value;
}
```

回到宏观的Transmitteer后，将ttlTransmitte中返回的HashMap，又套到了一个HashMap。

虽然一层套一层，但是最后当前线程的TTL中的holder数据确确实实写到了capturedRef这个属性中，在任务执行的时候，可以获取到。

#### 3.3.4 replay重放

在前面完成capture之后，任务开始执行。

任务执行时，执行任务的已经是工作线程了。。。。。

在做replay时，会返回backup，备份数据的内容，而且这个内容一会在restore会涉及到。

发现进到replay方法后，依然是宏观的处理，咱们也是针对性的只看ttlTransmittee中的实现。

这里主要做了两个事情：

* 备份工作线程中之前holder里存储的全部数据。并且移除一些capture中不存在的。
* 将capture中的数据，全部写入到工作线程的holder里。

```java
@Override
public HashMap<TransmittableThreadLocal<Object>, Object> replay
                                        (@NonNull HashMap<TransmittableThreadLocal<Object>, Object> captured) {

    // 声明HashMap，这个叫back，备份数据
    final HashMap<TransmittableThreadLocal<Object>, Object> backup = newHashMap(holder.get().size());

    // 循环拿到 holder 中的数据
    for (final Iterator<TransmittableThreadLocal<Object>> iterator = holder.get().keySet().iterator(); iterator.hasNext(); ) {
        TransmittableThreadLocal<Object> threadLocal = iterator.next();

        // 将holder的数据扔到backup这个集合中。 （备份）
        backup.put(threadLocal, threadLocal.get());

        // 如果capture不包含这个数据，需要将这个数据移除掉，避免污染此次任务。
        // （capture没有的，就把他移除掉，反正前面备份好了。。。）
        if (!captured.containsKey(threadLocal)) {
            // 移除任务从holders中
            iterator.remove();
            // 也要从ITL，TL中移除。
            threadLocal.superRemove();
        }
    }

    // 将capture中的数据，写入到当前线程的holder里。
    setTtlValuesTo(captured);

    // 钩子函数，你可以自己重写。
    doExecuteCallback(true);

    return backup;
}
```

#### 3.3.5 restore恢复

恢复操作其实就是在任务执行完毕后，将当前工作线程里的holder，恢复到执行任务之前的效果。

```java
@Override
public void restore(@NonNull HashMap<TransmittableThreadLocal<Object>, Object> backup) {
    // 钩子函数~~~
    doExecuteCallback(false);

    // 遍历当前线程的holder，此时，holder中存储的是main线程提供的holder中的数据
    for (final Iterator<TransmittableThreadLocal<Object>> iterator = holder.get().keySet().iterator(); iterator.hasNext(); ) {
        TransmittableThreadLocal<Object> threadLocal = iterator.next();

        // 如果备份中不包含这个数据
        if (!backup.containsKey(threadLocal)) {
            // 额外清除这个数据
            iterator.remove();
            // 清楚itl，跟tl里的这个数据
            threadLocal.superRemove();
        }
    }

    // 批量的将之前备份的数据写回到当前线程
    setTtlValuesTo(backup);
}
```

整个过程完毕，有同学会考虑一个事，其实这里代码不需要这么复杂，工作线程执行完任务，直接clear不就可以了嘛，这里还多了备份跟恢复的过程，好像多次一举。

其实不是的，如果你把任务提交给线程池之后，如果线程池触发了拒绝策略，拒绝策略走的是CallerRuns，会直接导致你最后的clear操作，清空了你自己的main线程中的数据。

#### 3.3.6 完整流程图如下

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260513231257910.png)
