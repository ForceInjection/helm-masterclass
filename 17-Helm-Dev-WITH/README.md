# Helm 开发 - 流程控制 With

## 步骤-01: 介绍

- `with` 动作控制变量作用域。
- `with` 动作可以允许你将当前作用域 (.) 设置为特定对象。

### with 动作语法

```bash
{{ with PIPELINE }}
  # 受限作用域
{{ end }}
```

## 步骤-02: 查看 values.yaml

```yaml
# 用于测试流程控制: with
podAnnotations: 
  appName: myapp1
  appType: webserver
  appTech: HTML
```

## 步骤-03: 实现 "with" 动作

- `with` 动作语句将点对象 "." 设置为 `.Values.podAnnotations`
- 在 `with` 动作块内部，点 "." 始终指向 `.Values.podAnnotations`
- 在 `with` 动作块外部，点 "." 指向根对象

```yaml
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}        
      {{- end }}    
```

## 步骤-04: 测试 "with" 动作实现

```bash
# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 观察结果:
我们应该看到所有注释都显示出来
      annotations:
        appName: myapp1
        appTech: HTML
        appType: webserver
```

## 步骤-05: 尝试在 "with" 动作块中访问任何根对象

```bash
# 在 with 块中添加根对象
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
        appManagedBy: {{ .Release.Service }}
      {{- end }}    

# 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 观察结果:
1. 它应该抛出错误并失败，因为 .Release.Service 不在 . 的受限作用域内，. 指向 ".Values.podAnnotations"。

## 示例错误
Error: template: helmbasics/templates/deployment.yaml:23:33: executing "helmbasics/templates/deployment.yaml" at <.Release.Service>: nil pointer evaluating interface {}.Service
```

## 步骤-06: 为根对象添加 $

- 要在 `with` 动作块内访问根对象，我们需要在该根对象前加上 `$`

```bash
# 访问根对象
       appManagedBy: {{ $.Release.Service }}

 # 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 观察结果:
1. 它应该按预期工作
      annotations:
        appName: myapp1
        appTech: HTML
        appType: webserver
        appManagedBy: Helm  
```

## 步骤-07: 为 "with" 动作块设置更详细的作用域

- 如何从 `.Values.myapps.data.config` 检索单个对象？
- 如果只需要从 `.Values.myapps.data.config` 中获取 1 或 2 个值怎么办？
- 如何访问 `.Values.myapps.data.config` 中的每个键值？

```yaml
# values.yaml
# 用于测试流程控制: with - 更详细的作用域
myapps:
  data: 
    config: 
      appName: myapp1
      appType: webserver
      appTech: HTML
      appDb: mysql

# 当前作用域: 使用作用域检索单个对象
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
data: 
{{- with .Values.myapps.data.config }}
  application-name: {{ .appName }}
  application-type: {{ .appType }}
{{- end}} 

 # 切换到 Chart 目录
cd helmbasics  

# Helm Template
helm template myapp101 .

# 使用 dry-run 进行 Helm 安装
helm install myapp101 . --dry-run  

# 观察结果:
1. 我们应该能够获取 {{ .appName }} 和 {{ .appType }} 的值
```
