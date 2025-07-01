# Helm 启动器

## 步骤-01: 介绍

- 我们将学习 Helm 启动器图表
- 创建/构建启动器图表
- 使用启动器图表构建基础图表

## 步骤-02: Helm 启动器图表

### 什么是 Helm 启动器图表？

1. 启动器图表与常规 Helm 图表相同
2. 启动器图表是可重用的模板，帮助我们构建新图表。
3. 如果您的组织已经有启动器图表，新开发人员就不需要从头开始。他们可以使用它们并在其基础上构建。
4. 我们还可以强制要求在创建的图表中提供某些资源。

### 我们在哪里放置启动器图表？

1. 我们需要将启动器图表放在 "$HELM_DATA_HOME/starters" 文件夹中

### 有什么缺点吗？

1. `Chart.yaml` 将被生成器覆盖。
2. 因此，我们无法从启动器图表模板中获取版本或依赖图表。

## 步骤-03: 创建简单的 Helm 图表并修改为启动器图表

- 这一步对您来说是完全可选的。
- 您将有 `mystarterchart` 文件夹准备好，可以继续演示中的下一步。

```bash
# Helm 创建
helm create mystarterchart

# 重要说明
1. 当我们使用 "helm create" 创建默认 helm 图表时，我们将把它修改为只有 "deployment.yaml" 和 "service.yaml" 的非常简单的图表

# Templates 文件夹中的更改
1. 删除 "tests" 文件夹
2. 删除 hpa.yaml、ingress.yaml 和 serviceaccount.yaml
3. 更新 "_helpers.tpl" 以删除 "serviceAccountName" 模板
4. 在 values.yaml 中，删除 serviceaccount、ingress 和 autoscaling 值
5. 在 values.yaml 中，将服务更新为 NodePort，端口为 31239
6. 在 values.yaml 中，将 repository 值更新为 "ghcr.io/stacksimplify/kubenginxhelm"
7. 在 deployment.yaml 中，删除 autoscaling 和 serviceaccount 引用
8. 在 service.yaml 中，添加 nodeport 参数，从 values.yaml 中获取端口 31239
9. 在 Chart.yaml 中，只需将 appversion 和 version 更改为 1.0.0。当我们使用启动器图表创建图表时，这将被覆盖，但只是想比较和测试它。
10. 在 Chart.yaml 中，更新 dependencies 部分。当我们使用启动器图表创建图表时，这将被覆盖，但只是想比较和测试它。
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
11. 子图表：下载并解压 Helm 图表到 "charts" 目录。我们将观察当我们从启动器图表创建图表时 "charts" 目录会发生什么
helm pull https://stacksimplify.github.io/helm-charts/mychart4-0.1.0.tgz --untar
12. 更新 NOTES.txt：删除 Ingress 的 if 语句
```

## 步骤-04: 在完全转换为启动器图表之前测试图表

```bash
# 切换目录
cd mystarterchart

# Helm Lint
helm lint 
URL: https://helm.sh/docs/helm/helm_lint/
1. 检查图表可能存在的问题
2. 此命令获取图表路径并运行一系列测试来验证图表格式是否正确。
3. 如果 linter 遇到会导致图表安装失败的问题，它将发出 [ERROR] 消息。
4. 如果遇到违反约定或建议的问题，它将发出 [WARNING] 消息。

# 安装 Helm Release
helm install myapp1 . --atomic

# 列出 Pods 和 Services
kubectl get pods
kubectl get svc

# 访问应用程序
父图表: http://localhost:31239
mychart4 图表: http://localhost:<port-from-get-svc-output>

# 卸载 Helm Release
helm uninstall myapp1
```

## 步骤-05: 在所有文件中将 "mystarterchart" 替换为 `<CHARTNAME>`

**重要说明：** 所有出现的 `<CHARTNAME>` 都将被替换为指定的图表名称，以便启动器图表可以用作模板。

1. _helpers.tpl
2. deployment.yaml
3. service.yaml
4. NOTES.txt
5. Chart.yaml
6. values.yaml (这里只是在注释中)

## 步骤-06: 将 mystarterchart 复制到 HELM_DATA_HOME/starters

```bash
# Helm env 命令
helm env

# Helm env HELM_DATA_HOME
helm env HELM_DATA_HOME
HELM_DATA_HOME="/Users/kalyan/Library/helm"

# 创建文件夹 helm 和 helm/starters
cd /Users/kalyan/Library/
mkdir helm
cd helm
mkdir starters

# 复制 mystarterchart 文件夹
cp -r mystarterchart /Users/kalyan/Library/helm/starters/
```

## 步骤-07: 使用启动器图表创建新图表

- [Docker 镜像: kubenginxhelm](https://github.com/users/stacksimplify/packages/container/package/kubenginxhelm)

```bash
# 切换目录
cd MYCHARTS

# 使用启动器图表创建 Helm
helm create mychart9 --starter=mystarterchart

# 查看 mychart9 文件
1. Chart.yaml
- 它应该被重新生成，版本和 appversion 都应该被覆盖为 0.1.0
- 将 appVersion 更新为 "0.3.0"，带引号（它应该是带引号的字符串）
- 我们的 Docker 镜像版本也是 "0.3.0"，与我们的图表 appVersion 匹配，所以我们很好。
- https://github.com/users/stacksimplify/packages/container/package/kubenginxhelm
2. deployment.yaml - 查看它
3. service.yaml - 查看它
4. values.yaml - 查看它
5. NOTES.txt - 查看它
6. "charts" 目录：我们应该看到 "mychart4" 应该作为打包文件 "mychart4-0.1.0.tgz" 存在，即使在我们的启动器图表中它是解压的
```

## 步骤-08: 从使用启动器图表创建的新图表创建 Helm Release

```bash
# 切换目录
cd MYCHARTS/mychart9

# Helm Lint
helm lint 

# 安装 Helm Release
helm install myapp901 .

# 列出 Pods 和 Services
kubectl get pods
kubectl get svc

# 访问应用程序
父图表: http://localhost:31239
mychart4 图表: http://localhost:<port-from-get-svc-output>

# 卸载 Helm Release
helm uninstall myapp901
```
