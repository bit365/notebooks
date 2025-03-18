# 关于 Aspire 应用宿主的编排

## 通过 Add 方法添加应用组件

通过 AddProject、AddContainer、AddExecutable、AddParameter，AddConnectionString 等方法，可以将应用宿主编排为一个完整的应用。

## 配置显式资源启动

```csharp
builder.AddProject<Projects.MyApp>("dbmigration").WithExplicitStart();
```

使用 WithExplicitStart 方法，可以配置显式资源启动，显式资源启动的资源需要手动启动。

## 资源的引用通过 WithReference 方法

```csharp
builder.AddProject<Projects.MyApp>("myapp").WithReference(postgresdb);
```

被引用资源可以是一个终结点，连接字符串，或者其他资源，所引用的资源会通过环境变量的方式传递给引用资源。

## 等待资源启动和完成

```csharp
builder.AddProject<Projects.MyApp>("myapp").WithWaitFor(postgresdb);
```

```csharp
builder.AddProject<Projects.MyApp>("myapp").WithWaitForCompletion("dbmigration");
```

## 自定义容器的启动参数

```csharp
var cache = builder.AddRedis("cache").WithImageTag("latest");
```

## 容器资源生存期

```csharp
var cache1 = builder.AddRedis("cache1").WithLifetime(ContainerLifetime.Persistent);
var cache2 = builder.AddRedis("cache1").WithLifetime(ContainerLifetime.Session);
```

Persistent 表示容器资源的生存期为持久，Session 表示容器资源的生存期为会话。

## 外部参数

在 Host 项目的 appsettings.json 配置文件中添加如下节点。

```json
{
    "Parameters": {
        "myvalue": "local-value"
    }
}
```

```csharp
var value1 = builder.AddParameter("myvalue");
var value2 = builder.AddParameter("myvalue", secret: true);

builder.AddProject<Projects.ApiService>("api").WithEnvironment("EXAMPLE_VALUE", value1);
```

## 外部连接字符串

在 Host 项目的 appsettings.json 配置文件中添加如下节点。

```json
{
    "ConnectionStrings": {
        "myconnection": "Server=myserver;Database=mydb;User=myuser;Password=mypassword;"
    }
}
```

```csharp
var connection = builder.AddConnectionString("myconnection");

builder.AddProject<Projects.ApiService>("api").WithReference(connection);
``` 

## 连接字符串和终结点引用

```csharp
var builder = DistributedApplication.CreateBuilder(args);

var customContainer = builder.AddContainer("myapp", "mycustomcontainer").WithHttpEndpoint(port: 9043, name: "endpoint");

var endpoint = customContainer.GetEndpoint("endpoint");

var apiservice = builder.AddProject<Projects.AspireApp_ApiService>("apiservice").WithReference(endpoint);

```

## 在 Redis 组件上启用管理扩展

```csharp
var redis = builder.AddRedis("redis").WithManagement();
var redis = builder.AddRedis("redis").WithRedisCommander();
```

## 注册生命周期钩子

```csharp
using Aspire.Hosting.Lifecycle;
using Microsoft.Extensions.Logging;

var builder = DistributedApplication.CreateBuilder(args);

builder.Services.AddLifecycleHook<LifecycleLogger>();

builder.Build().Run();

class LifecycleLogger(ILogger<LifecycleLogger> logger): IDistributedApplicationLifecycleHook
{
    public Task BeforeStartAsync(DistributedApplicationModel appModel, CancellationToken cancellationToken = default)
    {
        logger.LogInformation("BeforeStartAsync");
        return Task.CompletedTask;
    }

    public Task AfterEndpointsAllocatedAsync(
        DistributedApplicationModel appModel, CancellationToken cancellationToken = default)
    {
        logger.LogInformation("AfterEndpointsAllocatedAsync");
        return Task.CompletedTask;
    }

    public Task AfterResourcesCreatedAsync(
        DistributedApplicationModel appModel, CancellationToken cancellationToken = default)
    {
        logger.LogInformation("AfterResourcesCreatedAsync");
        return Task.CompletedTask;
    }
}
```

## 网络模型和多副本启动

```csharp
builder.AddProject<Projects.Networking_Frontend>("frontend")
       .WithHttpEndpoint(port: 5066, IsExternal: true, IsProxied: true)
       .WithReplicas(2);
```

IsExternal 和 IsProxied 属性决定了终结点的管理和公开方式，IsExternal 为 true 时，终结点会被公开，IsProxied 为 true 时，终结点会被代理。

https://learn.microsoft.com/zh-cn/dotnet/aspire/fundamentals/networking-overview


## 其它功能

- 自定义资源命令
- 将 Dockerfiles 添加到 .NET 应用模型
- 捕获 Aspire 中的事件

## 参阅资料

https://learn.microsoft.com/zh-cn/dotnet/aspire

