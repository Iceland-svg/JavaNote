`@Transactional` 是 **Spring 声明式事务管理**的核心注解。它并非校验数据的，而是**保证数据一致性（ACID）**的利器——让你只需在方法上标注，即可自动管理数据库事务的开启、提交或回滚，彻底告别手写 `begin/commit/rollback` 的样板代码。

> **补充说明**：`@Transactional()` 括号留空时，表示使用默认的事务配置（Propagation.REQUIRED、Isolation.DEFAULT、回滚仅限运行时异常）。

它的底层依赖 **AOP（动态代理）**。调用带注解的方法时，Spring 会拦截调用，在目标方法执行前开启事务，执行后提交事务，若发生异常则按规则回滚。

---

### 核心属性解析（括号里的配置）

| 属性 | 作用 | 常用值/说明 |
| :--- | :--- | :--- |
| **`propagation`** | 事务传播行为（方法间调用时，事务如何传递） | `REQUIRED`（默认，支持当前事务，若无则新建）、`REQUIRES_NEW`（挂起当前，新建独立事务）、`NESTED`（嵌套事务，Savepoint机制） |
| **`isolation`** | 事务隔离级别（解决脏读、不可重复读、幻读） | `DEFAULT`（默认，跟随数据库）、`READ_COMMITTED`（防脏读，Oracle默认）、`REPEATABLE_READ`（防脏读/不可重复读，MySQL默认）、`SERIALIZABLE`（串行化，性能极低） |
| **`timeout`** | 事务超时时间（秒） | 默认依赖事务管理器，超过时间未完成则自动回滚 |
| **`readOnly`** | 是否为只读事务 | `true`（仅查询时开启，可优化性能，Hibernate下会Flush模式设为NEVER） |
| **`rollbackFor`** | 指定**哪些异常**触发回滚 | **极其重要**！类型为 `Class<? extends Throwable>[]` |
| **`noRollbackFor`** | 指定**哪些异常**不回滚 | 极少使用，用于特殊业务场景 |

---

### 必须死记的默认回滚规则（新手必坑）
**默认情况下（括号留空时）**，Spring 只会在抛出 **`RuntimeException`（运行时异常）** 或 **`Error`（错误）** 时回滚，而 **`Exception`（受检异常，如 IOException、SQLException）不会回滚**！

```java
// ❌ 危险写法：抛出受检异常，数据会提交（不会回滚）
@Transactional
public void updateData() throws Exception {
    dao.insertA(); // 插入成功
    throw new Exception("出错了"); // 事务不回滚！A永久入库
}

// ✅ 安全写法：强制指定任何异常都回滚
@Transactional(rollbackFor = Exception.class)
public void updateData() throws Exception {
    dao.insertA();
    throw new Exception("出错了"); // 这次会回滚
}
```
> **生产强制规范**：绝大部分项目都会统一在配置或父类中指定 `rollbackFor = Exception.class`，以确保所有异常都触发回滚。

---

### 头号大坑：自调用（Self-Invocation）失效问题
既然 `@Transactional` 依赖代理，若在**同一个类内部**直接调用（`this.method()`），调用将绕过代理对象，**注解完全失效**！

```java
@Service
public class UserService {
    
    public void methodA() {
        // ❌ 直接调用，@Transactional 不生效！因为没走代理
        this.methodB(); 
    }

    @Transactional
    public void methodB() {
        // 插入数据库操作
    }
}
```

**三种解决方式**：
1. **注入自身**（最推荐）：`@Autowired private UserService self;` 然后调用 `self.methodB()`。
2. **AopContext 强制获取代理**：`((UserService) AopContext.currentProxy()).methodB()`（需开启 `@EnableAspectJAutoProxy(exposeProxy = true)`）。
3. 将 `methodB` 抽取到另一个 Service 类中。

---

### 传播行为详解（`propagation` 选型指南）
| 传播级别 | 核心逻辑 | 实际应用场景 |
| :--- | :--- | :--- |
| **REQUIRED**（默认） | 有事务则加入，无则新建 | **绝大多数增删改方法** |
| **REQUIRES_NEW** | **永远新建独立事务**，外部异常不影响内部提交 | 记录**操作日志**、**审计日志**（即使主业务报错，日志也要存进去） |
| **NESTED** | 嵌套事务，内层回滚仅回滚到 Savepoint（外层可捕获继续执行） | 复杂业务流程中的**子步骤回滚**（底层依赖 JDBC Savepoint） |
| SUPPORTS | 有事务则加入，无则以非事务执行 | **查询方法**（若只读且允许非事务） |

---

### 最佳实践与避坑指南

1. **放在 Service 层，而非 Controller 层**：事务应包裹完整的业务逻辑（含多个 Dao 操作），Controller 只负责接收参数。
2. **尽量让方法 public**：代理机制默认只能拦截 `public` 方法（`protected/private` 不会生效，除非开启 AspectJ 织入）。
3. **结合 `@Transactional(readOnly = true)` 优化查询**：在查询方法上标记只读，Hibernate 会跳过脏检查，显著提升性能。
4. **谨慎使用 REQUIRES_NEW**：注意 **连接池耗尽**风险，因为它会占用新的数据库连接。
5. **注意锁与隔离级别**：高并发场景下，`SERIALIZABLE` 虽安全但极慢，通常靠**乐观锁（版本号）** 替代。

---

### 与前文 Validation 注解的关系
- **`@NotNull/NotBlank/NotEmpty`**：负责**业务规则校验**（数据格式对不对），发生在事务开启**之前**（Controller层）。
- **`@Transactional`**：负责**数据一致性保障**（操作完是否原子），发生在事务执行**之中**（Service层）。

如果你在 `@Transactional` 方法中**手动捕获异常（try-catch）**而没有重新抛出，事务管理器检测不到异常，也会**提交成功**——这是另一个经典陷阱。如果遇到这种情况，记得在 catch 块中手动调用 `TransactionAspectSupport.currentTransactionStatus().setRollbackOnly()` 强制回滚。

如果你需要针对某个特定场景（比如嵌套事务 + 异常捕获）的代码示例，可以随时告诉我，我帮你写一段可运行的 Demo。😊