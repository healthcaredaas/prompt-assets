# 工作流阶段资产

本目录包含敏捷工作流的 Prompt 资产，帮助团队进行项目管理和流程优化。

## 目录结构

```
08-workflow/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   └── sprint-planning.md    # 冲刺计划模板
└── prompts/                   # Prompt文件
    └── story-point-estimation.md  # 故事点估算
```

## 使用场景

### 1. 冲刺计划
制定冲刺计划时，使用 `templates/sprint-planning.md`。

**输入**: 产品待办列表、团队能力
**输出**: 冲刺计划文档

### 2. 故事点估算
估算用户故事的工作量时，使用 `prompts/story-point-estimation.md`。

**输入**: 用户故事、技术复杂度
**输出**: 故事点估算结果

## 模板说明

### 冲刺计划模板 (templates/sprint-planning.md)
冲刺计划的标准模板，包含：
- 冲刺目标
- 待办事项
- 任务分配
- 风险识别

### 故事点估算Prompt (prompts/story-point-estimation.md)
故事点估算的指导文件，包含：
- 估算方法说明
- 复杂度评估
- 估算示例

## 最佳实践

1. **迭代计划**: 每个冲刺开始前进行计划会议
2. **团队参与**: 估算工作由团队共同完成
3. **持续改进**: 每个冲刺结束后进行回顾
4. **数据驱动**: 基于历史数据优化估算

## 相关资产

- 需求阶段: `01-requirements/`
- 测试阶段: `05-testing/`
- 项目上下文: `common/context-templates/project-context.md`