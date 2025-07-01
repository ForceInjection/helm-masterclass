# Helm 安装

## 步骤-01：介绍

我们将在此演示中使用以下命令：

- helm repo list
- helm repo add
- helm repo update
- helm search repo
- helm install
- helm list
- helm uninstall

## 步骤-02：列出、添加和搜索 Helm 仓库

- [使用 Helm 打包的 Bitnami 应用程序](https://bitnami.com/stacks/helm)
- 在 [Artifacthub.io](https://artifacthub.io/) 搜索 Helm Charts

```bash
# List Helm Repositories
helm repo list

# Add Helm Repository
helm repo add <DESIRED-NAME> <HELM-REPO-URL>
helm repo add mybitnami https://charts.bitnami.com/bitnami

# List Helm Repositories
helm repo list

# Search Helm Repository
helm search repo <KEY-WORD>
helm search repo nginx
helm search repo apache
helm search repo wildfly
```

## 步骤-03：安装 Helm Chart

安装 Helm Chart。

```bash
# Update Helm Repo
helm repo update  # Make sure we get the latest list of charts

# Install Helm Chart
helm install <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name>
helm install mynginx mybitnami/nginx
```

## 步骤-04：列出 Helm 发布

此命令列出指定命名空间的所有发布。

```bash
# List Helm Releases (Default Table Output)
helm list 
helm ls

# List Helm Releases (YAML Output)
helm list --output=yaml

# List Helm Releases (JSON Output)
helm list --output=json

# List Helm Releases with namespace flag
helm list --namespace=default
helm list -n default
```

## 步骤-05：列出 Kubernetes 资源

```bash
# List Kubernetes Pods
kubectl get pods

# List Kubernetes Services
kubectl get svc
Observation: Review the EXTERNAL-IP field and you will see it as localhost. Access the nginx page from local desktop localhost

# Access Nginx Application on local desktop browser
http://localhost:80
http://127.0.0.1:80

# Access Application using curl command
curl http://localhost:80
curl http://127.0.0.1:80
```

## 步骤-06：卸载 Helm 发布 - 无标志

```bash
# List Helm Releases
helm ls

# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall mynginx 
```
