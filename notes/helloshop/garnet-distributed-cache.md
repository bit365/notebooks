# 使用缓存提高系统性能

缓存是一种常见的技术，目标是提高系统的性能和伸缩性。 为缓存数据，它会暂时会经常访问的数据复制到位置靠近应用程序的快速存储。 如果这种快速数据存储比原始源更靠近应用程序，则缓存可以通过更快速提供数据，大幅改善客户端应用程序的响应时间。

![cache-aside](https://oss.xcode.me/notes/helloshop/cache-aside.png)

## 缓存类型

### 私有专用缓存

专用缓存是指缓存服务运行在应用程序的同一台服务器上，它是应用程序的一部分，通常是一个库或模块。专用缓存通常是一个内存缓存，如 `MemoryCache` 或 `ConcurrentDictionary`，它是一个轻量级的缓存，适用于小型应用程序。专用缓存的优点是简单易用，缺点是无法跨服务器共享数据，不适用于大型应用程序，使用 IMemoryCache 接口可以在 .NET 中使用专用缓存。

![privatecache](https://oss.xcode.me/notes/helloshop/private-cache.png)

```csharp

services.AddMemoryCache();

public class MyService(IMemoryCache cache) : IService
{
    public async Task Test()
    {
        cache.Set("key", "value");
        var value = cache.Get("key");
    }
}

```

### 分布式共享缓存


分布式缓存是指缓存服务运行在多台服务器上，它是一个独立的服务，可以跨服务器共享数据。分布式缓存通常是一个内存缓存，如 Redis、Memcached、Garnet 等，它是一个高性能、低延迟的缓存，适用于大型应用程序。分布式缓存的优点是高性能、低延迟，支持大规模数据存储和访问，缺点是复杂度高，需要额外的服务器资源，使用 IDistributedCache 接口可以在 .NET 中使用分布式缓存。

![cache-shared](https://oss.xcode.me/notes/helloshop/shared-cache.png)

```csharp

services.AddMemoryDistributedCache();

public class MyService(IDistributedCache cache) : IService
{
    public async Task Test()
    {
        await cache.SetStringAsync("key", "value");
        var value = await cache.GetStringAsync("key");
    }
}
```

## 使用 Redis 分布式缓存

Redis 是一个高性能、开源的分布式缓存系统，它是一个内存数据库，支持多种数据结构，如字符串、列表、哈希表、集合等，Redis 的设计目标是提供一个高性能、低延迟的分布式缓存系统，以支持大规模的数据存储和访问，同时提供高可用性和可扩展性。Redis 采用 RESP 协议，使用 C 语言编写，支持多种语言，如 C、C++、C#、Java、Python、Node.js 等，可以在 .NET 中使用 StackExchange.Redis 开源库对缓存进行操作。

Windows 下 Redis 不再维护，可以使用 WSL2 安装 Redis，或者使用 Docker 安装 Redis。

### 容器化 Redis 服务

```shell
docker run --name my-redis -d -p 6379:6379 -e REDIS_PASSWORD=guest redis
```

### 测试客户端工具

使用 `redis-cli` 连接到 Linux 缓存服务器，执行以下命令进行测试。

```shell
redis-cli -h localhost -p 6379 -a Password
auth Password
set key value
get key
```

### 使用 StackExchange.Redis 客户端

```shell
donet add package StackExchange.Redis
```
    
```csharp
 using StackExchange.Redis;

 var redis = ConnectionMultiplexer.Connect("localhost:6379,password=guest");
 var db = redis.GetDatabase();

 db.StringSet("key", "value");

 var value = db.StringGet("key");

```

### 使用 IDistributedCache 接口

```shell
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

```csharp
services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379,password=guest";
    options.InstanceName = "SampleInstance";
});
```

```csharp
using Microsoft.Extensions.Caching.Distributed;

public class MyService(IDistributedCache cache) : IService
{
    public async Task Test()
    {
        await cache.SetStringAsync("key", "value");
        var value = await cache.GetStringAsync("key");
    }
}
```

推荐使用 IDistributedCache 接口，它是一个抽象接口，可以方便的切换不同的缓存实现，如 Redis、Memcached、Garnet 等，同时支持 ASP.NET Core 的缓存中间件，可以方便的在 ASP.NET Core 中使用缓存。


## 使用微软 Garnet 分布式缓存

Garnet 是微软开源的分布式缓存系统，它是一个高性能、低延迟的分布式缓存系统，支持多种数据结构，如字符串、列表、哈希表、集合等，Garnet 的设计目标是提供一个高性能、低延迟的分布式缓存系统，以支持大规模的数据存储和访问，同时提供高可用性和可扩展性。

Garnet 采用流行的 RESP 协议，使用 C# 编写，支持 Redis 的大部分命令，同时提供了更多的功能，如集群搭建、数据持久化、数据复制、数据分片等，在 C# 中的可以使用 StackExchange.Redis 开源库对缓存进行操作。

https://microsoft.github.io/garnet/docs

### 在 .NET 程序中自托管

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

```shell
dotnet add package Microsoft.Garnet.Client
```

```csharp
using Garnet.Client;

var client = new GarnetClient("localhost", 6379, "Password");

client.Set("key", "value");

var value = client.Get("key");

```

###  在 Docker 中运行

```shell
docker run --name garnet -d -p 6379:6379 --ulimit memlock=-1 ghcr.io/microsoft/garnet --auth Password --password guest
```

### 使用 Window Service 运行

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

## 购物车使用分布式缓存

```shell
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
     options.Configuration = builder.Configuration.GetConnectionString("MyRedisConStr");
});

```

## 高可用性分布式缓存集群

使用多个缓存服务器搭建高可用性集群，可以提高系统的可用性和可靠性。

https://microsoft.github.io/garnet/docs/cluster/overview

https://redis.io/topics/cluster-tutorial

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


## 缓存技术点

缓存雪崩、缓存穿透、缓存击穿、缓存预热、缓存更新、缓存降级、缓存命中率、滑动过期、绝对过期、永不过期、缓存依赖。

https://developer.aliyun.com/article/841392

IDistributedCache 过期时间分为：绝对过期时间和滑动过期时间。

## 分布式缓存监控工具

可用 RedisInsight、Garnet Dashboard、Grafana 等工具监控分布式缓存系统。