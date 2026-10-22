
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

![](assets/8%20sentinel/file-20261022222313347.png)

压力测试

![](assets/8%20sentinel/file-20261022222313349.png)

![](assets/8%20sentinel/file-20261022222313348.png)


流控规则

QPS : 每秒请求数

并发线程数： 

![](assets/8%20sentinel/file-20261022222313350.png)

Warm up（冷启动）

![](assets/8%20sentinel/file-20261022222313348%201.png)

5秒后增长到QPS = 10

排队等待

![](assets/8%20sentinel/file-20261022222313351.png)

超过QPS会等待一个一个排队请求

根据调用链路进行限流

关联

![](assets/8%20sentinel/file-20261022222313358.png)


当write资源请求QPS大于5，对read进行限流


热点参数限流

### 限流算法

计数器

滑动窗口算法

漏桶算法

令牌桶算法

### 熔断降级
