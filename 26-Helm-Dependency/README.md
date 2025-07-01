# Helm 子图表 - 依赖命令

## 步骤-01: 介绍

- 创建父图表
- helm dependency list
- helm dependency update
- helm dependency build
- helm dependency 版本约束
- helm dependency 仓库 @REPO vs REPO-URL

## 步骤-02: 创建父图表

```bash
# 创建父图表
helm create parentchart
```

## 步骤-03: 在父图表 Chart.yaml 中更新 Helm 依赖

```yaml
apiVersion: v2
name: parentchart
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart1
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
- name: mysql
  version: "9.9.0"
  repository: "https://charts.bitnami.com/bitnami"
```

## 步骤-04: Helm 依赖命令 - 列表和更新

- **helm dependency list:** 列出图表中声明的所有依赖项。
- **helm dependency update:** 基于 `Chart.yaml` 文件的内容更新父图表的 `charts/` 文件夹

```bash
# Helm 依赖列表
helm dependency list
观察结果: 
您应该看到状态为 "missing"，因为我们还没有执行 helm dependency update

# 验证 parentchart 中的 Charts 文件夹
ls parentchart/charts
观察结果: 应该是空的。依赖子图表尚未下载

# Helm 依赖更新
helm dependency update CHART-NAME
helm dependency update parentchart/
ls parentchart/charts
观察结果: 
1. 我们应该看到所有图表 (mychart1-0.1.0.tgz, mychart2-0.4.0.tgz, mysql-9.9.0.tgz) 下载到 "parentchart/charts" 文件夹
2. 我们应该在 "parentchart" 文件夹中看到 "Chart.lock" 文件

# 查看 Chart.lock 文件
cat parentchart/Chart.lock 

# Helm 依赖列表
helm dependency list parentchart/
观察结果: 应该看到状态为 "OK"
```

## 步骤-05: Helm 依赖图表版本范围

- 更新父图表 `Chart.yaml`

### 步骤-05-01: Helm 图表版本表示法

```bash
Helm 图表版本表示法: Major.Minor.Patch 
MySQL Helm 图表版本: 9.10.8
Major: 9
Minor: 10
Patch: 8
```

### 步骤-05-02: 基本比较运算符

- 我们可以使用基本比较运算符定义版本约束
- 在可能的情况下，使用版本范围而不是固定到确切版本。

```bash
# 基本比较运算符
version: "= 9.10.8" 
version: "!= 9.10.8" 
version: ">= 9.10.8"
version: "<= 9.10.8"
version: "> 9.10.8"   
version: "< 9.10.8"
version: ">= 9.10.8 < 9.11.0"  
```

### 步骤-05-03: 主版本范围比较: 插入符号(ˆ)

- `x` 是占位符
- 插入符号 (^) 运算符用于在稳定版本 (1.0.0) 发布后的主版本级别更改。

```bash
# 主版本范围比较: 插入符号(ˆ)
^9.10.1  等同于 >= 9.10.1, < 10.0.0
^9.10.x  等同于 >= 9.10.0, < 10.0.0   
^9.10    等同于 >= 9.10, < 10
^9.x     等同于 >= 9.0.0, < 10        
^0       等同于 >= 0.0.0, < 1.0.0
```

### 步骤-05-05: 次版本范围比较: 波浪号符号(~)

- `x` 是占位符
- 波浪号 (~) 运算符用于
  - 当指定次版本时的补丁级别范围
  - 当缺少次版本号时的主版本级别更改。
- 建议的默认值是使用补丁级别版本匹配，这是下表中的第一个

```bash
# 次版本范围比较: 波浪号符号(~)
~9.10.1  等同于 >= 9.10.1, < 9.11.0 # 补丁级别版本匹配
~9.10    等同于 >= 9.10, < 9.11
~9       等同于 >= 9, < 10
^9.x     等同于 >= 9.0.0, < 10        
^0       等同于 >= 0.0.0, < 1.0.0
```

### 步骤-05-06: 通过一些示例进行验证

```yaml
dependencies:
- name: mysql
  version:" "9.10.9"
  #version: ">=9.10.1" # 应该下载当天可用的最新版本
  #version: "<=9.10.6" # 应该下载 9.10.6 mysql helm chart 包
  #version: "~9.9.0" # 应该从 9.9.x (补丁版本) 下载最新版本
  #version: "~9.9" # 应该从 9.9 下载最新版本
  #version: "~9" # 应该从 9.x 下载最新版本
  repository: "https://charts.bitnami.com/bitnami"


# helm dependency update
helm dependency update
或
helm dep update  
```

## 步骤-06: Helm 依赖构建命令

- **helm dependency build:** 基于 `Chart.lock` 文件重建 `charts/` 目录
- 简而言之，`dep update` 命令将与 `Chart.yaml` 中定义的版本约束协商，而 `dep build` 将尝试构建、下载或更新 `Chart.lock` 文件中预设的任何版本
- 如果找不到锁定文件，`helm dependency build` 将镜像 `helm dependency update` 的行为。

```bash
# helm dependency build
helm dependency build CHART-NAME
helm dependency build parentchart
```

## 步骤-07: Helm 依赖仓库 @REPO vs REPO-URL

- 当我们在跨环境的 DevOps 流水线中使用 Helm 时，不推荐使用 "@REPO" 方法
- 始终推荐使用 REPO-URL 方法 (repository: "<https://charts.bitnami.com/bitnami>")

```bash
# 使用仓库 URL (推荐方法)
dependencies:
- name: mysql
  version: ">=9.10.8"
  repository: "https://charts.bitnami.com/bitnami"

# 列出 Helm 仓库
helm repo list
helm search repo bitnami/mysql --versions

# 使用 @REPO (本地仓库引用 - 不推荐)
dependencies:
- name: mysql
  version: ">=9.10.8"
  repository: "@bitnami"

# 清理 Charts 文件夹和 Chart.lock
rm parentchart/charts/*
rm parentchart/Chart.lock

# 确保我们使用 repository: "@bitnami"
helm dependency update
ls parentchart/charts/
cat parentchart/Chart.lock
观察结果: 应该按预期工作
```
