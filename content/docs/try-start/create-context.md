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
lead = "本文将带您了解如何在 Kokoro 中构建上下文。"
toc = true
top = false
+++

## 理解 Context

在 Kokoro 框架中，`Context` 是核心组件，负责协调资源和行为模式。

### 泛型参数：Resource 和 Mode

`Context` 结构体包含两个泛型参数：`Resource` 和 `Mode`，它们定义了上下文的资源类型和工作模式。

### 工作模式的选择

您需要首先确定 `Context` 的工作模式。在本例中，我们采用 `MPSC` 模式，该模式源自 [kokoro-flume-channel](../../default-implement/pub-sub)，支持发布-订阅机制，适用于 `kokoro-plugin-tiny-http` 等基于发布-订阅的插件。

### 快速创建 Context

使用 [kokoro-flume-channel](../../default-implement/pub-sub) 提供的工具函数，您可以轻松创建一个 `MPSC` 模式的 `Context`：

```rust
fn main() {
    // 在 MPSC 模式下初始化上下文
    let ctx = channel_ctx();
}
```

默认情况下，创建的 `Context` 的 `Resource` 类型为 `()`，这意味着初始资源为空。

## 自定义 Context

进阶用户可能希望从头开始构建自定义的 `Context`。

### 自定义 Resource

例如，如果您希望 `Resource` 使用自定义类型，如下所示：

```rust
struct MyName {
    name: String
}
```

### 创建自定义 Context

您可以按照以下步骤创建一个携带自定义资源的 `Context`：

```rust
fn main() {
    let res = MyName { name: "Kokoro".to_string() };
    let scope = Scope::create(Arc::new(res));
    let mode = MPSC::<()>::default();
    let ctx = Context::create(Arc::new(scope), Arc::new(mode));
}
```

在这个过程中，您会看到多个 `Arc` 的使用，这是为了确保资源在多线程中的安全共享。

`Scope` 是另一个重要概念，我们将在后续章节中详细介绍。

## 结语

通过以上步骤，您可以灵活地创建和管理 Kokoro 中的上下文，为接下来的开发打下坚实基础。

在成功创建 Context 之后，下一步是将插件注册到上下文中。详细请参阅 [**注册插件**](../plug-in) 部分。
