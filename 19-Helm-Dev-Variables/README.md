# Helm 开发 - 变量

## 步骤-01: 介绍

- 如何使用变量？

## 步骤-02: Helm 模板中的变量

```yaml
# 变更-1: 在部署模板顶部添加变量
{{- $chartname := .Chart.Name -}}

# 变更-2: 在 deployment.yaml 中添加带有变量的 appHelmChart 注释
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
        appManagedBy: {{ $.Release.Service }}
        appHelmChart: {{ $chartname }}        
      {{- end }}  

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 观察结果:
我们应该看到变量值成功替换
```

## 步骤-03: 测试变量与管道的组合

```bash
# 添加带有 quote 和 upper 函数的管道
{{- $chartname := .Chart.Name | quote | upper -}}
apiVersion: apps/v1
kind: Deployment
metadata:

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 使用 --atomic 进行 Helm 安装
helm install myapp101 . --atomic 

# 列出 Helm Releases
helm list

# 列出 Kubernetes Pods
kubectl get pods

# Helm 获取清单
helm get manifest myapp101

# Helm 卸载
helm uninstall myapp101
```
