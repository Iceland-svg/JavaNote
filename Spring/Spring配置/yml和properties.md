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


---

## 获取配置文件内容

使用@Value注解（其实也是属性注入）
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

Spring会根据注入的类型修改配置文件中的数据类型（默认是String）

```java
@RequestMapping("/prop")  
@RestController  
public class propertiesController {  
    @Value("${spring.datasource.url}")  
    private String url;  
    @Value("${my.key1}")  
    private Integer key1;  
    @Value("${my.key2}")  
    private Boolean key2;  
    @RequestMapping("/read")  
    public String readProperties(){  
        return url;  
    }  
}
```

---

### 读取配置文件中student前缀下的信息

```java
student:  
  id: 1  
  name: zhangsan  
  age: 20
```

使用@ConfigurationProperties注解括号格式为（prefix = "前缀"）

```java
@ConfigurationProperties(prefix = "student")   
@Configuration  
@Data  
public class Student1 {  
    private String name;  
    private Integer age;  
    private Integer id;  
}
```
获取属性之后这个对象由于@Configuration 注解会交给Spring，所以我们可以直接使用@Autowired来注入对象

```java
@RestController  
@RequestMapping("/yml")  
public class ymlController {  
    @Autowired  
    private Student1 student1;  
    @RequestMapping("/read")  
    public String read(){  
        System.out.println(student1);  
        return "success";  
    }  
}
```


---

### 获取键值对形式数据

```java
dbtypes:  
  name:  
    - mysql  
    - sqlserver  
    - db2
```

一定要配合@Data来使用

```java
@Data
@Configuration  
@ConfigurationProperties(prefix = "dbtypes")  
public class DbtypeConfig {  
    private List<String> name;  
}
```


---


### map 类型获取
``` java
maptypes:
  map:
    k1: kk1
    k2: kk2
    k3: kk3

```



```java
@Component
@ConfigurationProperties("maptypes")
@Data

public class MapConfig {

  private HashMap<String,String> map;

}
```


---
