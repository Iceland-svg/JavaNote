
## 线程安全的单例模式（双重检查锁定 DCL）

### 标准代码

```java
public class Singleton {
    // 1. 必须用 volatile 修饰
    private static volatile Singleton instance;

    // 2. 构造方法私有化
    private Singleton() {}

    // 3. 对外提供获取方法
    public static Singleton getInstance() {
        if (instance == null) {                    // 第一次检查（无锁，快）
            synchronized (Singleton.class) {       // 类锁
                if (instance == null) {            // 第二次检查（有锁，安全）
                    instance = new Singleton();    // 真正创建
                }
            }
        }
        return instance;
    }
}
```

### 执行逻辑

- **第一次检查**：`instance` 不为 null，直接返回，省去加锁开销（绝大多数调用走这条快路）。
- **加锁**：可能有多线程同时发现 `instance == null`，竞争锁。
- **第二次检查**：抢到锁的线程再判断一次，防止在它创建之前别的线程已经创建好了。

---

## 为什么 instance 必须用 volatile？

**核心原因：`new Singleton()` 不是原子操作**，它分三大步：

| 步骤 | 伪代码 | 说明 |
|------|--------|------|
| 1. 分配内存 | `memory = allocate()` | 在堆上开辟空间 |
| 2. 初始化对象 | `init(memory)` | 执行构造方法，给属性赋值 |
| 3. 指向引用 | `instance = memory` | 让 instance 指向分配的内存 |

**问题**：JVM 的指令重排可能导致步骤 **3 在 2 之前执行**。

于是可能出现这种情况：
1. 线程 A 执行到 `new Singleton()`，先执行了步骤 3（引用指向了未初始化的对象），还没执行步骤 2。
2. 此时线程 B 走到第一次检查 `instance == null`，发现 **instance 已经有引用了（不是 null）**，直接返回使用。
3. 线程 B 拿到的是一个**半成品对象**，内部属性都是 null 或默认值，使用时就会出问题。

**volatile 的作用就是禁止这种指令重排**，保证步骤 2 一定在步骤 3 之前完成，让其他线程永远不会看到未初始化完毕的对象。

---

### 面试话术总结

> “双重检查锁定通过两次 null 判断加一次加锁，兼顾了安全和性能。`volatile` 是为了禁止 `new` 过程中的指令重排，防止线程拿到半初始化的对象，保证对象创建的安全发布。


