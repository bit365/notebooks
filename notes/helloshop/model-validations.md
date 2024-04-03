# 模型自动验证机制

## 使用 FluentValidation 开源库

FluentValidation 是一个.NET库，用于构建类型安全的验证规则。它的设计目标是提供一个简单、清晰的API，同时还能够支持复杂的验证规则。

```shell
dotnet add package FluentValidation.DependencyInjectionExtensions
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
builder.Services.AddValidatorsFromAssembly(Assembly.GetExecutingAssembly());
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

## 通用错误消息

```csharp
public class UserValidator : AbstractValidator<User>
{
    public UserValidator()
    {
        RuleFor(x => x.Name).NotEmpty().MaximumLength(32).WithMessage("{PropertyName} is required and must be less than {MaxLength} characters.");
        RuleFor(x => x.Email).NotEmpty().EmailAddress().WithMessage("{PropertyName} is required and must be a valid email address.");
    }
}
```