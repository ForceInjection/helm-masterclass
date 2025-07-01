# Helm 开发 - 流程控制 Range 动作与列表

## 步骤-01: 介绍

- 使用 `values.yaml` 中的 `值列表` 实现 `Range`
- 实现如何在 Range 循环中调用 `Helm 变量`

## 步骤-02: 使用 "值列表" 实现 "Range 动作"

- **源位置:** backupfiles/namespace.yaml
- **目标位置:** helmbasics/templates/namespace.yaml
- **文件名:** namespace.yaml

```bash
# values.yaml
# 流程控制: Range 与列表
namespaces:
  - name: myapp1
  - name: myapp2
  - name: myapp3

# Range 与列表
{{- range .Values.namespaces }}
apiVersion: v1
kind: Namespace
metadata:
  name: {{ .name }}
---  
{{- end }}      

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp1 .

# 使用 dry-run 进行 Helm 安装
helm install myapp1 . --dry-run 

# Helm 安装和测试
helm install myapp1 . --atomic
helm list

# Helm 状态
helm status myapp1 --show-resources

# 列出 k8s 命名空间
kubectl get ns

# 观察结果:
我们应该看到所有命名空间都已创建

# 卸载 Helm Release
helm uninstall myapp1
```

## 步骤-03: 使用变量实现 "Range 动作" 与 "值列表"

- **源位置:** backupfiles/namespace-with-variable.yaml
- **目标位置:** helmbasics/templates/namespace-with-variable.yaml
- **文件名:** namespace-with-variable.yaml

```bash
# values.yaml
# 流程控制: Range 与列表和 Helm 变量
environments:
  - name: dev
  - name: qa
  - name: uat  
  - name: prod    

# Range 与列表
{{- range $environment := .Values.environments }}
apiVersion: v1
kind: Namespace
metadata:
  name: {{ $environment.name }}
---  
{{- end }}           

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp1 .

# 使用 dry-run 进行 Helm 安装
helm install myapp1 . --dry-run 

# Helm 安装和测试
helm install myapp1 . --atomic
helm list

# Helm 状态
helm status myapp1 --show-resources

# 列出 k8s 命名空间
kubectl get ns

# 观察结果:
我们应该看到所有命名空间都已创建

# 卸载 Helm Release
helm uninstall myapp1
```
