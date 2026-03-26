### In-memory data structures

Redis是用键值对来存储数据的非关系型数据库

### Programmability

针对Redis的操作可以直接通过简单的交互式命令操作，也可以通过一些脚本的方式，批量执行一些操作

### Extensibility

可以在Redis原有的功能基础上再进行拓展，Redis提供了一组API,通过扩展让Redis支持更多的数据结构，支持更多的命令

### Persistence

持久化：Redis会把数据存两份，一份在内存上一份在硬盘上，内存为主硬盘为辅
，硬盘相当于时备份了，如果redis重启了，在就会在重启时加载硬盘中的数据，回复到重启时的状态

### Clustering

Redis支持集群，一个redis存储的数据是有限的，所以一般会引入多台台主机，部署多个redis节点，每个节点存储一部分数据
