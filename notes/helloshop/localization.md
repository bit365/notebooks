# 全球化与本地化

实现多语言的方法有很多种，可以使用资源文件、数据库、配置文件等方式，本文主要介绍使用资源文件的方式实现多语言，也是微软官方推荐的方式。其它的比如 PO 文件、JSON 文件等也可以实现多语言，但是不如资源文件方便.

## 创建资源文件

```text
Welcome.en-US.resx
Welcome.zh-CN.resx
```

## 添加本地化服务和资源定位

```csharp
public static IServiceCollection AddCustomLocalization(this IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    return services;
}
```

## 使用本地化服务

```csharp
public class HelloWorldController(IStringLocalizerFactory stringLocalizerFactory) : ControllerBase
{
    [HttpGet]
    public IActionResult Get()
    {
        var location = Assembly.GetExecutingAssembly().FullName;

        ArgumentException.ThrowIfNullOrWhiteSpace(location);

        var localizer = stringLocalizerFactory.Create("Welcome", location);

        return Ok(localizer["HelloWorld"].Value);
    }
}
```

## 使用 HTTP 请求设置语言

UseRequestLocalization 中间件从请求中获取语言设置，然后设置当前线程的语言，以便在后续的请求中使用，这样就可以实现全局的本地化，而不需要在每个控制器中设置本地化。

```csharp
public static IApplicationBuilder UseCustomLocalization(this IApplicationBuilder app)
{
    var supportedCultures = new[] { "zh-CN", "en-US" };

    var localizationOptions = new RequestLocalizationOptions().SetDefaultCulture(supportedCultures.First())
        .AddSupportedCultures(supportedCultures)
        .AddSupportedUICultures(supportedCultures);

    app.UseRequestLocalization(localizationOptions);

    return app;
}
```

使用下面的方式从 Header 、 QueryString 或者 Cookie 中获取语言设置：

```text
Accept-Language: en-US
```

```text
http://localhost:5000/?culture=en-Us
```

```text
.AspNetCore.Culture=en-US
```

## OpenApi 设置 Accept-Language 以实现多语言

```csharp
services.Configure<SwaggerGenOptions>(options => options.OperationFilter<AcceptLanguageHeaderOperationFilter>());
```

```csharp
public class AcceptLanguageHeaderOperationFilter : IOperationFilter
{
    public void Apply(OpenApiOperation operation, OperationFilterContext context)
    {
        var parameter = new OpenApiParameter
        {
            Name = HeaderNames.AcceptLanguage,
            In = ParameterLocation.Header,
            Required = false,
            Schema = new OpenApiSchema
            {
                Default = new OpenApiString("zh-CN"),
                Type = "string",
                Enum = [new OpenApiString("zh-CN"), new OpenApiString("en-US")]
            }
        };

        operation.Parameters.Add(parameter);
    }
}

```

## 实现数据展示的全球化

```csharp

public class HelloWorldController(IStringLocalizerFactory stringLocalizerFactory) : ControllerBase
{
    [HttpGet("time")]
    public IActionResult GetTime(
    {
        return Ok(TimeProvider.UtcNow.ToString());
    }
}
```

## 实现模型和属性的本地化

```csharp
    IStringLocalizerFactory localizerFactory = app.ApplicationServices.GetRequiredService<IStringLocalizerFactory>();

    ValidatorOptions.Global.DisplayNameResolver = (type, memberInfo, lambdaExpression) =>
    {
        string displayName = memberInfo.Name;

        DisplayAttribute? displayAttribute = memberInfo.GetCustomAttribute<DisplayAttribute>(true);

        displayName = displayAttribute?.Name ?? displayName;

        DisplayNameAttribute? displayNameAttribute = memberInfo.GetCustomAttribute<DisplayNameAttribute>(true);

        displayName = displayNameAttribute?.DisplayName ?? displayName;

        var localizer = localizerFactory.Create(type);

        return localizer[displayName];
    };
```

## 本地化验证错误消息

```csharp
public UserCreateRequestValidator(IdentityServiceDbContext dbContext, IStringLocalizer<UserCreateRequest> localizer)
{
    RuleFor(m => m.PhoneNumber).NotNull().NotEmpty().Length(11).Matches(@"^1\d{10}$").Must((model, phoneNumber) =>
    {
        return !dbContext.Users.Any(e => e.PhoneNumber == phoneNumber);
    }).WithMessage(localizer["PhoneNumberExists"]);
}
```

## 微软 Resx 编辑工具

必应搜索 `Resx Editor` 关键字可以找到很多工具，可以编辑 Resx 文件。

https://learn.microsoft.com/zh-cn/dotnet/framework/tools

## 使用必应翻译资源文件

必应搜索 `Resx Translation` 关键字可以找到很多翻译工具，可以将英文资源文件翻译成其他语言。

![自动翻译工具](https://oss.xcode.me/notes/helloshop/auto-resx-translator.png)

https://github.com/salarcode/AutoResxTranslator

https://github.com/stevencohn/ResxTranslator

https://github.com/HakanL/resxtranslator

https://apps.microsoft.com/detail/9mtpd7jzxnnn
