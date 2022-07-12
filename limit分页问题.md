# `Limit`深分页

## `Limit`深分页为何会变慢

```sql
select id,name,balance from account where update_time> '2020-09-19' limit 100000,10;
```

**此段深分页代码执行流程**

1. 通过普通的索引列`idx_update_time`过滤条件，找到满足条件的记录ID
2. **回表**操作，找到对应的主键
3. 扫描满足条件的100010条数据，然后删除前100000行

**`limit`深分页变慢原因：**

1. `limit`语句会先扫描offset+n行，然后舍弃前offset行，返回n条数据
2. 扫描更多的行数，意味着**回表操作**更加的频繁

## 优化方案

### 回表优化

​		让`SQL`查询命中聚集索引，不用走回表，将查询条件改为主键查询；

```sql
select id,name,balance FROM account where id >= (select a.id from account a where a.update_time >= '2020-09-19' limit 100000, 1) LIMIT 10
```

### `offset`偏移量优化

**标签记录法**

标记上一次查询到哪一条数据，下次来的时候，接着扫描就好

**`between...and...`**

如果知道查询范围的话，可以使用范围查询进行优化