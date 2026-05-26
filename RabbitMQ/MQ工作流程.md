![](assets/MQ工作流程/file-20260526161642745.png)


### 实际上RabbitMQ工作模型就是==生产者消费者模型==


### 两个客户端：

生产者发送消息，消费者接收消息,==消费者和队列是多对多的关系==

### Broker：

其实就是RabbitMQ服务器

### Queue:

可以理解为消息仓库，存储交换机路由来的消息，本质是队列

### Channel:

信道，通信渠道，一个connection可以有多个channel,

### Connection：

客户端和服务器建立Tcp连接,==channel就是connection的一个抽象层==，一个Tcp连接可以有多个channel,每个channel都是独立的虚拟连接
### Exchange:

交换机，消息发送到RebbitMQ服务器（Broker），优先通过交换机分发/路由业务数据给”消息“key标签对应的一个或者两个队列，==未匹配到则退回或者自行处理（丢掉）==

### Virtual host:

也是虚拟概念，Broker上有多个虚拟主机，多个用户使用RabbitMQ时，划分出多个虚拟主机，每个用户在自己的虚拟主机创建exchang/queue等

## AMQP

高级消息队列协议，定义了一套消息交换功能，RabbitMQ是遵循AMQP协议的，RabbitMQ就是AMQP基于Erlang的实现