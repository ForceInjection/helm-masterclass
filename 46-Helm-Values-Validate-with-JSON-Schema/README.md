# Helm Values - 使用 JSON Schema 验证

## 步骤-01：介绍

- Helm Values - 使用 JSON Schema 验证

## 步骤-02：查看 helmbasics Helm Chart

- 简单的 Helm Chart
- deployment.yaml
- 核心重点将是学习 `values.schema.json`

## 步骤-03：将 values.yaml 转换为 json

- [使用网站 json2yaml](https://www.json2yaml.com/)

## 步骤-04：将 Json 转换为 Json Schema

- [使用网站](https://transform.tools/json-to-json-schema)

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Generated schema for Root",
  "type": "object",
  "properties": {
    "replicaCount": {
      "type": "number"
    },
    "image": {
      "type": "object",
      "properties": {
        "repository": {
          "type": "string"
        },
        "pullPolicy": {
          "type": "string"
        },
        "tag": {
          "type": "string"
        }
      },
      "required": [
        "repository",
        "pullPolicy",
        "tag"
      ]
    }
  },
  "required": [
    "replicaCount",
    "image"
  ]
}
```

## 步骤-05：在 Helm Chart 根目录创建 values.schema.json 文件

- 创建文件 `values.schema.json`
- 从上一步复制 JSON 内容

## 步骤-06：为 pullPolicy 添加模式

```json
        "pullPolicy": {
          "type": "string",
          "pattern": "^(Always|Never|IfNotPresent)$"
        },
```

## 步骤-07：验证 values.schema.json

```bash
# 切换到 Chart 目录
cd helmbasics

# Helm lint
helm lint .

# 必需测试：传递空值并验证
helm template myapp1 . --set replicaCount=""

# 整数测试：将 replicaCount 提供为字符串
helm template myapp1 . --set replicaCount=kalyan

# 约束验证测试：提供无效值而不是允许的值（允许的值：Always、Never、IfNotPresent）
helm template myapp1 . --set image.pullPolicy=kalyan
```
