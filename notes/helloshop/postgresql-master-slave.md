# PostgreSQL 数据复制和主从同步

## 逻辑复制

逻辑复制是根据复制标识（通常是主键）复制数据对象及其更改的一种方法。 我们使用术语逻辑与物理复制相比，逻辑复制使用发布和订阅模型， 其中一个或多个订阅者订阅发布者节点上的一个或多个发布。 订阅者从他们订阅的发布中提取数据， 并可能随后重新发布数据以允许级联复制或更复杂的配置。

### 创建主数据库和从数据库

创建主数据库和从数据库。

```sql
CREATE DATABASE mydb1;
CREATE DATABASE mydb2;
```

### 设置数据库 WAL 日志级别

```sql
ALTER SYSTEM SET wal_level = logical;
```

### 在主数据库创建插槽

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

### 安装主从服务器

```shell
docker run --name postgres1 -e POSTGRES_PASSWORD=postgres -d -p 5431:5432 -v ${pwd}/data/postgres1:/var/lib/postgresql/data postgres
docker run --name postgres2 -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v ${pwd}/data/postgres2:/var/lib/postgresql/data postgres
```

启动后的主从服务器的 IP 地址分别为:

- 主服务器 postgres1 : 192.168.0.228:5431
- 从服务器 postgres2 : 192.168.0.228:5432


### 主服务器配置

创建复制用户，以免使用超级用户导致安全问题。
    
```sql
CREATE ROLE replica LOGIN REPLICATION ENCRYPTED PASSWORD 'replica_password';
```

修改 `pg_hba.conf` 文件，允许复制用户连接主服务器。

```shell
host replication replica all trust
```

### 生成从服务器备份

```shell
pg_basebackup -h 192.168.0.228 -p 5431 -D /var/lib/postgresql/data/data2 -U replica -P -X stream -R
```

### 主服务器查看流复制状态

```sql
SELECT * FROM pg_stat_replication;
```

### 从服务器查看流复制状态

```sql
SELECT * FROM pg_stat_wal_receiver;
```








