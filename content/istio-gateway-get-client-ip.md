+++
title = "Istio 中配置 Envoy 获取 Client 真实 IP"
date = 2019-03-22
[taxonomies]
tags=["istio", "kubernetes"]
+++
背景：使用 Istio Ingress Gateway(Envoy)，需要获取客户端真实 IP 地址

使用 Helm 安装 Istio 会安装一个名为 `istio-ingressgateway` 的 Service，类型为 `LoadBalancer`，可以将服务暴露到公网。 

这个 Service 的 `spec.externalTrafficPolicy` 默认值是 `Cluster`，我们只要将这个值更新为`Local`，就可以在 `X-Forwarded-For` 请求头获取到客户端真实 IP 地址了。
