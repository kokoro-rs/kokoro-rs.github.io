+++
title = "注册插件"
description = "如何注册插件"
date = 2021-05-01T19:30:00+00:00
updated = 2021-05-01T19:30:00+00:00
draft = false
weight = 20
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "插件是 Kokoro 实现松散耦合、高度模块化的关键。"
toc = true
top = false
+++

## 插件的形式

在 kokoro-default-impl 中定义的插件是一个特征。

这个特征看起来是这样的：

```rust
trait Plugin: Resource {
    type MODE: Mode + 'static;
    const NAME: &'static str;
    fn apply(ctx: Context<Self, Self::MODE>) -> Result<()>;
}
```

1. Plugin 必须实现 Resource。这是因为 Plugin 将被作为 Resource 存储，并且会被 Context 包装。

2. NAME 标明了插件的名字，理论上可以是任意字符串，但我们约定只能使用蛇命名。该规则不会被检查。

3. 当插件被插入上下文时会被执行的函数。在 关于 kokoro-core 中有提及，Context 可以被解引用为 Resource 的引用，这里用到了该特性。所以 apply 函数通过 ctx 可访问 self。通过这点使得 apply 很像是 Plugin 上的方法而不是函数。
