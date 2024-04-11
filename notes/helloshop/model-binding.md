## 模型绑定最佳实践

实体对象是 EF 中的概念， 每个实体对象对应数据库中的一张表。模型对象是 MVC 中的概念，是 HTTP 请求和响应的数据结构。HTTP 请求中通过 URL 参数、表单、标头、 JSON 数据等方式传递数据，这些数据最终会被绑定为模型对象中，模型经过转换为实体对象后，被持久化到数据库中。

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

```csharp
public class ProductModel
{
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

```csharp
public class ProductController : Controller
{
    private readonly ShopDbContext _context;

    public ProductController(ShopDbContext context)
    {
        _context = context;
    }

    [HttpPost]
    public async Task<IActionResult> Create(ProductModel model)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }

        var product = new Product
        {
            Name = model.Name,
            Price = model.Price
        };

        _context.Products.Add(product);
        await _context.SaveChangesAsync();

        return CreatedAtAction(nameof(Get), new { id = product.Id }, product);
    }
}
```

针对不同 HTTP 操作，应该使用不同的模型对象，比如创建模型、更新模型、查询模型、删除模型等，这样的好处是可以更好的区分模型对象的职责，比如创建模型只需要包含创建所需的字段，更新模型只需要包含更新所需的字段，查询模型只需要包含查询所需的字段，删除模型只需要包含删除所需的字段，职责单一，维护性好，还可以针对不同的模型提供不同的验证规则。

![model-entity-mapper](https://oss.xcode.me/notes/helloshop/model-entity-mapper.svg)
