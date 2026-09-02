## 理论
### 工作流程

Nacos的服务注册与发现，可以理解为**服务提供者、服务消费者与Nacos服务器**三方的一个高效协作过程。

### 1. 服务注册：提供者“报到”

当一个微服务启动时，它会作为服务提供者向Nacos服务器“报到”，这个过程主要包括：

*   **发起注册请求**：服务启动后，会通过Nacos客户端（如`NacosNamingService`）发起注册。客户端会将自己的**IP、端口、服务名、权重**等信息封装成实例对象。
*   **选择通信协议**：**临时实例**默认使用高效的**gRPC**协议，**持久化实例**则使用**HTTP**协议。客户端会向Nacos集群中**随机选择一个节点**发送注册请求，以实现负载均衡。
*   **服务端处理**：Nacos服务端收到请求后（由`InstanceController`等类处理），会将实例信息保存在内存注册表（一个`Map`结构）中。为保证并发安全，这里使用了**Copy-On-Write**思想：复制一份旧数据，修改完后再替换回去。
*   **集群数据同步**：如果Nacos是集群模式，接收到请求的节点会通过**一致性协议**（AP模式用Distro，CP模式用Raft）将新实例信息**异步同步**给集群内的其他节点，保证数据最终一致。

### 2. 服务发现：消费者“查询”与“感知”

当服务消费者需要调用一个服务时，它需要知道该服务有哪些可用的实例。

*   **主动拉取 (Pull)**：服务消费者启动时，会向Nacos服务器发送请求，**主动拉取**所需服务的完整实例列表。
*   **本地缓存与定时刷新**：消费者拿到实例列表后，会将其**缓存在本地内存**中。同时，它会启动一个**定时任务**，每隔一段时间（如10秒）向Nacos服务器**轮询**，检查服务列表是否有变化并更新本地缓存。
*   **被动通知 (Push)——UDP推送**：为了提高实时性，当服务列表发生变更时（如有新实例注册或旧实例下线），Nacos服务器会通过**UDP协议**主动向订阅了该服务的消费者推送变更消息。由于UDP不需要保持长连接，这种方式非常高效。如果UDP通知失败，消费者仍会通过定时拉取来保证数据的最终一致性。
*   **服务调用**：消费者从本地缓存中获取到可用的服务实例列表后，会通过内置的**负载均衡算法**（如随机、轮询等）选择一个实例进行调用。

### 3. 心跳机制与健康检查：维持“活性”

Nacos需要及时剔除宕机或不可用的服务实例，保证服务列表的准确性。这主要通过两种方式实现：

*   **客户端主动上报 (用于临时实例)**：服务提供者注册后，会**定时（默认每5秒）** 向Nacos服务器发送一个**心跳包**，报告自己“我还活着”。Nacos服务端会记录每个实例的**最后心跳时间**。如果**超过15秒**未收到某个实例的心跳，会将其**标记为“不健康”**；若**超过30秒**仍未收到心跳，则会将其**从服务列表中剔除**。
*   **服务端主动探测 (用于持久化实例)**：Nacos服务器会主动向服务提供者发送HTTP/TCP请求，来检测其是否健康。

### 4. AP与CP模式：不同场景的“双模”选择

Nacos的一个独特之处是同时支持**AP**和**CP**两种模式，以适应不同场景。

*   **AP模式 (优先可用性)**：这是Nacos的**默认模式**。它使用自研的**Distro**协议，追求**高可用**和**最终一致性**。服务实例信息可以短暂不一致，但服务注册、发现的速度快，集群压力小。**适用场景**：对服务实时一致性要求不高的**临时实例**，如大部分微服务调用。
*   **CP模式 (优先一致性)**：使用**Raft**协议，保证数据的**强一致性**。写入操作需要集群中**超过半数**的节点确认后才能成功，这可能会降低可用性。**适用场景**：对数据一致性要求极高的**持久化实例**或**配置管理**。

---

### ⚠️ 两个值得留意的关键点

*   **通信协议的演进**：在Nacos 2.0版本后，**临时实例**的注册、心跳和服务变更推送等核心功能都**基于gRPC长连接**实现。相比旧版的HTTP短连接，**gRPC**性能更高、延迟更低，是目前生产环境的主流选择。
*   **数据模型**：Nacos内部采用 **“服务-集群-实例”** 的三层模型来组织和管理服务。一个`Service`（服务）可以包含多个`Cluster`（集群），每个`Cluster`下又包含多个具体的`Instance`（实例）节点。

## 实战

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



### 配置中心


从nacos获取配置

![](assets/7%20Nacos/file-20260831113414954.png)

product-service 配置 bootstrap.yml

![](assets/7%20Nacos/file-20260901090630542.png)

application-pro.yml

![](assets/7%20Nacos/file-20260901090733612.png)

order-service配置 application.yml

![](assets/7%20Nacos/file-20260901090840886.png)

application-dev.yml

![](assets/7%20Nacos/file-20260901090911081.png)

配置中心写法

![](assets/7%20Nacos/file-20260901090958183.png)

### linux部署

1 打包
2 上传jar包到指定目录
3 删除占用端口 ps -ef | grep java
4 mkdir logs
5 nohup java -jar product-service-1.0-SNAPSHOT.jar --server.port=9091 >logs/product-9091.log &


![](assets/7%20Nacos/file-20260901090600834.png)


naocs基于推送模式服务列表有变化会实时推送给订阅者
eureka基于拉模式eureka cli会定期从server拉取服务信息

