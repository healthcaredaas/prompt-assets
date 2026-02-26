# 设计阶段资产

本目录包含设计阶段的 Prompt 资产，帮助设计师和架构师进行系统设计。

## 目录结构

```
02-design/
├── README.md              # 本文件
├── templates/             # 模板文件
│   ├── ui-design-spec.md  # UI设计规范
│   ├── api-design-spec.md # API设计规范
│   └── db-design-spec.md  # 数据库设计规范
└── prompts/               # Prompt文件
    ├── ui-component-design.md   # UI组件设计
    ├── api-interface-design.md  # API接口设计
    ├── database-modeling.md     # 数据库建模
    └── architecture-review.md   # 架构评审
```

## 使用场景

### 1. UI组件设计
使用 `prompts/ui-component-design.md` 设计UI组件。

**输入**: 组件需求描述
**输出**: 组件设计文档、代码示例

### 2. API接口设计
使用 `prompts/api-interface-design.md` 设计RESTful API。

**输入**: 业务需求、数据模型
**输出**: API接口文档

### 3. 数据库建模
使用 `prompts/database-modeling.md` 设计数据库结构。

**输入**: 业务实体、数据需求
**输出**: 数据库设计文档、建表SQL

### 4. 架构评审
使用 `prompts/architecture-review.md` 进行架构评审。

**输入**: 架构设计文档
**输出**: 架构评审报告

## 模板说明

### UI设计规范 (templates/ui-design-spec.md)
前端UI设计规范，包含：
- 设计原则
- 颜色规范
- 字体规范
- 间距规范
- 组件规范

### API设计规范 (templates/api-design-spec.md)
RESTful API设计规范，包含：
- URL设计规范
- 请求响应格式
- 错误码规范
- 版本控制规范

### 数据库设计规范 (templates/db-design-spec.md)
数据库设计规范，包含：
- 命名规范
- 字段规范
- 索引规范
- 分库分表规范

## 相关资产

- 需求阶段: `01-requirements/`
- 架构阶段: `03-architecture/`
- 技术栈上下文: `common/context-templates/tech-stack-context.md`