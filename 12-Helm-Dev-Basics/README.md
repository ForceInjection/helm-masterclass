# Helm 模板函数和管道

## 步骤-01: 介绍

1. 模板动作 `{{ }}`
2. 动作元素 `{{ .Release.Name }}`
3. Quote 函数
4. 管道
5. default 函数
6. lower 函数
7. 控制空白字符 `{{-  -}}`
8. indent 函数
9. nindent 函数
10. toYaml

## 步骤-02: 模板动作 "{{ }}"

- 模板动作 `{{ .Chart.Name }}` 之间的任何内容都称为动作元素
- 模板动作 `{{ .Chart.Name }}` 之间的任何内容都将由 helm 模板引擎渲染并替换必要的值
- 模板动作之外的任何内容都将按原样打印。
- 在 `{{ }}` 内定义的动作元素将帮助我们从其他源检索数据（例如：`.Chart.Name`）。

### 步骤-02-01: 有效的动作元素

```bash
# deployment.yaml 文件
apiVersion: apps/v1
kind: Deployment
metadata:
  # 带有动作元素的模板动作
  name: {{ .Release.Name }}-{{ .Chart.Name }}

# 切换到 CHART 目录
cd helmbasics

# Helm Template 命令
helm template myapp101 .
1. helm template 命令帮助我们检查完全渲染的 Kubernetes 资源模板中的 chart 输出。
2. 这在我们开发新 chart、修改 chart 模板、调试等时非常有用。
```

### 步骤-02-02: 无效的动作元素

```bash
# deployment.yaml 文件
apiVersion: apps/v1
kind: Deployment
metadata:
  # 带有动作元素的模板动作
  name: {{ something }}-{{ .Chart.Name }}
# 切换到 CHART 目录
cd helmbasics

# Helm Template 命令
helm template myapp101 .  
观察结果:
1. 应该失败并报错
2. 简而言之，在动作元素内部我们应该有

错误: parse error at (helmbasics/templates/deployment.yaml:10): function "something" not defined
```

## 步骤-03: 模板函数: quote

```bash
# 添加 Quote 函数
  annotations:    
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # quote 函数
    app.kubernetes.io/managed-by: {{ quote .Release.Service }} 

# 切换到 CHART 目录
cd helmbasics

# Helm Template 命令
helm template myapp101 .
```

## 步骤-04: 管道

- 管道是按顺序完成多个任务的有效方式。
- 在模板中反转顺序是常见做法 (.val | quote )

```bash
# 使用管道添加 Quote 函数
  annotations:    
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # quote 函数
    app.kubernetes.io/managed-by: {{ quote .Release.Service }} 
    # 使用管道的 quote 函数
    app.kubernetes.io/managed-by: {{ .Release.Service | quote }}               

# 切换到 CHART 目录
cd helmbasics

# Helm Template 命令
helm template myapp101 .
```

## 步骤-05: 模板函数: default 和 lower

- [default 函数](https://helm.sh/docs/chart_template_guide/function_list/#default)

```bash
# values.yaml
releaseName: "newrelease101"
replicaCount: 3

# Template Function default
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName | lower }}
spec:
  replicas: {{ default 1  .Values.replicaCount }}

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .
```

## Step-06: Controlling Whitespaces

- **{{- .Chart.name }}:**  If a hyphen is added before the statement, `{{- .Chart.name }}` then the leading whitespace will be ignored during the rendering
- **{{ .Chart.name -}}:** If a hyphen is added after the statement, `{{ .Chart.name -}}` then the trailing whitespace will be ignored during the rendering

```yaml
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName }}
    # Controlling Leading and Trailing White spaces 
    leading-whitespace: "   {{- .Chart.Name }}    kalyan"
    trailing-whitespace: "   {{ .Chart.Name -}}    kalyan"
    leadtrail-whitespace: "   {{- .Chart.Name -}}    kalyan"    

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .    
```

## Step-07: indent and nindent functions

- **indent:** The [indent function](https://helm.sh/docs/chart_template_guide/function_list/#indent) indents every line in a given string to the specified indent width. This is useful when aligning multi-line strings:
- **nindent:** The [nindent function](https://helm.sh/docs/chart_template_guide/function_list/#nindent) is the same as the indent function, but prepends a new line to the beginning of the string.

```yaml
# indent and nindent functions
  annotations:
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # Quote Function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }}        
    # Pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote | upper | lower }}        
    # default Function
    app.kubernetes.io/name: {{ default "MYRELEASE101" .Values.releaseName | lower }}
    # Controlling Leading and Trailing White spaces 
    leading-whitespace: "   {{- .Chart.Name }}    kalyan"
    trailing-whitespace: "   {{ .Chart.Name -}}    kalyan"
    leadtrail-whitespace: "   {{- .Chart.Name -}}    kalyan"  
    # indent function
    indenttest: "  {{- .Chart.Name | indent 4 -}}  "
    # nindent function
    nindenttest: "  {{- .Chart.Name | nindent 4 -}}  "  

# Change to CHART Directory
cd helmbasics

# Helm Template Command
helm template myapp101 .    
```

## 步骤-08: 模板函数: toYaml

- **toYaml:**
- 我们可以在 helm 模板动作中使用 [toYaml 函数](https://helm.sh/docs/chart_template_guide/function_list/#type-conversion-functions) 将对象转换为 YAML。
- 将列表、切片、数组、字典或对象转换为缩进的 yaml。

```bash
# values.yaml
# 用于测试模板函数的资源: toYaml 
resources: 
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi

# deployment.yaml
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
        resources: 
        {{- toYaml .Values.resources | nindent 10}}

# 切换到 CHART 目录
cd helmbasics

# Helm Template 命令
helm template myapp101 .

# 使用 --dry-run 进行 Helm 安装
helm install myapp101 . --dry-run

# Helm 安装
helm install myapp101 . --atomic

# 列出 k8s Pods
kubectl get pods 

# 描述 Pod
kubectl describe pod <POD-NAME>

# Helm 卸载
helm uninstall myapp101
```
