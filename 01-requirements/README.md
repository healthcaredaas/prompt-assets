# 需求阶段资产

本目录包含需求分析阶段的 Prompt 资产，帮助产品经理和需求分析师进行需求分析和文档编写。

## 目录结构

```
01-requirements/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   ├── prd-template.md        # PRD文档模板
│   ├── user-story-template.md # 用户故事模板
│   └── acceptance-criteria.md # 验收标准模板
└── prompts/                   # Prompt文件
    ├── requirement-analysis.md    # 需求分析
    ├── user-story-generation.md   # 用户故事生成
    └── priority-estimation.md     # 优先级评估
```

## 使用场景

### 1. 需求分析
当收到用户原始需求时，使用 `prompts/requirement-analysis.md` 进行结构化分析。

**输入**: 用户原始需求描述
**输出**: 标准化的需求分析文档

### 2. 用户故事生成
将需求细化为用户故事，使用 `prompts/user-story-generation.md`。

**输入**: 需求分析文档
**输出**: 用户故事列表

### 3. 优先级评估
评估需求的优先级，使用 `prompts/priority-estimation.md`。

**输入**: 需求列表、业务价值、技术复杂度
**输出**: 优先级排序结果

## 模板说明

### PRD模板 (templates/prd-template.md)
产品需求文档的标准模板，包含：
- 需求概述
- 用户角色
- 功能需求
- 非功能需求
- 验收标准
- 里程碑计划

### 用户故事模板 (templates/user-story-template.md)
用户故事的标准格式，包含：
- 作为[角色]
- 我想要[功能]
- 以便于[价值]
- 验收标准

### 验收标准模板 (templates/acceptance-criteria.md)
验收标准的编写规范，包含：
- Given-When-Then格式
- 边界条件
- 异常场景

## 最佳实践

1. **先分析后细化**: 先用需求分析Prompt整理思路，再生成用户故事
2. **多方参与**: 需求分析应邀请技术、产品、设计多方参与
3. **持续迭代**: 需求文档应随项目进展持续更新

## 相关资产

- 设计阶段: `02-design/`
- 架构阶段: `03-architecture/`
- 项目上下文: `common/context-templates/project-context.md`