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
sismember key 
```