# 基于策略和资源的授权系统

身份认证完成后 HttpContext.User 中包含了用户的身份信息，但是用户的身份信息并不包含用户的权限信息。用户的权限信息需要通过授权系统来获取。

## 授权三要素

授权系统的核心是授权三要素：授权主体、授权策略、资源。授权主体是指需要进行授权的对象，可以是用户，也可以是角色，也可以是组织机构。授权策略是指授权的规则，可以是基于角色的授权策略，也可以是基于资源的授权策略。资源是指需要授权的对象，可以是数据，也可以是服务。

例如：张三（授权主体）有订单001（资源）的查看权限（授权策略），管理员（授权主体）有所有订单（资源）的管理权限（授权策略）。

资源更细粒度的描述：资源类型、资源标识、资源属性。

例如：张三（授权主体）有订单001的金额和支付时间（资源）的查看权限（授权策略）

值得一说的是组织结构，部门，第三方系统，等其它类型的主体，都可以与某个角色绑定，这样就可以实现对某个部门的权限控制。


## 常见的权限控制模型

常见的权限控制模型有：DAC（Discretionary Access Control）、MAC（Mandatory Access Control）、RBAC（Role-Based Access Control）、ABAC（Attribute-Based Access Control）。

## 基于角色的访问控制

基于角色的访问控制是指通过角色来控制用户对资源的访问权限。角色是一组权限的集合，用户通过分配角色来获取相应的权限。基于角色的访问控制模型简单易用，但是角色的管理和权限的分配比较复杂。

## 权限 ACL 存储设计

```csharp
public class PermissionGranted
{
    public int Id { get; set; }

    public int RoleId { get; set; }

    public required string PermissionName { get; set; }

    public string? ResourceType { get; set; }

    public string? ResourceId { get; set; }
}
```

## 在 DbContext 中配置 ACL 实体

```csharp
public void Configure(EntityTypeBuilder<PermissionGranted> builder)
{
    builder.ToTable("PermissionGranted");

    builder.Property(x => x.Id);
    builder.Property(x => x.PermissionName).HasMaxLength(64);
    builder.Property(x => x.ResourceType).HasMaxLength(16);
    builder.Property(x => x.ResourceId).HasMaxLength(32);

    builder.HasOne<Role>().WithMany().HasForeignKey(x => x.RoleId).IsRequired();

    builder.HasIndex(x => new { x.RoleId, x.PermissionName, x.ResourceType, x.ResourceId }).IsUnique();
}

```

## 设计一个权限检查器

```csharp
public interface IPermissionChecker
{
    Task<bool> IsGrantedAsync(string name, string? resourceType = null, string? resourceId = null);

    Task<bool> IsGrantedAsync(ClaimsPrincipal claimsPrincipal, string name, string? resourceType = null, string? resourceId = null);
}
```

## 实现权限检查器


![permission-checker](https://oss.xcode.me/notes/helloshop/permission-checker.svg)

```csharp
public class RemotePermissionChecker: IPermissionChecker

public class LocalPermissionChecker: IPermissionChecker
```

## 实现权限检查器

使用 DbContext 实现本地权限检查器，使用 HttpClient 实现远程权限检查器。

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