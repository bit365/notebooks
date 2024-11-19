# 实现 CQRS 中的 Query 模式

在 CQRS 模式的全程是 Command Query Responsibility Segregation，即命令查询职责分离。在 CQRS 模式中，命令和查询是分开实现的，命令负责写操作，查询负责读操作，这样可以更好地实现单一职责原则，同时也可以更好地实现性能优化。

https://learn.microsoft.com/zh-cn/azure/architecture/patterns/cqrs

https://www.xcode.me/Training/UnitNote/576

### 定义订单查询接口

```csharp
public interface IOrderQueries
{
    Task<OrderDetails> GetOrderAsync(int id);
}
```

### 实现订单查询接口

```csharp
public class OrderQueries : IOrderQueries
{
    private readonly OrderingContext _context;

    public OrderQueries(OrderingContext context)
    {
        _context = context;
        dbContext.Database.SetConnectionString("db2_connection_string");
    }

    public async Task<OrderDetails> GetOrderAsync(int id)
    {
      // do something
    }
}
```

### 注册订单查询接口

```csharp
services.AddScoped<IOrderQueries, OrderQueries>();
```

### 使用订单查询接口

```csharp
public class OrderController : ControllerBase
{
    private readonly IOrderQueries _orderQueries;

    public OrderController(IOrderQueries orderQueries)
    {
        _orderQueries = orderQueries;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetOrderAsync(int id)
    {
        var order = await _orderQueries.GetOrderAsync(id);
        return Ok(order);
    }
}
```


