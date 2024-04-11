# 基于资源授权的最佳实践

## 在 Identity Service 中提供权限检查接口

```csharp
[HttpHead]
public async Task<IActionResult> CheckPermission(string permissionName, string? resourceType = null, string? resourceId = null
{
    if (await permissionChecker.IsGrantedAsync(permissionName, resourceType, resourceId))
    {
        return Ok();
    }

    return Forbid();
}
```

## 远程权限检查器

```csharp
Dictionary<string, string?> parameters = new()
{
    [nameof(name)] = name,
    [nameof(resourceType)] = resourceType,
    [nameof(resourceId)] = resourceId
};

string queryString = QueryHelpers.AddQueryString(string.Empty, parameters);

HttpRequestMessage request = new(HttpMethod.Head, queryString);

using HttpResponseMessage response = await httpClient.SendAsync(request);

return response.IsSuccessStatusCode;
```

## 重新实现权限处理程序

```csharp
public class PermissionRequirementHandler(IPermissionChecker permissionChecker) : AuthorizationHandler<OperationAuthorizationRequirement>
{
    protected override async Task HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement)
    {
        if (context.Resource is IAuthorizationResource resource)
        {
            if (await permissionChecker.IsGrantedAsync(context.User, requirement.Name, resource.ResourceType, resource.ResourceId))
            {
                context.Succeed(requirement);
            }
            else
            {
                context.Fail();
            }

            return;
        }

        if (await permissionChecker.IsGrantedAsync(context.User, requirement.Name))
        {
            context.Succeed(requirement);
            return;
        }

        context.Fail();
    }
}
```

## 取消缓存以便测试
    
```csharp
await distributedCache.SetObjectAsync(cacheKey, new PermissionGrantCacheItem(isGranted), new DistributedCacheEntryOptions
{
    AbsoluteExpiration = DateTimeOffset.Now
});
```

## 可读性重构

重新生成演示数据，并将授权中的 Name 改为 PermissionName，具有更强的可读性。

## 资源描述重构

```csharp
public record struct ResourceInfo(string ResourceType, string ResourceId) : IAuthorizationResource
{
    public override readonly string ToString() => $"{ResourceType}:{ResourceId}";
}
```

