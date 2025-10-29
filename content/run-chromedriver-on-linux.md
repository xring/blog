+++
title = "在 Linux 上运行 ChromeDriver"
date = 2025-10-28
[taxonomies]
tags=["linux", "chromedriver"]
+++
环境和需求：
- Ubuntu Linux 服务器
- Docker 运行业务代码，配置网络 `--net host`
- `chromedriver` 运行在宿主机
- **需要使用 `非 headless` 模式（有头模式）**

以 `root` 运行的业务代码调用 `chromedriver` 会得到类似提示：
> Running as root without --no-sandbox/--headless is not supported.

这是因为：当业务代码以 root 身份运行时，它通过 `chromedriver` 启动的 Chrome 浏览器进程也会以 root 身份运行，而 Chrome 不允许以 root 身份运行

解决方法：
- Docker 中不使用 root 用户：
```Dockerfile
# 创建非特权用户
RUN useradd -m -u 1001 appuser

# 切换到非特权用户
USER appuser

# 指定工作目录
WORKDIR /home/appuser
```

同时在业务代码中按需要添加额外参数（Rust 示例）：
```rust
let mut caps = DesiredCapabilities::chrome();

caps.add_arg("--no-sandbox").ok()?; // 禁用沙箱
caps.add_arg("--disable-dev-shm-usage").ok()?; // 禁用 /dev/shm 使用
caps.add_arg("--disable-gpu").ok()?; // 禁用 GPU
```


在终端下启动 `chromedriver` 会得到类似提示：
> Missing X server or $DISPLAY

需要在图形界面/桌面 上，启动 `chromedriver`，在启动前可以使用 `echo $DISPLAY` 来确认。

或者使用 `Xvfb` 技术，Xvfb 会创建一个内存中的虚拟屏幕，ChromeDriver 可以在这个虚拟屏幕上运行有头模式的 Chrome 浏览器，就像在一个真正的桌面上一样，但实际上不会有任何窗口弹出。
