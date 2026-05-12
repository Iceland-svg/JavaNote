
### hset命令

```
hset key value [filed value...]

hset key1 f1 111
```

返回值是设置成功的键值对（filed - value）的个数

### hget命令

```
hget key value filed

hget key1 f1

"111"
```

### hexist命令

判断hash中是否有指定字段

```
hexist key1 f1
1

hexist key1 f2
0
```

### hdel命令

返回删除的字段个数

```
hdel key1 f1

1
```


hkeys命令

先根据key找到对应的hash再遍历hash

```
hset key h1 111 f2 222 f3 333
hkeys key
"f1"
"f2"
"f3"
```

!  !  ! 该操作存在风险，类似于keys*

### hmget 

一次查询多个

```
hmeget key f1 f2 f3
"111"
"222"
"333"
```

hgetall是获取所有

尽量去使用hmget这种渐进式遍历，而不是hgetall, hvals, hkeys这种一次遍历完的

### hlen命令

获取hash中字段的个数

```
hlen 
```
