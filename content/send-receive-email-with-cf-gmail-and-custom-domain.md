+++
title = "使用 Gmail 实现域名邮箱收发功能"
date = 2023-04-06
[taxonomies]
tags=["cloudflare", "gmail"]
+++

业界良心 Cloudflare 提供了很多实用的功能，其中之一就是 Email Routing，可以用来便捷的将自定义域名邮箱收到的邮件转发到某个目标邮箱。再配合上 Gmail 的一些设置，即可以轻松实现使用 Gmail 来收和发域名邮箱的邮件。

> 域名需要在 Cloudflare 或者使用 Cloudflare 的 Nameserver

## 收邮件
- 在 Cloudflare 的控制台，选择配置 Email --> Email Routing --> Routes
- 添加 自定义邮箱地址 xx@yy.com
- 然后选择转发的`目标邮箱地址`即可。

可以在 Destination addresses 处添加多个 目标邮箱地址。目标邮箱地址在第一次添加时需要进行验证操作。

通过上面的配置，发送到 xx@yy.com 的邮件会被转发到配置的 目标邮箱地址，但是在 Gmail 回复邮件的时候仍然使用 目标邮箱地址。需要继续下面的配置才能将回信发件人地址修改为 xx@yy.com。
 
## 发邮件
- 需要 Gmail 开启了 2FA，https://myaccount.google.com/signinoptions/two-step-verification
- 创建一个 Gmail 的 App 密码，https://security.google.com/settings/security/apppasswords 分别选择 Mail 和 Mac 两个选项后生成，得到密码 `ABC`
- 在 Gmail 页面，打开 see all settings --> Accounts and Import --> Send mail as --> add another email address
- 填写表单的第一步 Email Address 填写 xx@yy.com，即自定义域名邮箱
- 填写表单的第二步 SMTP Server 填写 http://smtp.gmail.com，用户名填写 gmail 邮箱地址，密码填写上面得到的 ABC
- 添加账户，然后在写信或者回信时就可以选择 xx@yy.com

也可以在 Send mail as 处勾选 `Reply from the same address the message was sent to` 这样回信的时候就默认使用收信的地址了。

配置好上面两个部分，就完成了使用 Gmail 来收和发域名邮箱的邮件。