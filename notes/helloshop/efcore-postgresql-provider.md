#  使用 PostgreSQL 数据库存储数据

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

## 定义实体类型

```csharp
public class User
{
    public int Id { get; set; }
    public string UserName { get; set; }
    public string PasswordHash { get; set; }
    public DateTimeOffset CreationTime { get; set; } = DateTimeOffset.UtcNow;
}
```

## 配置实体类型

```csharp
public class UserEntityTypeConfiguration : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("Users");
        builder.HasKey(x => x.Id);
        builder.Property(x => x.UserName).IsRequired().HasMaxLength(50);
        builder.Property(x => x.PasswordHash).IsRequired().HasMaxLength(50);
        builder.Property(x => x.CreationTime);
    }
}
```

## 创建 DbContext 上下文

```csharp
public class IdentityServiceDbContext : DbContext
{
    public IdentityServiceDbContext(DbContextOptions<IdentityServiceDbContext> options) : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        builder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());

        AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true);
        AppContext.SetSwitch("Npgsql.DisableDateTimeInfinityConversions", true);
    }
}
```

## 数据库连接字符串

```json
{
  "ConnectionStrings": {
    "IdentityDatabase": "Host=localhost;Port=5432;Database=IdentityService;Username=postgres;Password=postgres"
  }
}
```

## 注册数据库上下文

```csharp
builder.Services.AddDbContext<IdentityServiceDbContext>(options =>
{
    options.UseNpgsql(builder.Configuration.GetConnectionString("IdentityDatabase"));
});
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

## PostgreSQL 驱动时间自动转换配置


```csharp

AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true);
AppContext.SetSwitch("Npgsql.DisableDateTimeInfinityConversions", true);
    
```