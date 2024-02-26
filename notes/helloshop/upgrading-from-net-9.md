# 更新 .NET 8.0 到 .NET 9.0

## 更新 Visual Studio 最新预览版

下载并安装 Visual Studio 预览版，安装 .NET 9.0 SDK。

## 更新项目文件

打开项目文件，将 `<TargetFramework>net8.0</TargetFramework>` 更新为 `<TargetFramework>net9.0</TargetFramework>`。

## 更新 NuGet 包

打开 NuGet 包管理器，更新所有 NuGet 包到最新版本。

## 更新 Aspire 和  MAUI 工作负载

打开 Aspire 工作负载，更新所有 Aspire 包到最新版本。

```shell
dotnet workload install aspire android ios maccatalyst maui-android maui-desktop maui-mobile maui-windows
```

## 若要将 Tab 自动补全添加到适用于 .NET CLI 的 PowerShell

https://learn.microsoft.com/zh-cn/dotnet/core/tools/enable-tab-autocomplete
