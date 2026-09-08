
步骤

1 创建项目

2 引入依赖

```
<dependencies>  
    <dependency> 
    <groupId>com.alibaba.cloud</groupId>  
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>  
    </dependency>    
    <dependency>        
    <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-loadbalancer</artifactId>  
    </dependency>    
    <dependency>        
    <groupId>org.springframework.cloud</groupId>  
        <artifactId>spring-cloud-starter-gateway</artifactId>  
    </dependency>
    </dependencies>
```

3 编写启动类，注意加上@SpringBootApplication

```java
@SpringBootApplication  
public class GatewayApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(GatewayApplication.class);  
    }  
}
```

4 编写配置文件

```
server:  
  port: 9093  
spring:  
  application:  
    name: gateway  
  cloud:  
    nacos:  
      discovery:  
        server-addr: 120.77.216.183:8848  
    gateway:  
      routes:  
        - id: order-service  
          uri: lb://order-service/  
          predicates:  
            - Path=/order/**,/feign/**  
        - id: product-service  
          uri: lb://product-service/  
          predicates:  
            - Path=/product/**
```