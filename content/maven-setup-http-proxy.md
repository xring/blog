+++
title = "Maven 设置 HTTP 代理"
date = 2017-02-10
[taxonomies]
tags=["maven", "java"]
+++
有时候基于公司安全因素考虑或者由于一些特殊原因，Maven 无法正常访问外部仓库来下载所需要的资源。这种情况下，可以通过为 Maven 配置 HTTP 代理来解决问题。

通过以下步骤来为 Maven 配置 HTTP 代理：
```
- 获取 HTTP 代理信息
- 找到 Maven 配置文件 settings.xml
- 将代理信息添加到配置文件
```
假设我们有如下 HTTP 代理信息：
```
host: 9.30.123.123
port: 3712
username: xring
password: volcano
```
下一步来寻找 Maven 配置文件。`settimgs.xml` 文件可能存在于两个位置：
– ~/.m2/settings.xml
– M2_HOME/conf/settings.xml

如果 `~/.m2/settings.xml` 文件存在，则 `优先` 使用这个文件进行配置，如果不存在则需要使用 `M2_HOME/conf/settings.xml` 文件。如果没有配置 `M2_HOME` 这个环境变量或者忘记了 Maven 的安装位置，可以通过 `mvn -version` 可以找到 Maven home 目录，即上面提到的 `M2_HOME`，在 Mac 下得到类似输出：
```bash
mvn -version
...
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
...
```
则 `settimgs.xml` 文件就在 `/usr/local/Cellar/maven/3.3.9/libexec/conf` 目录中。

编辑找到的 settings.xml 文件，搜索 proxies 关键字，找到类似下面的这一段：
```xml
<proxies>
  <!-- proxy
   | Specification for one proxy, to be used in connecting to the network.
   |
  <proxy>
    <id>optional</id>
    <active>true</active>
    <protocol>http</protocol>
    <username>proxyuser</username>
    <password>proxypass</password>
    <host>proxy.host.net</host>
    <port>80</port>
    <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
  </proxy>
  -->
</proxies>
```
在 proxies 下可以配置多个 proxy 元素，如果声明了多个 proxy 元素，则默认情况下第一个被激活的 proxy 元素会被使用。
当 proxy 元素里的 active 被设置为 true 时表示该 proxy 处于激活状态。
id 字段是 proxy 元素的一个标识
当代理不需要认证时，username 和 password 可以被注释掉
nonProxyHost 元素用来指定哪些主机名不需要代理，当有多个主机名不需要代理时用 | 来分隔主机名，主机名里支持通配符（如 *.google.com）。

了解了这段 XML 各元素的意义后将得到的 HTTP 代理信息依次填入相应元素：
```xml
<proxies>
  <proxy>
    <id>volcano-proxy</id>
    <active>true</active>
    <protocol>http</protocol>
    <username>xring</username>
    <password>volcano</password>
    <host>9.30.123.123</host>
    <port>3712</port>
    <!--
    <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    -->
  </proxy>
</proxies>
```
保存 `settings.xml` 配置文件，Maven 配置 HTTP 代理完成。
