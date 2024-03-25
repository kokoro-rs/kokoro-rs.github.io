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

## Rust 开发环境配置

为了开始使用 Rust 进行项目开发，您首先需要配置 Rust 开发环境。详细的安装指南请参考 [Rust 官方文档](https://doc.rust-lang.org/book/ch01-01-installation.html)。

## 项目创建与依赖管理

一旦您的开发环境准备就绪，您可以创建一个新的 Rust 项目。Rust 的包管理器 Cargo 允许您轻松管理项目依赖。

### 添加 Kokoro 作为项目依赖

可以通过以下 Cargo 命令将其添加到您的项目中：

```bash
cargo add kokoro
```

### 引入 Kokoro 库

在项目中的适当位置，您需要引入 Kokoro 库以使用其功能。

```rust
use kokoro::prelude::*;
```

在完成基本的环境搭建和依赖管理后，您可以阅读 [**创建上下文**](../../try-start/create-context) 部分来进一步了解如何在您的项目中有效地使用 Kokoro 库。
