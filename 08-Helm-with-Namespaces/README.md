# Helm 与 Kubernetes 命名空间

## 步骤-01：介绍

- 我们使用 HELM 管理的任何资源都特定于 Kubernetes 命名空间
- 默认情况下，Kubernetes 资源使用默认命名空间部署到 k8s 集群，因此我们不需要显式指定命名空间名称
- 如果我们想要将 k8s 资源部署到命名空间（默认命名空间以外），那么我们需要在 `helm install` 命令中使用标志 `--namespace` 或 `-n` 指定
- 此外，我们还可以在 `helm install` 期间使用标志 `--namespace` `--create-namespace` 创建命名空间

## 步骤-02：通过创建 Kubernetes 命名空间 dev 安装 Helm 发布

```bash
# List Kubernetes Namespaces 
kubectl get ns

# Install Helm Release by creating Kubernetes Namespace
helm install dev101 stacksimplify/mychart2 --version "0.1.0" --namespace dev --create-namespace 

# List Kubernetes Namespaces 
kubectl get ns
Observation: Found the dev namespace created as part of `helm install`

# List Helm Release
helm list --> NO RELEASES in default namespace
helm list -n dev
helm list --namespace dev

# Helm Status
helm status dev101 --show-resources -n dev
helm status dev101 --show-resources --namespace dev

# List Kubernetes Pods
kubectl get pods -n dev
kubectl get pods --namespace dev

# List Services
kubectl get svc -n dev

# List Deployments
kubectl get deploy -n dev

# Access Application
http://localhost:31232
```

## 步骤-03：为 dev 命名空间中存在的资源运行 helm 升级

```bash
# Helm Upgrade
helm upgrade dev101 stacksimplify/mychart2 --version "0.2.0" --namespace dev 
or
helm upgrade dev101 stacksimplify/mychart2 --version "0.2.0" -n dev

# List Helm Release
helm list -n dev
helm list --namespace dev

# Helm Status
helm status dev101 --show-resources -n dev
helm status dev101 --show-resources --namespace dev

# Access Application
http://localhost:31232
```

## 步骤-04：从 dev 命名空间卸载 Helm 发布

```bash
# Uninstall Helm Releas
helm uninstall dev101 --namespace dev
helm uninstall dev101 -n dev

# List Helm Release
helm list -n dev
helm list --namespace dev

# List Kubernetes Namespaces
kubectl get ns
Observation: 
1. When uninstalling helm release, it will not delete the Kubernetes Resource: dev namespace. 
2. If we dont need that dev namespace we need to manually delete it from kubernetes using kubectl

# Delete dev namespace
kubectl delete ns dev

# List Kubernetes Namespaces
kubectl get ns
```
