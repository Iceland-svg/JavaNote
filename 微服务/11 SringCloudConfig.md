![](assets/11%20SringCloudConfig/file-20260928153916190.png)
### 1 配置服务器

搭建ConfigServer

创建项目

添加依赖

```
<dependencies>  
    <dependency>        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>    <dependency>        <groupId>org.springframework.cloud</groupId>  
        <artifactId>spring-cloud-config-server</artifactId>  
    </dependency></dependencies>  
<build>  
    <plugins>        <plugin>            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-maven-plugin</artifactId>  
        </plugin>    </plugins>    <resources>        <resource>            <directory>src/main/resources</directory>  
            <filtering>true</filtering>  
            <includes>                <include>**/**</include>  
            </includes>        </resource>    </resources></build>
```

启用ConfigServer

```
//添加注解 @EnableConfigServer 即可
@EnableConfigServer  
@SpringBootApplication  
public class ConfigServerApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(ConfigServerApplication.class,args);  
    }  
}
```

修改了配置中心，ConfigServer需要重启

完善配置

![](assets/11%20SringCloudConfig/file-20260928162157117.png)

初始化git仓库

测试

![](assets/11%20SringCloudConfig/file-20260928162615358.png)
### 2 配置客户端

配置管理


![](assets/11%20SringCloudConfig/file-20260928164337331.png)

添加依赖

```
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-config</artifactId>  
</dependency>  
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-bootstrap</artifactId>  
</dependency>
```

bootstrap.yml

```
spring:  
  profiles:  
    active: dev  
  application:  
    name: product-service  
  cloud:  
    config:  
      uri: http://127.0.0.1:9091 # 指定配置服务端的地址  
# profile: dev
```

ConfigController

```java

@RestController  
@RequestMapping("/config")  
public class ConfigController {  
    @Value("${data.env}")  
    private String env;  
  
    @RequestMapping("/getEnv")  
    public String getEnv() {  
        return "data.env" + env;  
    }  
}
```

汇总

![](assets/11%20SringCloudConfig/file-20260929102616339.png)
### 3 版本控制集成


配置中心自动刷新




