# Helm Printf 函数

## 步骤-01: 介绍

- **[printf](https://helm.sh/docs/chart_template_guide/function_list/#printf):** 基于格式化字符串和按顺序传递给它的参数返回一个字符串。

## 步骤-02: 使用 printf 函数创建命名模板

```bash
{{/* Kubernetes 资源名称: 使用连字符进行字符串连接 */}}
{{- define "helmbasics.resourceName" }}
{{- printf "%s-%s" .Release.Name .Chart.Name }}
{{- end }}
```

## 步骤-03: 在 deployment.yaml 中调用命名模板

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "helmbasics.resourceName" . }}-deployment 
  labels:
```

## 步骤-04: 测试更改

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myapp1 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp1 . --dry-run

# 使用 --atomic 标志进行 Helm 安装
helm install myapp1 . --atomic

# Helm 卸载
helm uninstall myapp1
```
