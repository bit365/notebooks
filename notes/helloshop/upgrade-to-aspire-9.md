# 零度框架升级到 Aspire 9.0 版本

## 升级 Visual Studio 开发工具

使用 Visual Studio Installer 升级，升级到  v17.12  后，自动安装 .NET 9.0 SDK 和 Aspire 9.0 工作负载。

## 非 Visual Studio 开发环境

### 安装 .NET Core SDK 9.0 版本

[安装最新 .NET SDK 9.0 版](https://dotnet.microsoft.com/zh-cn/download)

查看 .NET SDK 版本：

```bash
dotnet --version
dotnet --list-sdks
```

### 升级 Aspire 工作负载

在项目目录下执行以下命令：

```bash
dotnet workload uninstall aspire
dotnet workload install aspire
dotnet workload list
dotnet workload update
```

## 升级项目文件

Host 项目添加新节点：

```xml
<Sdk Name="Aspire.AppHost.Sdk" Version="9.0.0" />
```
所有项目文件的目标框架改为 net9.0：

```xml
<TargetFramework>net9.0</TargetFramework>
```

安装 EF Core 基础包

```bash
dotnet add package Microsoft.EntityFrameworkCore
```

升级解决方案中所有 NuGet 包

```bash
dotnet package upgrade --to-latest
```

## 可使工具简化

https://learn.microsoft.com/zh-cn/dotnet/core/porting/upgrade-assistant-install

## 服务发现支持 https+http 写法

```csharp
httpClient.BaseAddress = new Uri("https+http://identityservice")
```

