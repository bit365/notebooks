# 基于策略和资源的授权机制

## ASP.NET Core 中的授权系统

ASP.NET Core 中的授权系统是基于策略的授权系统，可以通过声明式的方式来定义授权策略。授权策略可以基于角色，也可以基于资源，也可以基于其他的条件。授权策略可以通过声明式的方式来定义，也可以通过代码的方式来定义。

![authorization](https://oss.xcode.me/notes/helloshop/authorization.svg)

## 使用 IAuthorizationService 接口

IAuthorizationService 接口是 ASP.NET Core 中的授权服务接口，可以通过该接口来进行授权操作。IAuthorizationService 接口提供了多个授权方法，可以通过这些方法来进行授权操作。

```csharp
public interface IAuthorizationService
{
    Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, object resource, IEnumerable<IAuthorizationRequirement> requirements);
    Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, object resource, string policyName);
    Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, object resource, AuthorizationPolicy policy);
}
```

AuthorizationResult 是授权结果，包含了授权的结果和失败的原因。


## 实现授权策略提供者

使用 OperationAuthorizationRequirement 表示授权条件，将上一期视频中的权限定义转成授权条件提供给系统，一个策略中本可以有多个条件，但为了简单这里的策略中只加一个条件。

需要注意的是，多个策略是 OR 的关系，多个条件是 AND 的关系。

```csharp

public class CustomAuthorizationPolicyProvider(IOptions<AuthorizationOptions> options, IPermissionDefinitionManager permissionDefinitionManager) : DefaultAuthorizationPolicyProvider(options)

```

## 实现资源表示

```csharp
public interface IAuthorizationResource
{
    string ResourceType => GetType().Name;

    string ResourceId => GetType().GetProperty("Id")?.GetValue(this)?.ToString() ?? throw new NotImplementedException();
}
```

## 设计一个表示资源的记录类

一般来说，实体类型就是一种资源，可以直接从 IAuthorizationResource 接口实现，但为了方便表示资源，可以设计一个表示资源的记录类。

```csharp
public record struct ResourceInfo : IAuthorizationResource
```

## 实现授权条件处理器

```csharp
public class PermissionRequirementHandler(IPermissionChecker permissionChecker) : AuthorizationHandler<OperationAuthorizationRequirement>
public class ResourcePermissionRequirementHandler(IPermissionChecker permissionChecker) : AuthorizationHandler<OperationAuthorizationRequirement, IAuthorizationResource>

protected abstract Task HandleRequirementAsync(AuthorizationHandlerContext context, TRequirement requirement);
protected abstract Task HandleRequirementAsync(AuthorizationHandlerContext context, TRequirement requirement, TResource resource);
```

## 在授权处理器中使用 IPermissionChecker

```csharp
public class PermissionRequirementHandler(IPermissionChecker permissionChecker) : AuthorizationHandler<OperationAuthorizationRequirement>
{
    private readonly IPermissionChecker _permissionChecker;

    public PermissionRequirementHandler(IPermissionChecker permissionChecker)
    {
        _permissionChecker = permissionChecker;
    }

    protected override async Task HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement)
    {
        if (await _permissionChecker.IsGrantedAsync(context.User, requirement.Name))
        {
            context.Succeed(requirement);
        }
    }
}           
``` 