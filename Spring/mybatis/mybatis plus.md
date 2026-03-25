1 添加依赖和配置
```
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-spring-boot4-starter</artifactId>  
    <version>3.5.15</version>  
</dependency>
```

```
spring:  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/w?characterEncoding=utf8&useSSL=false  
    username: root  
    password: root  
    driver-class-name: com.mysql.cj.jdbc.Driver  
  
mybatis:  
  configuration: # 配置打印 MyBatis⽇志  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    map-underscore-to-camel-case: true #配置驼峰⾃动转换
```
创建Mapper并添加@MapperScan("com.example.plus.mapper")  扫描和Mapper注解二选一

```java
@MapperScan("com.example.plus.mapper")  
@SpringBootApplication  
public class PlusApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(PlusApplication.class, args);  
    }  
  
}
```

任意创建实体类,继承BaseMapper<>

```java
public interface UserInfoMapper extends BaseMapper<UserInfo> {}
```

此时生成测试代码

```java
@SpringBootTest  
class UserInfoMapperTest {  
    @Autowired  
    private UserInfoMapper userInfoMapper;  
    @Test  
    public void testSelect(){  
        List<UserInfo> userInfoList = userInfoMapper.selectList(null);  
    }  
}
```

会发现自动就生成了这么多方法，我们当然没有写但是我们可以在BaseMapper里找到这些方法

![](assets/mybatis%20plus/file-20260324215619536.png)