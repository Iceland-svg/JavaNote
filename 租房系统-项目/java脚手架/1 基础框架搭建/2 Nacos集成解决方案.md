微服务创建模板

创建模块，接口子模块，业务子模块，引入依赖，编写启动类

![](assets/2%20Nacos集成解决方案/file-20261026143520476.png)


![](assets/2%20Nacos集成解决方案/file-20261026145227179.png)

![](assets/2%20Nacos集成解决方案/file-20261026145242216.png)

引入核心依赖

```
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>  
</dependency>   
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>  
</dependency>
```

创建bootStarp.yaml文件

![](assets/2%20Nacos集成解决方案/file-20261026145940628.png)

```
spring:  
  application:  
    name: mstemplate  
  profiles:  
    active: ${RUN_ENV}  
  cloud:  
    nacos:  
      discovery:  
        username: nacos  
        password: bite@123  
        namespace: frameworkjava-${RUN_ENV}  
        server-addr: ${NACOS_ADDR}  
      config:  
        username: nacos  
        password: bite@123  
        namespace: frameworkjava-${RUN_ENV}  
        server-addr: ${NACOS_ADDR}  
        file-extension: yaml  
# 这⼀配置⽤于定义应⽤在控制台输出⽇志时所遵循的格式规则  
logging:  
  pattern:  
    console: '[%thread] %-5level %logger{36} - %msg%n'
```

都配置为环境变量