# 使用分布式锁解决并发问题

## 单进程应用程序中的锁

在单机环境下，我们可以使用线程锁来解决并发问题，但是在分布式系统中，线程锁无法解决并发问题，因为分布式系统中的线程锁只能锁住当前进程，无法锁住其他进程。

在 .NET 中，我们可以使用 `lock` 关键字来实现线程锁。

https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/lock

```csharp
private static readonly object _lock = new object();

public void DoSomething()
{
    lock (_lock)
    {
        // 业务逻辑
    }
}
```

C# 13 引入了新的线程同步类型 System.Threading.Lock，它通过作用域管理的方式简化了锁的使用，使代码更加清晰可靠。

```csharp
using System.Threading.Lock;

private static readonly Lock _lock = new Lock();

public void DoSomething()
{
    using (_lock.EnterScope())
    {
        // 业务逻辑
    }
}
```

## 开源分布式锁的实现

DistributedLock 是一个 .NET 库，它基于各种底层技术提供强大且易于使用的分布式互斥锁、读写器锁和信号量。

https://github.com/madelson/DistributedLock

## 零度框架中的分布式锁

零度框架中提供分布式锁的基本抽象，并基于 Dapr 实现了分布式锁。

https://docs.dapr.io/developing-applications/building-blocks/distributed-lock/distributed-lock-api-overview


## Redis 分布式锁原理

Redis 分布式锁的实现原理是通过 SETNX 命令，SETNX 是 Redis 的一个原子性操作，它会在键不存在时设置键的值，如果键已经存在，则不做任何操作。