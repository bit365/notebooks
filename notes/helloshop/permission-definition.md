# 设计权限定义终结点

## 权限定义

每个微服务是自治的，权限定义也是在微服务内进行，各自定义自己的权限。

## 架构设计

![权限定义](https://oss.xcode.me/notes/helloshop/permission-definition.svg)


## 权限扩展方法

```csharp
IServiceCollection AddPermissionDefinitions(this IServiceCollection services)

EndpointRouteBuilder MapPermissionDefinitions(this IEndpointRouteBuilder endpoints, params string[] tags)
```