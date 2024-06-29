# 零度框架升级 8.0 稳定版

随着微软 Aspire 正式版的发布，零度框架 HelloShop 8.0 版本也正式发布了，本次改进高达 180 多项，代码已推送到 Github，主要包括以下几个方面。

## 升级到最新稳定版

之前，零度框架使用的是 Aspire 的多个预览版，导致了一些不稳定的问题，现在，零度框架和所有依赖包都升级到了最新稳定版，不再引用任何预览包，卸载掉 Visual Studio 2022 预览版，并升级到 17.10.0 稳定版，卸载掉所有 .NET 9.0 预览版，安装 .NET 8.0 SDK 稳定版，卸载掉所有 Aspire 预览版，将 Aspire 工作负载升级到 8.0.0 稳定版。项目所依赖的所有 NuGet 包都升级到了最新稳定版。

现在，零度框架和所有依赖包都升级到了最新稳定版，不再引用任何预览包。


## 远程权限检查改进

`PermissionsController` 和 `RemotePermissionChecker` 引入 RoleId 角色权限检查，

## 创建虚拟授权类用于测试

使用 `FakePermissionChecker` 类来模拟权限检查，方便后续单元和集成测试。

## 创建 Aspire 集成测试项目


创建 `HelloShop.FunctionalTests` 项目，该项目使用 Aspire 模板创建，用于集成测试，包括了一个 `FirstWebApiIntegrationTest` 测试类，用于测试所有微服务 Web API 的集成。

## 创建产品单元测试项目

创建 `HelloShop.ProductService.UnitTests` 项目，该项目使用 xUnit 模板创建，用于单元测试产品微服务中的代码，包括了一个 `ProductsControllerTest` 测试类，用于演示使用内存数据库模拟数据进行单元测试。包括一个 `MockProductsControllerTest` 测试类，用于演示使用 Moq 框架模拟数据进行单元测试。

## 创建产品集成测试项目

创建 `HelloShop.ProductService.FunctionalTests` 项目，该项目使用 xUnit 模板创建，用于集成测试产品微服务中的代码，包括了一个 `BrandApiIntegrationTest` 测试类，用于演示产品品牌 API 接口的集成测试，为了提高集成测试效率，使用 SQLite 内存数据库模拟数据，同时使用 `WebApplicationFactory` 类来模拟 Web 主机，以便在集成测试中使用真实的 HTTP 请求。通过使用 `WebApplicationFactory` 类，实现自动登录和令牌传递，以便在集成测试中使用真实的用户身份。

## 项目启动端口配置

为了项目更好的同时兼容 HTTP 和 HTTPS，每个微服务 `launchSettings` 文件中添加了 HTTP 和 HTTPS 端口配置，并约定 80xx 为 HTTP 端口，81xx 为 HTTPS 端口, 任何微服务都可通过两种协议访问以便于测试。

## 修复由于包升级导致的编译错误

这些升级包括 OpenApi 界面、删除过期的代码，修复由于包升级导致的编译错误。

## MAUI 项目升级

将 MAUI 项目升级到最新稳定版，修复由于包升级导致的编译错误。

## 后期版本计划

后期内容包括：Blazor 中控界面搭建、前端认证授权， MAUI 跨平台开发、安卓、IOS、MacOS、Windows 跨平台界面制作，后台自动任务管理、gRPC 微服务最佳实践、基于 Dapr 的事件服务总线和密钥管理、基于 Orleans 的分布式计算、基于 Aspire 项目的单机部署， K8S 集群容器化部署和 CI/CD 最佳实践，使用 OpenTelemetry 实现监控和追踪，使用 Prometheus 和 Grafana 实现监控和报警，分布式日志链路跟踪，性能追踪。