# PostgreSQL 高可用性和读写分离

## 基本概念

一个操作系统可以安装多个 PostgreSQL 实例，每个实例都有自己的配置文件、数据目录和端口号。每个实例中可以创建多个数据库，每个数据库中可以创建多个表。使用 Docker 可以方便的创建多个 PostgreSQL 实例。

![postgresql-host](https://oss.xcode.me/notes/helloshop/postgresql-host.svg)


## 安装主从服务器

```shell
docker run --name postgres1 -e POSTGRES_PASSWORD=postgres -d -p 5431:5432 -v ${pwd}/postgres1/data:/var/lib/postgresql/data postgres
docker run --name postgres2 -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v ${pwd}/postgres2/data:/var/lib/postgresql/data postgres
```

## 逻辑复制

逻辑复制是根据复制标识（通常是主键）复制数据对象及其更改的一种方法。 我们使用术语逻辑与物理复制相比，逻辑复制使用发布和订阅模型， 其中一个或多个订阅者订阅发布者节点上的一个或多个发布。 订阅者从他们订阅的发布中提取数据， 并可能随后重新发布数据以允许级联复制或更复杂的配置。

### 创建主数据库和从数据库

在 postgres1 中创建数据库 mydb1 数据库。

```sql
CREATE DATABASE mydb1;
```

在 postgres2 中创建数据库 mydb2 数据库。

```sql
CREATE DATABASE mydb2;
```

在 mydb1 和 mydb2 数据库中创建结构相同的表 mytable。

```sql
CREATE TABLE mytable (id int PRIMARY KEY, name text NOT NULL);
``` 

### 将服务器日志级别设置为逻辑

在 postgres1 的 mydb1 数据库中设置服务器日志级别为逻辑。

```sql
ALTER SYSTEM SET wal_level = logical;
```

在 postgres2 的 mydb2 数据库中设置服务器日志级别为逻辑。

```sql
ALTER SYSTEM SET wal_level = logical;
```

### 主库创建发布者

#### 在 postgres1 的 mydb1 主库中创建发布者。

为所有表创建发布者。

```sql
CREATE PUBLICATION my_pub FOR ALL TABLES;
```

为特定表创建发布者。

```sql
CREATE PUBLICATION my_pub FOR TABLE mytable;
CREATE PUBLICATION my_pub FOR TABLE mytable1, mytable2;
```

### 从库创建订阅者

#### 在 postgres2 的 mydb2 从库中创建订阅者。

```sql
CREATE SUBSCRIPTION my_sub CONNECTION 'dbname=mydb1 host=postgres1_ip_address port=5431 user=postgres password=postgres' PUBLICATION my_pub
```

### 测试逻辑复制

分别在 postgres1 主库 mdb1 数据库和 postgres2 从库 mydb2 数据库中创建表 mytable。

在 postgres1 主库 mydb1 中插入数据。

```sql
INSERT INTO mytable VALUES ('hello');
```

在 postgres2 从库 mydb2 中查询数据。

```sql
SELECT * FROM mytable;
```

### 注意事项

对于新创建的表，需要重新创建发布者和订阅者，如果使用 `FOR ALL TABLES` 创建发布者，新创建的表会自动加入发布者，但是订阅者需要重新创建。

另外可以使用 pgAdmin 等图形化工具来创建发布者和订阅者。


## 流复制

流复制是一种物理复制方法，它通过将 WAL 记录从一个服务器传输到另一个服务器来复制数据。 流复制是 PostgreSQL 的内置功能，它可以在不同的服务器之间复制数据。

### 主服务器配置

在 postgres1 中创建复制用户，以免使用超级用户导致安全问题，创建一个 testuser 用户， 并且只允许该用户登录和复制功能，密码为 testpwd。

```sql
CREATE ROLE testuser LOGIN REPLICATION ENCRYPTED PASSWORD 'testpwd';
```

在 postgres1 中修改 `pg_hba.conf` 文件，允许 testuser 用户连接到主服务器来。

```shell
host replication testuser all scram-sha-256
```

### 从服务器配置

```shell
export PGPASSWORD='testpwd' && pg_basebackup -h postgres1_ip_address -p 5431 -D /var/lib/postgresql/data/my_data_backup -U testuser -P -X stream -R
```

以上命令会将 postgres1 的数据备份到 /var/lib/postgresql/data/my_data_backup 目录中，-X stream 表示使用流复制，-R 表示备份完成后自动启动从服务器，后续 postgres1 的数据会自动同步到 postgres2，无需手动操作。

以上命令会在 my_data_backup 目录中生成一个名为 `standby.signal` 的文件，表示从服务器已经启动，同时会在 `postgresql.auto.conf` 文件中添加 `primary_conninfo` 配置，表示主服务器的连接信息。

替换掉 postgres2 的数据目录，将备份的数据目录移动到 postgres2 的数据目录中。

```shell
rm -rf /var/lib/postgresql/data
mv /var/lib/postgresql/data/my_data_backup /var/lib/postgresql/data
```

### 异步和同步流复制的区别

异步复制是指主服务器将 WAL 记录发送到从服务器，但是不等待从服务器确认，主服务器会继续处理其他事务，这样可以提高主服务器的性能，但是可能会丢失数据。

同步复制是指主服务器将 WAL 记录发送到从服务器，等待从服务器确认，只有从服务器确认后，主服务器才会继续处理其他事务，这样可以保证数据的一致性，但是会降低主服务器的性能。

#### 主服务器配置同步复制

在 postgres1 执行以下命令。

```sql
ALTER SYSTEM SET synchronous_standby_names = 'standby2';
```

#### 从服务器配置

在 postgres2 的数据目录中修改 `postgresql.auto.conf` 文件，添加以下配置。

```text
primary_conninfo = 'application_name=standby2'
```

https://www.cnblogs.com/haha029/p/16721007.html