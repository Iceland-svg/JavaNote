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