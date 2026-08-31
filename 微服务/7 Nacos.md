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

客户端主动上报机制：客户端通过心跳上报的方式向服务端汇报健康状态，默认心跳间隔5s,超过30秒会认为不健康，之后就会删除实例

服务端反向探测机制：服务端主动探测客户端健康状态，默认间隔20秒，检查失败后实例会被立即标为不健康，但是不会被立即删除

### 实例类型

临时实例 ：客户端主动上报机制

设置

![](assets/7%20Nacos/file-20260831110418552.png)


非临时实例（永久实例）：服务端反向探测机制

![](assets/7%20Nacos/file-20260831110448202.png) 
修改临时实例为非临时实例，或者非临时实例修改为临时实例需要删除data/protocol/raft,和对应实例进程(kill -9)后重启nacos，一般不允许修改

![](assets/7%20Nacos/file-20260831110825677.png)

### 环境隔离

开发，测试，预发布（不对外），发布（对外）

![](assets/7%20Nacos/file-20260831111806855.png)

配置

![](assets/7%20Nacos/file-20260831111927167.png)

![](assets/7%20Nacos/file-20260831112121008.png)