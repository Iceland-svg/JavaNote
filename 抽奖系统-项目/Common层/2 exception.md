![](assets/2%20exception/file-20260414214003328.png)
### ControllerException

```java
  
@Data   
//@Data会生成自己的equals，hashcode方法，不带父类属性，易导致问题  
@EqualsAndHashCode(callSuper = true)
public class ControllerException extends RuntimeException{  
    /**  
     * @see com.example.lotterysysteam.common.errorcode.ControllerErrorCodeConstants  
     */    private Integer code;  
    //异常消息  
    private String msg;  
  
    public ControllerException() {  
    }  
  
    public ControllerException(Integer code, String msg) {  
        this.code = code;  
        this.msg = msg;  
    }  
  
    /**  
     * 从错误码获取异常码和异常消息的构造  
     * @param errorCode  
     */  
    public ControllerException(ErrorCode errorCode){  
        this.code = errorCode.getCode();  
        this.msg = errorCode.getMsg();  
    }  
}
```

### ServiceException

```java
  
@Data  
//@Data会生成自己的equals，hashcode方法，不带父类属性，易导致问题  
@EqualsAndHashCode(callSuper = true)
public class ServiceException extends RuntimeException{  
    /**  
     * @see com.example.lotterysysteam.common.errorcode.ServiceErrorCodeConstants  
     */    private Integer code;  
    //异常消息  
    private String msg;  
  
    public ServiceException() {  
    }  
  
    public ServiceException(Integer code, String msg) {  
        this.code = code;  
        this.msg = msg;  
    }  
  
    /**  
     * 从错误码获取异常码和异常消息的构造  
     * @param errorCode  
     */  
    public ServiceException(ErrorCode errorCode){  
        this.code = errorCode.getCode();  
        this.msg = errorCode.getMsg();  
    }  
}
```