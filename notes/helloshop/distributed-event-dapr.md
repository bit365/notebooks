# 分布式事件最佳实践

## 安装 Dapr CLI 脚手架工具

https://docs.dapr.io/zh-hans/getting-started/install-dapr-cli

## 基于容器初始化 Dapr 运行时

https://docs.dapr.io/zh-hans/getting-started/install-dapr-selfhost

```shell
dapr init
```

## 不用容器离线初始化 Dapr 运行时

```shell
dapr init -s
```


## 使用离线包初始化 Dapr 运行时

https://docs.dapr.io/zh-hans/operations/hosting/self-hosted/self-hosted-airgap/

```shell
dapr init --from-dir C:\daprbundle
```

## 打开默认初始化目录

```shell
explorer "$env:USERPROFILE\.dapr"
explorer "%USERPROFILE%\.dapr"
```

## 使用 Dashboard 查看 Dapr 运行状态

```shell
dapr dashboard
```

## 发布订阅配置分布式事件

```shell
/components/redis-pubsub.yaml
```

### 基于 Redis 发布订阅配置

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub
spec:
  type: pubsub.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
```

### 基于 RabbitMQ 发布订阅配置

```shell
/components/rabbitmq-pubsub.yaml
```

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub
spec:
  type: pubsub.rabbitmq
  version: v1
  metadata:
  - name: host
    value: "amqp://guest:guestpwd@localhost:5672"
  - name: durable
    value: "false"
  - name: deletedWhenUnused
    value: "false"
  - name: autoAck
    value: "false"
  - name: reconnectWait
    value: "0"
  - name: concurrency
    value: parallel
```

### 使用 Dapr 命令启动 Sidecar 和应用程序
    
```shell
dapr run --app-id myapp --dapr-http-port 3500 --dapr-grpc-port 50001 --app-port 5000 --log-level debug --config ./configuration/config.yaml --components-path ./components dotnet run
```

## 使用 Aspire 命令启动 Sidecar 和应用程序

```shell
dotnet add package Aspire.Hosting.Dapr
```

```csharp
var pubsub = builder.AddDaprPubSub("pubsub", new DaprComponentOptions { LocalPath = "./DaprComponents/" });

var productService = builder.AddProject<Projects.HelloWorld_ProductService>("productservice")
    .WithReference(identityService)
    .WithDaprSidecar()
    .WithReference(pubsub);

```


## 使用 RabbitMQ 发布订阅配置

```shell
var username = builder.AddParameter("username", secret: true);
var password = builder.AddParameter("password", secret: true);

var messaging = builder.AddRabbitMQ("messaging", username, password);

// Service consumption
builder.AddProject<Projects.ExampleProject>()
       .WithReference(messaging);
```