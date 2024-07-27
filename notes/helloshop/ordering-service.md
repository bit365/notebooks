# 使用 CQRS 模式实现订单服务

CQRS 是命令和查询责任分离的英文缩写，它是一种将读取操作和更新操作分离的模式，查询返回结果，不改变系统的状态，没有副作用。命令执行更改系统状态，幂等性是指相同的命令多次执行，结果是一样的，不会有副作用。


## 为什么要使用 CQRS 模式

在传统的体系结构中，使用同一数据模型查询和更新数据库。 这十分简单，非常适用于基本的 CRUD 操作。

![crud](https://oss.xcode.me/notes/helloshop/crud.png)


CQRS 将读取和写入分离到不同的模型，使用命令来更新数据，使用查询来读取数据。读取存储可以是写入存储的只读副本，或者读取和写入存储可以具有完全不同的结构。 使用多个只读副本可以提高查询性能，尤其是在只读副本靠近应用程序实例的分布式方案中。


![cqrs](https://oss.xcode.me/notes/helloshop/cqrs.png)


## 使用 MediatR 实现 CQRS 中的 Command 模式

```shell
dotnet add package MediatR
```

```csharp
public class CreateOrderCommand : IRequest<bool>
{
    public string Product { get; set; }
    public int Quantity { get; set; }
}

public class CreateOrderCommandHandler1 : IRequestHandler<CreateOrderCommand, bool>
{
    public Task<bool> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        return Task.FromResult(1);
    }
}

public class CreateOrderCommandHandler2 : IRequestHandler<CreateOrderCommand, bool>
{
    public Task<bool> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        return Task.FromResult(2);
    }
}
```

```csharp

public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;

    public OrdersController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    public async Task<IActionResult> CreateOrder(CreateOrderCommand command)
    {
        var orderId = await _mediator.Send(command);
        return Ok(orderId);
    }
}
```

```csharp
builder.Services.AddMediatR(options => options.RegisterServicesFromAssembly(Assembly.GetExecutingAssembly()));
```

## 使用 Mediator 请求管道处理 CQRS 中的命令

`LoggingBehavior`、`ValidatorBehavior` 和 `TransactionBehavior`

```csharp
builder.Services.AddMediatR(options =>
{
    options.RegisterServicesFromAssembly(Assembly.GetExecutingAssembly());
    options.AddOpenBehavior(typeof(LoggingBehavior<,>));
    options.AddOpenBehavior(typeof(ValidatorBehavior<,>));
    options.AddOpenBehavior(typeof(TransactionBehavior<,>));
});
```

或者使用 `Pipeline` 扩展方法

```csharp
builder.Services.AddScoped(typeof(IPipelineBehavior<,>), typeof(LoggingBehavior<,>));
builder.Services.AddScoped(typeof(IPipelineBehavior<,>), typeof(ValidatorBehavior<,>));
builder.Services.AddScoped(typeof(IPipelineBehavior<,>), typeof(TransactionBehavior<,>));
```

## 实现 CQRS 中的 Query 模式

```csharp
public interface IOrderQueries
{
    Task<OrderDetails> GetOrderAsync(int id);

    Task<IEnumerable<OrderSummary>> GetOrdersFromUserAsync(int userId);
}
```

```csharp
builder.Services.AddScoped<IOrderQueries,OrderQueries>();
```

## 使用 Dapr.AspNetCore 处理集成事件
    
```shell
dotnet add package Dapr.AspNetCore
```

```csharp
services.AddDaprClient();
```

## 使用

## 参考资料

https://learn.microsoft.com/zh-cn/azure/architecture/patterns/cqrs

https://learn.microsoft.com/zh-cn/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns

https://www.xcode.me/Training?keyword=cqrs