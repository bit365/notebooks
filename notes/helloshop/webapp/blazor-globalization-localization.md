# Blazor 全球化与本地化

Blazor 应用程序可以通过使用 .NET Core 的全球化和本地化功能来支持多种语言和文化。本文将介绍如何在 Blazor 应用程序中实现全球化和本地化。

## Blazor WebAssembly

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```
```shell
dotnet add package Microsoft.Extensions.Localization
```

## Blazor Server

```csharp
builder.Services.AddCustomLocalization();
```
```csharp
app.UseCustomLocalization();
```
