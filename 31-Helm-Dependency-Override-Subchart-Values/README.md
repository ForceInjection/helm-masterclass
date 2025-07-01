# Helm 依赖 - 覆盖子 Chart 值

## 步骤-01: 介绍

- 从父 chart 覆盖子 chart（子图表）的值

## 步骤-02: 查看 Chart.yaml

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
  condition: mychart4.enabled
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  condition: mychart2.enabled
```

## 步骤-03: 查看 mychart4、mychart2 子 chart 的 replicaCount 值

```bash
# 切换目录
cd 31-Helm-Dependency-Override-Subchart-Values

# 从 Helm 包查看 mychart4 的值
helm show values parentchart/charts/mychart4-0.1.0.tgz

# 从 Helm 包查看 mychart2 的值
helm show values parentchart/charts/mychart2-0.4.0.tgz 
```

## 步骤-04: 更新 values.yaml

- 从父 chart 的 `values.yaml` 覆盖子 chart 中的 `replicaCount` 值

```yaml
# 使用 Chart 名称为子 Chart 设置值
mychart4:
  enabled: true
  replicaCount: 3
mychart2:
  enabled: true  
  replicaCount: 3
```

## 步骤-05: 部署和测试

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
观察结果:
1. 我们应该看到每个子 chart 有 3 个 pod
2. parentchart 有 1 个 pod
3. 我们已经成功从 parentchart values.yaml 覆盖了子 chart 的值

# 列出服务
kubectl get svc

# 访问应用程序
parentchart: http://localhost:<port-from-get-svc-output>
mychart4: http://localhost:<port-from-get-svc-output>
mychart2: http://localhost:31232
```

## 步骤-06: 卸载 Helm 发布

```bash
# Helm 卸载
helm uninstall myapp1
```
