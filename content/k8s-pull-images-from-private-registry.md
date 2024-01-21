+++
title = "Kubernetes 从私有镜像仓库拉取镜像"
date = 2019-01-11
[taxonomies]
tags=["kubernetes"]
+++
在企业内部使用 private registry 来存储私有镜像显然是必需的，本例的私仓使用 [Harbor](https://github.com/goharbor/harbor)，下面来看看在 Kubernetes 集群中怎么配置使用我们建立的私有镜像仓库。假设私有镜像仓库地址为：`https://hub.xring.info`，我们要用的镜像为 nginx:v0.1.0，存在仓库 mynginx 中。如果使用 docker pull 来拉取镜像则命令为：
```
docker pull hub.xring.info/mynginx/nginx:v0.1.0
```
为了在 Kubernetes 中使用私有镜像仓库，我们需要做两个事：
- 创建一个存储了私有镜像 credential 信息的 Secret 资源对象
- 在 Pod 创建模板使用 spec.imagePullSecrets 中指定创建的 Secret 对象
<!--more-->

### 创建 Secret 资源对象
```
kubectl create secret docker-registry my-hub-secret --docker-server=hub.xring.info --docker-username=xbot --docker-password=123456 --docker-email=xbot@xring.info
```

### 指定 Secret 对象
以下面的 Pod 资源定义为例：
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-v010
  labels:
    app: nginx-v010
spec:
  imagePullSecrets:
  - name: my-hub-secret
  containers:
  - image: hub.xring.info/mynginx/nginx:v0.1.0
    name: nginx-v010
```

然后再使用 `kubectl apply -f mynginx.yaml` 来创建 Pod 对象，就可以从私有镜像仓库中拉取镜像了。
