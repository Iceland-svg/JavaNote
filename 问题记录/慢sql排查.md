
SQL 慢查询排查的标准答案

1. 先定位哪些 SQL 慢

· 开启慢查询日志（以 MySQL 为例）：
  · slow_query_log = ON
  · long_query_time 设置阈值，比如超过 2 秒就记录
  · log_queries_not_using_indexes 可以顺带记录没走索引的 SQL
· 查看慢日志文件，也可以用 pt-query-digest 这类工具聚合分析，找出频率最高、总耗时最长的 SQL。

2. 拿到具体的慢 SQL，用 EXPLAIN 分析执行计划

· EXPLAIN SELECT ...，重点看这些字段：
  · type：访问类型，从好到差依次是 system > const > eq_ref > ref > range > index > ALL。看到 ALL（全表扫描）就要警惕。
  · key：实际用到的索引，如果为 NULL 说明没走索引。
  · rows：预估要扫描的行数，越大越要优化。
  · Extra：额外信息，Using filesort（文件排序）、Using temporary（临时表）往往是性能杀手。

3. 常见优化方向

· 加索引：在 WHERE、JOIN、ORDER BY 的列上建合适的索引，尽量覆盖查询字段，避免回表。
· 优化 SQL 写法：避免 SELECT *，不要在 WHERE 里对字段做函数运算（如 WHERE DATE(create_time) = '2026-01-01' 会导致索引失效），可以用范围查询代替。
· 分页优化：大偏移量的 LIMIT 100000, 10 可以用“延迟关联”或记录上次主键位置的方式优化。
· 表结构优化：大字段拆分、适时归档历史数据、合理选择字段类型。
· 调整数据库参数：比如 InnoDB 的缓冲池大小 innodb_buffer_pool_size，让热数据尽量在内存命中。

4. 实操案例示例
假设慢日志里有一条：

```sql
SELECT * FROM orders WHERE status = 0 ORDER BY create_time DESC LIMIT 10;
```

· EXPLAIN 发现 type=ALL，全表扫描。
· 优化：在 (status, create_time) 上建联合索引。
· 优化后 type=ref，rows 大幅降低。

