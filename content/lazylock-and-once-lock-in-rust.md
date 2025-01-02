+++
title = "Rust 中的 LazyLock 和 OnceLock"
date = 2025-01-02
[taxonomies]
tags=["rust"]
+++
`LazyLock` 和 `OnceLock` 是 Rust 中用于实现惰性初始化的两种不同的锁机制。它们都可以用于延迟初始化某个值，直到第一次访问时才进行计算和存储。它们被视为对 `lazy_static` 和 `once_cell` 的现代替代方案，提供了更简洁和类型安全的接口。
```rust
use std::sync::LazyLock;

static CONFIG: LazyLock<String> = LazyLock::new(|| {
    // 运行时初始化逻辑
    println!("Initializing CONFIG...");
    "Runtime configuration".to_string()
});

fn main() {
    // 第一次访问时进行初始化
    println!("Config: {}", *CONFIG);
    // 再次访问时不会重新初始化
    println!("Config: {}", *CONFIG);
}
```
`Initializing CONFIG...` 的内容只会被打印一次。


```rust
use std::sync::OnceLock;

static CONFIG: OnceLock<String> = OnceLock::new();

fn main() {
    // 第一次调用 get_or_init 时进行初始化
    let config = CONFIG.get_or_init(|| {
        println!("Initializing CONFIG...");
        "Runtime configuration".to_string()
    });

    println!("Config: {}", config);

    // 再次调用 get_or_init 时不会重新初始化
    let config_again = CONFIG.get_or_init(|| {
        println!("This will not be printed.");
        "Another configuration".to_string()
    });

    println!("Config again: {}", config_again);
}
```
在已初始化的值上多次调用 `get_or_init` 不会重新初始化。

`LazyLock`
- 线程安全性: LazyLock 使用内部的同步机制来确保初始化逻辑只会执行一次，即使在多个线程同时访问的情况下。它通常用于全局静态变量的惰性初始化。
- 使用场景: 适合在程序启动时就能确定初始化逻辑的场景，尤其是全局静态变量。

`OnceLock`
- 线程安全性: OnceLock 也使用同步机制来确保初始化逻辑只会执行一次。它提供了一个简单的 API 来在运行时进行初始化。
- 使用场景: 适合需要在运行时初始化的场景，尤其是在需要确保某个值只被初始化一次的情况下。