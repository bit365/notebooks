# 使用 Visual Studio 2022 中的 .HTTP 文件

## 注释

以 # 或 // 开头的行是注释，当 Visual Studio 发送 HTTP 请求时，将忽略这些行。

![Alt text](https://oss.xcode.me/notes/2023-08-30-13-52-55_553ef9ac.png)

## 变量

以 @ 开头的行使用语法 @VariableName=Value 定义变量。

```http
@hostname=localhost
@port=44320
GET https://{{hostname}}:{{port}}/weatherforecast
```

## 多个请求

请求格式为：

```http
[<HTTP-verb>] <url> [<HTTP-version>]
[<request-headers>]
[<request-body>]
```

```http
GET https://localhost:7220/weatherforecast

###

GET https://localhost:7220/weatherforecast?date=2023-05-11&location=98006

###

GET https://localhost:7220/weatherforecast HTTP/3

###
```

## 请求标头

```http
GET https://localhost:7220/weatherforecast
Date: Wed, 27 Apr 2023 07:28:00 GMT

###

GET https://localhost:7220/weatherforecast
Cache-Control: max-age=604800
Age: 100

###
```

## 请求正文

```http
POST https://localhost:7220/weatherforecast
Content-Type: application/json

{
  "date": "2023-05-11",
  "location": "98006"
}
```

## 使用模板创建 HTTP 文件

1. 右键单击 ASP.NET Core 项目
2. 添加>新建项
3. ASP.NET Core>常规
4. 选择 HTTP 文件，然后选择添加

## 使用终结点资源管理器

视图>其他窗口>终结点资源管理器

## 定义一个 ef core dbcontext

```csharp
public class WeatherForecastContext : DbContext
{
    public WeatherForecastContext(DbContextOptions<WeatherForecastContext> options)
        : base(options)
    {
    }

    public DbSet<WeatherForecast> WeatherForecasts { get; set; }
}
```
