# Helm 依赖 - 使用标签

## 步骤-01: 介绍

- 我们将使用 `tags` 而不是 `condition`
- 如果我们有大量需要分组的子 chart，那么我们需要使用 `tags` 而不是 `condition`

## 步骤-02: 查看 Chart.yaml

- 我们将使用 `tags` 而不是 `condition`

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
  #condition: childchart4dev.enabled
  tags: 
    - frontend 
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa1
  #condition: childchart4qa.enabled
  tags: 
    - frontend   
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa2
  #condition: childchart4qa2.enabled
  tags: 
    - frontend        
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
  #condition: childchart2.enabled
  tags: 
    - backend
 ```

## 步骤-03: 用例-1: 前端和后端都为 false

 ```bash
 # 用例-1: 前端和后端都为 false
 # values.yaml
tags:
  frontend: false
  backend: false

# Helm 安装
helm install myapp1 parentchart/ --atomic

# 列出 Pod
kubectl get pods
观察结果:
1.  我们应该只看到 1 个 pod（parentchart）在运行
```

## 步骤-04: 用例-2: 后端为 True，前端为 false

 ```bash
# Helm 升级
helm upgrade myapp1 parentchart/ --atomic --set tags.backend=true

# 列出 Pod
kubectl get pods
观察结果:
1.  我们应该看到 2 个 pod（parentchart 和 childchart2）在运行
```

## 步骤-05: 用例-3: 后端为 True，前端为 True

 ```bash
# Helm 升级
helm upgrade myapp1 parentchart/ --atomic --set tags.backend=true --set tags.frontend=true

# 列出 Pod
kubectl get pods
观察结果:
1.  我们应该看到 5 个 pod（parentchart、childchart2、childchart4dev、childchart4qa1、childchart4qa2）在运行
```

## 步骤-06: 卸载 Helm Chart

```bash
# Helm 卸载
helm uninstall myapp1
```
