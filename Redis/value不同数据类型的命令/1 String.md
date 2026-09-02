
## 常用命令


## set/get

## mget/mset

## setnx/setex/psetex

## incr/incrby

## decr/decryby/incrbyfloat

## append

## getrange

## setrange

## strlen

---

## 内部实现方式

### (1) int

8个字节长整型

### (2) embstr

小于等于39个字节的字符串

### (3) raw

大于等于39个字节的字符串

string会根据当前值的类型和和长度==动态决定==使用哪种内部编码实现