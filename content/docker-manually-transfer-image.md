+++
title = "Docker 手动迁移镜像"
date = 2017-02-02
[taxonomies]
tags=["docker"]
+++
使用公共或者私有的 Registry 可以方便的将 Docker 镜像进行转移，在某些场景可能期望手动来迁移镜像。

此时可以使用 `docker save` 和 `docker load` 指令将镜像打包然后在其它位置加载回来。这里使用 nginx 镜像来做演示，将镜像从一台机器手动迁移到另一台机器。
<!--more-->
### 打包镜像
```bash
docker save -o nginx.tar nginx:latest
```
然后 `nginx:latest` 镜像被打包为 `nginx.tar` 文件，此时可以通过 scp 之类的方法传输这个镜像文件。

> 在打包过程中可以使用类似 `docker save  nginx | gzip > nginx.tar.gz` 的命令进行压缩减小文件体积。

更多参数请参考 [官方文档](https://docs.docker.com/engine/reference/commandline/save/) 对此命令的说明。

### 加载镜像
在另一台没有 `nginx:latest` 镜像的机器上执行：
```bash
docker load -i nginx.tar
# 下面为命令输出
3358360aedad: Loading layer [==================================================>]  58.44MB/58.44MB
c632afbadb38: Loading layer [==================================================>]  53.91MB/53.91MB
180ab8f004dc: Loading layer [==================================================>]  3.584kB/3.584kB
```
执行完成后 `nginx:latest` 镜像就被手动迁移到了另一台机器上。

更多参数请参考 [官方文档](https://docs.docker.com/engine/reference/commandline/load/) 对此命令的说明。
