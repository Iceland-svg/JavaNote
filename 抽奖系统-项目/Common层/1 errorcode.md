## 设计优势

1. 明确性
2. 对客户端友好
3. 错误分类
4. 维护性
5. 易检索
## 当前结构

![](assets/1%20errorcode/file-20260414213623778.png)

## 当前实现



```java
@Data  
public class ErrorCode {  
    //错误码  
    private final Integer code;  
    //错误信息  
    private final String msg;  
  
    public ErrorCode(Integer code, String msg) {  
        this.code = code;  
        this.msg = msg;  
    }  
}
```

