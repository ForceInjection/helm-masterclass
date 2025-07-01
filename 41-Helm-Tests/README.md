# Helm 测试

## 步骤-01: 介绍

- helm test 命令

## 步骤-02: 创建 Helm 图表和 Release

```bash
# Helm 创建
helm create mydemoapp

# Helm 安装
helm install myapp101 mydemoapp/

# 列出 Helm Releases
helm list
```

## 步骤-03: 查看 Helm 测试 Yaml 文件

- **文件位置:** mydemoapp/templates/test/test-connection.yaml
- 主要查看测试钩子: `"helm.sh/hook": test`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "mydemoapp.fullname" . }}-test-connection"
  labels:
    {{- include "mydemoapp.labels" . | nindent 4 }}
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['{{ include "mydemoapp.fullname" . }}:{{ .Values.service.port }}']
  restartPolicy: Never
```

## 步骤-04: Helm 测试和验证

```bash
# 列出 Kubernetes Pods
kubectl get pods

# Helm 测试
helm test <RELEASE-NAME>
helm test myapp101

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 测试连接 pod 应该被创建并处于完成状态

# 示例输出
Kalyans-Mac-mini:51-Helm-Tests kalyan$ helm test myapp101
NAME: myapp101
LAST DEPLOYED: Thu Aug  3 16:48:46 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE:     myapp101-mydemoapp-test-connection
Last Started:   Thu Aug  3 16:48:50 2023
Last Completed: Thu Aug  3 16:49:00 2023
Phase:          Succeeded
```

## 步骤-05: 卸载 Helm Release

```bash
# 卸载 Helm Release
helm uninstall myapp101

# 列出 Helm Releases
helm list

# 列出 Kubernetes Pods
kubectl get pods
```
