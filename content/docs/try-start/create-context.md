+++
title = "创建上下文"
description = "如何创建上下文"
date = 2021-05-01T19:30:00+00:00
updated = 2021-05-01T19:30:00+00:00
draft = false
weight = 10
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "使用 Kokoro 从创建上下文开始。"
toc = true
top = false
+++

## Context

`Context` 是 Kokoro 的核心，一切都从这里出发。

它有两个泛型，分别是 `Resource` 和 `Mode`。

首先要决定 `Mode` 它指明了 `Context` 在何种模式下工作。

我们这里使用 [kokoro-flume-channel](../../default-implement/pub-sub) 中的 `MPSC`。

它可以提供发布订阅的能力，并且 `kokoro-plugin-tiny-http` 也是基于发布订阅的。

[kokoro-flume-channel](../../default-implement/pub-sub) 提供了一个简单创建 Context 的工具函数。

```rust
fn main() {
    // 创建了一个工作在 MPSC 模式下的上下文
    let ctx = channel_ctx();
}
```

这样创建的 `Context` 它的 `Resource` 是 `()` 我们暂时不需要为起点增加任何资源，所以让我们保持默认。

## 进阶

说白了就是自己从头创建 `Context`。

假设我们仍然使用 `MPSC` 但是 `Resource` 改用自己的，比如说

```rust
struct MyName {
    name: String
}
```

那么 `Context` 可以这样创建

```rust
fn main() {
    let res = MyName { name: "Kokoro".to_string() };
    let scope = Scope::create(Arc::new(res));
    let mode = MPSC::<()>::default();
    let ctx = Context::create(Arc::new(scope), Arc::new(mode));
}
```

可以看到有一堆 `Arc`，但是不用怕，再慢也不会卡死。

这里面出现了 `Scope`，这个稍后再说。
