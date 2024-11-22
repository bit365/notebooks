# 使用 TimeProvider 获取时间

## 传统方式获取时间

我们通常使用 `DateTime.Now` 和 `DateTimeOffset.Now` 来获取当前时间，但是这样的代码很难测试，时间依赖于系统时间，很难控制和重现，只能修改系统时间。

```csharp
var now1 = DateTime.Now;
var nowUtc1 = DateTime.UtcNow;

var now2 = DateTimeOffset.Now;
var nowUtc2 = DateTimeOffset.UtcNow;
```

## 最新的 TimeProvider 类型

为了解决上述问题，在 .NET 8 中新增了 TimeProvider 类型，可以用来获取时间，并且可以在测试中轻松地控制时间。

```csharp
var now = TimeProvider.System.GetLocalNow();
var nowUtc = TimeProvider.System.GetUtcNow();
```

## 重写 TimeProvider 模拟时间

我们可以重写 TimeProvider 类型，模拟时间，方便测试。

```csharp
public class MyTimeProvider : TimeProvider
{
    public override DateTimeOffset GetUtcNow() => DateTimeOffset.UtcNow;
}
```

## 依赖注入 TimeProvider 实现

默认实现 TimeProvider.System 使用系统时间。

```csharp
builder.Services.AddSingleton<TimeProvider>(TimeProvider.System);
```

注入 MyTimeProvider 使用自定义时间。

```csharp
builder.Services.AddSingleton<TimeProvider, MyTimeProvider>();
```

## 使用 TimeProvider 的代码

```csharp
public class MyService(TimeProvider timeProvider)
{

    public void DoSomething()
    {
        var now = timeProvider.GetLocalNow();
        var nowUtc = timeProvider.GetUtcNow();
    }
}
```

演示零度框架中使用 TimeProvider 代码。


## 关于时间的单元测试

[Microsoft.Extensions.TimeProvider.Testing](https://github.com/dotnet/extensions/tree/main/src/Libraries/Microsoft.Extensions.TimeProvider.Testing)


