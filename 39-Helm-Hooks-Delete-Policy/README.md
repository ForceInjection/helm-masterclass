# Helm 钩子删除策略

## 步骤-01: 介绍

- 实现 Helm 钩子删除策略

## 步骤-02: 列出 Kubernetes Pods

- **重要说明:** 我们继续之前的演示

```bash
# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到钩子 pod 处于完成状态但没有被移除
2. 我们需要如何移除它们？
选项-1: 手动删除它们
选项-2: 使用 Helm 钩子删除策略
```

## 步骤-03: 什么是 Helm 钩子删除策略？

1. 我们可以使用钩子删除策略定义何时删除钩子资源
2. **before-hook-creation:** 在启动新钩子之前删除之前的资源（默认）
3. **hook-succeeded:** 在钩子成功执行后删除资源
4. **hook-failed:** 如果钩子在执行期间失败则删除资源

```yaml
annotations:
  "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded, hook-failed
```

## 步骤-04: 部署新的 Helm Release

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 列出 Kubernetes Pods
kubectl get pods
观察结果: 在安装新 release 之前记录 pod 运行的时间

# 安装 Helm Release
helm install myapp101 . 

# 列出 Helm Release
helm list

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到 "myhook-preinstall" pod 刚刚被删除并重新创建
2. 这是如何发生的？
3. 对于 Helm 钩子删除策略，即使在我们的 hookpod yaml 文件中没有定义，"before-hook-creation" 是一个默认值，它被触发了。所以在 "helm install" 期间，旧的钩子 pod 被删除，新的被创建

"helm.sh/hook-delete-policy": before-hook-creation
before-hook-creation: 在启动新钩子之前删除之前的资源（默认）
```

## 步骤-05: 卸载 Helm Release 并清理

- 我们将卸载 helm release 并在测试我们添加的钩子删除策略更改之前清理所有钩子 pod。

```bash
# 卸载 Helm Release
helm uninstall myapp101

# 列出 Kubernetes Pods
kubectl get pods

# 删除钩子 pods
kubectl delete pod myhook-preinstall
kubectl delete pod myhook-preupgrade
kubectl delete pod myhook-postdelete
```

## 步骤-06: 使用以下钩子删除策略更新 hookpod yaml 文件

- 使用注解 `helm.sh/hook-delete-policy` 更新以下 3 个文件
- preinstall-hookpod.yaml
- preupgrade-hookpod.yaml
- postdelete-hookpod.yaml

```yaml
  "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
```

## 步骤-07: 安装 Helm Release 并测试钩子删除策略

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 安装 Helm Release
helm install myapp101 .

# 列出 Kubernetes Pods
kubectl get pods
观察结果: 
1. 我们应该看不到 "myhook-preinstall" pod
2. 它被创建、完成并删除，因为我们在 "helm.sh/hook-delete-policy" 中提供了 "hook-succeeded"
```

## 步骤-08: 升级 Helm Release 并测试钩子删除策略

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 升级 Helm Release
helm upgrade myapp101 . --set image.tag=0.2.0

# 列出 Kubernetes Pods
kubectl get pods
观察结果: 
1. 我们应该看不到 "myhook-preupgrade" pod
2. 它被创建、完成并删除，因为我们在 "helm.sh/hook-delete-policy" 中提供了 "hook-succeeded"
```

## 步骤-09: 卸载 Helm Release 并测试钩子删除策略

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 卸载 Helm Release
helm uninstall myapp101 

# 列出 Kubernetes Pods
kubectl get pods
观察结果: 
1. 我们应该看不到 "myhook-postdelete" pod
2. 它被创建、完成并删除，因为我们在 "helm.sh/hook-delete-policy" 中提供了 "hook-succeeded"
```

## 步骤-10: 使用 hook-failed 的缺点

1. **hook-failed:** 如果钩子在执行期间失败则删除资源
2. 在图表开发阶段的缺点是，当我们的钩子失败并且其资源被删除时，我们将没有选项进行故障排除。
3. 如果我们不使用 `hook-failed`，我们创建的资源将存在，我们可以描述该资源、查看事件并进行故障排除。
4. 这不是建议，只是我的个人观察。
