# 使用生成名称标志的 Helm 安装

## 步骤-01：介绍

- `helm install` 的 `--generate-name` 标志是一个非常重要的选项
- 从 `helm install` 的角度来看，这是一个很好的选项
- 当我们实施 DevOps 流水线时，如果我们想要生成发布的名称而不抛出重复发布错误，我们可以使用此设置。

## 步骤-02：使用 --generate-name 标志安装 helm

```bash
# Install helm with --generate-name flag
helm install <repo_name_in_your_local_desktop/chart_name> --generate-name
helm install stacksimplify/mychart1 --generate-name

# List Helm Releases
helm list
helm list --output=yaml
Observation:
We can see the name as "name: mychart1-1689683948" some auto-generated number

# Helm Status
helm status mychart1-1689683948 
helm status mychart1-1689683948 --show-resources

# Access Application
http://localhost:31231
```

## 步骤-03：卸载 Helm 发布

```bash
# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall mychart1-1689683948
```
