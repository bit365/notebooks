# 聚合 OpenApi 文档

每个微服务都有自己的 OpenApi 文档，但是在实际开发中，我们更希望能够将所有微服务的 OpenApi 文档聚合到一起，以便于查看和调试，微服务都是基于 Aspire 框架开发的，所以我们可以使用 Aspire 框架提供的服务发现功能来自动聚合所有微服务的 OpenApi 文档。

## 使用 Aspire 服务发现自动配置 OpenApi 文档

```csharp
public class OpenApiConfigureOptions() : IConfigureOptions<SwaggerUIOptions>
```

```csharp
builder.Services.AddTransient<IConfigureOptions<SwaggerUIOptions>, OpenApiConfigureOptions>();
```

## 定制 OpenApi 文档样式

在 Resource/OpenApi 文件夹下创建 `Custom.css` 文件。

```css
.swagger-ui .topbar-wrapper img {
    content: url('https://test.com/logo.svg');
}

.swagger-ui .topbar-wrapper .link::after {
    margin-left: 0.5rem;
    content: "HelloWorld";
}
```

## 关联 CSS 文件到 OpenApi 文档

```csharp
public static class OpenApiExtensions
{
    public static IServiceCollection AddOpenApi(this IServiceCollection)
    {
       services.Configure<SwaggerUIOptions>(options =>
       {
           options.DocumentTitle = Assembly.GetExecutingAssembly().GetName().Name;
           options.InjectStylesheet("/ServiceDefaults/Resources/OpenApi/Custom.css");
       });
    }
}
```

```csharp
public static IApplicationBuilder UseOpenApi(this IApplicationBuilder app)
{
    // Configure the HTTP request pipeline.
    app.UseSwagger(apiConfigureOptions)
    app.Map("/ServiceDefaults", appBuilder => appBuilder.UseStaticFiles(new StaticFileOptions
    {
        FileProvider = new EmbeddedFileProvider(Assembly.GetExecutingAssembly())
    }))

    return app;
}
```
