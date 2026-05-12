
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

