# 运维阶段资产

本目录包含运维阶段的 Prompt 资产，帮助运维工程师进行系统运维和故障处理。

## 目录结构

```
07-operations/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   └── runbook.md            # 运维手册模板
└── prompts/                   # Prompt文件
    ├── troubleshooting.md    # 故障排查
    └── log-analysis.md       # 日志分析
```

## 使用场景

### 1. 运维手册编写
编写系统运维手册时，使用 `templates/runbook.md`。

**输入**: 系统架构、运维流程
**输出**: 标准化的运维手册

### 2. 故障排查
系统故障时，使用 `prompts/troubleshooting.md` 进行排查。

**输入**: 故障现象、日志信息
**输出**: 故障原因、解决方案

### 3. 日志分析
分析系统日志时，使用 `prompts/log-analysis.md`。

**输入**: 日志文件、分析需求
**输出**: 日志分析报告

## 模板说明

### 运维手册模板 (templates/runbook.md)
运维手册的标准模板，包含：
- 系统概述
- 运维流程
- 监控告警
- 故障处理
- 应急预案

### 故障排查Prompt (prompts/troubleshooting.md)
故障排查的指导文件，包含：
- 故障现象分析
- 排查步骤指导
- 常见问题解决方案

### 日志分析Prompt (prompts/log-analysis.md)
日志分析的指导文件，包含：
- 日志格式解析
- 错误日志识别
- 日志统计报告

## 最佳实践

1. **预防为主**: 建立完善的监控告警体系
2. **快速响应**: 制定标准化的故障处理流程
3. **持续改进**: 每次故障后进行复盘总结
4. **文档沉淀**: 将经验沉淀为运维文档

## 相关资产

- 部署阶段: `06-deployment/`
- 安全阶段: `09-security/`
- 项目上下文: `common/context-templates/project-context.md`