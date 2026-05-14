### sadd命令

集合添加元素

```
sadd key 1 2 3 4
4
```

### smembers命令

获取集合中元素

```
smembers key 
"1"
"2"
"3"
"4"
```

### sismember命令

判定当前元素是否在集合中

```
sismember key 1
1
sismember key 100
0
```

### spop命令

随机删除集合中元素

```
spop key 
"1"
spop key
"4"
```
### smove命令

移动元素

```
sadd key2 2
smove key key2 1
smember key2 
"2"
"1"
smember key
"2"
"3"
"4"
```

### srem命令

删除指定元素,返回删除元素个数

```
srem key 2
1
smember key
"3"
"4"
```

### sinter命令

求两个集合交集

```
sadd key3 1 2 3 4
sadd key4 1 2 3 4
sinter key3 key4
"1"
"2"
"3"
"4"
```

### sinterstore命令

求交集并存到另一个集合

```
sinterstore key key3 key4
smember key
"1"
"2"
"3"
"4"
```





