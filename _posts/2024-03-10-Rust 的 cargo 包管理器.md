---
layout: post
title:  "Rust 的 cargo 包管理器"
date:   2024-03-10 23:38:30 +0800
categories: posts
---

# Rust 的 cargo 包管理器

## 什么是包

1. Package 和 crate 都是指包
2. Package 是概念层的包，包含了单个或多个 crate
3. crate 是实际编译单元
4. codegen-unit: crate 默认会被按 LLVM IR 切割为 16 份

## 包管理器 Cargo

1. Rust 的包管理器 Cargo
2. Cargo 可以让包管理声明各种依赖，并且方便重复的构建

## TOML

1. Cargo 使用 TOML 格式的文件 Cargo.toml 来描述包的元数据
2. [TOML](https://toml.io/en/) 是一种简单的配置文件格式

## Cargo 通用命令

1. `cargo new` 创建新的包
2. `cargo check` 对当前 crate 及其所有依赖进行静态检查，不构建
3. `cargo build` 执行静态检查且生成编译文件
4. `cargo run` 检查+构建+允许
5. `cargo clean` 清除 cargo 构建文件
6. `cargo doc` 生成文档
7. `cargo fix`
8. `cargo add`
9. `cargo audit`

10. `cargo test` 测试包
11. `cargo doc` 生成包的文档
12. `cargo publish` 发布包
13. `cargo install` 安装包
14. `cargo update` 更新包
15. `cargo uninstall` 卸载包
16. `cargo search` 搜索包
17. `cargo clean` 清理包
13.
1.  `cargo fix` 修复包
2.  `cargo fmt` 格式化包
3.  `cargo clippy` 代码检查包
4.  `cargo bench` 基准测试包
5.  `cargo package` 打包包
6.  `cargo metadata` 包元数据
7.  `cargo tree` 包依赖树
8.  `cargo yank` 撤销发布包
9.  `cargo login` 登录包
10. `cargo owner` 包所有者
