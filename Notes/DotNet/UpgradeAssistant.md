# 使用 .NET 迁移助手升级项目

## 什么是 .NET 升级助手？

.NET 升级助手是一个工具，可帮助您将老项目迁移到最新 .NET 框架的工具，它可以帮助您：

- 识别项目中的依赖项和 API 使用情况。
- 为项目提供 .NET 5 目标框架的建议。
- 提供升级项目所需的代码更改。


## 在 Visual Studio 中使用 .NET 升级助手

可以使用 Visual Studio 2022 安装 .NET 升级助手扩展。


## 在命令行中使用 .NET 升级助手

在命令行中使用 .NET 安装升级助手，需要安装 .NET 升级助手 CLI 工具。

```powershell
dotnet tool install -g upgrade-assistant
```

将工具迁移工具升级到最新版本

```powershell
dotnet tool update -g upgrade-assistant
```

在项目所在的文件夹中运行以下命令：

```powershell
upgrade-assistant --apply-updates
```
即可升级项目到最新版本。







