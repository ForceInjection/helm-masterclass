# Helm 依赖 - 别名

## 步骤-01: 介绍

- 条件
- 别名
- 从父 chart 覆盖子 chart（子图表）的值

## 步骤-02: 将父 Chart 的 ClusterIP 更新为 NodePort 服务

```yaml
# values.yaml
service:
  type: NodePort
  port: 80
```

## 步骤-03: Chart.yaml

- 理解在定义依赖时 `alias` 的重要性

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4dev
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa  
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
```

## 步骤-04: 部署和测试

```bash
# Helm 依赖更新
helm dependency update parentchart/
或
helm dep update parentchart/

# Helm 安装
helm install myapp1 parentchart/ --atomic

# Helm 列表
helm list

# Helm 状态
helm status myapp1 --show-resources

# 列出部署
kubectl get deploy

# 列出 Pod
kubectl get pods

# 列出服务
kubectl get svc

# 访问应用程序
parentchart: http://localhost:<port-from-get-svc-output>
childchart4dev: http://localhost:<port-from-get-svc-output>
childchart4qa: http://localhost:<port-from-get-svc-output>
mychart2: http://localhost:31232
```

## 步骤-05: 卸载 Helm 发布

```bash
# Helm 卸载
helm uninstall myapp1
```
