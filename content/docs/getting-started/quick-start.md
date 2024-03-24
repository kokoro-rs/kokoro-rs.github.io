+++
title = "快速开始"
description = "使用 Kokoro 完成一个最简单的 Web 服务器吧！"
date = 2021-05-01T08:20:00+00:00
updated = 2021-05-01T08:20:00+00:00
draft = false
weight = 20
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "使用 Kokoro 完成一个最简单的 Web 服务器吧！"
toc = true
top = false
+++

## 环境搭建

首先你需要 Rust 开发环境，这里不再过多赘述。

创建一个项目。

你可以使用 Cargo 来管理依赖，使用以下指令将 Kokoro 作为依赖添加到你的项目中。

```bash
cargo add kokoro
```

然后在你需要用到 kokoro 的地方引入它。

```rust
use kokoro::prelude::*;
```
