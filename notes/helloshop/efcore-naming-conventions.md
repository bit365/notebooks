# 数据库对象命名约定

不同数据库对象的命名约定不同，EF Core 为了适配不同数据库，提供了一些命名约定的配置选项。

## 常见的命名方法

| Naming policy  | Original | Converted |
|----------------|--------------|--------------|
| PascalCase     | HelloShop  | HelloShop  |
| CamelCase      | HelloShop  | helloShop  |
| SnakeCaseLower | HelloShop  | hello_shop | 
| SnakeCaseUpper | HelloShop  | HELLO_SHOP |
| KebabCaseLower | HelloShop  | hello-shop |
| KebabCaseUpper | HelloShop  | HELLO-SHOP |

## 手动指定表名和列名

### 表名

默认情况下，EF Core 使用实体类型的名称作为表名。可以通过重写 `OnModelCreating` 方法来修改表名：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().ToTable("blogs");
}
```

### 列名

默认情况下，EF Core 使用属性名作为列名。可以通过 `HasColumnName` 方法来修改列名：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().Property(b => b.Url).HasColumnName("blog_url");
}
```

## PostgreSQL 命名约定

PostgreSQL 的命名约定采用小写蛇形命名法，即单词之间使用下划线分隔，如 `blog_url`，表名使用单数形式。

## 使用 EFCore.NamingConventions 库

EFCore.NamingConventions 库提供了一些命名约定的配置选项，可以方便地配置表名、列名等。

### 安装 EFCore.NamingConventions 库

```bash
dotnet add package EFCore.NamingConventions
```

### 配置命名约定

使用 OnConfiguring 方法配置命名约定：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    base.OnConfiguring(optionsBuilder);
    optionsBuilder.UseSnakeCaseNamingConvention();
}
```

或者在依赖注入容器中配置：

```csharp
builder.Services.AddDbContext<MyDbContext>(options => options.UseNpgsql().UseSnakeCaseNamingConvention());
```

### 修改迁移历史表名

默认情况下，EF Core 在名为 __EFMigrationsHistory 的表中记录哪些迁移已应用到数据库中，从而跟踪这些迁移。 出于各种原因，可能需要自定义此表，以更好地满足你的需求。

```csharp
options.UseSqlServer(connectionString,x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

https://learn.microsoft.com/zh-cn/ef/core/managing-schemas/migrations/history-table
