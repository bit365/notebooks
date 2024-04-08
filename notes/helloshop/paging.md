# 分页排序和多条件查询

## 分页参数

请求应使用 GET 方法

```shell
http://localhost:8080/api/products?keyword=test&pagenumber=1&pagesize=5&orderby=id desc,price asc
```

响应返回如下

```json
{
    "totalCount": 100,
    "items": [
        {
            "id": 1,
            "name": "test",
            "price": 100
        },
        {
            "id": 2,
            "name": "test",
            "price": 200
        }
    ]
}
```

## 分页请求模型

```csharp
public class PagedAndSortedRequest : PagedRequest
{
    public string? OrderBy { get; init; }
}
```

## 分页响应模型

```csharp
public class PagedResponse<T>(IReadOnlyList<T> items, int totalCount)
{
    public IReadOnlyList<T> Items { get; init; } = items;

    public int TotalCount { get; init; } = totalCount;
}
```

## 扩展 IQueryable 以便通过属性名称排序

```shell
QueryableOrderByExtensions.cs
```

将字符串条件转化为排序表达式

```csharp
IOrderedQueryable<TSource> OrderBy(IQueryable<TSource> source, string propertyName)
```

## 扩展 IQueryable 以便搜索和排序

```shell
QueryableExtensions.cs
```

扩展分页和排序方法

```csharp
IQueryable<TEntity> SortBy<TEntity>(this IQueryable<TEntity> query, string? orderBy = null);
IQueryable<TEntity> PageBy<TEntity>(this IQueryable<TEntity> query, PagedRequest pagedRequest)
IQueryable<TEntity> SortAndPageBy<TEntity>(this IQueryable<TEntity> query, PagedAndSortedRequest? pagedAndSortedRequest = null);
```

再扩展一个 WhereIf 条件查询

```csharp
IQueryable<TSource> WhereIf<TSource>(this IQueryable<TSource> source, bool condition, Expression<Func<TSource, bool>> predicate);
```

## 在 API 控制器中使用扩展方法

```csharp
[HttpGet]
[Authorize(IdentityPermissions.Users.Default)]
public async Task<ActionResult<PagedResponse<UserListItem>>> GetUsers([FromQuery] UserListRequest model)
{
    IQueryable<User> users = dbContext.Set<User>();

    if (model.Keyword is not null)
    {
        users = users.Where(e => e.UserName != null && e.UserName.Contains(model.Keyword));
    }

    users = users.WhereIf(model.PhoneNumber is not null, e => e.PhoneNumber == model.PhoneNumber);

    var pagedUsers = users.SortAndPageBy(model);

    var list = new List<UserListItem>();

    return new PagedResponse<UserListItem>(mapper.Map<List<UserListItem>>(await pagedUsers.ToListAsync()), await users.CountAsync());
}
```

## 实现灵活的复杂查询

可以使用 OData 或者 GraphQL 来实现更复杂的查询。

OData 是一种基于 REST 的协议，它使用 URL 来查询和操作数据。OData 通过 URL 查询字符串参数来过滤、排序、分页和选择数据。示例：

```shell
http://localhost:8080/api/products?$filter=price gt 100&$orderby=price desc&$top=5&$skip=10
```

关于 OData 的更多信息请参考 [零度 OData 课程](https://www.xcode.me/Training?keyword=odata)

GraphQL 是一种用于 API 的查询语言，它提供了一种更高效、强大和灵活的替代方案。GraphQL 通过一个单一的端点来查询和操作数据。示例：

```shell
http://localhost:8080/graphql
```

```graphql
query {
  products(filter: {price: {gt: 100}}, orderBy: {price: desc}, top: 5, skip: 10) {
    id
    name
    price
  }
}
```