+++
title = "关于发布订阅者模式"
description = "发布订阅者模式，为什么，是什么，怎么用。"
date = 2021-05-01T18:10:00+00:00
updated = 2021-05-01T18:10:00+00:00
draft = false
weight = 410
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = """发布-订阅模式是一种行为设计模式，它允许多个插件或实例通过事件的发布和订阅来进行通信。
<br/>
在这种模式中，发布者(又称为主题)负责发布事件，而订阅者(也称为观察者)则通过订阅主题来接收这些事件。
<br/>
这种模式使得应用程序的不同部分能够松散耦合，并且可以动态地添加或删除订阅者。"""
toc = true
top = false
+++

### kokoro-flume-channel
鉴于发布-订阅模式支持动态特性，Kokoro 也采用了这种模式。我们提供了 `kokoro-flume-channel`（以下简称 `flume-channel`），它基于 [flume](https://github.com/zesterer/flume) 库实现了进程内的发布-订阅模式。`flume` 库的详细特性可在其[官方仓库](https://github.com/zesterer/flume)中查看。我们对 `flume` 进行了封装，以简化发布-订阅模式的实现。

#### 示例用法
```rust
// flume-channel 提供的函数，用于创建一个 `Mode` 为 `MPSC` 的 `Context`，其 `Resources` 为 `()`
let ctx = channel_ctx();

// 注册一个 `Subscriber`
ctx.subscribe(..);
```

`Subscriber` 是一个 `trait`，任何实现了 `trait Subscriber` 的类型都可以注册为订阅者。默认实现包括：

1. `FnMut()`
2. `FnMut(&Context)`
3. `FnMut(Query)`
4. `FnMut(&Context, Query)`

订阅者被执行的时机由 `Query` 决定，有关 `Query` 的相关信息，请参阅 [关于订阅查询 (待补充)](#)。

```rust
// 定义事件 `Hello`
#[derive(Event)]
struct Hello(String);

fn foo(e: &Hello) {
    println!("{}", e.0);
}

let ctx = channel_ctx();
ctx.subscribe(foo); // 订阅事件 `Hello`
ctx.publish(Hello("Hello World".to_string())); // 发布事件 `Hello`
```

在上述代码中，订阅者不会立即执行，因为发布操作本质上是 `sender.send`，还需要调用 `receiver.recv`。因此，我们提供了以下方法：

1. `ctx.run()` - 迭代 `receiver`（会阻塞线程）
2. `ctx.next()` - 单次 `recv`（暂未实现）
3. `ctx.run_no_block()` - 非阻塞迭代 `receiver`（暂未实现）

最终，我们实现了一个简单的发布-订阅示例 **Hello World**：

```rust
#[derive(Event)]
struct Hello(String);
fn foo(e: &Hello) {
    println!("{}", e.0);
}
let ctx = channel_ctx();
ctx.subscribe(foo);
ctx.publish(Hello("Hello World".to_string()));
// 运行
ctx.run();
// 输出：Hello World
```

在 `kokoro-flume-channel` 中，一个事件可以由多个发布者发布，并且可以由多个订阅者订阅。需要注意的是，这是一个广播系统，而不是单一消费者模型。

在 **Hello World** 示例中，发布操作并不是常规的发布方式。发布者应在单独的线程中工作，以便与 `ctx.run()` 协同运行。关于线程的生成和终止时机，请参阅 [**关于线程** (待补充)](#)。
