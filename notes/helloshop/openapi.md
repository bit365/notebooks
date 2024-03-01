# 使用 OpenAPI 规范生成 API 文档

Swagger 是一个与语言无关的规范，用于描述 REST API。 它使计算机和用户无需直接访问源代码即可了解 REST API 的功能，Swagger 项目已于捐赠给 OpenAPI 计划，自此它被称为 OpenAPI 规范。

Swashbuckle 是一个用于 .NET Core 的开源项目，它是 OpenAPI 工具的集合，用于生成 OpenAPI 规范的文档。 它可以自动生成 API 文档，包括 API 的描述、请求和响应的格式、参数的描述等。

## 安装 Swashbuckle.AspNetCore

在 .NET Core 项目中，可以通过 NuGet 安装 Swashbuckle.AspNetCore 包。

```shell
dotnet add package Swashbuckle.AspNetCore
```

## 配置 Swashbuckle

在 Startup.cs 文件中，可以通过以下方式配置 Swagger：

```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
```

