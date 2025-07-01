# Helm 安装原子标志

## 步骤-01：介绍

- 我们将学习在安装 Helm 发布时使用 `--atomic` 标志，并以实际方式理解使用它的重要性

## 步骤-02：安装 Helm Chart - 发布：dev101

```bash
# Install Helm Chart 
helm install dev101 stacksimplify/mychart1

# List Helm Release
helm list 

# List Kubernetes Resources Deployed as part of this Helm Release
helm status dev101 --show-resources

# Access Application
http://localhost:31231
```

## 步骤-03：安装 Helm Chart - 发布：qa101

```bash
# Install Helm Chart 
helm install qa101 stacksimplify/mychart1

# List Helm Release
helm list 
Observation: You should see qa101 release installed with FAILED status

Error: INSTALLATION FAILED: 1 error occurred:
 * Service "qa101-mychart1" is invalid: spec.ports[0].nodePort: Invalid value: 31231: provided port is already allocated

# Uninstall qa101 release which is in failed state
helm uninstall qa101

# List Helm Release
helm list 
```

## 步骤-04：使用 --atomic 标志安装 Helm Chart - 发布：qa101

- 当设置 `--atomic` 标志时，安装过程在失败时会删除安装。
- 如果使用 `--atomic`，`--wait` 标志将自动设置
- `--wait` 将等待所有 Pod、PVC、Service 以及 Deployment、StatefulSet 或 ReplicaSet 的最小 Pod 数量处于就绪状态，然后才将发布标记为成功。它将等待 `--timeout` 指定的时间
- `--timeout` 等待任何单个 Kubernetes 操作（如钩子的 Job）的时间（默认 5m0s）

```bash
# Install Helm Chart 
helm install qa101 stacksimplify/mychart1 --atomic

# List Helm Release
helm list 
Observation: We will not see qa101 FAILED release, --atomic flag deleted the release as soon as it is failed with error

Error: INSTALLATION FAILED: 1 error occurred:
 * Service "qa101-mychart1" is invalid: spec.ports[0].nodePort: Invalid value: 31231: provided port is already allocated
```

## 步骤-05：卸载 dev101 发布

```bash
# Uninstall dev101 release
helm uninstall dev101

# List Helm Releases
helm list
```
