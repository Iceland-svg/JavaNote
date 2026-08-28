单机启动配置修改

![](assets/7%20Nacos/file-20260828102150551.png)

在服务器上通过 bash startup.sh -m standalone 命令来实现单机启动

![](assets/7%20Nacos/file-20260828113627239.png)


![](assets/7%20Nacos/file-20260828113651554.png)

复制一份之前的项目把上面 order，product 和pom都改成 naocs,就可以直接用idea打开

## nacos使用

引入 spring cloud alibaba 依赖

```
<properties>
<spring-cloud-alibaba.version>2022.0.0.0-RC2</spring-cloud-alibaba.version>
</properties>
<dependency>
<groupId>com.alibaba.cloud</groupId>
<artifactId>spring-cloud-alibaba-dependencies</artifactId>
<version>${spring-cloud-alibaba.version}</version>
<type>pom</type>
<scope>import</scope>
</dependency>
```

引入 nacos 依赖

```
<dependency> <groupId>com.alibaba.cloud</groupId>
<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

修改配置

远程调用

测试


