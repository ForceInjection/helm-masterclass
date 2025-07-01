# 在 GitHub 上托管 Helm 仓库

## 步骤-01：介绍

- 在 GitHub 上托管 Helm 仓库

## 步骤-02：创建 GitHub 仓库

- **仓库名称：** helm-charts-repo
- **描述：** GitHub 上的 Helm Charts 仓库
- **仓库类型：** 公开
- **初始化此仓库：** 勾选添加 README 文件
- 点击 **创建仓库**

## 步骤-03：创建 gh-pages 分支

- **新分支名称：** gh-pages
- **源：** main
- 点击 **创建新分支**

## 步骤-04：为 gh-pages 分支启用 GitHub Pages（如果默认未启用）

- 转到仓库：helm-charts-repo -> Settings -> Code and Automation -> Pages
- 查看 **Branch**
- 同时访问 GitHub pages 站点
- <https://stacksimplify.github.io/helm-charts-repo/>

## 步骤-05：将 GitHub 仓库克隆到本地桌面

```bash
# 克隆 GitHub 仓库
git clone git@github.com:stacksimplify/helm-charts-repo.git
```

## 步骤-06：查看并复制 GitRepo 文件

```bash
# 更改目录
cd 44-Helm-Repo-on-GitHub

# 将内容从 gitrepo-content 复制到 helm-charts-repo
1. .github 文件夹：包含 GitHub Actions release.yaml
2. charts 文件夹：包含 "myfirstchart" helm chart
```

## 步骤-07：创建 Chart 发布版本 0.1.0

### 步骤-07-01：验证 Chart.yaml

- 确保我们有 `appVersion: "0.1.0"` 和 `version: 0.1.0`

```yaml
apiVersion: v2
appVersion: "0.1.0"
description: Signed Charts
name: myfirstchart
type: application
version: 0.1.0
```

### 步骤-07-02：将代码提交到远程 GitHub 仓库

```bash
# 提交代码
git add .
git commit -am "0.1.0 commit"
git push
```

### 步骤-07-03：验证 GitHub 仓库中的 Actions

- 转到 helm-charts-repo -> Actions
- 查看工作流运行
  - 0.1.0 commit
  - pages build and deployment

### 步骤-07-04：切换到 gh-pages 分支并验证 index.yaml

- 切换到 `gh-pages` 并查看 `index.yaml`
- <https://github.com/stacksimplify/helm-charts-repo/blob/gh-pages/index.yaml>

### 步骤-07-05：验证发布版本和标签

- 转到 **Releases** 并验证
- 转到 **Tags** 并验证

## 步骤-08：创建 Chart 发布版本 0.2.0

### 步骤-08-01：更新 Chart.yaml 版本

```bash
# 更新 Chart.yaml
version: 0.2.0
appVersion: "0.2.0"
```

### 步骤-08-02：将代码提交到远程 GitHub 仓库

```bash
# 提交代码
git add .
git commit -am "0.2.0 commit"
git push
```

### 步骤-08-03：验证 GitHub 仓库中的 Actions

- 转到 helm-charts-repo -> Actions
- 查看工作流运行
  - 0.1.0 commit
  - pages build and deployment

### 步骤-08-04：切换到 gh-pages 分支并验证 index.yaml

- 切换到 `gh-pages` 并查看 `index.yaml`
- <https://github.com/stacksimplify/helm-charts-repo/blob/gh-pages/index.yaml>

### 步骤-08-05：验证发布版本和标签

- 转到 **Releases** 并验证
- 转到 **Tags** 并验证

## 步骤-09：在本地桌面添加 GitHub Helm 仓库并搜索仓库

```bash
# Helm 仓库 URL
https://stacksimplify.github.io/helm-charts-repo/

# 列出 Helm 仓库
helm repo list

# 添加 Helm 仓库
helm repo add mygithelmrepo https://stacksimplify.github.io/helm-charts-repo/

# 列出 Helm 仓库
helm repo list

# Helm 搜索仓库
helm search repo mygithelmrepo/myfirstchart

# 使用 --versions 搜索 Helm 仓库
helm search repo mygithelmrepo/myfirstchart --versions
```

## 步骤-10：从 GitHub Helm 仓库部署和验证

```bash
# Helm 安装
helm install myapp1 mygithelmrepo/myfirstchart --atomic

# Helm 状态
helm status myapp1 --show-resources

# 访问应用程序
http://localhost:31239

# Helm 卸载
helm uninstall myapp1
```
