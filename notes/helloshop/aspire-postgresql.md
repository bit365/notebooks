# 在 Aspire 中集成 PostgreSQL 数据库

## 在 Host 项目中添加 PostgreSQL 数据库

```shell
dotnet add package Aspire.Hosting.PostgreSQL
```

```csharp
var builder = DistributedApplication.CreateBuilder(args);

var postgres = builder.AddPostgres("postgres");
var postgresdb = postgres.AddDatabase("postgresdb");

var exampleProject = builder.AddProject<Projects.ExampleProject>().WithReference(postgresdb);
```

## 添加 PostgreSQL pgAdmin 资源

```csharp
var postgres = builder.AddPostgres("postgres").WithPgAdmin();
```


## 添加 PostgreSQL pgWeb 资源

```csharp
var postgres = builder.AddPostgres("postgres").WithPgWeb();
```

## 在最终的应用中引用 PostgreSQL 数据库

```shell
dotnet add package Aspire.Npgsql.EntityFrameworkCore.PostgreSQL
```

```csharp
builder.AddNpgsqlDbContext<IdentityServiceDbContext>(connectionName: DbConstants.ConnectionStringName, configureDbContextOptions: options =>
{
    new NpgsqlDbContextOptionsBuilder(options).MigrationsHistoryTable(DbConstants.MigrationsHistoryTableName);
});
```
