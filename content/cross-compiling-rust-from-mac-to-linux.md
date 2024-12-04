+++
title = "Mac 平台交叉编译 Rust 至 Linux 平台"
date = 2024-10-21
[taxonomies]
tags=["rust", "cross-compiling"]
+++
最近在用 Rust 开发一些提效小工具，在 Mac 平台编码但运行环境为 Linux（裸跑，非容器），将相关过程记录一下。

之前这种跨平台的编译是使用 [cross](https://github.com/cross-rs/cross) 来完成的，使用类似下面的命令来进行编译：
```bash
cross build --release --target x86_64-unknown-linux-gnu
```

本次直接用 cargo 来解决：
- 使用 `rustup target add` 来添加目标平台的标准库
- 安装目标平台的 `链接器（linker）`
- 配置 cargo 来使 `rustc` 能够使用正确的链接器
- 设置 `TARGET_CC` 环境变量
- 使用 `cargo build` 来进行编译

## 添加目标 target
```bash
rustup target add x86_64-unknown-linux-gnu
```

## 安装目标平台的链接器
```bash
brew install SergioBenitez/osxct/x86_64-unknown-linux-gnu
```

## 添加 cargo 配置文件
```bash
mkdir .cargo
touch config.toml
```
添加下面的配置文件：
```toml
[target.x86_64-unknown-linux-gnu]
linker = "x86_64-unknown-linux-gnu-gcc"
```

## 设置环境变量
```bash
export TARGET_CC=x86_64-unknown-linux-gnu-gcc
```

## 开始编译
```bash
cargo build --release --target x86_64-unknown-linux-gnu
```

设置环境变量和编译可以在同一步进行：
```bash
TARGET_CC=x86_64-unknown-linux-gnu-gcc cargo build --release --target x86_64-unknown-linux-gnu
```

然后在 `./target/x86_64-unknown-linux-gnu/release/` 可以找到编译产物。

## 运行环境 glibc 版本过低问题
当在运行环境执行 `./tool-name` 时，部分服务器报错了：
```bash
./tool-name: /lib64/libc.so.6: version GLIBC_2.25' not found (required by ./tool-name)
```
查看 ldd 版本：
```bash
ldd --version
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.
```
这是因为 `glibc` 版本较低（部分服务器运行好多年了，一直未替换），此时可以升级版本或者使用静态链接方案。升级版本考虑的问题比较多，下面继续看使用静态链接来编译。

当 target 是 `x86_64-unknown-linux-gnu` 时，会使用`动态链接`，也就是在运行时来链接。因此会出现运行环境 glibc 版本不满足要求的情况。换用 `x86_64-unknown-linux-musl` 这个目标平台来解决问题。

```bash
# 添加目标平台
rustup target add x86_64-unknown-linux-musl
# 安装链接器
brew install FiloSottile/musl-cross/musl-cross
```
然后在 cargo 配置文件 `.cargo/config.toml` 中添加如下内容：
```toml
[target.x86_64-unknown-linux-musl]
linker = "x86_64-linux-musl-gcc"
```
最后执行编译：
```bash
TARGET_CC=x86_64-linux-musl-gcc cargo build --release --target x86_64-unknown-linux-musl
```
在 `./target/x86_64-unknown-linux-musl/release/` 可以找到编译产物。这样得到的可执行文件就是通过静态链接的了，应该在大部分的 Linux 上都可以执行。
