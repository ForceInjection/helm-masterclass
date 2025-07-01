# Helm 开发 - 流程控制 If-Else

## 步骤-01: 介绍

- 我们可以使用 `if/else` 在 Helm 模板中创建条件块
- **eq:** 对于模板，运算符（eq、ne、lt、gt、and、or 等）都作为函数实现。
- 在管道中，操作可以用括号（(, 和 )）分组。
- [附加参考: 运算符是函数](https://helm.sh/docs/chart_template_guide/functions_and_pipelines/#operators-are-functions)

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
```

## 步骤-03: 逻辑和流程控制函数: and

- [逻辑和流程控制函数](https://helm.sh/docs/chart_template_guide/function_list/#logic-and-flow-control-functions)
- **eq:** 返回参数的布尔相等性（例如，Arg1 == Arg2）。

```bash
# and 语法
eq .Arg1 .Arg2
```

## 步骤-04: 为副本数实现 if-else

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- if eq .Values.myapp.env "prod" }}
  replicas: 4 
{{- else if eq .Values.myapp.env "qa" }}  
  replicas: 2
{{- else }}  
  replicas: 1
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

# Helm Template（当 values.yaml 中 env: prod 时）
## 测试 IF 语句
helm template myapp1 .

# Helm Template（使用 --set 设置 env: qa 时）
## 测试 ELSE IF 语句
helm template myapp1 . --set myapp.env=qa
 
# Helm Template（使用 --set 设置 env: dev 或 env: null 时）
## 测试 ELSE 语句
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
