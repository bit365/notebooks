# 分布式总线设计

## 抽象分布式总线

使用设计模式，将分布式总线抽象为一个接口，定义了总线的基本功能。

![event-bus-abstraction](https://oss.xcode.me/notes/helloshop/event-bus-abstraction.svg)

## 基于事件总线的组件

![event-bus-component](https://oss.xcode.me/notes/helloshop/event-bus-component.svg)

https://github.com/dotnet/eShop/tree/main/src/EventBusRabbitMQ

## 事件总线的 Dapr 实现

![event-bus-endpoint](https://oss.xcode.me/notes/helloshop/event-bus-endpoint.svg)
