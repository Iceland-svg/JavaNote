
入门使用

1.引入依赖

2.开启feign

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
