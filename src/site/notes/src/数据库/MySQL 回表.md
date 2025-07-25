---
{"dg-publish":true,"dg-permalink":"MySQL 回表","permalink":"/MySQL 回表/"}
---


## 什么情况会发生回表操作？

指当使用非聚簇索引查询时的这一过程：
1. 索引本身只包含部分字段和主键值，无法满足查询所需的**所有列**数据
2. MySQL会先通过非聚簇索引查到对应的**主键值**，再利用主键值回到聚簇索引中访问**完整**列数据

例如对于表：

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  age INT,
  nick_name VARCHAR(50)
);
-- 创建一个非聚簇索引
CREATE INDEX idx_name_age ON users (name, age);
```

查询语句：

```sql
-- 不会执行回表操作，因为走了 idx_name_age 的联合索引
-- 但是该索引的叶子结点存放了 name/age 的数据，满足查询的所有列需求
SELECT name, age FROM users WHERE name = '张三';

-- 会执行回表操作，因为不满足查询的 nick_name 列数据
SELECT name, age, nick_name FROM users WHERE name = '张三';
```

## 回表操作有哪些影响？

- 回表增加了I/O开销和CPU消耗，特别在大数据量和高并发时可能成为性能瓶颈
- 回表会延长查询的效率

## 如何避免回表操作？

可以通过设计覆盖索引（即索引包含查询所需**所有列**字段）避免回表，从而提高查询效率

```sql
-- 创建联合索引
CREATE INDEX idx_name_age ON users (name, age);

-- 查询语句的字段覆盖所有的联合索引
SELECT name, age FROM users WHERE name = '张三';
```