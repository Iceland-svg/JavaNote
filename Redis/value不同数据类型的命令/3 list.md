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
lrange 0 -1
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

头删数据,返回被删除数据

```
lpop key 
"1"
```
### rpop

尾删数据，返回被删除的数据

```
rpop key
"4"
```

### lindex


### linsert

头插插入数据

```
linsert key 1
lrange key 0 -1
(1) "1"
(2) "2"
(3) "3"
(4) "4"
(5) "1"
(6) "2"
(7) "3"
```
### llen

求长度

```
llen key
7 
```

### lrem


### lset

### blpop

### brpop
### list应用