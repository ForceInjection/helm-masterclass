# 使用 Chart 版本进行 Helm 升级

## 步骤-01：介绍

- 我们将学习 `helm search repo` 命令的一些附加标志
- 我们将使用 Chart 版本安装和升级 Helm 发布
- 此外，我们还将学习 Helm 回滚
- helm install
- helm search repo
- helm status
- helm upgrade
- helm rollback
- helm history

## 步骤-02：在 Helm 仓库中搜索 mychart2

- [在 Github 仓库中查看 mychart2](https://github.com/stacksimplify/helm-charts/tree/main)
- mychart2 有 4 个 chart 版本（0.1.0、0.2.0、0.3.0、0.4.0）
- mychart2 Chart 版本 -> 应用版本
- 0.1.0 -> 1.0.0
- 0.2.0 -> 2.0.0
- 0.3.0 -> 3.0.0
- 0.4.0 -> 4.0.0
- [查看 Artifacthub.io](https://artifacthub.io/packages/helm/stacksimplify/mychart2/)

```bash
# Search Helm Repo
helm search repo mychart2
Observation: Should display latest version of mychart2 from stacksimplify helm repo

# Search Helm Repo with --versions
helm search repo mychart2 --versions
Observation: Should display all versions of mychart2

# Search Helm Repo with --version
helm search repo mychart2 --version "CHART-VERSIONS"
helm search repo mychart2 --version "0.2.0"
Observation: Should display specified version of helm chart 
```

## 步骤-03：通过指定 Chart 版本安装 Helm Chart

```bash
# Install Helm Chart by specifying Chart Version
helm install myapp101 stacksimplify/mychart2 --version "CHART-VERSION"
helm install myapp101 stacksimplify/mychart2 --version "0.1.0"

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232

# View Pod logs
kubectl get pods
kubectl logs -f POD-NAME
```

## 步骤-04：使用 Chart 版本进行 Helm 升级

```bash
# Helm Upgrade using Chart Version
helm upgrade myapp101 stacksimplify/mychart2 --version "0.2.0"

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232

# List Release History
helm history myapp101
```

## 步骤-05：不指定 Chart 版本的 Helm 升级

```bash
# Helm Upgrade using Chart Version
helm upgrade myapp101 stacksimplify/mychart2

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should take the latest release which is Appversion 4.0.0, Chart Version 0.4.0 (Which is default or latest Chart version)

# List Release History
helm history myapp101
```

## 步骤-06：Helm 回滚

- 将发布回滚到先前的修订版本或特定修订版本

```bash
# Rollback to previous version
helm rollback RELEASE-NAME 
helm rollback myapp101

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should see V2 version of Application (Chart Version 0.2.0, AppVersion 2.0.0)

# List Release History
helm history myapp101
```

## Step-07: Helm Rollback to specific Revision

- Roll back a release to a previous revision or a specific revision

```bash
# Rollback to previous version
helm rollback RELEASE-NAME REVISION
helm rollback myapp101 1

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application
http://localhost:31232
Observation: Should see V1 version of Application (Chart Version 0.1.0, AppVersion 1.0.0)

# List Release History
helm history myapp101
```
