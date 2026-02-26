# 企业应用开发 Prompt 资产库

## 概述

本资产库提供一套可复用、可版本管理、可演进的企业应用开发prompt资产，覆盖产品研发全生命周期。

## 技术栈

- **后端**: Java + Spring Cloud
- **前端**: Vue
- **部署**: Docker + Kubernetes

## 目录结构

```
prompt-assets/
├── 01-requirements/          # 产品需求阶段
├── 02-design/               # 设计阶段
├── 03-architecture/         # 技术架构阶段
├── 04-coding/               # 编码阶段
│   ├── backend/             # 后端编码
│   └── frontend/            # 前端编码
├── 05-testing/              # 测试阶段
├── 06-deployment/           # 部署阶段
├── 07-operations/           # 运维阶段
├── 08-workflow/             # 敏捷工作流
├── 09-security/             # 安全规范
├── common/                  # 通用资产
└── tools/                   # 辅助工具
```

## 快速开始

### 1. 与 Claude Code 集成

在项目根目录创建 `.claude/CLAUDE.md`：

```markdown
# 项目Prompt资产引用

## 技术栈上下文
当前项目使用：Java + Spring Cloud 后端, Vue 前端, Docker + K8s 部署

## Prompt资产路径
- 需求分析: ./prompt-assets/01-requirements/prompts/
- 架构设计: ./prompt-assets/03-architecture/prompts/
- 后端开发: ./prompt-assets/04-coding/backend/prompts/
- 前端开发: ./prompt-assets/04-coding/frontend/prompts/
- 测试: ./prompt-assets/05-testing/prompts/
- 部署: ./prompt-assets/06-deployment/prompts/
- 运维: ./prompt-assets/07-operations/prompts/
```

### 2. 使用方式

在 Claude Code 对话中引用：

```
参考 ./prompt-assets/04-coding/backend/prompts/code-generation.md，为订单模块生成CRUD代码
```

## 各阶段资产说明

| 阶段 | 目录 | 核心Prompt |
|------|------|------------|
| 需求 | 01-requirements | 需求分析、用户故事生成、优先级评估 |
| 设计 | 02-design | UI组件设计、API接口设计、数据库建模 |
| 架构 | 03-architecture | Spring Cloud架构、Vue架构、微服务拆分 |
| 编码 | 04-coding | 代码生成、代码审查、重构建议 |
| 测试 | 05-testing | 测试用例生成、集成测试、E2E测试 |
| 部署 | 06-deployment | Docker镜像构建、K8s部署设计、GitOps |
| 运维 | 07-operations | 日志分析、性能监控、故障排查 |
| 工作流 | 08-workflow | 故事点估算、迭代待办、速率分析 |
| 安全 | 09-security | 代码安全审计、API安全审查、依赖扫描 |

## Prompt 设计规范

每个Prompt遵循统一结构：

```markdown
# [Prompt名称]

## 元信息
- 版本: v1.0
- 适用角色: [产品/开发/测试/运维]
- 输入要求: [需要的上下文信息]
- 输出格式: [期望的输出格式]

## 角色设定
你是一个[具体角色]，负责[具体职责]...

## 任务描述
[具体的任务说明]

## 输出要求
1. [输出格式要求]
2. [质量标准]
3. [交付物清单]

## 示例
[输入输出示例]
```

## 版本管理

- 语义化版本控制 (SemVer)
- 变更日志见 [VERSION.md](./VERSION.md)
- 向后兼容性保证

## 贡献指南

1. Fork 本仓库
2. 创建特性分支
3. 遵循 Prompt 设计规范
4. 提交 Pull Request

## 许可证

MIT License