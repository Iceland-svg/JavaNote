#### 功能：

服务发现

(1) 加入依赖

```
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>  
</dependency>
```

(2) 修改配置

```
server:  
  port: 9090  
spring:  
  application:  
    name:product-service  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/cloud_product?characterEncoding=utf8&useSSL=false  
    username: root  
    password: 123456  
    driver-class-name: com.mysql.cj.jdbc.Driver  
  mybatis:  
    configuration: # 配置打印 MyBatis⽇志  
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
      map-underscore-to-camel-case: true #配置驼峰⾃动转换  
eureka:  
  client:  
    service-url:  
      defaultZone: http://127.0.0.1:10010/eureka/
```

(3) 启动，测试

服务注册

(1) 加入依赖
(2) 修改配置
(3) 修改远程调用代码

```java
public OrderInfo selcetOrderInfoById(Integer orderId){  
        OrderInfo orderInfo = orderMapper.SelectOrderById(orderId);  
//        String url = "http://127.0.0.1:9090/product/"+ orderInfo.getProductId();  
        List<ServiceInstance> instances = discoveryClient.getInstances("product-service");  
        String uri = instances.get(0).getUri().toString();  
        String url = uri + "/product/" + orderInfo.getProductId();  
        log.info("远程调用url: {}",url);  
        ProductInfo productInfo = restTemplate.getForObject(url, ProductInfo.class);  
        orderInfo.setProductInfo(productInfo);  
        return orderInfo;  
    }
```

(4) 启动，测试

## 如何搭建注册中心


(1) 创建项目

单独建一个eureka-server

(2) 引入eurek依赖


```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

(3) 添加eureka配置


```
server:  
  port: 10010  
spring:  
  application:  
    name: eureka-server  
eureka:  
  instance:  
    hostname: localhost  
  client:  
    fetch-registry: false # 表示是否从Eureka Server获取注册信息,默认为true.因为这是一个单点的Eureka Server,不需要同步其他的Eureka Server节点的数据,这里设置为false  
    register-with-eureka: false # 表示是否将自己注册到Eureka Server,默认为true.由于当前应用就是Eureka Server,故而设置为false.  
    service-url:  
      # 设置Eureka Server的地址,查询服务和注册服务都需要依赖这个地址  
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  
logging:  
  pattern:  
    console: '%d{MM-dd HH:mm:ss.SSS} %c %M %L [%thread] %m%n'
```

(4) 启动类，开启eureka

```java  
@EnableEurekaServer  
@SpringBootApplication  
public class EurekaServerAppliation {  
    public static void main(String[] args) {  
        SpringApplication.run(EurekaServerAppliation.class,args);  
    }  
}
```



