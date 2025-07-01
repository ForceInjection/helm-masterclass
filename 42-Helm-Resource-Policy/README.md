# Helm 资源策略演示

## 步骤-01: 介绍

- 有时候有些资源在 Helm 运行 helm uninstall 时不应该被卸载
- 图表开发者可以向资源添加注解来防止它被卸载。
- 注解 "helm.sh/resource-policy": keep 指示 Helm 在 helm 操作（如 helm uninstall、helm upgrade 或 helm rollback）会导致删除时跳过删除此资源。
- 但是，此资源会变成孤立的。
- Helm 将不再以任何方式管理它。
- 如果在已经卸载但保留了资源的 release 上使用 helm install --replace，这可能会导致问题。

## 步骤-02: 查看 Helm 资源策略注解

```yaml
metadata:
  annotations:
    "helm.sh/resource-policy": keep
```

## 步骤-03: 创建图表并向 deployment.yaml 添加资源策略注解

- **文件位置:** respolicytest/templates/deployment.yaml

```bash
# Helm 创建
helm create respolicytest

# 使用资源策略更新 deployment.yaml
metadata:
  # 测试 Helm 资源策略
  annotations:
    "helm.sh/resource-policy": keep
```

## 步骤-04: Helm 安装、卸载和验证

```bash
# 切换到图表目录
cd respolicytest

# 安装 Helm Release 
helm install myapp1 .

# 列出 Deployment、pods 和 Services
kubectl get deploy
kubectl get pods
kubectl get svc

# 卸载 Helm Release
helm uninstall myapp1

# 列出 Deployment、pods 和 Services
kubectl get deploy
kubectl get pods
kubectl get svc
观察结果:
1. 我们应该看到 deployment 不应该被卸载
2. 它的 pods 也应该处于运行状态

# 清理
kubectl delete deploy myapp1-respolicytest
```
