### 配置服务器

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

完善配置



初始化git仓库

### 配置客户端

### 版本控制集成

![](assets/11%20SringCloudConfig/file-20260928153916190.png)

![](assets/11%20SringCloudConfig/file-20260928162157117.png)