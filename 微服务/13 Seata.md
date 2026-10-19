多库多服务怎么实现完整性和一致性


### 分布式事务

##### 分布式事务问题 ：

![](assets/13%20Seata/file-20261016104323137.png)

也叫分布式数据一致性问题，此问题是存储资源分布性导致的，比如说数据会存储到不同的数据库，而不同的服务查询的数据库，表都不同

实际应用中应该从设计层面避免分布式事务问题，设计后解决一定会增加系统复杂性

##### 分布式事务解决方案：

从CAP理论出发解决

![](assets/13%20Seata/file-20261015171438647.png)

**一致性（Consistency）：** CAP中的一致性指的是强一致性，所有节点在同一时间有相同的数据

**可用性(Availability)：** 保证每个请求都有响应

**分区容错性（Partition Tolerance）：** 出现网络分区（故障）后系统仍能对外提供服务

一个系统不可能同时满足CAP，所以便出现了，CP, AP架构

![](assets/13%20Seata/file-20261016102806001.png)

BASE理论

![](assets/13%20Seata/file-20261016102905943.png)


#### 基本可用性: 

系统出现故障时，允许损失一部分功能，优先保证系统核心功能可用

#### 软状态：

允许系统中的数据存在中间状态，也就是允许不同节点之间数据的同步存在延时，但是延时不影响可用性

#### 最终一致性：

中间状态的数据（软状态）最终会达到数据一致


#### 分布式事务模型

X/OPEN DTP模型

![](assets/13%20Seata/file-20261016104151137.png)

#### AP: 

application

#### RM: 

resource manager

#### TM：

事务管理器，管理子事务，可以理解为管理RM

![](assets/13%20Seata/file-20261016105242291.png)

#### 两阶段提交：

![](assets/13%20Seata/file-20261016110003712.png)

#### 阻塞问题：

如果TM出现问题,RM会阻塞整个流程会一直阻塞

#### 资源占用：

参与者收到准备请求会优先锁定一部分资源（redo日志）保证原子性，直到事务提交，资源利用率低，导致其他事务无法获取资源而等待

#### 数据不一致：

第二阶段，如果TM向RM commit 时网络异常，会导致commit只被接收到了一部分导致数据的不一致

#### 优点：

保证了高可用性

实现简单



### 三阶段提交

![](assets/13%20Seata/file-20261016112050754.png)

改进:

没有预留资源，避免资源的占用，引入超时机制（Timeout）避免了阻塞状态

容错能力：即使协调者出现问题，参与者个也可以自己决定commit根据预提交状态

缺点：

复杂性提高

存在数据不一致风险，网络分区出现导致Precommit时，协调者和参与者无法进行后续通信（参参与者可能会返回NO），此时如果按照预提交提交会导致数据不一致

### TCC事务

#### Try:

尝试执行业务操作，并预留必要资源，不真正执行事务，只是进行资源的预占（有timeout机制）

#### Confirm: 

try阶段成功进入confirm阶段，使用之前预留资源


#### Cancel:

try阶段失败进入cancel阶段，参与者回滚try阶段执行的操作

![](assets/13%20Seata/file-20261016114949212.png)

### Seata部署

下载seata

修改application.yml

config

```
seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: nacos
    nacos:
      server-addr: 120.77.216.183:8848
      namespace:
      group: SEATA_GROUP
      context-path:
      ##1.The following configuration is for the open source version of Nacos
      username:
      password:
      ##2.The following configuration is for the MSE Nacos on aliyun
      #access-key:
      #secret-key:
      ##3.The following configuration is used to deploy on Aliyun ECS or ACK without authentication
      #ram-role-name:
      data-id: seataServer.properties
```


register

```
registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: nacos
    nacos:
      application: seata-server
      server-addr: 120.77.216.183:8848
      group: SEATA_GROUP
      namespace:
      cluster: default
      context-path:
      ##1.The following configuration is for the open source version of Nacos
      username:
      password:
      ##2.The following configuration is for the MSE Nacos on aliyun
      #access-key:
      #secret-key:
      ##3.The following configuration is used to deploy on Aliyun ECS or ACK without authentication
      #ram-role-name:
```


store

```
store:
    # support: file 、 db 、 redis 、 raft
    mode: db
    db:
      datasource: druid
      db-type: mysql
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://127.0.0.1:3306/seata?rewriteBatchedStatements=true
      user: root
      password: 123456
      min-conn: 10
      max-conn: 100
      global-table: global_table
      branch-table: branch_table
      lock-table: lock_table
      distributed-lock-table: distributed_lock
      vgroup-table: vgroup_table
      query-limit: 1000
      max-wait: 5000
```

双击seata-server.bat启动

![](assets/13%20Seata/file-20261017105620375.png)

访问界面

![](assets/13%20Seata/file-20261017105646374.png)

登录

![](assets/13%20Seata/file-20261017105732321.png)

linux部署

省略修改配置文件

![](assets/13%20Seata/file-20261017113825596.png)

启动

![](assets/13%20Seata/file-20261017114525115.png)

未指定端口会导致nacos服务以内网ip注册

```
bash seata-server.sh -h 120.77.216.183 -p 8091
```


### 微服务集成seata

引入依赖

```
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>  
</dependency>
```

修改配置文件

![](assets/13%20Seata/file-20261019084407189.png) 
### XA模式

![](assets/13%20Seata/file-20261019085511493.png)

![](assets/13%20Seata/file-20261019090001433.png)

#### 优点：

无代码入侵

事务强一致性

#### 缺点

性能较差

依赖关系型数据库

### AT模式

![](assets/13%20Seata/file-20261019094009362.png)