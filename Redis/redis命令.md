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

###
exsist 查询存在
expire 给指定的key设置过期时间
