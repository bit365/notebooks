# 身份认证系统

## 在 Docker 中 启动 PostgreSQL 数据库

https://www.postgresql.org

```shell
docker pull postgres
docker run --name postgres -e POSTGRES_PASSWORD=postgres -e TZ=Asia/Shanghai -d -p 5432:5432 postgres
```

## 使用 PgAdmin 连接 PostgreSQL 数据库

https://www.pgadmin.org

```shell
SHOW timezone;
```

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

## EF Core 列迁移排序

默认情况下，在使用迁移创建表时，EF Core 首先为主键列排序，然后为实体类型和从属类型的属性排序，最后为基类型中的属性排序。 但是，你可以指定不同的列顺序：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .HasColumnOrder(2);

    modelBuilder.Entity<Blog>()
        .Property(b => b.Rating)
        .HasColumnOrder(3);
}
```

## PostgreSQL 数据库驱动时间自动转换


```csharp

AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true);
AppContext.SetSwitch("Npgsql.DisableDateTimeInfinityConversions", true);
    
```

## Identity 体系结构说明

|实体类型|说明                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |表示用户。                                         |
|`Role`     |表示角色。                                           |
|`UserClaim`|表示用户拥有的声明。                    |
|`UserToken`|表示用户的身份验证令牌。               |
|`UserLogin`|将用户与登录名相关联。                              |
|`RoleClaim`|表示向角色内的所有用户授予的声明。|
|`UserRole` |关联用户和角色的联接实体。               |


- 每个 `User` 可以有多个 `UserClaims`
- 每个 `User` 可以有多个 `UserLogins`。
- 每个 `User` 可以有多个 `UserTokens`。
- 每个 `Role` 可以有多个关联的 `RoleClaims`。
- 每个 `User` 可以有多个关联的 `Roles`
- 每个 `Role` 可以与多个 `Users`关联。 一个多对多关系，联接表由 `UserRole` 表示。
