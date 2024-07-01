# 零度框架中的测试

使用自动测试是确保应用程序代码按作者期望执行操作的一种绝佳方式。 零度框架中提供单元测试、集成测试和负载测试，.NET 平台上的测试框架有 `xUnit`、`NUnit` 和 `MSTest`，用的最多的是 `xUnit`，无论使用任何一种测试框架，都可以通过命令行或者 IDE 来运行测试。

## 单元测试

单元测试是一种试验单个软件组件或方法（也称为“工作单元”）的测试。 单元测试仅应测试开发人员控件内的代码。 它们不测试基础结构问题。 基础结构问题包括与数据库、文件系统和网络资源的交互，零度框架中的单元测试使用 `xUnit` 框架，

项目命名规范： `<ProjectName>.UnitTests`。

```csharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}

public class CalculatorTests
{
    [Fact]
    public void Add_WhenCalled_ReturnsTheSumOfArguments()
    {
        // Arrange
        var calculator = new Calculator();

        // Act
        var result = calculator.Add(1, 2);

        // Assert
        Assert.Equal(3, result);
    }
}
```

### 单元测试最佳做法

https://learn.microsoft.com/zh-cn/dotnet/core/testing/unit-testing-best-practices

## 集成测试

集成测试也称为功能测试，与单元测试的不同之处在于，它试验两个或更多软件组件一同工作集成能力。这些测试在更广泛范围的受测系统上运行，而单元测试则侧重于单个组件函数，通常，集成测试会包括对基础结构问题的测试，例如数据库、文件系统和网络资源的交互。

项目命名规范： `<ProjectName>.FunctionalTests`。

```csharp
public class Controller: ControllerBase
{
    private readonly IService _service;

    public Controller(IService service)
    {
        _service = service;
    }

    [HttpGet]
    public async Task<IActionResult> Get(int id)
    {
        var result = await _service.Get(id);
        return Ok(result);
    }
}

public class Service: IService
{
    public async Task<Model> Get(int id)
    {
        return await _repository.Get(id);
    }
}

public class ServiceTest: IClassFixture<WebApplicationFactory<Startup>>
{
    private readonly WebApplicationFactory<Startup> _factory;

    public ServiceTest(WebApplicationFactory<Startup> factory)
    {
        _factory = factory;
    }

    [Fact]
    public async Task Get_WhenCalled_ReturnsModel()
    {
        // Arrange
        var client = _factory.CreateClient();
        var response = await client.GetAsync("/api/controller/1");
        response.EnsureSuccessStatusCode();
        var model = await response.Content.ReadAsAsync<Model>();

        // Assert
        Assert.NotNull(model);
    }
}
```