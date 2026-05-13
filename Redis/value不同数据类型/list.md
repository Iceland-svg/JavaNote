### lpush

头插

```
lpush key 1 2 3 4
```

### lrange

取出对应范围,此处区间是闭区间

```
lrange key 0 -1
(1) "1"
(2) "2"
(3) "3"
(4) "4"
```

### lpushx

存在不插入，不存在则插入

### rpush

尾插数据

```
rpush key 1 2 3 4
(1) "1"
(2) "2"
(3) "3"
(4) "4"
(5) "1"
(6) "2"
(7) "3"
(8) "4"
```

### rpushx

不存在才尾插数据
### lpop

删除数据,返回被删除数据

```
lpop key 
"1"
```
### rpop

### lindex

### linsert

### llen

### lrem

### lset

### blpop

### brpop
