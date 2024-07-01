# 负载测试和压力测试

## 负载测试

测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标，应用在正常状态下运行。

## 压力测试

在极端条件下（通常为长时间）运行时测试应用的稳定性，测试会对应用施加高用户负载（峰值或逐渐增加的负载）或限制应用的计算资源。压力测试可确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为，在压力下，应用在异常高的压力下运行。

## 测试工具

云端测试 和 Apache JMeter 是两种流行的负载测试工具。负载测试和压力测试应在发布和生产模式下完成，而不是在调试和开发模式下进行。 

## 使用 Apache JMeter 工具

Apache JMeter 是一个开源的 Java 应用程序，用于执行负载测试、功能测试和性能测试。JMeter 可以模拟多种类型的负载，包括负载测试、压力测试、功能测试、基准测试等。

提供强大的图形化界面，可以通过图形化界面创建测试计划，然后执行测试计划。JMeter 支持多种协议，包括 HTTP、HTTPS、FTP、JMS、SOAP、TCP 和 LDAP，还可以通过插件支持其他协议。

https://jmeter.apache.org/usermanual/build-web-test-plan.html


## 基准测试

基准测试是一种测试方法，用于确定应用程序的性能基准，以便在应用程序的生命周期中进行性能优化。使用 `BenchmarkDotNet` 可以在 .NET 应用程序中进行基准测试。

```shell
dotnet add package BenchmarkDotNet
```

```csharp
public class Md5VsSha256
{
    [Benchmark]
    public byte[] Sha256() => sha256.ComputeHash(data);

    [Benchmark]
    public byte[] Md5() => md5.ComputeHash(data);
}
```
```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BenchmarkRunner.Run<Md5VsSha256>();
    }
}
```