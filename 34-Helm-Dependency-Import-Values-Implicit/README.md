# Helm 依赖 - 隐式导入值

## 步骤-01: 介绍

- 实现隐式导入值用例

## 步骤-02: 查看/更新 parentchart Chart.yaml

- **文件位置:** parentchart/Chart.yaml
- 定义 `import-values`

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart1
  version: "0.1.0"
  repository: "file://charts/mychart1"
  alias: childchart1
  tags: 
    - frontend

- name: mychart2
  version: "0.4.0"
  repository: "file://charts/mychart2"
  alias: childchart2
  tags: 
    - backend
  import-values: # 隐式值用例
    - child: service 
      parent: mychart2service   
    - child: image 
      parent: mychart2image      
```

## 步骤-03: 查看/更新 parentchart configmap.yaml

- **文件位置:** parentchart/templates/configmap.yaml
- 在 `configmap.yaml` 中使用导入的值

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name:  {{ include "parentchart.fullname" . }}-import-implicit
data:
  serviceType: {{ .Values.mychart2service.type }}
  servicePort: {{ .Values.mychart2service.port | quote}}
  servicenodePort: {{ .Values.mychart2service.nodePort | quote }}
  imageRepository: {{ .Values.mychart2image.repository }}
```

## 步骤-04: 隐式导入值：部署和验证

```bash
# 切换到图表目录
cd parentchart

# Helm 安装
helm install myapp1 . --atomic

# Helm 列表
helm list

# Helm 状态
helm status myapp1 --show-resources

# 列出 k8s 部署
kubectl get deploy

# 列出 k8s pods
kubectl get pods

# 列出 k8s ConfigMaps
kubectl get cm
kubectl get cm myapp1-parentchart-import-implicit -o yaml
观察结果:
我们应该看到从 parentchart/charts/mychart2/values.yaml 导出的数据成功导入到 parentchart 中的 configmap。

# Helm 卸载
helm uninstall myapp1 
```

## 步骤-06: 测试当 mychart2 被禁用时

```bash
# 切换到图表目录
cd parentchart

# Helm 安装 (当 mychart2 被禁用时)
helm install myapp1 . --atomic --set tags.backend=false
观察结果:
应该失败并显示错误

## 错误
Kalyans-Mac-mini:parentchart kalyanreddy$ helm install myapp1 . --atomic --set tags.backend=false
Error: INSTALLATION FAILED: template: parentchart/templates/configmap.yaml:6:25: executing "parentchart/templates/configmap.yaml" at <.Values.mychart2service.type>: nil pointer evaluating interface {}.type
```
