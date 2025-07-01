# 使用 set 选项进行 Helm 升级

## 步骤-01：介绍

我们将使用 `helm upgrade` 命令结合 `--set "image.tag=<DOCKER-IMAGE-TAGS>` 来升级 HELM 发布。我们将在此演示中使用以下 Helm 命令：

- helm repo
- helm search repo
- helm install
- helm upgrade
- helm history
- helm status

## 步骤-02：自定义 Helm 仓库

### 步骤-02-01：查看我们的自定义 Helm 仓库

- [托管在 GitHub 上的 StackSimplify Helm 仓库](https://stacksimplify.github.io/helm-charts/)
- [StackSimplify Helm 仓库的 GitHub 仓库](https://github.com/stacksimplify/helm-charts)
- [artifacthub.io](https://artifacthub.io)：搜索 `stacksimplify`
- [来自 artifacthub.io 的 mychart1](https://artifacthub.io/packages/helm/stacksimplify/mychart1)

### 步骤-02-02：添加自定义 Helm 仓库

```bash
# List Helm Repositories
helm repo list

# Add Helm Repository
helm repo add <DESIRED-NAME> <HELM-REPO-URL>
helm repo add stacksimplify https://stacksimplify.github.io/helm-charts/

# List Helm Repositories
helm repo list

# Search Helm Repository
helm search repo <KEY-WORD>
helm search repo mychart1
```

## 步骤-03：从我们的自定义 Helm 仓库安装 Helm Chart

```bash
# Install myapp1 Helm Chart
helm install <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name>
helm install myapp1 stacksimplify/mychart1 
```

## 步骤-04：列出资源并在浏览器中访问应用程序

```bash
# List Helm Release
helm ls 
or 
helm list

# List Pods
kubectl get pods

# List Services 
kubectl get svc

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
```

## 步骤-04：Helm 升级

- [带有 1.0.0、2.0.0、3.0.0、4.0.0 版本的 kubenginx Docker 镜像](https://github.com/users/stacksimplify/packages/container/package/kubenginx)

```bash
# Review the Docker Image Versions we are using
https://github.com/users/stacksimplify/packages/container/package/kubenginx
Image Tags: 1.0.0, 2.0.0, 3.0.0, 4.0.0

# Helm Upgrade
helm upgrade <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name> --set <OVERRIDE-VALUE-FROM-values.yaml>
helm upgrade myapp1 stacksimplify/mychart1 --set "image.tag=2.0.0"
```

## 步骤-05：helm 升级后列出资源

```bash
# List Helm Releases
helm list 
Observation: We should see Revision as 2

# Additional List commands
helm list --superseded
helm list --deployed

# List and Describe Pod
kubectl get pods
kubectl describe pod <POD-NAME> 
Observation: In the Pod Events you should find that "ghcr.io/stacksimplify/kubenginx:2.0.0" is pulled or if already exists on desktop it will be used to create this new pod

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
Observation: Version 2 of application should be displayed
```

## Step-06: Do two more helm upgrades - For practice purpose

```bash
# Helm Upgrade to 3.0.0
helm upgrade myapp1 kalyan-repo/myapp1 --set "image.tag=3.0.0"

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231

# Helm Upgrade to 4.0.0
helm upgrade myapp1 kalyan-repo/myapp1 --set "image.tag=4.0.0"

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231
```

## Step-07: Helm History

- History prints historical revisions for a given release.

```bash
# helm history
helm history RELEASE_NAME
helm history myapp1
```

## Step-08: Helm Status

- This command shows the status of a named release.

```bash
# Helm Status
helm status RELEASE_NAME
helm status myapp1

# Helm Status - Show Description (display the description message of the named release)
helm status myapp1 --show-desc    

# Helm Status - Show Resources (display the resources of the named release)
helm status myapp1  --show-resources   

# Helm Status - revision (display the status of the named release with revision)
helm status RELEASE_NAME --revision int
helm status myapp1 --revision 2
```

## Step-09: Uninstall Helm Release

```bash
# Uninstall Helm Release
helm uninstall myapp1
```
