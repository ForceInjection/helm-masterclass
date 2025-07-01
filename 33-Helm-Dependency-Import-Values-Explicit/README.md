# Helm 依赖 - 显式导入值

## 步骤-01: 介绍

- 显式导入值

## 步骤-02: 查看/更新子图表 values.yaml

- **文件位置:** parentchart/charts/mychart1/values.yaml

```yaml
# 导出值 - MyChart1 (用于显式导入值用例)
exports:
  mychart1Data:
    mychart1appInfo:
      appName: kapp1
      appType: MicroService
      appDescription: Used for listing products    
```

## 步骤-03: 查看/更新 Chart.yaml 中 mychart1 依赖的 import-values 参数

```yaml
- name: mychart1
  version: "0.1.0"
  repository: "file://charts/mychart1"
  alias: childchart1
  tags: 
    - frontend
  import-values:
    - mychart1Data # 显式值导入用例
```

## 步骤-04: 查看/更新 parentchart configmap.yaml

- **文件位置:** parentchart/templates/configmap.yaml

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name:  {{ include "parentchart.fullname" . }}-import-explicit
data:
{{- toYaml .Values.mychart1appInfo | nindent 2 }}
```

## 步骤-05: 显式导入值：部署和验证

```bash
# 切换到图表目录
cd parentchart

# Helm 安装
helm install myapp1 . --atomic

# Helm 列表
helm list

# Helm 状态
helm status myapp1 --show-resources

# 列出 k8s 部署
kubectl get deploy

# 列出 k8s pods
kubectl get pods

# 列出 k8s ConfigMaps
kubectl get cm
kubectl get cm myapp1-parentchart-import-explicit -o yaml
观察结果:
我们应该看到从 parentchart/charts/mychart1/values.yaml 导出的数据成功导入到 parentchart 中的 configmap。

# Helm 卸载
helm uninstall myapp1 
```

## 步骤-06: 测试当 mychart1 被禁用时

```bash
# 切换到图表目录
cd parentchart

# Helm 安装
helm install myapp1 . --atomic --set tags.frontend=false

# 查看 Configmap
kubectl get cm myapp1-parentchart-import-explicit -o yaml
观察结果:
1. 我们应该看不到 configmap 的任何数据

# Helm 卸载
helm uninstall myapp1
```
