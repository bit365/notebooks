# 运行 Aspire 微服务程序

## 准备数据库

```shell
docker run --name postgres -e POSTGRES_PASSWORD=postgres -e TZ=Asia/Shanghai -d -p 5432:5432 postgres
```

```shell
docker run --name pgadmin -e PGADMIN_DEFAULT_EMAIL=test@test.com -e PGADMIN_DEFAULT_PASSWORD=test -e TZ=Asia/Shanghai -d -p 5050:80 dpage/pgadmin4
```

## 初始化 Dapr 运行时

```shell
dapr init -s --from-dir C:\dapr
```

## 延长等待 RabbitMQ 启动时间

```csharp
public static IResourceBuilder<IDaprSidecarResource> WithReference(this IResourceBuilder<IDaprSidecarResource> builder, IResourceBuilder<IResourceWithConnectionString> resourceBuilder, int waitInSeconds = 10)
```

## 修改 MediatR 依赖注入

之前使用 options.AddBehavior 方法注册行为，现在使用 options.AddOpenBehavior 方法注册行为。

```csharp

builder.Services.AddMediatR(options =>
{
    options.RegisterServicesFromAssembly(Assembly.GetExecutingAssembly());
    options.AddOpenBehavior(typeof(LoggingBehavior<,>));
    options.AddOpenBehavior(typeof(ValidatorBehavior<,>));
    options.AddOpenBehavior(typeof(TransactionBehavior<,>));
});
```

## 使用分布式事件的微服务需要依赖注入上下文

```csharp
builder.Services.AddHttpContextAccessor();
```

## Azure Data Studio 删除数据库

```sql
-- 终止所有连接到目标数据库的会话
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'my_database'
  AND pid <> pg_backend_pid();

-- 删除数据库
DROP DATABASE my_database;
```

## IdentityService 演示数据生成前自动创建数据库

```csharp
await serviceProvider.GetRequiredService<IdentityServiceDbContext>().Database.EnsureCreatedAsync(cancellationToken);
```

## 统一使用 DateTimeOffset 类型而不是 DateTime 类型

使用 UTC 时间的优点是可以在不同的时区之间进行转换，而不会丢失时间信息，让程序更好的处理时间，有利于跨时区的应用程序国际化本地化。

```csharp
var utcNow = DateTimeOffset.UtcNow;
```
当显示时间时，可以使用 ToLocalTime 方法转换为本地时间。

```csharp
var localTime = now.LocalDateTime;
```

PostgreSQL 数据库中的时间类型是 timestamp with time zone，它会将时间转换为 UTC 时间存储。

所有 Postgres 数据库中的时间都是 UTC 时间，但是在查询时会根据时区转换为本地时间，这个过程是透明的，自动的。

删除之前配置不优雅的时间转换设置。

```csharp
AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true);
AppContext.SetSwitch("Npgsql.DisableDateTimeInfinityConversions", true);
```

## 优先使用 TimeProvider 获取当前时间

尽可能使用 TimeProvider 抽象类，原因是可以在测试时更方便的模拟时间。

```csharp
var now = TimeProvider.System.Now;
var utcNow = TimeProvider.System.UtcNow;
```
相当于
    
```csharp
var now = DateTimeOffset.Now;
var utcNow = DateTimeOffset.UtcNow;
```

```csharp
public class MyTimeProvider: TimeProvider
{
    public override DateTimeOffset Now => new DateTimeOffset(2021, 1, 1, 0, 0, 0, TimeSpan.Zero);
    public override DateTimeOffset UtcNow => new DateTimeOffset(2021, 1, 1, 0, 0, 0, TimeSpan.Zero);
}
```

```csharp
services.AddSingleton<TimeProvider, MyTimeProvider>();
```

```csharp
public class MyService
{
    private readonly TimeProvider _timeProvider;

    public MyService(TimeProvider timeProvider)
    {
        _timeProvider = timeProvider;
    }

    public void DoSomething()
    {
        var now = _timeProvider.Now;
        var utcNow = _timeProvider.UtcNow;
    }
}
```

## 配置 OrderingService 身份验证

```csharp
  const string issuerSigningKey = ServiceDefaults.Constants.IdentityConstantsIssuerSigningKey;

  builder.Services.AddAuthentication().AddJwtBearer(options =>
  {
      options.TokenValidationParameters.ValidateIssuer = false;
      options.TokenValidationParameters.ValidateAudience = false;
      options.TokenValidationParameters.IssuerSigningKey = new SymmetricSecurityKey(Encoding.Default.GetBytes(issuerSigningKey));
  });
```

## 配置 OrderingService 对象映射

```csharp
    public class OrdersMapConfiguration : Profile
    {
        public OrdersMapConfiguration()
        {
            CreateMap<CreateOrderRequest, CreateOrderCommand>().ForMember(dest => dest.OrderItems, opt => opt.MapFrom(src => src.Items));
            CreateMap<BasketItem, CreateOrderCommand.CreateOrderCommandItem>().ForMember(dest => dest.Units, opt => opt.MapFrom(src => src.Quantity));
            CreateMap<CreateOrderCommand.CreateOrderCommandItem, OrderItem>();
            CreateMap<CreateOrderCommand, Address>();
        }
    }
```

## 配置 ProductService 实体 Product 字段长度

```csharp
public class ProductEntityTypeConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.Property(x => x.Name).HasMaxLength(64);
    }
}
```