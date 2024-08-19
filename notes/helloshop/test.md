# PostgreSQL 数据复制和主从同步

## 逻辑复制

逻辑复制是根据复制标识（通常是主键）复制数据对象及其更改的一种方法。 我们使用术语逻辑与物理复制相比，逻辑复制使用发布和订阅模型， 其中一个或多个订阅者订阅发布者节点上的一个或多个发布。 订阅者从他们订阅的发布中提取数据， 并可能随后重新发布数据以允许级联复制或更复杂的配置。

### 创建源数据库和目标数据库

创建源数据库和目标数据库。

```sql
CREATE DATABASE mydb1;
CREATE DATABASE mydb2;
```

### 设置数据库 WAL 日志级别

```sql
ALTER SYSTEM SET wal_level = logical;
```

### 在源数据库创建插槽

```sql
SELECT * FROM pg_create_logical_replication_slot('my_slot', 'pgoutput');
```

```sql
SELECT * FROM pg_replication_slots;
```

```sql
SELECT * FROM pg_drop_replication_slot('my_slot');
```


### 数据库创建表

```sql
CREATE TABLE mytable (id serial PRIMARY KEY, name text);

INSERT INTO mytable (name) VALUES ('Alice');
INSERT INTO mytable (name) VALUES ('Bob');
INSERT INTO mytable (name) VALUES ('Charlie');
```

## 流复制

流复制是一种物理复制方法，它通过将 WAL 记录从一个服务器传输到另一个服务器来复制数据。 流复制是 PostgreSQL 的内置功能，它可以在不同的服务器之间复制数据。