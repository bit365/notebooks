# 聚合 API 网关

使用网关可将多个单独请求聚合成一个请求。 当客户端必须向不同的后端系统发出多个调用来执行某项操作时，此模式非常有用。


## 上下文和问题

在某些情况下，客户端需要向多个后端系统发出多个请求。 例如，客户端可能需要从多个服务中检索数据，然后将这些数据聚合到一个响应中。 在这种情况下，客户端必须发出多个请求，这可能会导致性能问题。 此外，客户端还必须处理多个响应，这可能会导致复杂性问题。

![gateway-aggregation-problem](https://oss.xcode.me/notes/helloshop/gateway-aggregation-problem.svg)



## 解决方案

使用网关减少客户端与服务之间的通信频率。 网关会接收客户端请求，将请求分派到不同的后端系统，然后聚合结果并将其返回给请求客户端。此模式可以减少应用程序向后端服务发出的请求数，并通过高延迟网络改进应用程序的性能。

![gateway-aggregation](https://oss.xcode.me/notes/helloshop/gateway-aggregation.svg)

## 权限聚合

```csharp
public interface IPermissionService
{
    Task<IReadOnlyList<PermissionGroupDefinitionResponse>> GetAllPermissionDefinitionsAsync(CancellationToken cancellationToken=default);
}
```

## 重构用户和角色控制器

修改 UsersController 从 IdentityServiceDbContext 更改为 UserManager<User>

实现 RolesController 使用 RoleManager<Role>

UserCreateRequestValidator 从 IdentityServiceDbContext 更改为 UserManager<User>

权限定义端点返回的 Json 中 name 属性更改为 groupName


## 远程权限检查 BUG 修复

```csharp
public async Task<IActionResult> CheckPermission(int roleId, string permissionName, string? resourceType = null, string? resourceId = null)
```

## 注意事项

- 网关不应在后端服务之间造成服务耦合。

- 网关应靠近后端服务，以尽量降低延迟。
- 网关服务可能会造成单一故障点。 请确保网关设计合理，符合应用程序的可用性要求。
- 网关可能造成瓶颈。 请确保网关可提供足够的性能来处理负载，并可根据预期的发展进行缩放。
- 对网关执行负载测试，确保不会对服务造成连锁故障。
- 使用隔舱、断路、重试和超时等技术实施弹性设计。
- 如果一个或多个服务调用花费的时间过长，那么超时并返回部分数据集是可以接受的。 请考虑应用程序处理这种情况的方式。
- 使用异步 I/O 来确保后端延迟不会导致应用程序中出现性能问题。
- 使用关联 ID 实施分布式跟踪，以跟踪每个调用。
- 监视请求指标和响应大小。
- 考虑返回缓存的数据（作为故障转移策略）来处理故障。
- 不要在网关中内置聚合，而应考虑将聚合服务放在网关后面。 请求聚合的资源要求可能与网关中其他服务不同，并可能影响网关的路由和卸载功能。

