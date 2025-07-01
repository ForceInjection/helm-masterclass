# Helm 依赖 - 条件

## 步骤-01: 介绍

- 实现 `Condition` 来启用或禁用子 Chart 或子图表
- 从父 chart 覆盖子 chart（子图表）的值

## 步骤-02: Chart.yaml

- 理解在定义依赖时 `condition` 的重要性
- 默认情况下，无论是否定义条件 `condition: mychart4.enabled`，定义后 chart 都会被启用
- 要禁用它，我们需要在 `values.yaml` 中明确将其设置为 `false`

```yaml
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4
  condition: mychart4.enabled
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
  condition: mychart2.enabled
```

## 步骤-03: 部署和测试 - 默认启用为 true

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
mychart4: http://localhost:<port-from-get-svc-output>
mychart2: http://localhost:31232

# Helm 卸载
helm uninstall myapp1
```

## 步骤-04: 更新 values.yaml

```yaml
# 使用 Chart 名称为子 Chart 设置值
mychart4:
  enabled: false
mychart2:
  enabled: false  
```

## 步骤-05: 部署和测试 - 当子 chart 被禁用时

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
1. 子 Chart 不会被部署。
2. 不会为子 chart 创建 k8s 资源

# 列出 Pod
kubectl get pods

# 列出服务
kubectl get svc

# 访问应用程序
parentchart: http://localhost:<port-from-get-svc-output>
```

## 步骤-06: 卸载 Helm 发布

```bash
# Helm 卸载
helm uninstall myapp1
```
