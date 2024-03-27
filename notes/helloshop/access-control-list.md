# 实现权限访问控制列表

## 授权三要素

主体、资源、操作。 例如：用户（主体）对文件（资源）的读写（操作）。主体、资源、操作三者之间的关系称为授权关系，基于这种关系，我们可以将授权关系抽象为一个三元组（主体，资源，操作），这个三元组就是授权的基本单位。

## 基于角色的访问控制

基于角色的访问控制（Role-Based Access Control，简称 RBAC）是一种访问控制模型，通过角色来控制用户对资源的访问权限。角色是一组权限的集合，用户通过分配角色来获取相应的权限。基于角色的访问控制模型简单易用。还有其它的访问控制模型，如基于属性的访问控制（Attribute-Based Access Control，简称 ABAC）。

![role-based-access-control](https://oss.xcode.me/notes/helloshop/role-based-access-control.svg)


## 权限访问控制列表

权限访问控制列表（Access Control List，简称 ACL）是一种权限控制模型，用于控制用户对资源的访问权限。ACL 通过为每个资源定义一个访问控制列表，来控制用户对资源的操作权限。

## ACL 存储设计

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

## 实现抽象的权限检查器

遍历每个角色的权限，如果有一个角色拥有该权限，则返回 true，表示授权通过。

```csharp

public abstract class PermissionChecker : IPermissionChecker
{
    public abstract Task<bool> IsGrantedAsync(string name, string? resourceType = null, string? resourceId = null);

    public abstract Task<bool> IsGrantedAsync(ClaimsPrincipal claimsPrincipal, string name, string? resourceType = null, string? resourceId = null);
}

```

## 实现权限检查器

ACL 列表存储在数据库中，可以通过 DbContext 实现本地权限检查器，也可以通过 HttpClient 实现远程权限检查器。


![permission-checker](https://oss.xcode.me/notes/helloshop/permission-checker.svg)

```csharp
public class RemotePermissionChecker: PermissionChecker

public class LocalPermissionChecker: PermissionChecker
```