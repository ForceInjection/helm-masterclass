# Helm 钩子权重

## 步骤-01: 介绍

- 钩子权重可以是正数或负数，但必须表示为字符串（用双引号 "8"）

```yaml
annotations:
  "helm.sh/hook-weight": "5"
```

- 当 Helm 开始执行特定类型（例如：kind:pod）的钩子执行周期时，它将按升序对这些钩子进行排序。

## 步骤-02: 查看钩子 Pod 模板注解

### preinstall-hookpod1.yaml

```yaml
  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "-2"
```

### preinstall-hookpod2.yaml

```yaml
  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "5"
```

### preinstall-hookpod3.yaml

```yaml
  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "6"
```

## 步骤-03: 安装 Helm Release

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 安装 Helm Release
helm install myapp101 . 

# 列出 Helm Release
helm list

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到所有 3 个钩子 pod 被创建并处于完成状态。
2. 验证 AGE 字段以了解它们执行的时间
3. 具有最低钩子权重的钩子 pod 将首先执行
4. 简而言之，钩子将按钩子权重的升序执行

# 验证 Pod 开始和结束时间戳
kubectl describe pod myhook-preinstall1 | grep -E 'Anno|Started:|Finished:'
kubectl describe pod myhook-preinstall2 | grep -E 'Anno|Started:|Finished:'
kubectl describe pod myhook-preinstall3 | grep -E 'Anno|Started:|Finished:'

```

## 步骤-04: 卸载 Helm Release 并清理

```bash
# 列出 Helm Releases
helm list

# 卸载 Helm Release
helm uninstall myapp101

# 删除钩子 Pods
kubectl get pods
kubectl delete pod myhook-preinstall1 
kubectl delete pod myhook-preinstall2
kubectl delete pod myhook-preinstall3
```
