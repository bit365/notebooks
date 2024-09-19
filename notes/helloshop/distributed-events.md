# 使用 Dapr 实现分布式事件总线

## 抽象分布式总线

使用设计模式，将分布式总线的使用方式进行抽象，定义了总线的基本功能。

![event-bus-abstraction](https://oss.xcode.me/notes/helloshop/event-bus-abstraction.svg)

## 基于事件总线的组件

使用组件化的方式实现分布式事件总线，将总线的实现细节封装在组件中。

![event-bus-component](https://oss.xcode.me/notes/helloshop/event-bus-component.svg)

基于 RabbbitMQ 实现：https://github.com/dotnet/eShop/tree/main/src/EventBusRabbitMQ

## 事件总线的 Dapr 实现

Dapr 是一个开源的分布式应用程序运行时，发布和订阅模块使微服务能够使用事件驱动架构的消息相互通信，Dapr 提供了一个事件总线的实现，使用 Dapr 发布事件，提供 Dapr 订阅终结点，将消息路由到不同的事件处理程序。
 

![event-bus-endpoint](https://oss.xcode.me/notes/helloshop/event-bus-endpoint.svg)
