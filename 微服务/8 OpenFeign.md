
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

本地测试需要关闭云服务器上服务

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

