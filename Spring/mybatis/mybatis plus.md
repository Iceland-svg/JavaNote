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
创建Mapper并添加@MapperScan("com.example.plus.mapper")  扫描

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