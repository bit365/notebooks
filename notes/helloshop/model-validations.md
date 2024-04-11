# 模型自动验证机制

模型验证是 ASP.NET Core MVC 中的一个重要特性，它可以帮助我们验证用户输入的数据是否符合预期。 

## 基于数据注解的验证

参考微软 [数据注解](https://learn.microsoft.com/zh-cn/aspnet/core/mvc/models/validation?view=aspnetcore-5.0#built-in-attributes-1) 文档。

```csharp
public class User
{
    [Required]
    [StringLength(32)]
    public string Name { get; set; }

    [Required]
    [EmailAddress]
    public string Email { get; set; }
}
```

## 基于链式调用的验证

FluentValidation 是一个.NET库，用于构建类型安全的验证规则。它的设计目标是提供一个简单、清晰的API，同时还能够支持复杂的验证规则。

参考 [FluentValidation](https://fluentvalidation.net/) 官方文档。

```shell
dotnet add package FluentValidation.AspNetCore
```

## 实现验证器

```csharp
public class UserValidator : AbstractValidator<User>
{
    public UserValidator()
    {
        RuleFor(x => x.Name).NotEmpty().MaximumLength(32);
        RuleFor(x => x.Email).NotEmpty().EmailAddress();
    }
}
```

## 自动依赖注入

```csharp
services.AddValidatorsFromAssembly(assembly).AddFluentValidationAutoValidation();

ValidatorOptions.Global.LanguageManager = new CustomFluentValidationLanguageManager();
```

## 自定义验证错误消息

```csharp
public class UserValidator : AbstractValidator<User>
{
    public UserValidator()
    {
        RuleFor(x => x.Name).NotEmpty().MaximumLength(32).WithMessage("Name is required and must be less than 32 characters.");
        RuleFor(x => x.Email).NotEmpty().EmailAddress().WithMessage("Email is required and must be a valid email address.");
    }
}
```

## 自定义验证逻辑

```csharp
public class UserCreateRequestValidator : AbstractValidator<UserCreateRequest>
{
    public UserCreateRequestValidator(IdentityDbContext context)
    {
        RuleFor(m => m.UserName).NotNull().NotEmpty().Length(5, 20).Matches("^[a-zA-Z]+$");
        RuleFor(m => m.PhoneNumber).NotNull().NotEmpty().Length(11).Matches(@"^1\d{10}$").Must((model, phoneNumber) =>
        {
            return !context.Users.Any(e => e.PhoneNumber == phoneNumber);
        });
        RuleFor(m => m.Password).NotNull().NotEmpty().Length(5, 20);
        RuleFor(m => m.Email).EmailAddress().Length(5, 50);
    }
}
```


## 通用错误消息

```csharp
public class CustomFluentValidationLanguageManager : FluentValidation.Resources.LanguageManager
{
    public CustomFluentValidationLanguageManager()
    {
        AddTranslation("en", "NotNullValidator", "The {PropertyName} field is required.");
        AddTranslation("en", "MaximumLengthValidator", "The {PropertyName} field must be less than {MaxLength} characters.");
        AddTranslation("en", "EmailAddressValidator", "The {PropertyName} field must be a valid email address.");
    }
}
```