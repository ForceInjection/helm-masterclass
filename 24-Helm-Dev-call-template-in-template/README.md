# Helm 命名模板 - 在模板中调用模板

## 步骤-01: 介绍

- 我们可以在其他命名模板中调用一个命名模板。

## 步骤-02: 更新 _helpers.tpl

- 我们将通过调用命名模板 `helmbasics.resourceName` 来更新模板 `helmbasics.labels`，添加 `template-in-template` 作为额外标签

```bash
{{/* 通用标签 */}}
{{- define "helmbasics.labels"}}
    app.kubernetes.io/managed-by: helm
    app: nginx
    chartname: {{ .Chart.Name }}
    template-in-template: {{ include "helmbasics.resourceName" . }}
{{- end }}
```

## 步骤-03: 测试更改

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
