# Helm 插件 - 构建

## 步骤-01: 介绍

- [构建 Helm 插件](https://helm.sh/docs/topics/plugins/#building-plugins)
- 我们将构建 3 个简单的插件并测试

## 步骤-02: 使用 env 命令创建 myplugin1 - 安装和验证

```bash
# myplugin1
name: "myplugin1"
version: "0.1.0"
usage: "打印 Helm 环境变量"
description: |-
  打印 Helm 环境变量
command: "env"

# 列出 Helm 插件
helm plugin list

# 安装 Helm 插件
helm plugin install myplugin1/

# 列出 Helm 插件
helm plugin list

# 运行 Helm 插件
helm <PLUGIN-NAME>
helm myplugin1

# 观察结果
打印 Helm 环境变量
```

## 步骤-03: 使用 platformCommand 创建 myplugin2 - 安装和验证

```bash
# myplugin2
name: "myplugin2"
version: "0.1.0"
usage: "helm myplugin2"
description: "打印 Helm 插件目录"
command: echo my helm plugin directory is $HELM_PLUGINS default command
platformCommand:
  - os: linux
    arch: i386
    command: "echo my helm plugin directory is $HELM_PLUGINS os is linux i386"
  - os: linux
    arch: amd64
    command: "echo my helm plugin directory is $HELM_PLUGINS os is linux amd64"
  - os: windows
    arch: amd64
    command: "echo my helm plugin directory is $HELM_PLUGINS os is windows amd64"

# 列出 Helm 插件
helm plugin list

# 安装 Helm 插件
helm plugin install myplugin2/

# 列出 Helm 插件
helm plugin list

# 运行 Helm 插件
helm <PLUGIN-NAME>
helm myplugin2

# 观察结果
应该执行默认命令部分的命令，因为我们在 MacOS 桌面上运行，而它不在 "platformCommand" 中
```

## 步骤-04: 使用 shell 脚本创建 myplugin3 - 安装和验证

```bash
# myplugin3
name: "myplugin3"
version: "0.1.0"
usage: "helm myplugin3"
description: "使用脚本 app.sh 打印 Helm 插件目录"
command: "$HELM_PLUGIN_DIR/app.sh"

# app.sh
#!/bin/sh
echo "my helm plugin directory is $HELM_PLUGINS from SHELL SCRIPT"

# 列出 Helm 插件
helm plugin list

# 安装 Helm 插件
helm plugin install myplugin3/

# 列出 Helm 插件
helm plugin list

# 运行 Helm 插件
helm <PLUGIN-NAME>
helm myplugin3

# 观察结果
我们将看到 "app.sh" 成功执行
```

## 步骤-05: 卸载插件

```bash
# 卸载 Helm 插件
helm plugin uninstall <PLUGIN-NAME>
helm plugin uninstall myplugin1
helm plugin uninstall myplugin2
helm plugin uninstall myplugin3
```
