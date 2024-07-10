# 使用微软 Garnet 分布式缓存

## 什么是 Garnet 分布式缓存

Garnet 是微软开源的分布式缓存系统，它是一个高性能、低延迟的分布式缓存系统，支持多种数据结构，如字符串、列表、哈希表、集合等，Garnet 的设计目标是提供一个高性能、低延迟的分布式缓存系统，以支持大规模的数据存储和访问，同时提供高可用性和可扩展性。

Garnet 采用流行的 RESP 协议，使用 C# 编写，支持 Redis 的大部分命令，同时提供了更多的功能，如集群搭建、数据持久化、数据复制、数据分片等，在 C# 中的可以使用 StackExchange.Redis 开源库对缓存进行操作。

https://microsoft.github.io/garnet/docs

## 部署 Garnet 分布式缓存

## 在 .NET 程序中自托管

```shell
dotnet add package Microsoft.Garnet
```

```csharp
using Garnet;

try
{
    using var server = new GarnetServer(args);
    server.Start();
    Thread.Sleep(Timeout.Infinite);
}
catch (Exception ex)
{
    Console.WriteLine($"Unable to initialize server due to exception: {ex.Message}");
}
```

##  在 Docker 中运行

### 使用 Redis 缓存

```shell
docker run --name my-redis -d -p 6379:6379 -e REDIS_PASSWORD=guest redis
```

### 使用 Garnet 缓存

```shell
docker run --name garnet -d -p 6379:6379 --ulimit memlock=-1 ghcr.io/microsoft/garnet --auth Password --password guest
```

设置日志记录级别

```shell
docker run --name garnet -d -p 6379:6379 --ulimit memlock=-1 ghcr.io/microsoft/garnet --auth Password --password guest --logger-level Trace
```

## 使用 Window Service 托管 Garnet 服务

```shell
dotnet add package Microsoft.Extensions.Hosting.WindowsServices
```

```csharp
using Garnet;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .UseWindowsService()
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<GarnetService>();
            });
}
```

```csharp

using System;
using System.Threading;
using System.Threading.Tasks;
using Garnet;
using Microsoft.Extensions.Hosting;

public class GarnetService : IHostedService
{
    private GarnetServer _server;

    public Task StartAsync(CancellationToken cancellationToken)
    {
        _server = new GarnetServer();
        _server.Start();
        return Task.CompletedTask;
    }

    public Task StopAsync(CancellationToken cancellationToken)
    {
        _server.Stop();
        return Task.CompletedTask;
    }
}
```

```shell
sc create GarnetService binPath= "C:\path\to\GarnetService.exe"

sc start GarnetService

```

```shell
dotnet add package Microsoft.Garnet.Client
```

```csharp
using Garnet.Client;

var client = new GarnetClient("localhost", 6379);

client.Set("key", "value");

var value = client.Get("key");

Console.WriteLine(value);
``` 

## 购物车使用 Garnet 缓存

```shell
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
     options.Configuration = builder.Configuration.GetConnectionString("MyRedisConStr");
});
```

## 缓存测试工具

使用 `redis-cli` 连接到 Garnet 缓存服务器，执行以下命令进行测试。

```shell
redis-cli -h localhost -p 6379 -a Password
auth Password
set key value
get key
```

## 高可用性集群

使用多个 Garnet 缓存服务器搭建高可用性集群，可以提高系统的可用性和可靠性。

https://microsoft.github.io/garnet/docs/cluster/overview

```csharp

services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379,localhost:6380,localhost:6381";
    options.InstanceName = "SampleInstance";
});

```

```csharp

services.AddStackExchangeRedisCache(options =>
{
    options.InstanceName = "SampleInstance";
    options.ConfigurationOptions = new ConfigurationOptions
    {
        Password = "Password",
        AbortOnConnectFail = false,
        EndPoints =
        {
            { "localhost", 6379 },
            { "localhost", 6380 },
            { "localhost", 6381 }
        }
    };
});

```


## 缓存最佳实践

https://learn.microsoft.com/zh-cn/azure/architecture/best-practices/caching

https://learn.microsoft.com/zh-cn/azure/architecture/patterns/cache-aside


## 课外阅读


关键字：缓存雪崩、缓存穿透、缓存击穿、缓存预热、缓存更新、缓存降级、缓存命中率、滑动过期、绝对过期、永不过期、缓存依赖。


https://developer.aliyun.com/article/841392


