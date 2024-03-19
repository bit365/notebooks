# 设计权限定义和权限提供者

## 权限定义

每个微服务是自治的，权限定义也是每个微服务的职责，各自定义自己的权限，权限管理器会自动合并权限定义，权限定义上下文是微服务内的权限定义和全局权限定义的合集，权限提供者会根据权限定义上下文提供权限数据，权限分组是为了方便权限定义的管理，权限分组是权限定义的一部分，权限提供者自动注入到容器，提供权限查询端点。


## 架构设计

![权限定义](https://oss.xcode.me/notes/helloshop/permission-definition.svg)


## 权限扩展方法

```csharp
IServiceCollection AddPermissionDefinitions(this IServiceCollection services)

EndpointRouteBuilder MapPermissionDefinitions(this IEndpointRouteBuilder endpoints, params string[] tags)
```