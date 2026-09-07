
redis集群是狭义的集群，redis提供的解决存储空间提供的集群

## 数据分片算法

给定一个数据那么这个数据该放在那个分片

数据分片

![](assets/9%20Redis集群/file-20260905150102980.png)

### 哈希求余

针对要插入的数据key（redis都是键-值形式的数据）,计算hash值（MD5），再把hash%分片个数就得到对应数据下标，放入分片

MD5：
计算结果定长
计算结果分散，两个字符串哪怕只差一个字符，算出来的值差别也很大
计算结果不可逆

#### 缺点：

哈希求余分片在扩容时N增加，数据需要大量的搬运，开销极大

![](assets/9%20Redis集群/file-20260905151121508.png)

![](assets/9%20Redis集群/file-20260905151317943.png)

### 一致性哈希算法

将整个哈希值空间组织成一个**虚拟圆环**（0 ~ 2^32-1）其实就是空间上线，而对应索引就是哈希值


![](assets/9%20Redis集群/file-20260905151556188.png)



![](assets/9%20Redis集群/file-20260905151750364.png)


相对于哈希求余把交替出现改进与连续出现

扩容时，直接把分片插入，接管开始位置顺时针到另一个老节点位置的空间，然后把属于老节点的数据搬运给新节点

环上有老节点 A（位置 100）和 B（位置 200）。

- 数据 X 落在位置 150，顺时针找，遇到 B，所以 X 存在 B 上。  
    现在扩容，加入新节点 C（位置 180）。
    
- **扩容动作**：C 插在了 180 的位置。
    
- **受影响区间**：C（180）往前走，遇到的老节点是 B（200）。C 只接管 **180 ~ 200** 这一小段弧长。
    
- **搬运数据**：原来 B 负责 150~200 这段范围，现在 B 只需要把 **180~200** 范围内的数据（比如数据 X 如果在 190）**搬运给 C** 即可。B 依然留着 150~180 的数据。

缺点：

数据倾斜，如果物理节点太少，在环上分布不均，会导致某台机器存满，另一台很空

### 哈希槽分区算法

位置确定/索引（类似）

![](assets/9%20Redis集群/file-20260905152342006.png)

![](assets/9%20Redis集群/file-20260905152454889.png)

每个分片的槽位可以时连续的也可以是不连续的

扩容

![](assets/9%20Redis集群/file-20260905152758386.png)

扩容时只有被移动的槽位对应的数据才需要搬运

### 一个redis集群最多16384个分片吗？

Redis集群的理论最大分片数是**16384**，但官方强烈建议的上限是**1000个**

Redis Cluster将数据空间固定划分为 **16384个槽位（Slot）**。每个分片（Master节点）负责管理其中**一个或多个**槽位

**分片数≤槽位数** ,理论上，可以让每个分片只负责1个槽位，这样最多就能有16384个分片

- 16384个槽位，位图大小是 **2KB**

- 如果槽位数是65536，位图就会膨胀到 **8KB**

- 节点越多，心跳包越多，**8KB**的心跳包对网络带宽是巨大的负担

**性能瓶颈**：当节点数超过1000个时，集群内部的Gossip协议通信会变得非常频繁，可能导致网络拥塞，影响集群的整体性能和稳定性

### 为什么时16384个槽位？

心跳包包含了该节点持有那些slots,需要表示出该节点持有那些槽位

16384这个值个数上够用了同时占用的空间不是很大，消耗的网络带宽不大



## 集群搭建

创建目录和生成文件，删除其他运行节点

![](assets/9%20Redis集群/file-20260907084734697.png)

编辑shell脚本批量创建

![](assets/9%20Redis集群/file-20260907093611385.png)

```
for port in $(seq 1 9); \
do \
mkdir -p redis${port}/
touch redis${port}/redis.conf5 cat << EOF > redis${port}/redis.conf
port 6379
bind 0.0.0.0
protected-mode no
appendonly yes
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.30.0.10${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
EOF
done
# 注意 cluster-announce-ip 的值有变化.
for port in $(seq 10 11); \
do \
mkdir -p redis${port}/
touch redis${port}/redis.conf
cat << EOF > redis${port}/redis.conf
port 6379
bind 0.0.0.0
protected-mode no
appendonly yes
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.30.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
EOF
done
```

编辑docker-compose

```
version: '3.7'

networks:
  mynet:
    ipam:
      config:
        - subnet: 172.30.0.0/24

services:
  redis1:
    image: 'redis:5.0.9'
    container_name: redis1
    restart: always
    volumes:
      - ./redis1/:/etc/redis/
    ports:
      - 6371:6379
      - 16371:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.101

  redis2:
    image: 'redis:5.0.9'
    container_name: redis2
    restart: always
    volumes:
      - ./redis2/:/etc/redis/
    ports:
      - 6372:6379
      - 16372:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.102

  redis3:
    image: 'redis:5.0.9'
    container_name: redis3
    restart: always
    volumes:
      - ./redis3/:/etc/redis/
    ports:
      - 6373:6379
      - 16373:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.103

  redis4:
    image: 'redis:5.0.9'
    container_name: redis4
    restart: always
    volumes:
      - ./redis4/:/etc/redis/
    ports:
      - 6374:6379
      - 16374:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.104

  redis5:
    image: 'redis:5.0.9'
    container_name: redis5
    restart: always
    volumes:
      - ./redis5/:/etc/redis/
    ports:
      - 6375:6379
      - 16375:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.105

  redis6:
    image: 'redis:5.0.9'
    container_name: redis6
    restart: always
    volumes:
      - ./redis6/:/etc/redis/
    ports:
      - 6376:6379
      - 16376:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.106

  redis7:
    image: 'redis:5.0.9'
    container_name: redis7
    restart: always
    volumes:
      - ./redis7/:/etc/redis/
    ports:
      - 6377:6379
      - 16377:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.107

  redis8:
    image: 'redis:5.0.9'
    container_name: redis8
    restart: always
    volumes:
      - ./redis8/:/etc/redis/
    ports:
      - 6378:6379
      - 16378:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.108

  redis9:
    image: 'redis:5.0.9'
    container_name: redis9
    restart: always
    volumes:
      - ./redis9/:/etc/redis/
    ports:
      - 6379:6379
      - 16379:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.109

  redis10:
    image: 'redis:5.0.9'
    container_name: redis10
    restart: always
    volumes:
      - ./redis10/:/etc/redis/
    ports:
      - 6380:6379
      - 16380:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.110

  redis11:
    image: 'redis:5.0.9'
    container_name: redis11
    restart: always
    volumes:
      - ./redis11/:/etc/redis/
    ports:
      - 6381:6379
      - 16381:16379
    command: redis-server /etc/redis/redis.conf
    networks:
      mynet:
        ipv4_address: 172.30.0.111
```

启动

![](assets/9%20Redis集群/file-20260907103820592.png)

执行构建集群

![](assets/9%20Redis集群/file-20260907104648275.png)