## 格式

### yml格式

```JAVA
spring:  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/mycnblog?characterEncoding=utf8&useSSL=false  
    username: root  
    password: root  
    driver-class-name: com.mysql.cj.jdbc.Driver
```

### properties格式

```java
spring.application.name=spring-ioc-demo  
#配置数据库连接信息  
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/testdb?  
characterEncoding=utf8&useSSL=false  
spring.datasource.username=root  
spring.datasource.password=root
```


## 获取配置文件内容

使用@Value注解
括号内格式为"${XXXXXX}"
```java
@RequestMapping("/prop")  
@RestController  
public class propertiesController {  
    @Value("${spring.datasource.url}")  
private String url;  
    @RequestMapping("/read")  
    public String readProperties(){  
        return url;  
    }  
}
```