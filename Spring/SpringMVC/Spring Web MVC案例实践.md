## 1 软件设计原则——高内聚低耦合
（1）模块和模块之间尽可能减少依赖叫做低耦合
（2）模块内部联系紧密叫做高内聚（一般不用可以去设计，内部互相调用能自然实现）
## 2 三层结构
(1)表现层
(2)业务逻辑层
(3)数据层

### 留言板项目

### 1 Controller模块

#### MessageController

```java
@RequestMapping("/message")  
@RestController  
public class MessageController {  
    @Autowired  
    private MessageService messageService;  
    @RequestMapping("/publish")  
    public Boolean publish(@RequestBody MessageInfo messageInfo){  
        if(!StringUtils.hasLength(messageInfo.getFrom())  
                ||!StringUtils.hasLength(messageInfo.getMessage())  
                ||!StringUtils.hasLength(messageInfo.getTo())){  
            return false;  
        }  
        Integer result = messageService.insertMessage(messageInfo);  
        return result == 1;  
    }  
    @RequestMapping("/getList")  
    public void getList(){  
        System.out.println(messageService.getList());  
    }  
}
```

### entity模块

#### MessageInfo

```java
@Data  
public class MessageInfo {  
    private Integer id;  
    private String from;  
    private String to;  
    private String message;  
    private Integer deleteFlag;  
    private Date creatTime;  
    private Date updateTime;  
  
    public MessageInfo() {  
    }  
  
    public MessageInfo(String from, String to, String message) {  
        this.from = from;  
        this.to = to;  
        this.message = message;  
    }  
}
```


Mapper模块

MessageInfoMapper

```java
@Mapper  
public interface MessageInfoMapper {  
  
    @Insert("insert into message_info('from','to','message')value (#{from},#{to},#{message})")  
    Integer insert(MessageInfo messageInfo);  
  
    @Select("select* from message_info where delete_flag = 1")  
    List<MessageInfo> selectall();  
}
```

Service模块

MessageService

```java
@Service  
public class MessageService {  
    @Autowired  
    private MessageInfoMapper messageInfoMapper;  
    public Integer insertMessage(MessageInfo messageInfo){  
         return messageInfoMapper.insert(messageInfo);  
    }  
    public List<MessageInfo> getList(){  
         return messageInfoMapper.selectall();  
    }  
}
```