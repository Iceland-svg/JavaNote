
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

### predicatefactory


After

这个工厂需要一个日期时间(Java的 ZonedDateTime对象), 匹配指定⽇期之后的请求

```
predicates:
  - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```


Before

匹配指定日期之前的请求
```
predicates:
  -Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```


Between

匹配两个指定时间之间的请求datetime2 的参数必须在datetime1 之后

```
predicates:
  - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
```


Cookie

请求中包含指定Cookie, 且该Cookie值符合指定的正则表达式

```
predicates:
  - Cookie=chocolate, ch.p
```


Header

请求中包含指定Header, 且该Header值符合指定的正则表达式

```
predicates:
  - Header=X-Request-Id, \d+
```


Host

请求必须是访问某个host(根据请求中的Host字段进行匹配)

```
predicates:
  - Host=**.somehost.org,**.anotherhost.org
```


Method

匹配指定的请求⽅式

```
predicates:
  - Method=GET,POST
```


Path

匹配指定规则的路径

```
predicates:
  - Path=/red/{segment},/blue/{segment}
```


RemoteAddr

请求者的IP必须为指定范围

```
predicates:
  - RemoteAddr=192.168.1.1/24 
```

 
### getawayfilter


限流算法


固定窗口

滑动窗口

漏桶算法

令牌桶算法



### 自定义过滤器

