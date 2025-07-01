# Helm 开发 - 流程控制 Range 与字典

## 步骤-01: 介绍

- 使用 `values.yaml` 中的映射或字典实现 Range
- 实现如何在 Range 循环中调用 `Helm 变量`

## 步骤-02: Range 与键值对或映射或字典

- **源位置:** backupfiles/namespace.yaml
- **目标位置:** helmbasics/templates/namespace.yaml
- **文件名:** namespace.yaml

```yaml
# values.yaml
# Range 与字典
myapps:
  config1: 
    appName: myapp1
    appType: webserver
    appTech: HTML
    appDb: mysql
  config2: 
    appName: myapp2
    appType: webserver
    appTech: HTML
    appDb: mysql
  
# Range 与字典
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-configmap1
data: 
{{- range $key, $value := .Values.myapps.config1 }}
{{- $key | nindent 2}}: {{ $value }}
{{- end}}  

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
kubectl get configmap
kubectl get configmap <NAME-OF-CONFIGMAP> -o yaml
kubectl get configmap myapp1-helmbasics-configmap1 -o yaml

# 观察结果:
我们应该看到带有键值对的 configmap

# 卸载 Helm Release
helm uninstall myapp1
```

## 步骤-03: Range - 使用 Helm 变量在 Range 内部访问根内置对象

- **源位置:** backupfiles/namespace-with-variable.yaml
- **目标位置:** helmbasics/templates/namespace-with-variable.yaml
- **文件名:** namespace-with-variable.yaml

```yaml
# values.yaml
# Range 与字典
myapps:
  config1: 
    appName: myapp1
    appType: webserver
    appTech: HTML
    appDb: mysql
  config2: 
    appName: myapp2
    appType: webserver
    appTech: HTML
    appDb: mysql
  
# Range: 使用 Helm 变量在 Range 中访问根对象
{{- $chartName := .Chart.Name  }}
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-configmap2
data: 
{{- range $key, $value := .Values.myapps.config2 }}
{{- $key | nindent 2}}: {{ $value }}-{{ $chartName }}
{{- end}}  

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp1 .

# 使用 dry-run 进行 Helm 安装
helm install myapp1 . --dry-run 

# Helm 安装和测试
helm install myapp1 . --atomic
helm list

# 列出 k8s 命名空间
kubectl get configmap
kubectl get configmap <NAME-OF-CONFIGMAP> -o yaml
kubectl get configmap myapp1-helmbasics-configmap2 -o yaml

# 观察结果:
我们应该看到带有键值对的 configmap

# 卸载 Helm Release
helm uninstall myapp1
```
