# Helm 依赖 - 带别名的条件

## 步骤-01: 介绍

- 实现 `Condition` 来启用或禁用子 Chart 或子图表
- 从父 chart 覆盖子 chart（子图表）的值

## 步骤-02: Chart.yaml

- 如果我们有多个具有相同 chart 名称 `mychart4` 但具有不同别名（如 `childchart4dev` 和 `childchart4qa`）的依赖，在这种情况下，我们需要在 values.yaml 中使用 `别名` 来启用或禁用这些子 chart

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
  condition: childchart4dev.enabled
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa
  condition: childchart4qa.enabled  
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
  condition: childchart2.enabled
```

## 步骤-03: 更新 values.yaml

- 这里只启用 `childchart4qa`，所以除了父 chart 资源外，只应为该 chart 创建 k8s 资源

```yaml
# 使用 Chart 别名为子 Chart 设置值
childchart4dev:
  enabled: false 
childchart4qa:
  enabled: true   
childchart2:
  enabled: false 
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
观察结果:
1. 除了父 chart 外，还应为 childchart4qa 创建资源

# 列出 Pod
kubectl get pods

# 列出服务
kubectl get svc

# 访问应用程序
parentchart: http://localhost:<port-from-get-svc-output>
childchart4qa: http://localhost:<port-from-get-svc-output>
```

## 步骤-05: 卸载 Helm 发布

```bash
# Helm 卸载
helm uninstall myapp1
```
