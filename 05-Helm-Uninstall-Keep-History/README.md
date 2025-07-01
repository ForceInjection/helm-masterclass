# Helm 卸载保留历史

## 步骤-01：介绍

- 我们将学习以最有效的方式（最佳实践）卸载 Helm 发布，这样我们就不会丢失 Helm 发布的历史记录
- **重要提示：** 此演示是前一个发布演示的延续

## 步骤-02：使用 --keep-history 标志卸载 Helm 发布

```bash
# List Helm Releases
helm list
helm list --superseded
helm list --deployed

# List Release History
helm history myapp101

# Uninstall Helm Release with --keep-history Flag
helm uninstall <RELEASE-NAME> --keep-history
helm uninstall myapp101 --keep-history

# List Helm Releases which are uninstalled
helm list --uninstalled
Observation:
We should see uninstalled release

# helm status command
helm status myapp101
Observation:
1. works only when we use --keep-history flag
2. We can see all the details of release with "Status: Uninstalled"
```

## 步骤-03：回滚已卸载的发布

```bash
# List Release History
helm history myapp101

# Rollback Helm Uninstalled Release
helm rollback <RELEASE> [REVISION] [flags]
helm rollback myapp101 3
Observation: It should rollback to specific revision number from revision history

# List Helm Releases
helm list

# List Kubernetes Resources
kubectl get pods
kubectl get svc

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Access Application 
http://localhost:31232
```

## 步骤-04：卸载 Helm 发布 - 无标志

```bash
# List Helm Releases
helm list

# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall myapp101

# List Helm Releases which are uninstalled
helm list --uninstalled
Observation:
We should not see uninstalled release, this command will completely remove the release and its all references

# helm status command
helm status myapp101
Observation:
As the release is permanently removed, we dont get an error "Error: release: not found"

# List Helm History
helm history myapp101
```

## 步骤-05：回滚已卸载的发布

```bash
# Rollback Helm Uninstalled Release
helm rollback <RELEASE> [REVISION] [flags]
helm rollback myapp101 1 
Observation: 
Should throw error "Error: release: not found"
```

## 步骤-06：Helm 卸载的最佳实践

- 建议始终使用 `--keep-history 标志`，原因如下：
- 跟踪已卸载的发布
- 如果需要该发布，可以快速回滚
