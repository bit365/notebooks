# 使用 TimeProvider 服务

System.TimeProvider 是一种时间抽象，它以 DateTimeOffset 类型的形式提供时间点。 通过使用 TimeProvider，可确保代码可测试且可预测。 TimeProvider 已在 .NET 8 中引入。

## 默认实现

默认情况下，TimeProvider 使用 DateTimeOffset.UtcNow 作为时间源。

```csharp
Console.WriteLine($"Local: {TimeProvider.System.GetLocalNow()}");
Console.WriteLine($"Utc:   {TimeProvider.System.GetUtcNow()}");
```

## 自定义实现

```csharp
public class CustomTimeProvider: TimeProvider
{
    public override DateTimeOffset GetUtcNow() => DateTimeOffset.UtcNow.AddHours(1);
}
```

```csharp
builder.Services.AddSingleton(TimeProvider.System);
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
        Console.WriteLine(now);
    }
}
```

## FakeTimeProvider 实现

```powershell
dotnet add package Microsoft.Extensions.TimeProvider.Testing
```

```csharp
FakeTimeProvider fakeTimeProvider = new();
fakeTimeProvider.SetUtcNow(fakeTimeProvider.GetUtcNow().AddHours(1));

MyService service = new (timeProvider);
service.DoSomething();
```