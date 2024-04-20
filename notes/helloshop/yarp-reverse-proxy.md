# 网关和服务发现

## 构建和设计 API 网关

API 网关是一个服务器，它是客户端和后端服务之间的中介。它接收来自客户端的请求，然后将这些请求转发到后端服务。API 网关还可以执行其他任务，例如身份验证、监视、负载平衡、缓存、请求分析和日志记录。

## BFF 模式聚合多个服务

API 网关可以聚合多个服务，基于 BFF（后端用于前端）模式，当客户端请求数据时，API 网关可以调用多个服务来获取数据，然后将数据聚合到一个响应中返回给客户端，以减少客户端与后端服务之间的通信次数，从而提高性能。

## 常见的 API 网关

Nginx、Envoy、Ocelot、Yarp、Zuul、Kong、Traefik、Tyk、Ambassador、Gloo、AWS API Gateway、Azure API Management、Google Cloud Endpoints、Apigee、MuleSoft Anypoint API Gateway、WSO2 API Manager、TIBCO Mashery、3scale API Management、Akana、CA API Gateway、Dell Boomi、DreamFactory、IBM API Connect、Mashape、Mashery、MuleSoft Anypoint Platform、OpenLegacy、Oracle API Platform Cloud Service、Repre。

## 基于.NET 的开源 API 网关

Ocelot 和 Yarp 是一个基于.NET 的开源 API 网关，它可以用于构建和设计微服务架构中的 API 网关。Yarp 提供了很多功能，例如路由、负载平衡、认证、授权、限流、熔断、重试、缓存、请求转发、请求重写、请求日志、响应缓存、响应压缩、响应缓存、响应重写、响应日志、监视、跟踪、跨域、安全、性能、可扩展性、可配置性、可管理性、可监视性、可测试性、可部署性、可维护性、可扩展性、可升级。

[Github 开源仓库](https://github.com/microsoft/reverse-proxy)

[官方参考文档](https://microsoft.github.io/reverse-proxy)

## 使用 Yarp 构建和设计 API 网关

```shell
dotnet add package Yarp.ReverseProxy
```

## 使用配置文件配置 Yarp

```json
{
  "ReverseProxy": {
    "Routes": {
      "testserviceRoute": {
        "ClusterId": "testServiceCluster",
        "Match": {
          "Path": "testservice/{**remainder}"
        },
        "Transforms": [
          { "PathPattern": "{**remainder}" }
        ]
      }
    },
    "Clusters": {
      "testServiceCluster": {
        "Destinations": {
          "testServiceCluster/destination1": {
            "Address": "http://identityservice"
          }
        }
      }
    }
  }
}
```

## .NET 中的服务发现

服务发现是一种用于发现和定位服务的机制，它可以帮助客户端找到服务的位置和地址。服务发现可以用于构建和设计微服务架构中的 API 网关，以便客户端可以通过 API 网关访问后端服务。

## 使用服务发现

https://learn.microsoft.com/zh-cn/dotnet/core/extensions/service-discovery

```shell
dotnet add package Microsoft.Extensions.ServiceDiscovery --prerelease
```

```csharp
builder.Services.AddServiceDiscovery();

builder.Services.ConfigureHttpClientDefaults(static http =>
{
    // Turn on service discovery by default
    http.UseServiceDiscovery();
});
```

```json
{
  "Services": {
    "catalog": {
      "https": [
        "localhost:8080",
        "10.46.24.90:80"
      ]
    }
  }
}
```

## 在反向代理中使用服务发现
```shell
dotnet add package Microsoft.Extensions.ServiceDiscovery.Yarp
```