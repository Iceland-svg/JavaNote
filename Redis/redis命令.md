### 1 get/set
#### get

根据key取value
#### set

存key和value

### 2 keys命令

时间复杂度（On）,一般禁止使用keys,尤其是keys*

keys * 占所有字符
keys ? 占单个字符
keys  [ 选择字符 ]

### 3 exsist 查询存在

expire 给指定的key设置过期时间
expire 设置秒级，成功返回1，失败0
pexpire 设置毫秒级

### 4 ttl

查看过期时间还剩多少（time to live）

---

## key的过期策略
