
## 入门使用

1.引入依赖

调用方

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
  
    @GetMapping("/{productId}")  
    ProductInfo getProductInfo(@PathVariable("productId") Integer productId);  
  
    @GetMapping("/p1")  
    String p1(@RequestParam("id") Integer id);  
}
```

productController

```java
@Slf4j  
@RestController  
@RequestMapping("product")  
public class ProductController {  
    @Autowired  
    private ProductService productService;  
  
    @RequestMapping("/{productId}")  
    public ProductInfo selcetProductById(@PathVariable("productId") Integer productId){  
        log.info("接收参数，productId",productId);  
        return productService.selcecProductById(productId);  
    }  
  
    @RequestMapping("/p1")  
    public String p1(Integer id){  
        return "product-service 接收到参数, id:"+id;  
    }  
}
```

4.远程调用

```java
@RestController  
@RequestMapping("/feign")  
public class FeignController {  
  
    @Autowired  
    private ProductApi productApi;  
  
    /**  
     * 通过远程调用访问商品服务p1--先调Feign客户端再远程调用product-service  
     * @param id  
     * @return  
     */  
    @RequestMapping("/o1")  
    public String o1(Integer id){  
        return productApi.p1(id);  
    }  
}
```

不要忘记搭配负载均衡使用

5.测试

本地测试需要关闭云服务器上其他服务

![](assets/8%20OpenFeign/file-20260902112504371.png)

## 参数传递

远程调用顺序 FeignController->ProductApi->ProductController

单个参数

ProductController

```java
//单个参数  
@RequestMapping("/p1")  
public String p1(Integer id){  
    return "product-service 接收到参数, id:"+id;  
}
```

ProductApi

```java
//单个参数  
@GetMapping("/p1")  
String p1(@RequestParam("id") Integer id);
```

FeignController

```java
@RequestMapping("/o1")  
public String o1(Integer id){  
    return productApi.p1(id);  
}
```

多个参数

ProductController

```java
//多个参数  
@GetMapping("/p2")  
public String p2(Integer id,String name){  
    return "product-service 接收到参数, id:"+id+"product-service 接收到参数, name"+name;  
}
```

ProductApi

```java
//多个参数  
@GetMapping("/p2")  
String p2(@RequestParam("id") Integer id,@RequestParam("name") String name);
```

FeignController

```java
@RequestMapping("/o2")  
public String o2(Integer id,String name){  
    return productApi.p2(id,name);  
}
```

对象

ProductController

```java
//参数是对象  
@GetMapping("/p3")  
public String p3(ProductInfo productInfo){  
    return "product-service 接收到参数, productInfo:"+productInfo.toString();  
}
```

ProductApi

```java
//参数是对象  
@GetMapping("/p3")  
String p3(@SpringQueryMap ProductInfo productInfo);
```

FeignController

```java
@RequestMapping("/o3")  
public String o3(ProductInfo productInfo){  
    return productApi.p3(productInfo);  
}
```

JSON

ProductController

```java
//JSON参数  
@GetMapping("/p4")  
public String p4(@RequestBody ProductInfo productInfo){  
    return "product-service 接收到参数, productInfo:"+productInfo.toString();  
}
```

ProductApi

```java
@GetMapping("p4")  
String p4(@RequestBody ProductInfo productInfo);
```

FeignController

```java
@RequestMapping("/o4")  
public String o4(@RequestBody ProductInfo productInfo){  
    return productApi.p4(productInfo);  
}  
```

### 最佳实践

### （1）继承

![](assets/8%20OpenFeign/file-20260902145534932.png)


![](assets/8%20OpenFeign/file-20260902145819702.png)



![](assets/8%20OpenFeign/file-20260902145847610.png)


![](assets/8%20OpenFeign/file-20260902150056662.png)

### （2）抽取

抽取
![](assets/8%20OpenFeign/file-20260905162650065.png)

打包![](assets/8%20OpenFeign/file-20260902145819702.png)

服务调用方引入客户端

![](assets/8%20OpenFeign/file-20260905163410191.png)

### 服务部署


确认配置

prodcut-service

bootstrap.yml

```
spring:  
  application:  
    name: product-service  
  profiles:  
    active: @profile.name@  
#管加载本地那个配置文件  
  cloud:  
    nacos:  
      config:  
        server-addr: 120.77.216.183:8848  
#       namespace: d0bb0eeb-0678-404b-9b5f-1025840bedf5  
#命名空间决定会读取配置中心那个环境--public/dev
```

application-prod.yml

```
server:  
  port: 9090  
spring:  
  application:  
    name: product-service  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/cloud_product?characterEncoding=utf8&useSSL=false  
    username: root  
    password: 1234567  
    driver-class-name: com.mysql.cj.jdbc.Driver  
  cloud:  
    nacos:  
      discovery:  
        server-addr: 120.77.216.183:8848  
        cluster-name: BJ  
#        ephemeral: false  
    loadbalancer:  
      nacos:  
        enabled: true  
mybatis:  
  configuration: # 配置打印 MyBatis⽇志  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    map-underscore-to-camel-case: true #配置驼峰⾃动转换
```

order-service

application.yml

```
spring:  
  application:  
    name: order-service  
  profiles:  
    active: @profile.name@  
  cloud:  
    nacos:  
      discovery:  
        server-addr: 120.77.216.183:8848  
#        namespace: d0bb0eeb-0678-404b-9b5f-1025840bedf5  
    loadbalancer:  
          nacos:  
            enabled: true  
    openfeign:  
      client:  
        config:  
          default:  
            connectTimeout: 2000 # 连接超时(ms)  
            readTimeout: 5000 # 读取超时(ms)  
            loggerLevel: basic # 打印请求方法、URL、响应状态码  
mybatis:  
  configuration: # 配置打印 MyBatis⽇志  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    map-underscore-to-camel-case: true #配置驼峰⾃动转换  
logging:  
  level:  
    org.example.order.api: debug # Feign 接口所在包设为 debug，loggerLevel 才会输出
```

application-prod.yml

```
spring:  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/cloud_order?characterEncoding=utf8&useSSL=false  
    username: root  
    password: 1234567  
    driver-class-name: com.mysql.cj.jdbc.Driver
```


![](assets/8%20OpenFeign/file-20260905170519985.png)

修改order-service pom文件

```
<dependency>  
    <groupId>org.example</groupId>  
    <artifactId>product-api</artifactId>  
    <version>1.0-SNAPSHOT</version>  
    <scope>system</scope>  
    <systemPath>C:\Users\24103\.m2\repository\org\example\product-api\1.0-SNAPSHOT\product-api-1.0-SNAPSHOT.jar</systemPath>  
</dependency>
```



```
<plugin>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-maven-plugin</artifactId>  
    <configuration>
            <includeSystemScope>true</includeSystemScope>  
    </configuration>
    </plugin>
```

父项目没被install也需要install

![](assets/8%20OpenFeign/file-20260905205512026.png)

![](assets/8%20OpenFeign/file-20260905210414420.png)