# Helm 开发 - 命名模板

## 步骤-01: 介绍

- 创建命名模板
- 使用模板动作调用命名模板
- 如果我们在命名模板中使用 Helm 内置对象，则将根对象点 (.) 传递给模板动作
- 对于 `模板调用` 使用 `管道` 并查看是否有效
- 将 `模板调用` 替换为特殊用途函数 `include` 与 `管道` 结合并测试

## 步骤-02: 创建命名模板

- **文件位置:** deployment.yaml
- 在 `deployment.yaml` 中定义以下命名模板

```bash
{{/* 通用标签 */}}
{{- define "helmbasics.labels"}}
    app: nginx
{{- end }}
```

## 步骤-03: 使用模板动作调用命名模板

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment 
  labels:
  {{- template "helmbasics.labels" }}
```

## 步骤-04: 使用模板动作测试输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run

# Helm Release
helm install myapp101 . --atomic
kubectl get deploy
kubectl describe deploy <DEPLOYMENT-NAME>
helm uninstall myapp101
```

## 步骤-05: 向标签添加一个内置对象 Chart.Name

```bash
{{/* 通用标签 */}}
{{- define "helmbasics.labels"}}
    app: nginx
    chartname: {{ .Chart.Name }}
{{- end }}
```

## 步骤-06: 使用模板动作测试输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run
观察结果:
1. Chart 名称字段应该为空
2. Chart 名称不在我们定义的模板作用域内。
3. 当渲染命名模板（使用 define 创建）时，它将接收模板调用传入的作用域。
4. 没有传入作用域，所以在模板内我们无法访问 "." 中的任何内容
5. 这很容易修复。我们只需将作用域传递给模板
```

## 步骤-07: 将作用域传递给模板调用

- 在模板调用末尾添加点 "."（根对象或句点）以将作用域传递给模板调用

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment # 动作元素
  labels:
  {{- template "helmbasics.labels" . }}
```

## 步骤-08: 当作用域传递给模板调用时，使用模板动作测试输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run
观察结果:
应该显示 Chart 名称
```

## 步骤-09: 将 Upper 函数管道传递给模板

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment # 动作元素
  labels:
  {{- template "helmbasics.labels" . | upper }}
```

## 步骤-10: 测试模板动作 + 管道 + upper 函数时的输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run
观察结果:
1. 应该失败并出现错误。失败的原因是什么？
2. Template 是一个动作，而不是函数，无法将模板调用的输出传递给其他函数；
```

## 步骤-11: 用特殊用途的 include 函数替换 Template 动作

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment # 动作元素
  labels:
  {{- include "helmbasics.labels" . | upper }}
```

## 步骤-10: 测试 include 函数的输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run
观察结果:
1. 调用 include "helmbasics.labels" -- 应该成功
2. 应该显示所有标签为大写
```

## 步骤-11: 下划线文件 (_helpers.tpl)

- 将命名模板 `helmbasics.labels` 移动到 `_helpers.tpl` 文件
- 但是名称以下划线 (_) 开头的文件被假定为内部没有 kubernetes 清单。
- 这些文件不会渲染为 Kubernetes 对象定义，但在其他 chart 模板中随处可用。
- 这些文件用于存储部分模板和辅助函数。

```bash
{{/* 通用标签 */}}
{{- define "helmbasics.labels"}}
    app: nginx
    chartname: {{ .Chart.Name }}
{{- end }}
```

## 步骤-12: 将命名模板移动到 _helpers.tpl 后测试输出

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm Template 命令
helm template myap101 .

# 使用 dry-run 命令进行 Helm 安装
helm install myapp101 . --dry-run
观察结果:
1. 调用 include "helmbasics.labels" -- 应该成功
2. 应该显示所有标签为大写
```
