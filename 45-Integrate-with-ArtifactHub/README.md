# 与 ArtifactHub 集成

## 步骤-01：介绍

- 在 Artifact Hub 上列出我们的公共 Helm 仓库

## 步骤-02：什么是 Artifact Hub

- 导航到 [Artifact Hub](https://artifacthub.io)

## 步骤-03：注册 Artifact Hub

- 您可以使用 GitHub 账户注册 Artifact Hub

## 步骤-04：在 Artifact Hub 中添加仓库

- Artifact Hub -> User -> Control Panel -> Add
- **Kind:** Helm Charts
- **Name:** stacksimplify-helm-charts-repo
- **Display Name:** StackSimplify Helm Charts Repo
- **URL:** <https://stacksimplify.github.io/helm-charts-repo/>
- **Security Scanner Disabled:** 保留默认设置
- 点击 **ADD**

## 步骤-05：在 helm-charts-repo Git 仓库中创建 artifacthub-repo.yml 文件

- **artifacthub-repo.yml**

```yaml
# Artifact Hub 仓库元数据文件
#
# 某些设置（如已验证发布者标志或被忽略的包）在下次处理仓库之前不会生效。
# 请记住，如果仓库自上次处理以来没有更改，则不会被处理。
# 根据仓库类型，检查方式有所不同。对于基于 HTTP 的 Helm 仓库，
# 我们认为如果 `index.yaml` 文件发生更改，则仓库已更改。
# 对于基于 git 的仓库，当您设置的分支中最后一次提交的哈希值发生更改时，
# 仓库就会被认为已更改。这不适用于所有权声明操作，这些操作会立即处理。
#
#repositoryID: 包将发布到的 Artifact Hub 仓库的 ID（可选，但它启用已验证发布者）
repositoryID: < Artifact Hub 仓库的 ID>
owners: # （可选，用于声明仓库所有权）
  - name: Kalyan Reddy Daida
    email: stacksimplify@gmail.com
ignore: # （可选，不应被 Artifact Hub 索引的包）
  - name: package1
  - name: package2 # 精确匹配
    version: beta # 正则表达式（省略时，所有版本都被忽略）
```

- 将此文件添加到 Git 仓库 helm-charts-repo

```bash
# 将新文件提交到 Git 仓库：helm-charts-repo
git add .
git commit -am "artifacthub-repo.yml added"
git push
```

## 步骤-06：在 Artifact Hub 中，等待下次检查

- 转到 Artifact Hub -> Control Panel

## 步骤-07：从 Artifact Hub 搜索我们的 Helm Charts

- 从 Artifact Hub 搜索我们的 Helm Charts

## 步骤-08：我们可以在组织中私有托管 Artifact Hub

- 参考链接 [在您的组织中托管 Artifact Hub](https://artifacthub.io/packages/helm/artifact-hub/artifact-hub)

## 步骤-09：搜索 Artifact Hub

- [helm search hub](https://helm.sh/docs/helm/helm_search_hub/)

```bash
# Helm 搜索 hub
helm search hub myfirstchart
```
