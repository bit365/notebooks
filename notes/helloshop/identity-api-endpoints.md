# 身份认证系统

## ASP.NET Core Identity 体系结构

ASP.NET Core Identity 是一个成熟的身份认证系统，它提供了用户管理、角色管理、声明管理、密码管理、登录管理、外部登录管理、双重身份认证、电子邮件确认、电话号码确认、安全令牌管理、用户锁定、用户解锁、用户注销、用户删除、用户恢复等功能。

## Identity 体系结构说明

|实体类型|说明                                                 |
|-----------|--------------------------------------------------|
|`User`     |表示用户。                                        |
|`Role`     |表示角色。                                        |
|`UserClaim`|表示用户拥有的声明。                    |
|`UserToken`|表示用户的身份验证令牌。               |
|`UserLogin`|将用户与登录名相关联。                            |
|`RoleClaim`|表示向角色内的所有用户授予的声明。|
|`UserRole` |关联用户和角色的联接实体。               |


## 使用 EfCore 存储 Identity 数据

### 定义实体类型

```csharp
public class User : IdentityUser<int>
{
    public DateTimeOffset CreationTime { get; set; } = DateTimeOffset.UtcNow;
}

public class Role : IdentityRole<int>
{
    public DateTimeOffset CreationTime { get; set; } = DateTimeOffset.UtcNow;
}
```

### 配置实体类型

```csharp
public class UserEntityTypeConfiguration : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("Users");
        builder.HasKey(x => x.Id);
        builder.Property(x => x.CreationTime);
    }
}
```

### 复用 Identity 数据库上下文

```csharp
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

```csharp
public class IdentityServiceDbContext(DbContextOptions<IdentityServiceDbContext> options) : IdentityDbContext<User, Role, int(options)
{
    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
        builder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
        AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true);
        AppContext.SetSwitch("Npgsql.DisableDateTimeInfinityConversions", true);
    }
}
```

## 使用 Identity API 终结点

```csharp
builder.Services.AddIdentityApiEndpoints<User>(options => {
    options.Password.RequireDigit = false;
    options.Password.RequireLowercase = false;
    options.Password.RequireUppercase = false;
    options.Password.RequireNonAlphanumeric = false;
    options.Password.RequiredLength = 5;
    options.SignIn.RequireConfirmedAccount = false;
}).AddEntityFrameworkStores<IdentityServiceDbContext>();

app.MapGroup("identity").MapIdentityApi<User>();
```

## 操作 Identity 数据 

UserManager、RoleManager、SignInManager、PasswordValidator 等服务都可以用来操作 Identity 数据，没有特殊情况不建议使用 DbContext 直接操作 Identity 数据。

## 使用终结点资源管理器

可以在 Visual Studio 2022 以上版本中，点击 "视图" > "其他窗口" > "终结点资源管理器" 来打开该功能。

## 测试 API 接口

CURL 命令行工具、Edge 网络控制台、OpenApi、HTTP 文件、Postman、Insomnia、Visual Studio Code REST Client 插件等工具都可以用来测试 API 接口。


## 参考资料

https://learn.microsoft.com/zh-cn/aspnet/core/security/authentication/identity-api-authorization

https://www.cnblogs.com/dongfo/p/17808249.html