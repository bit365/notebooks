# 多租户应用程序设计

## 什么是多租户应用程序

多租户应用程序是一种软件架构设计，允许单个实例的软件服务多个客户，每个客户被称为一个租户，租户之间的数据自动隔离的，租户之间的数据不会相互影响。

![multi-tenancy](https://oss.xcode.me/notes/helloshop/multi-tenancy.svg)


零度编程官网搜索「多租户」查看多租户设计视频教程。

## 单表多字段隔离租户数据

单表多租户设计是指在一个表中存储多个租户的数据，通过在表中增加一个租户 TenantId 字段来区分不同租户的数据。

### 多租户数据查询

```csharp
public class FieldIsolationServiceDbContext(DbContextOptions<FieldIsolationServiceDbContext> options, ICurrentTenant currentTenant) : DbContext(options)
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Product>().Property(p => p.Name).HasMaxLength(32);

        foreach (IMutableEntityType entityType in modelBuilder.Model.GetEntityTypes())
        {
            if (entityType.ClrType.IsAssignableTo(typeof(IMultiTenant)))
            {
                modelBuilder.Entity(entityType.ClrType).AddQueryFilter<IMultiTenant>(e => e.TenantId == currentTenant.TenantId);
            }
        }

        base.OnModelCreating(modelBuilder);
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.AddInterceptors(new TenantSaveChangesInterceptor(currentTenant));

        base.OnConfiguring(optionsBuilder);
    }
}
```

### 保存数据时自动设置租户编号

```csharp
private void MultiTenancyTracking(DbContext dbContext)
{
    IEnumerable<EntityEntry<IMultiTenant>> multiTenancyEntries = dbContext.ChangeTracker.Entries<IMultiTenant>().Where(entry => entry.State == EntityState.Added || entry.State == EntityState.Modified);

    multiTenancyEntries?.ToList().ForEach(entityEntry =>
    {
        entityEntry.Entity.TenantId ??= currentTenant.TenantId;
    });
}
```

## 多数据库隔离租户数据

多数据库多租户设计是指为每个租户创建一个独立的数据库，通过数据库连接字符串来区分不同租户的数据。

```csharp

protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    if (currentTenant.TenantId != null)
    {
        string? connectionString = configuration.GetConnectionString(currentTenant.TenantId);

        optionsBuilder.UseNpgsql(connectionString);
    }

    base.OnConfiguring(optionsBuilder);
}
```

[ZeroFramework TenantDbConnectionInterceptor](https://github.com/bit365/zeroframework/blob/master/Services/DeviceCenter/ZeroFramework.DeviceCenter.Infrastructure/ConnectionStrings/TenantDbConnectionInterceptor.cs)

## 基于 Schema 的多租户设计

基于 Schema 的多租户设计是指为每个租户创建一个独立的 Schema，通过 Schema 来区分不同租户的数据。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (currentTenant.TenantId != null)
    {
        modelBuilder.HasDefaultSchema(currentTenant.TenantId);
    }

    base.OnModelCreating(modelBuilder);
}
```

目前 EF Core 不支持在迁移时自动创建 Schema，需要手动创建 Schema。


## 零度框架中实现多租户

设计租户表，在登录令牌中包括租户编号，在每次请求中提取令牌中的租户编号，根据租户编号查询租户信息，将租户信息存储在当前请求的上下文中，通过租户信息来隔离租户数据。