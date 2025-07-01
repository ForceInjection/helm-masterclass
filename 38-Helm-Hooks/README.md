# Helm 钩子

## 步骤-01: 介绍

- 理解 Helm 钩子

## 步骤-02: 从启动器图表创建简单图表

- **重要说明:** 这一步对您来说是可选的，因为您将拥有所有图表文件和文件夹，可以在相应部分实现 hooksdemo1

```bash
# 从启动器图表创建 Helm 图表
helm create hooksdemo1 --starter=mystarterchart
```

## 步骤-03: 创建/查看 pre-install 钩子

- **文件位置:** templates/preinstall-hookpod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: myhook-preinstall
  annotations:
    "helm.sh/hook": "pre-install"
spec:
  restartPolicy: Never
  containers:
    - name: myhook-preinstall-container
      image: busybox
      imagePullPolicy: IfNotPresent
      command:  ['sh', '-c', 'echo Pre-install hook Pod is running && sleep 15']      
```

## 步骤-04: 创建/查看 pre-upgrade 钩子

- **文件位置:** templates/preupgrade-hookpod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: myhook-preupgrade
  annotations:
    "helm.sh/hook": "pre-upgrade"
spec:
  restartPolicy: Never
  containers:
    - name: myhook-preupgrade-container
      image: busybox
      imagePullPolicy: IfNotPresent
      command:  ['sh', '-c', 'echo preupgrade hook Pod is running && sleep 15']       
```

## 步骤-05: 创建/查看 post-delete 钩子

- **文件位置:** templates/postdelete-hookpod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: myhook-postdelete
  annotations:
    "helm.sh/hook": "post-delete"
spec:
  restartPolicy: Never
  containers:
    - name: myhook-postdelete-container
      image: busybox
      imagePullPolicy: IfNotPresent
      command:  ['sh', '-c', 'echo post-delete hook Pod is running && sleep 15']
```

## 步骤-06: 测试 Helm 钩子: pre-install

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 安装 Helm Release
helm install myapp101 . --atomic

# 列出 Helm Release
helm list

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到 "myhook-preinstall" pod，它应该是完成状态

# 描述 Pod
kubectl describe pod myhook-preinstall

# 验证 Pod 开始和结束时间
kubectl get pods
kubectl describe pod myhook-preinstall | grep -E 'Anno|Started:|Finished:'
kubectl describe pod myapp101-hooksdemo1-65b7c4d5b9-2rqfx | grep -E 'Anno|Started:|Finished:'

# 访问应用程序
kubectl get svc
http://localhost:31239
观察结果: 我们应该看到应用程序的 V1 版本
```

## 步骤-07: 钩子和 Release 生命周期

1. 假设对于 `helm install` 生命周期，我们定义了两个钩子 `pre-install` 和 `post-install`，让我们了解会发生什么
2. 通过查看文档 [钩子和 Release 生命周期](https://helm.sh/docs/topics/charts_hooks/#hooks-and-the-release-lifecycle) 进行讨论

## 步骤-08: 测试 Helm 钩子: pre-upgrade

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 升级 Helm Release
helm list
helm upgrade myapp101 . --set image.tag=0.2.0

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到 "myhook-preupgrade" pod，它应该是完成状态

# 描述 Pod
kubectl describe pod myhook-preupgrade

# 验证 Pod 开始和结束时间
kubectl get pods
kubectl describe pod myhook-preupgrade | grep -E 'Anno|Started:|Finished:'
kubectl describe pod myapp101-hooksdemo1-7b997b4556-t6s75 | grep -E 'Anno|Started:|Finished:'

# 访问应用程序
kubectl get svc
http://localhost:31239
观察结果: 我们应该看到应用程序的 V2 版本
```

## 步骤-09: 测试 Helm 钩子: post-delete

```bash
# 切换目录 (在 Helm 图表文件夹中)
cd hooksdemo1

# 卸载/删除 Helm Release
helm list
helm uninstall myapp101 

# 列出 Kubernetes Pods
kubectl get pods
观察结果:
1. 我们应该看到 "myhook-postdelete" pod，它应该是完成状态
2. 即使在删除/卸载 release 后，我们应该看到所有 3 个钩子 pod 仍然存在
```

## 步骤-10: 钩子资源不与相应的 release 一起管理

1. 钩子创建的资源目前不作为 release 的一部分进行跟踪或管理。
2. 一旦 Helm 验证钩子已达到就绪状态，它将不再管理钩子资源。
3. 简而言之，`helm uninstall` 不会删除钩子资源。
