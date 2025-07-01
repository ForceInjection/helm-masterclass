# Helm - 使用基于 OCI 的注册表

## 步骤-01: 介绍

- 我们将使用 Docker Hub 作为我们的 OCI 注册表来存储 Helm Charts

## 步骤-02: 检查 Helm Chart

```bash
# 创建 Chart
helm create myocidemo

# values.yaml: 将服务更新为 NodePort
service:
  type: NodePort
  port: 80

# values.yaml: 将 Docker 镜像更改为 kubenginxhelm
image:
  repository: ghcr.io/stacksimplify/kubenginxhelm

# Chart.yaml: 更新应用版本
version: 0.1.0
appVersion: "0.1.0"

# 打包 Helm Chart
cd 47-Helm-Use-OCI-based-Registries
helm package myocidemo
观察结果:
将创建文件名为 "myocidemo-0.1.0.tgz" 的包
```

## 步骤-03: OCI 注册表: Docker Hub

```bash
# 注册并登录到 Docker Hub
https://hub.docker.com

# 命令行: docker login
docker login
Username: xxxxxxxxx
Password: xxxxxxxxx

# 将 Helm Chart 推送到 Docker Hub
cd 47-Helm-Use-OCI-based-Registries
helm push <HELM-PACKAGE>  oci://registry-1.docker.io/<DOCKER-NAMESPACE>
helm push myocidemo-0.1.0.tgz  oci://registry-1.docker.io/stacksimplify

# 在 Docker Hub 上验证
查看选项卡
1. General
2. Tags
```

## 步骤-04: 更新并推送 Chart 版本: 0.2.0

```bash
# 使用 Chart 版本和应用版本 0.2.0 进行打包
helm package myocidemo --version "0.2.0" --app-version "0.2.0"

# 将 Helm Chart 推送到 Docker Hub
helm push myocidemo-0.2.0.tgz  oci://registry-1.docker.io/stacksimplify
```

## 步骤-05: 从 OCI 注册表拉取 Helm Chart

```bash
# 创建目录
mkdir mypackages

# Helm Pull
helm pull oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0
helm pull oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.2.0
```

## 步骤-06: Helm Template 和 Show 命令

```bash
# Helm Template 命令
helm template <my-release> oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0
helm template myapp1 oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0
helm template myapp1 oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.2.0

# Helm Show 命令
helm show all oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0
helm show all oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.2.0
```

## 步骤-07: 从 OCI 注册表进行 Helm 安装和升级

```bash
# Helm 安装
helm install <my-release> oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0
helm install myapp1 oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.1.0

# Helm 状态
helm status myapp1 --show-resources 

# 列出 k8s 服务
kubectl get svc

# 访问应用程序
http://localhost:<get-from-svc-output>

# Helm 升级
helm upgrade <my-release> oci://registry-1.docker.io/stacksimplify/myocidemo --version 0.2.0

# 列出 k8s 服务
kubectl get svc

# 访问应用程序
http://localhost:<get-from-svc-output>
```

## 步骤-08: 从经典 Chart 仓库迁移到 OCI 注册表

```bash
# 列出并添加 Helm Chart 仓库
helm repo list
helm repo add mygithelmrepo https://stacksimplify.github.io/helm-charts-repo/
helm repo update

# 搜索 Helm 仓库
helm search repo myfirstchart

# 创建迁移文件夹
mkdir migrate
cd migrate

# Helm Pull (下载最新的 chart 版本 - 在我们的例子中是 0.2.0)
helm pull mygithelmrepo/myfirstchart

# Helm Pull --version (下载指定的 chart 版本)
helm pull mygithelmrepo/myfirstchart --version 0.1.0

# Docker 登录 (如果尚未登录)
docker login

# Helm Push
helm push myfirstchart-0.1.0.tgz  oci://registry-1.docker.io/stacksimplify
helm push myfirstchart-0.2.0.tgz  oci://registry-1.docker.io/stacksimplify

# 在 Docker Hub 上验证
https://hub.docker.com
```
