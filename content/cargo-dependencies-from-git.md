+++
title = "在 Cargo 项目中使用 git 仓库作为依赖"
date = 2024-03-28
[taxonomies]
tags=["cargo", "rust"]
+++
在 Cargo 项目中，最简单的依赖方式为：
```toml
[dependencies]
serde = "1"
```
这会直接使用 [crates.io](https://crates.io/) 的依赖和版本，某些时候可能需要从源码仓库直接依赖某个分支或者依赖未发布到 [crates.io](https://crates.io/) 。此时，最小化的配置为：
```toml
[dependencies]
regex = { git = "https://github.com/rust-lang/regex.git" }

```
如果需要指定分支，可以配置为：
```toml
[dependencies]
regex = { git = "https://github.com/rust-lang/regex.git", branch = "next" }

```
上面两种使用基于 HTTPS 的认证，如果项目是非开仓库，需要进行额外配置，参考[这里](https://doc.rust-lang.org/cargo/appendix/git-authentication.html#https-authentication)

另外一种是基于 SSH 的认证，此时相关配置格式为：
```toml
[dependencies]
regex = { git = "ssh://git@example.com/user/repo.git" }
```
基于 SSH 的认证要求 `ssh-agent`，可以使用下面的方式快速添加：
```
# 注意 SSH 的私钥位置
ssh-add ~/.ssh/id_rsa
```
使用 `ssh-add -l` 来确认密钥已经成功添加到 `ssh-agent`。

如果 SSH 密钥是有密码的，这个步骤需要输入密码。通过 `ssh-agent` 后续 Cargo 访问私仓时将不需要输入密码。
