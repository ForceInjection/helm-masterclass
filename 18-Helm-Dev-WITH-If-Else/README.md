# Helm 开发 - 流程控制 If-Else

## 步骤-01: 介绍

- 我们可以使用 `if/else` 在 Helm 模板中创建条件块
- **eq:** 对于模板，操作符（eq、ne、lt、gt、and、or 等）都实现为函数。
- 在管道中，操作可以用括号 ((, and )) 分组。
- [附加参考: 操作符是函数](https://helm.sh/docs/chart_template_guide/functions_and_pipelines/#operators-are-functions)

### IF-ELSE 语法

```bash
{{ if PIPELINE }}
  # 做某事
{{ else if OTHER PIPELINE }}
  # 做其他事
{{ else }}
  # 默认情况
{{ end }}
```

## 步骤-02: 查看 values.yaml

```yaml
# If, else if, else
myapp:
  env: prod
  retail:
    enableFeature: true
```

## 步骤-03: 逻辑和流程控制函数: and

- [逻辑和流程控制函数](https://helm.sh/docs/chart_template_guide/function_list/#logic-and-flow-control-functions)
- **and:** 返回两个或多个参数的布尔 AND（第一个空参数，或最后一个参数）。

```bash
# and 语法
and .Arg1 .Arg2
```

## 步骤-04: 使用布尔值为副本实现 if-else

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- with .Values.myapp }}
{{- if and .retail.enableFeature (eq .env "prod") }}
  replicas: 6
{{- else if eq .env "prod" }}
  replicas: 4
{{- else if eq .env "qa" }}  
  replicas: 2
{{- else }}  
  replicas: 1  
{{- end }}
{{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

## 步骤-05: 验证 if-else

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 
helm template myapp1 . --set myapp.retail.enableFeature=true
helm template myapp1 . --set myapp.retail.enableFeature=false
helm template myapp1 . --set myapp.env=qa
helm template myapp1 . --set myapp.env=dev

# Helm 安装 Dry-run 
helm install myapp1 . --dry-run

# Helm 安装
helm install myapp1 . --atomic

# 验证 Pods
helm status myapp1 --show-resources

# 卸载 Release
helm uninstall myapp1
```
