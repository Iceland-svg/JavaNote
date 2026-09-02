
## 入门使用

1.引入依赖

```
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-openfeign</artifactId>  
</dependency>
```

2.开启feign

```java
@EnableFeignClients  
@SpringBootApplication  
public class OrderServiceApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(OrderServiceApplication.class,args);  
    }  
}
```

3.编写客户端

```java
@FeignClient(value = "product-service",path = "/product")  
public interface ProductApi {  
  
    @RequestMapping("/productId")  
    ProductInfo getProductInfo(@PathVariable("productId") Integer productId);  
}
```

4.远程调用
5.测试

## 参数传递

单个参数
多个参数
对象
JSON

