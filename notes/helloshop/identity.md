# 身份认证系统

## 在 Docker 中 启动 PostgreSQL 数据库

https://www.postgresql.org

```shell
docker pull postgres
docker run --name postgres -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 postgres
```

## 使用 PgAdmin 连接 PostgreSQL 数据库

https://www.pgadmin.org

## EfCore 使用 PostgreSQL 数据库

```shell
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
```

## 使用 EfCore 存储 Identity

```shell
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

## 迁移数据库

```shell
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
```

```shell
dotnet ef migrations add InitialCreate --output-dir EntityFrameworks/Migrations
dotnet ef database update
```

## PostgreSQL 数据库命名约定

https://github.com/efcore/EFCore.NamingConventions

## 删除数据库和迁移
    
```shell
dotnet ef database drop --force
dotnet ef migrations remove
```

## 使用脚本迁移
    
```shell
dotnet ef migrations script
```

