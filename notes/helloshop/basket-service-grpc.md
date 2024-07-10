# 使用 gRPC 实现购物车服务

## 什么是 gRPC 通信框架

gRPC 是一个高性能、开源和通用的 RPC 框架，由 Google 开发，基于 HTTP/2 协议，支持多种语言（如 Go、Java、Python、C++、Node.js、Ruby、C#、Objective-C、PHP 和 Dart）。

gRPC 使用 Protocol Buffers 作为接口定义语言（IDL），Protocol Buffers 是一种轻便高效的结构化数据序列化方法，类似于 XML 或 JSON，但更小、更快、更简单。

![basket-service-grpc](https://oss.xcode.me/notes/helloshop/basket-service-grpc.svg)

## ASP.NET Core 中使用 gRPC 实现服务

定义一个 gRPC 服务，需要创建一个 gRPC 服务定义文件（.proto 文件），然后使用 gRPC 工具生成服务端和客户端代码。


## 带有OpenAPI 的 gRPC 服务

```shell
dotnet add package Microsoft.AspNetCore.Grpc.Swagger
```

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddGrpc().AddJsonTranscoding();
builder.Services.AddGrpcSwagger();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1",
        new OpenApiInfo { Title = "gRPC transcoding", Version = "v1" });
});

var app = builder.Build();
app.UseSwagger();
if (app.Environment.IsDevelopment())
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
    });
}
app.MapGrpcService<GreeterService>();

app.Run();
```

```proto
// My amazing greeter service.
service Greeter {
  // Sends a greeting.
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  // Name to say hello to.
  string name = 1;
}
message HelloReply {
  // Hello reply message.
  string message = 1;
}
```

## 单元测试 gRPC 服务

```shell
dotnet add package moq
```
  
```csharp
public class GreeterServiceTests
{
    [Fact]
    public async Task SayHello_ReturnsHello()
    {
        // Arrange
        var mockLogger = new Mock<ILogger<GreeterService>>();
        var service = new GreeterService(mockLogger.Object);
        var request = new HelloRequest { Name = "Unit Test" };
        var context = new ServerCallContext();

        // Act
        var reply = await service.SayHello(request, context);

        // Assert
        Assert.Equal("Hello Unit Test", reply.Message);
    }
}
```

## 集成测试 gRPC 服务

```shell
dotnet add package Microsoft.AspNetCore.TestHost
```

```csharp
public class GreeterServiceIntegrationTests
{
    private readonly TestServer _server;
    private readonly Greeter.GreeterClient _client;

    public GreeterServiceIntegrationTests()
    {
        _server = new TestServer(new WebHostBuilder()
            .ConfigureServices(services =>
            {
                services.AddGrpc();
            })
            .Configure(app =>
            {
                app.UseRouting();
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGrpcService<GreeterService>();
                });
            }));
        _client = new Greeter.GreeterClient(_server.CreateGrpcChannel());
    }

    [Fact]
    public async Task SayHello_ReturnsHello()
    {
        // Arrange
        var request = new HelloRequest { Name = "Integration Test" };

        // Act
        var reply = await _client.SayHelloAsync(request);

        // Assert
        Assert.Equal("Hello Integration Test", reply.Message);
    }
}
```