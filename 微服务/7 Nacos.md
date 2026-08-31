单机启动配置修改

![](assets/7%20Nacos/file-20260828102150551.png)

在服务器上通过 bash startup.sh -m standalone 命令来实现单机启动

![](assets/7%20Nacos/file-20260828113627239.png)


![](assets/7%20Nacos/file-20260828113651554.png)

复制一份之前的项目把上面 order，product 和pom都改成 naocs,就可以直接用idea打开

## nacos使用

引入 spring cloud alibaba 依赖放入父项目

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

引入 nacos 依赖放入子项目

```
<dependency> <groupId>com.alibaba.cloud</groupId>
<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

修改配置
```
cloud:
  nacos:
    discovery:
      server-addr:
```

![](assets/7%20Nacos/file-20260828151709916.png)

服务名称

![](assets/7%20Nacos/file-20260828151722438.png)

远程调用

![](assets/7%20Nacos/file-20260828152029442.png)

远程调用只需要把url改成应用名

测试

![](assets/7%20Nacos/file-20260828205023738.png)

版本选择

![](assets/7%20Nacos/file-20260828205622466.png)

![](assets/7%20Nacos/file-20260828205903226.png)


如果某台机器出现问题
可以通过直接下线的操作优先止损，解决问题后在上线

==优先级：止损 > 解决问题==

![](assets/7%20Nacos/file-20260828210155672.png)

nacos权重需要在配置中开启

![](assets/7%20Nacos/file-20260831104615634.png)

这里是order服务远程调用product所以需要给order（调用方）配置权重

局域网修改权重时可能会报错 解决方法--->删除 nacos/data/protocol文件再重启

同集群优先访问 

![](assets/7%20Nacos/file-20260831104308332.png)

设置 集群 BJ 则会优先访问BJ集群内服务


### 健康检查