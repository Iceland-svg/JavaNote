
### sentinel使用

（1）引入依赖

```
<dependency>  
    <groupId>com.alibaba.csp</groupId>  
    <artifactId>sentinel-core</artifactId>  
    <version>1.8.6</version>  
</dependency>
```

SpringCloud集成Sentinel

添加依赖

```
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
```

配置控制台

```
sentinel:  
  transport:  
    dashboard: 127.0.0.1:8100   #sentinel控制台地址  
  web-context-unify: false   #关闭context整合
```

设置限流

![](assets/12%20sentinel/file-20261009113224457.png)

压力测试

![](assets/12%20sentinel/file-20261009113618928.png)

![](assets/12%20sentinel/file-20261009113210381.png)


流控规则