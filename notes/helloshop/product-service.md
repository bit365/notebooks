# 实现其它微服务

## 创建实体

```csharp
HelloWorld.ProductService.Entities.Products

Product & CatalogBrand

```

## EfCore 使用 PostgreSQL 数据库

```shell
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
```

## 创建 DbContext 上下文

```csharp
namespace HelloWorld.ProductService.EntityFrameworks
{
    public class ProductServiceDbContext(DbContextOptions<ProductServiceDbContext> options) : DbContext(options)
    {
        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);

            builder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
        }
    }
}
```

## 创建实体配置类

```csharp
namespace HelloWorld.ProductService.EntityFrameworks.EntityConfigurations.Products
{
    public class ProductEntityTypeConfiguration : IEntityTypeConfiguration<Product>
    {
        public void Configure(EntityTypeBuilder<Product> builder)
        {
            builder.ToTable("Products");

            builder.Property(x => x.Name).HasMaxLength(32);

            builder.HasOne(x => x.Brand).WithMany();
        }
    }
}
```


## 数据库连接字符串

```json
{
  "ConnectionStrings": {
    "ProductDatabase": "Host=localhost;Port=5432;Database=ProductService;Username=postgres;Password=postgres"
  }
}
```

## 注册数据库上下文

```csharp
builder.Services.AddDbContext<ProductServiceDbContext>(options =>
{
    options.UseNpgsql(builder.Configuration.GetConnectionString(DbConstants.ConnectionStringName));
});
```

## 迁移数据库

```shell
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
```

```shell
dotnet ef migrations add InitialCreate --output-dir EntityFrameworks/Migrations
dotnet ef database update
```

## 创建模型
    
```csharp
namespace HelloWorld.ProductService.Models.Products
{
    public class ProductCreateRequest
    {
        public required string Name { get; init; }

        public string? Description { get; init; }

        public decimal Price { get; init; }

        public int BrandId { get; init; }

        public string? ImageUrl { get; init; }
    }
}
```

## 创建自动映射
    
```csharp
namespace HelloWorld.ProductService.AutoMapper
{
    public class ProductsMapConfiguration : Profile
    {
        public ProductsMapConfiguration()
        {
            CreateMap<ProductCreateRequest, Product>();
            CreateMap<ProductUpdateRequest, Product>();
            CreateMap<Product, ProductListItem>().AfterMap((src, dest) => dest.BrandName = src.Brand.Name);
            CreateMap<Product, ProductDetailsResponse>();

            CreateMap<BrandCreateRequest, Brand>();
            CreateMap<BrandUpdateRequest, Brand>();
            CreateMap<Brand, BrandDetailsResponse>();
            CreateMap<Brand, BrandListItem>();
        }
    }
}
```

## 创建验证器

```csharp
namespace HelloWorld.ProductService.Validations.Products
{
    public class BrandUpdateRequestValidator : AbstractValidator<BrandUpdateRequest>
    {
        public BrandUpdateRequestValidator()
        {
            RuleFor(x => x.Id).GreaterThan(0);
            RuleFor(x => x.Name).NotNull().NotEmpty().Length(8, 32);
        }
    }
}
```

## 创建控制器

```csharp

namespace HelloWorld.ProductService.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
}
```

## 定义权限

```csharp
namespace HelloWorld.ProductService.PermissionProviders
{
    public static class CatalogPermissions

    public class CatalogPermissionDefiitionProvider : IPermissionDefinitionProvider
}
```

## 权限本地化
    
```csharp
CatalogPermissionDefinitionProvider.en-US.resx
CatalogPermissionDefinitionProvider.zh-CN.resx
```

## 重复性工作

重复性工作可以通过代码生成工具来减少，例如 Visual Studio 插件， T4 模板， Roslyn 等。