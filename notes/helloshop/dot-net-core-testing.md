# 零度框架中的测试

使用自动测试是确保应用程序代码按作者期望执行操作的一种绝佳方式。 零度框架中提供单元测试、集成测试和负载测试，.NET 平台上的测试框架有 `xUnit`、`NUnit` 和 `MSTest`，用的最多的是 `xUnit`，无论使用任何一种测试框架，都可以通过命令行或者 IDE 来运行测试。

## 单元测试

单元测试是一种试验单个软件组件或方法（也称为“工作单元”）的测试。 单元测试仅应测试开发人员控件内的代码。 它们不测试基础结构问题。 基础结构问题包括与数据库、文件系统和网络资源的交互。

项目命名规范： `<ProjectNamej>.UnitTests`。

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

集成测试与单元测试的不同之处在于，它试验两个或更多软件组件一同工作（也称为其“集成”）的能力。这些测试在更广泛范围的受测系统上运行，而单元测试则侧重于单个组件。 通常，集成测试会包括对基础结构问题的测试。

项目命名规范： `<ProjectNamej>.FunctionalTests`。

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

## 负载测试和压力测试


负载测试：测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标，应用在正常状态下运行。

压力测试：在极端条件下（通常为长时间）运行时测试应用的稳定性，测试会对应用施加高用户负载（峰值或逐渐增加的负载）或限制应用的计算资源。

压力测试可确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为，在压力下，应用在异常高的压力下运行。

## 在 Blazor 中测试 Razor 组件

 Playwright for .NET 就是一个可用于 Blazor 应用的 E2E 测试框架。没有面向 Blazor 的官方 Microsoft 测试框架，但社区驱动的项目 bUnit 提供一种方便的方法来对 Razor 组件进行单元测试。bUnit 适用于常规用途的测试框架，例如 MSTest、NUnit 和 xUnit。