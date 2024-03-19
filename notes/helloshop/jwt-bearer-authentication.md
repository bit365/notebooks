# 使用 JwtBearer 令牌进行身份验证


## 介绍

JwtBearer 令牌身份验证是一种基于 JSON Web 令牌的身份验证方法, 用于验证用户的身份, 它是一种无状态的身份验证方法, 适用于 Web API 和 Web 应用程序。


## 安装 NuGet 包

```shell
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

## 自建 Identity Api 终结点

```csharp
builder.Services.AddIdentity<User, Role>(options =>
{
    options.SignIn.RequireConfirmedAccount = false;
    options.Password.RequireDigit = false;
    options.Password.RequireLowercase = false;
    options.Password.RequireUppercase = false;
    options.Password.RequireNonAlphanumeric = false;
    options.Password.RequiredLength = 5;
}).AddEntityFrameworkStores<IdentityServiceDbContext>();
```

## 使用 JwtBearer 验证令牌

```csharp

builder.Services.AddAuthentication(options =>
{
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultSignInScheme = CustomJwtBearerDefaults.AuthenticationScheme;
}).AddJwtBearer(options =>
{
    options.TokenValidationParameters.ValidateIssuer = false;
    options.TokenValidationParameters.ValidateAudience = false;
    options.TokenValidationParameters.IssuerSigningKey = new SymmetricSecurityKey(Encoding.Default.GetBytes(issuerSigningKey));

})
```

## 自定义身份认证处理程序

```csharp
public class CustomJwtBearerDefaults

public class CustomJwtBearerOptions

public class CustomJwtBearerHandler

public class CustomJwtBearerExtensions
```

## 配置令牌生成

```csharp
builder.Services.AddAuthentication().AddJwtBearer().AddCustomJwtBearer(options =>
{
    options.IssuerSigningKey = issuerSigningKey;
    options.SecurityAlgorithm = SecurityAlgorithms.HmacSha256;
});
```

## 在容器中运行 pgAdmin 管理工具

```shell

docier pull dpage/pgadmin4

docker run --name pgadmin -e PGADMIN_DEFAULT_EMAIL=test@test.com -e PGADMIN_DEFAULT_PASSWORD=test -e TZ=Asia/Shanghai -d -p 5050:80 dpage/pgadmin4
```