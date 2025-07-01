# Helm 子 Chart - 在子 Chart 中使用全局值

## 步骤-01: 介绍

- 手动管理依赖
- 定义全局值

## 步骤-02: 查看 Chart.yaml

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "file://charts/mychart4"
  alias: childchart4
  tags: 
    - frontend
- name: mychart2
  version: "0.4.0"
  repository: "file://charts/mychart2"
  alias: childchart2
  tags: 
    - backend
```

## 步骤-03: 使用 helm pull 命令拉取 chart

- 我们将使用 `helm pull` 命令将 chart 拉取到 `parentchart/charts` 目录
- 同时确保这些包被解压或解压缩

```bash
# 切换目录
cd parentchart/charts

# Helm 拉取 MyChart4
helm pull https://stacksimplify.github.io/helm-charts/mychart4-0.1.0.tgz --untar

# Helm 拉取 MyChart2
helm pull https://stacksimplify.github.io/helm-charts/mychart2-0.4.0.tgz --untar

# 删除包文件 .tgz 文件
rm -rf *.tgz
```

## 步骤-04: 构建或打包子 Chart

```bash
# 切换到 Chart 目录
cd parentchart

# Helm 依赖列表
helm dependency list

## 示例输出
Kalyans-MacBook-Pro:parentchart kalyan$ helm dependency list
NAME     VERSION REPOSITORY              STATUS  
mychart4 0.1.0   file://charts/mychart4  unpacked
mychart2 0.4.0   file://charts/mychart2 unpacked

# Helm 依赖更新/构建
helm dependency update

# 查看 charts 文件夹
ls charts/
观察结果: 你应该找到两个 chart 的 *.tgz 文件

> # helm dep list 应该显示状态为 OK
Kalyans-MacBook-Pro:parentchart kalyan$ helm dep list
NAME     VERSION REPOSITORY             STATUS
mychart4 0.1.0   file://charts/mychart4 ok    
mychart2 0.4.0   file://charts/mychart2 ok  


# 删除子 chart tgz 文件
rm charts/*.tgz
```

## 步骤-05: 在父 Chart values.yaml 中定义全局值

- **文件:** parentchart/values.yaml

```yaml
# 定义全局值
global:
  replicaCount: 4
```

## 步骤-06: 更新父 Chart 和子 Chart 的 deployment.yaml

- **文件:** parentchart/templates/deployment.yaml
- **文件:** charts/mychart4/templates/deployment.yaml
- **文件:** charts/mychart2/templates/deployment.yaml

```yaml
replicas: {{ .Values.global.replicaCount }}
```

## 步骤-07: 测试全局值

```bash
# 切换到 Chart 目录
cd parentchart

# Helm 安装
helm install myapp1 . --atomic

# 验证所有 3 个 chart 的 Pod
kubectl get pods
观察结果: 
基于 ".Values.global.replicaCount=4"，所有 3 个 chart 都应该启动 4 个 pod

# helm 卸载
helm uninstall myapp1
```
