+++
title = "关于 kokoro-core"
description = "关于 kokoro-core"
date = 2021-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
weight = 30
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = '''
Kokoro 是为了构建高解耦的应用程序而存在的。
'''
toc = true
top = false
+++

Kokoro 框架的设计理念主要聚焦于抽象和可扩展性，它旨在成为一种编程范式而非传统库。在 Rust 语言中，我们能够为几乎所有类型实现自定义特征。

利用这一特性，Kokoro 将 `Context` 定义为一个可扩展的上下文。通过在 `Context` 上实现方法，用户可以扩展当前上下文的功能。

此外，用户可以将自己的类型实例包装在 `Context` 实例中，从而利用 `Context` 提供的独特方法。值得注意的是，`Context` 支持在线程间转移且可复制，因此需要使用 `Arc` 对类型进行包装。

当 `Context` 解引用时，它将变为被包装类型实例的引用，从而在扩展的同时保持实例的可访问性。

```rust
struct MyStruct;
impl MyStruct {
    fn hello_kokoro() {
        println!("Kokoro yyds");
    }
}
let context = context.with(Arc::new(Scope::create(Arc::new(MyStruct))));
context.hello_kokoro();
```

在这个例子中，`Context` 作为扩展的载体，为用户自定义类型提供了扩展能力。

同时，`Context` 不仅具备扩展其他类型的能力，还拥有一个伴随其生命周期的类型实例。

```rust
struct Global;
struct MyStruct;
let scope = Scope::create(Arc::new(MyStruct));
let context = Context::create(Arc::new(scope), Arc::new(Global));
```

在 `Context` 中，`Global` 负责承载 `Context` 自身扩展过程中所需的类型实例。这与 `kokoro-flume-channel` 中的 `MPSC` 类似，后者存储了 `sender`、`receiver` 和 `runner`。

在泛型中，我们将 `Global` 称为 `Mode`，因为它代表了 `Context` 的工作模式。

关于发布订阅功能，Kokoro 最初是作为发布订阅模式框架而诞生的。尽管我们对 Kokoro 的定义和实现进行了调整，但这并不影响其作为发布订阅模式框架的使用。

为了方便用户实现 Mode MPSC，我们提供了一个官方包 `kokoro-flume-channel`。该包遵循 `Context` 的设计原则，支持可扩展性。

[关于 kokoro-flume-channel](../../default-implement/pub-sub)

## 定位问题

`kokoro-core` 作为 Kokoro 框架的核心组件，虽然其本身并未包含完整的默认实现，但它在整个框架中扮演着至关重要的角色。由于 `kokoro-core` 高度抽象，它的存在感知度可能较低，但过度抽象是为了实现更高的可扩展性和灵活性。

那么，`kokoro-core` 的真正定位是什么呢？我们认为主要包括以下几点：

1. **提供核心抽象**：`kokoro-core` 定义了 Kokoro 框架的核心抽象，如 `Context` 和 `Scope`，这些抽象为用户提供了丰富的扩展点，使得框架能够适应各种不同的应用场景。

2. **促进代码复用**：通过提供通用的接口和抽象，`kokoro-core` 鼓励开发者编写可复用的代码。这样，在不同的项目或领域中，开发者可以利用现有的实现快速构建出符合需求的应用程序。

4. **支持模块化开发**：`kokoro-core` 的设计允许开发者根据自己的需求选择性地引入和使用不同的模块。这种模块化开发方式有助于减少项目的复杂性，提高代码的可维护性。

5. **推动生态发展**：通过提供清晰、稳定的接口和抽象，`kokoro-core` 鼓励第三方开发者为其编写扩展和插件。这将有助于形成一个繁荣的生态系统，使得 Kokoro 框架能够不断发展和壮大。

总之，`kokoro-core` 的定位是为用户提供一个灵活、可扩展且易于使用的核心框架。虽然它在某些方面可能显得过于抽象，但这种设计选择是为了更好地满足用户的多样化需求，推动框架的长远发展。
