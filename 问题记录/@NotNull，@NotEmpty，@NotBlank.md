
这三个注解都来自 **Jakarta Bean Validation**（原 javax.validation）及 **Hibernate Validator**，用于数据校验。它们都**不允许为 null**，但对“空”的定义和适用数据类型截然不同。

为了让你一眼看懂，先看核心对比表：

| 注解              | 核心规则                               | 适用类型                                | 典型“无效”例子                            |
| :-------------- | :--------------------------------- | :---------------------------------- | :---------------------------------- |
| **`@NotNull`**  | **值不能为 `null`**                    | 任何类型（String、集合、Map、数组、Integer、对象等）  | `null`                              |
| **`@NotEmpty`** | **不能为 `null`，且** **长度/大小 > 0**     | **字符串、集合、Map、数组**（有 length/size 属性） | `null`、`""`（空串）、`[]`（空数组）、`{}`（空集合） |
| **`@NotBlank`** | **不能为 `null`，且** **去除首尾空格后长度 > 0** | **仅限 String（字符串）**                  | `null`、`""`、`"   "`（全空格）            |

---

### 1. `@NotNull` —— 最宽松的“非空”
它只检查内存地址是否为空，**不关心内容**。

- **验证逻辑**：`object != null`
- **能通过的值**：`""`（空字符串）、`" "`（空格）、`new ArrayList()`（空集合）、`0`（数字）
- **失效的值**：仅 `null`

### 2. `@NotEmpty` —— 强调“有元素或有字符”
它在 `@NotNull` 的基础上，额外检查长度或大小是否大于 0。

- **验证逻辑**：`object != null && object.length/size > 0`
- **对字符串特例**：`" "`（空格）**能通过**，因为空格也算字符（长度为1）。
- **失效的值**：`null`、`""`、空集合、空数组、空 Map

### 3. `@NotBlank` —— 专门针对“有实际意义的文字”
这是**最严格**的字符串校验，会忽略前后空白字符（trim）。

- **验证逻辑**：`object != null && object.trim().length() > 0`
- **注意**：它**只接受字符串**，不能用在集合或数字上，否则会报类型转换异常。
- **失效的值**：`null`、`""`、`"   "`（全空格）、`" \t\n "`（制表符换行）
- **能通过的值**：`" 你好 "`（因为中间有非空白字符）

---

### 实战代码示例（实体类）
```java
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotEmpty;
import jakarta.validation.constraints.NotNull;
import java.util.List;

public class UserRequest {

    @NotNull(message = "ID不能为null，但可以是0")
    private Long id; // 包装类型必用NotNull

    @NotBlank(message = "用户名必须有实际内容，不能是空格")
    private String username; // 如 "张三" 通过， "   " 不通过

    @NotEmpty(message = "标签集合不能为空，至少有一个元素")
    private List<String> tags; // 如 ["Java"] 通过， [] 不通过

    @NotEmpty(message = "密码不能为空字符串")
    private String password; // 注意：密码如 "  "（空格）会通过，因为NotEmpty不trim
}
```

---

### 生产环境中的最佳选型建议

1.  **字符串必填字段（用户名、标题、名称）**：**首选 `@NotBlank`**。它可以拦截用户输入的空格，保证数据干净。
2.  **集合/Map/数组（列表数据）**：只能用 **`@NotEmpty`**（`NotBlank`不支持）。
3.  **数字类型（Integer, Long, Double）**：只能用 **`@NotNull`**。因为数字没有“空”的概念，只有“是否为0”或“是否为null”。校验值大小用 `@Min`/`@Max`。
4.  **可选的字符串（允许不传，但传了不能为空串）**：组合使用 `@Size(min = 1)`，或者不加校验（由业务逻辑处理）。

### 高频避坑点
- **`@NotBlank` 依赖 `String.trim()`**，它只去除 Unicode 码小于等于空格的字符（如空格、制表符、换行）。全角空格（中文空格 `　`）在某些版本中不会被 trim 掉，需要注意。
- **数据库字段**：`@Column(nullable = false)` 对应 `@NotNull`，`@Column(length = 0)` 对应 `@NotBlank` 的校验逻辑，两者要分清（一个是数据库约束，一个是业务校验）。
