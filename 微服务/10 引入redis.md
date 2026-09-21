
引入依赖

```
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
```

配置（隧道）

```
data:  
  redis:  
    host: localhost  
    port: 8888  
    timeout: 60s #连接空闲超过N(s秒、ms毫秒)后关闭，0为禁用，这里配置值和tcp-keepalive值一致  
    lettuce:  
      pool:  
        max-active: 8  #允许最大连接数  
        max-idle: 8 #最大空闲连接数, 默认8  
        min-idle: 0  #最小空闲连接数  
        max-wait: 5s  #请求获取连接等待时间
```


![](assets/10%20引入redis/file-20260921101532760.png)

默认redis没有密码