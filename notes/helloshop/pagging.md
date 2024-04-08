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
