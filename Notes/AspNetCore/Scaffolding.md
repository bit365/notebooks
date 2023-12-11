# ASP.NET 脚手架和模板

## 什么是脚手架

脚手架是一种代码生成器，它可以根据模板生成代码。ASP.NET Core 脚手架是一个基于命令行的代码生成器，它可以生成基于 ASP.NET Core 的 Web 应用程序和代码。脚手架可以生成基于 MVC 和 Web API 的项目，还可以生成基于 Razor Pages 的项目。脚手架还可以生成控制器、视图和数据访问代码，以及用于测试的基础单元测试代码。

## 使用 Visual Studio 生成代码

右键单击项目“文件夹” > “添加” > “已搭建基架的新项”，选择要生成的项，然后单击添加。

## Visual Studio 系统模板

```
C:\Program Files\Microsoft Visual Studio\2022\Enterprise\Common7\IDE
```

## Visual Studio 用户模板

```
%UserProfile%\Documents\Visual Studio 2022\Templates
```

## 导出模板

在 Visual Studio 中，选择“项目” > “导出模板”，然后按照向导中的说明操作。

```
%UserProfile%\Documents\Visual Studio 2022\My Exported Templates
```

## 自定义基架模板

按照惯例，Visual Studio 将首先在 MVC 项目中查找 Templates 文件夹，然后再使用计算机级文件夹。

```
%UserProfile%\.nuget\packages\microsoft.visualstudio.web.codegenerators.mvc
```

## 使用命令行工具生成代码

#### 通过以下命令安装工具

```
dotnet tool install -g dotnet-aspnet-codegenerator
```

#### 以下命令演示控制器生成

```
dotnet aspnet-codegenerator controller --controllerName CustomersController --model Customer --dataContext EFContext --relativeFolderPath Controllers --force --useDefaultLayout --referenceScriptLibraries --layout "~/Views/Shared/_Layout.cshtml"
```

### 补充说明

通过源代码生成技术可以实现批量自动化生成，.NET Compiler Platform（Roslyn）SDK 附带的源生成器。 通过源生成器，C# 开发人员可以在编译用户代码时检查用户代码。 生成器可以动态创建项目文件，这些文件还可以添加到用户的编译过程中。

## 微软开源脚手架

https://github.com/dotnet/Scaffolding


