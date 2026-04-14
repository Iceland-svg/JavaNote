![](assets/2%20exception/file-20260414214003328.png)
### ControllerException

```java
@Data  
public class ControllerException extends RuntimeException{  
    //异常码  
    private Integer code;  
    //异常消息  
    private String msg;  
  
  //为了序列化
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
public class ServiceException extends RuntimeException{  
    //异常码  
    private Integer code;  
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