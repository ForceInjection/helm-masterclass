# 创建和打包 Helm Charts

## 步骤-01: 介绍

1. 我们将学习以下内容
2. 使用 helm create 创建新的 Helm Chart
3. 使用基本信息更新 Chart，如我们的 Docker 镜像、应用版本、chart 版本
4. 更新 Chart 以支持 NodePort 服务，helm install 和测试
5. helm package
6. helm package --app-version --version

- [使用的 Docker 镜像](https://github.com/users/stacksimplify/packages/container/package/kubenginx)

## 步骤-02: Helm 创建 Chart

```bash
# Helm 创建 Chart
helm create <CHART-NAME>
helm create myfirstchart
观察结果: 
1. 它将创建一个基础的 Helm Chart 模板
2. 我们可以称之为启动器 chart。
```

## 步骤-03: 使用我们的应用程序 Docker 镜像更新 values.yaml

- [使用的 Docker 镜像](https://github.com/users/stacksimplify/packages/container/package/kubenginx)
- 查看 `templates/deployment.yaml`

```yaml
image:
  repository: ghcr.io/stacksimplify/kubenginx
  pullPolicy: IfNotPresent
  # 覆盖镜像标签，默认值是 chart appVersion。
  tag: ""
```

## 步骤-04: 将 Kubernetes 服务转换为 NodePort

```yaml
# 更新 values.yaml
service:
  type: NodePort
  port: 80 
  nodePort: 31231

# 更新 templates/service.yaml
nodePort: {{ .Values.service.nodePort }}
```

## 步骤-05: 更新 Chart.yaml

```bash
### Chart 版本和描述
# 之前
version: 0.1.0
description: A Helm chart for Kubernetes

# 之后
version: 1.0.0
description: A Helm Chart with NodePort Service

### appVersion
# 之前
appVersion: "1.16.0"

# 之后 (更新我们的 Docker 镜像标签版本)
appVersion: "1.0.0"
```

## 步骤-06: Helm 安装 - Chart 版本 1.0.0 并测试

```bash
# Helm 安装
cd myfirstchart
helm install myapp1v1 .

# 列出 Helm 发布
helm list
helm list --output=yaml

# Helm 状态
helm status myapp1v1 --show-resources

# 使用 kubectl 命令
kubectl get pods
kubectl get svc

# 在浏览器中访问
http://127.0.0.1:31231
http://localhost:31231
```

## 步骤-07: Helm 打包 - v1.0.0

```bash
# 检查您是否在目录中
25-Helm-Create-and-Package-Chart

# Helm 打包
helm package myfirstchart/ --destination packages/
或
helm package myfirstchart/ -d packages/

# 查看包文件
cd pakcages
ls -lrta
包文件名: myfirstchart-1.0.0.tgz
```

## 步骤-08: Helm 打包 - v2.0.0

```bash
### Chart 版本和描述
# 之前
version: 1.0.0
description: A Helm Chart with NodePort Service

# 之后
version: 2.0.0
description: A Helm Chart with NodePort Service

### appVersion
# 之后 (更新我们的 Docker 镜像标签版本)
appVersion: "1.0.0"

# 之后 (更新我们的 Docker 镜像标签版本)
appVersion: "2.0.0"

# Helm 打包
helm package myfirstchart/ --destination packages/
helm package myfirstchart/ -d packages/

# 查看包文件
cd pakcages
ls -lrta
包文件名: myfirstchart-1.0.0.tgz
包文件名: myfirstchart-2.0.0.tgz
```

## 步骤-09: 通过打包 chart 的路径进行 Helm 安装并验证

```bash
# Helm 安装
helm install myapp1v2 packages/myfirstchart-2.0.0.tgz --set service.nodePort=31232

# 使用 kubectl 命令
kubectl get pods
kubectl get svc

# 列出 Helm 发布
helm list
helm list --output=yaml

# Helm 状态
helm status myapp1v2 --show-resources

# 在浏览器中访问
http://127.0.0.1:31232
http://localhost:31232
```

## 步骤-10: 使用 --app-version, --version 进行 Helm 打包

- [使用的 Docker 镜像](https://github.com/users/stacksimplify/packages/container/package/kubenginx)

```bash
# Helm 打包 --app-version
helm package myfirstchart/ --app-version "3.0.0" --version "3.0.0" --destination packages/
```

## 步骤-11: Helm 安装并测试 --version "3.0.0" 是否有效

```bash
# 从包进行 Helm 安装
helm install myapp1v3 packages/myfirstchart-3.0.0.tgz --set service.nodePort=31233

# 使用 kubectl 命令
kubectl get pods
kubectl get svc

# 列出 Helm 发布
helm list
helm list --output=yaml

# Helm 状态
helm status myapp1v3 --show-resources

# 在浏览器中访问
http://127.0.0.1:31233
http://localhost:31233
观察结果:
1. 我们可以看到应用程序的 V3 版本
```

## 步骤-12: 卸载 Helm 发布

```bash
# 列出 Helm 发布
helm list
helm list --output=yaml

# 卸载 Helm 发布
helm uninstall myapp1v1
helm uninstall myapp1v2
helm uninstall myapp1v3
```

## 步骤-13: Helm Show 命令

- **helm show:** 显示 chart 的信息

```bash
# Helm Show Chart
helm show chart myfirstchart/
helm show chart packages/myfirstchart-2.0.0.tgz

# Helm Show Values
helm show values myfirstchart/
helm show values packages/myfirstchart-2.0.0.tgz

# Helm Show readme
helm show readme myfirstchart/

# Helm Show All
helm show all myfirstchart/
helm show all packages/myfirstchart-2.0.0.tgz
```
