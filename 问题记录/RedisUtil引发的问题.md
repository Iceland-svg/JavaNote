这个问题的核心在于**依赖管理与对象职责**的区别。下面分点解释：

---

### 1. 为什么 `RedisUtil` 需要交给 Spring 管理？
`RedisUtil` 通常封装了对 Redis 的操作（如 `set`、`get`），其内部需要依赖 `RedisTemplate` 或 `StringRedisTemplate`。  
`RedisTemplate` 是 Spring 容器中的 Bean，因此：
- 要让 `RedisUtil` 能**自动注入** `RedisTemplate`，它自己必须也是 Spring 管理的 Bean。
- 如果不交给 Spring，你需要手动创建 `RedisUtil` 并传入 `RedisTemplate`，增加耦合和样板代码。

```java
@Component
public class RedisUtil {
    @Autowired
    private RedisTemplate<String, Object> redisTemplate; // 依赖容器中的Bean
    
    public void set(String key, Object value) {
        redisTemplate.opsForValue().set(key, value);
    }
}
```

---

### 2. 为什么其他工具类（如 `DateUtils`、`StringUtils`）一般不需要交给 Spring？
这些工具类通常是**无状态**的，所有方法都是 `static` 的，不依赖任何 Spring Bean，也不需要在容器中作为单例保存。  
例如：
```java
public class DateUtils {
    public static String format(Date date) {
        // 无外部依赖，纯计算
    }
}
```
- 直接 `DateUtils.format(new Date())` 调用即可，**无需创建实例**。
- 交给 Spring 反而增加无谓的 Bean 定义，浪费内存。

---

### 3. 是不是交给 Spring 管理的类都需要注入？其他类不需要注入？
- **交给 Spring 管理的类，不一定都需要注入其他 Bean**。  
  有些 Bean 只是单纯为了统一配置或生命周期管理（如 `@PostConstruct` 初始化资源），不注入任何依赖也是允许的。
- **不被 Spring 管理的类，也可以使用注入**？不能直接使用 `@Autowired`。  
  但你可以通过 `ApplicationContext.getBean()` 获取 Spring Bean，不过这样会侵入代码，不推荐。

**注入的本质**：只有 Spring 容器中的 Bean 才能享受自动注入（`@Autowired`、构造器注入等）。普通 Java 对象无法自动感知 Spring 容器。

---

### 4. 交给 Spring 管理 vs 不交给 Spring 管理的区别

| 维度 | 交给 Spring 管理（`@Component`/`@Bean`） | 不交给 Spring 管理 |
|------|-------------------------------------------|--------------------|
| **依赖注入** | 可以使用 `@Autowired` 获取其他 Bean | 无法自动注入，需手动传参或从 `ApplicationContext` 获取 |
| **生命周期控制** | 受 Spring 管理（初始化、销毁回调、作用域等） | 完全由使用者控制（`new` 或静态调用） |
| **单例保证** | 默认单例，由 Spring 容器保证全局唯一 | 需自己实现单例模式（如 `static final`） |
| **AOP 支持** | 可应用事务、日志、缓存等切面 | 无法享受 Spring AOP（除非通过代理手动调用） |
| **测试友好性** | 可使用 `@MockBean` 轻松替换 | 静态方法难以 Mock，常需 PowerMock 或重构 |
| **资源占用** | 多一个 Bean 定义，但通常可忽略 | 无额外 Bean 开销 |

---

### 5. 典型取舍建议

- **需要被注入其他 Bean、需要 AOP、需要管理生命周期** → 交给 Spring。
- **纯函数、无状态、无依赖、不涉及多态** → 保持静态工具类，不交给 Spring。
- **介于两者之间**：如果工具类未来可能扩展为需要注入配置或替换实现，可以提前交给 Spring，但通常没必要。

**结论**：`RedisUtil` 因依赖 `RedisTemplate` 而必须被 Spring 管理；普通工具类无依赖且常为静态方法，无需交给 Spring。这不是“必须注入”的规则，而是“有依赖就需要被管理”的自然结果。