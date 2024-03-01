# 用于生成初始数据的提供程序

数据播种是使用一组初始数据填充数据库的过程，通常在数据库首次创建时执行，这些数据通常是静态的，不会随时间变化，数据播种通常用于填充一些基本数据，例如用户、角色、权限、商品、分类等。 

## 通用接口设计

```csharp
public interface IDataSeedingProvider
{
    Task SeedingAsync(IServiceProvider serviceProvider);
}
```

## 实现具体接口

```csharp
public class DataSeedingProvider(UserManager<User> userManager) : IDataSeedingProvider
{
    public async Task SeedingAsync(IServiceProvider ServiceProvider)
    {
        var user = await userManager.FindByNameAsync("admin");

        if (user == null)
        {
            await userManager.CreateAsync(new User
            {
                UserName = "admin",
                Email = "admin@test.com"
            }, "admin");
        }
    }
}
```

## 在容器中注册

```csharp
builder.Services.AddTransient<IDataSeedingProvider, DataSeedingProvider>();
```

## 自动执行扩展

```csharp
public static class DataSeedingExtensions
{
    public static IApplicationBuilder UseDataSeedingProviders(this IApplicationBuilder app)
    {
        using var serviceScope = app.ApplicationServices.CreateScope();

        var dataSeedingProviders = serviceScope.ServiceProvider.GetServices<IDataSeedingProvider>();

        foreach (IDataSeedingProvider dataSeedingProvider in dataSeedingProviders)
        {
            dataSeedingProvider.SeedingAsync(serviceScope.ServiceProvider).Wait();
        }

        return app;
    }
}
```

## 自动注册实现

```csharp
public static class DataSeedingExtensions
{
    public static IServiceCollection AddDataSeedingProviders(this IServiceCollection services, Assembly? assembly = null)
    {
        assembly ??= Assembly.GetCallingAssembly();

        var dataSeedProviders = assembly.ExportedTypes.Where(t => t.IsAssignableTo(typeof(IDataSeedingProvider)) && t.IsClass);

        dataSeedProviders.ToList().ForEach(t => services.AddTransient(typeof(IDataSeedingProvider), t));

        return services;
    }
}
``` 

## 执行顺序问题

自动注册的提供程序的执行顺序是不确定的，如果需要确定执行顺序，可以设计 Order 属性，然后在 UseDataSeedingProviders 方法中按照 Order 属性排序执行。


## 在微服务中使用

```csharp
builder.Services.AddDataSeedingProviders();
app.UseDataSeedingProviders();
```