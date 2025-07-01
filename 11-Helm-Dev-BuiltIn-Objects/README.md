# Helm 内置对象

## 步骤-01：介绍

- 对象从模板引擎传递到模板中。
- 对象可以是简单的，只有一个值，也可以包含其他对象或函数。
- 例如：Release 对象包含多个对象（如 .Release.Name），Files 对象有一些函数。

Helm 内置对象

- Release
- Chart
- Values
- Capabilities
- Template
- Files

## 步骤-02：创建一个简单的 chart 并清理 NOTES.txt

```bash
# Create Helm Chart
helm create CHART-NAME
helm create builtinobjects

# Remove all content from NOTES.txt
cd builtinobjects/templates
>NOTES.txt

# Change to Chart Directory
cd builtinobjects

# helm install --dry-run
helm install myapp1 . --dry-run
```

## 步骤-03：Helm 对象：根或点或句号 (.)

```bash
# Update NOTES.txt
{{/* Root or Dot or Period Object */}}
Root Object: {{ . }}

# Change to Chart Directory
cd builtinobjects

# helm install with --dry-run
helm install myapp101 . --dry-run
```

## 步骤-04：Helm 对象：Release

- 此对象描述 Helm 发布。
- 它内部有几个与 Helm 发布相关的对象。
- 将以下内容放入 `NOTES.txt` 并测试

```bash
{{/* Release Object */}}
Release Name: {{ .Release.Name }}
Release Namespace: {{ .Release.Namespace }}
Release IsUpgrade: {{ .Release.IsUpgrade }}
Release IsInstall: {{ .Release.IsInstall }}
Release Revision: {{ .Release.Revision }}
Release Service: {{ .Release.Service }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
NOTES:
Release Name: myapp101
Release Namespace: default
Release IsUpgrade: false
Release IsInstall: true
Release Revision: 1
Release Service: Helm
```

## 步骤-05：Helm 对象：Chart

- Chart.yaml 中的任何数据都可以使用 Chart 对象访问。
- 例如 {{ .Chart.Name }}-{{ .Chart.Version }} 将输出 builtinobjects-0.1.0。
- [完整的 Chart.yaml 对象参考](https://helm.sh/docs/topics/charts/#the-chartyaml-file)
- 将以下内容放入 `NOTES.txt` 并测试

```bash
{{/* Chart Objet */}}
Chart Name: {{ .Chart.Name }}
Chart Version: {{ .Chart.Version }}
Chart AppVersion: {{ .Chart.AppVersion }}
Chart Type: {{ .Chart.Type }}
Chart Name and Version: {{ .Chart.Name }}-{{ .Chart.Version }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
Chart Name: builtinobjects
Chart Version: 0.1.0
Chart AppVersion: 0.1.0
Chart Type: application
Chart Name and Version: builtinobjects-0.1.0
```

## Step-06: Helm Objects: Values, Capabilities, Template

- **Values Object:** Values passed into the template from the values.yaml file and from user-supplied files. By default, Values is empty.
- **Capabilities Object:** This provides information about what capabilities the Kubernetes cluster supports
- **Template Object:** Contains information about the current template that is being executed
- Put the below in `NOTES.txt` and test it

```bash
{{/* Values Object */}}
Replica Count: {{ .Values.replicaCount }}
Image Repository: {{ .Values.image.repository }}
Service Type: {{ .Values.service.type }}

{{/* Capabilities Object */}}
Kubernetes Cluster Version Major: {{ .Capabilities.KubeVersion.Major }}
Kubernetes Cluster Version Minor: {{ .Capabilities.KubeVersion.Minor }}
Kubernetes Cluster Version: {{ .Capabilities.KubeVersion }} and {{ .Capabilities.KubeVersion.Version }}
Helm Version: {{ .Capabilities.HelmVersion }}
Helm Version Semver: {{ .Capabilities.HelmVersion.Version }}

{{/* Template Object */}}
Template Name: {{ .Template.Name }} 
Template Base Path: {{ .Template.BasePath }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
Replica Count: 1
Image Repository: ghcr.io/stacksimplify/kubenginxhelm
Service Type: NodePort

Kubernetes Cluster Version Major: 1
Kubernetes Cluster Version Minor: 27
Kubernetes Cluster Version: v1.27.2 and v1.27.2
Helm Version: {v3.12.1 f32a527a060157990e2aa86bf45010dfb3cc8b8d clean go1.20.5}
Helm Version Semver: v3.12.1

Template Name: builtinobjects/templates/NOTES.txt 
Template Base Path: builtinobjects/templates
```

## Step-07: Helm Objects: Files

- **Files Object:**
- Put the below in `NOTES.txt` and test it
- [Additional Reference: Access Files Inside Templates](https://helm.sh/docs/chart_template_guide/accessing_files/)

```bash
{{/* File Object */}}
File Get: {{ .Files.Get "myconfig1.toml" }}
File Glob as Config: {{ (.Files.Glob "config-files/*").AsConfig }}
File Glob as Secret: {{ (.Files.Glob "config-files/*").AsSecrets }}
File Lines: {{ .Files.Lines "myconfig1.toml" }}
File Lines: {{ .Files.Lines "config-files/myconfig2.toml" }}
File Glob: {{ .Files.Glob "config-files/*" }}

# Change to CHART Directory 
cd builtinobjects 

# Helm Install with --dry-run
helm install myapp101 . --dry-run

# Sample Output
File Get: message1 = Hello from config 1 line1
message2 = Hello from config 1 line2
message3 = Hello from config 1 line3

File Glob as Config: myconfig2.toml: |-
  appName: myapp2
  appType: db
  appConfigEnable: true
myconfig3.toml: |-
  appName: myapp3
  appType: app
  appConfigEnable: false
File Glob as Secret: myconfig2.toml: YXBwTmFtZTogbXlhcHAyCmFwcFR5cGU6IGRiCmFwcENvbmZpZ0VuYWJsZTogdHJ1ZQ==
myconfig3.toml: YXBwTmFtZTogbXlhcHAzCmFwcFR5cGU6IGFwcAphcHBDb25maWdFbmFibGU6IGZhbHNl
File Lines: [message1 = Hello from config 1 line1 message2 = Hello from config 1 line2 message3 = Hello from config 1 line3 ]
File Lines: [appName: myapp2 appType: db appConfigEnable: true]
File Glob: map[config-files/myconfig2.toml:[97 112 112 78 97 109 101 58 32 109 121 97 112 112 50 10 97 112 112 84 121 112 101 58 32 100 98 10 97 112 112 67 111 110 102 105 103 69 110 97 98 108 101 58 32 116 114 117 101] config-files/myconfig3.toml:[97 112 112 78 97 109 101 58 32 109 121 97 112 112 51 10 97 112 112 84 121 112 101 58 32 97 112 112 10 97 112 112 67 111 110 102 105 103 69 110 97 98 108 101 58 32 102 97 108 115 101]]
```

## Additional Reference

- [Helm Built-In Objects](https://helm.sh/docs/chart_template_guide/builtin_objects/)
- [Helm Chart.yaml Fields](https://helm.sh/docs/chart_template_guide/builtin_objects/)
