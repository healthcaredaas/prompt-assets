# 企业应用开发 Prompt 资产库

## 概述

本资产库提供一套可复用、可版本管理、可演进的企业应用开发 prompt 资产，覆盖产品研发全生命周期。

## 技术栈

### DataSphere 项目技术栈
- **后端**: Java 21 + Spring Boot 3.5.11 + MyBatis-Plus 3.5.x
- **前端**: Vue 3.4+ + TypeScript 5.4+ + Vite 5+ + wujie-vue3
- **数据库**: MySQL 8.0+
- **部署**: Docker + Kubernetes

### 通用技术栈
- **后端**: Java + Spring Cloud
- **前端**: Vue
- **部署**: Docker + Kubernetes

## 目录结构

```
prompt-assets/
├── skills/                    # Skills 技能定义
│   ├── commit.md              # Git 提交规范
│   ├── review.md              # 代码审查规范
│   ├── refactor.md            # 重构指南
│   └── migrate.md             # 迁移指南
│
├── 01-requirements/           # 产品需求阶段
├── 02-design/                 # 设计阶段
├── 03-architecture/           # 技术架构阶段
├── 04-coding/                 # 编码阶段
│   ├── backend/prompts/       # 后端编码 prompts
│   └── frontend/prompts/      # 前端编码 prompts
├── 05-testing/                # 测试阶段
├── 06-deployment/             # 部署阶段
├── 07-operations/             # 运维阶段
├── 08-workflow/               # 敏捷工作流
├── 09-security/               # 安全规范
├── common/                    # 通用资产
└── tools/                     # 辅助工具
```

## Skills 技能索引

| 技能 | 用途 | 文档位置 |
|------|------|----------|
| commit | Git 提交规范 | [skills/commit.md](skills/commit.md) |
| review | 代码审查规范 | [skills/review.md](skills/review.md) |
| refactor | 重构指南 | [skills/refactor.md](skills/refactor.md) |
| migrate | 迁移指南 | [skills/migrate.md](skills/migrate.md) |

## 快速开始

### 1. 使用 Skills

在 Claude Code 中引用 skills：

```
参考 ./prompt-assets/skills/commit.md

请为以下变更生成提交消息：
添加了质量规则测试接口
```

### 2. 使用 Prompts

```
参考 ./prompt-assets/04-coding/backend/prompts/code-generation.md

为订单模块生成 CRUD 代码
```

### 3. 项目上下文

```
参考 ./prompt-assets/projects/datasphere/project-context.md

了解项目结构和技术栈
```

## 各阶段资产说明

| 阶段 | 目录 | 核心资产 |
|------|------|----------|
| Skills | skills | commit, review, refactor, migrate |
| 需求 | 01-requirements | 需求分析、用户故事生成、优先级评估 |
| 设计 | 02-design | UI组件设计、API接口设计、数据库建模 |
| 架构 | 03-architecture | Spring Cloud架构、Vue架构、微服务拆分 |
| 编码 | 04-coding | 代码生成、代码审查、重构建议 |
| 测试 | 05-testing | 测试用例生成、集成测试 |
| 部署 | 06-deployment | Docker镜像构建、K8s部署设计 |
| 运维 | 07-operations | 日志分析、性能监控、故障排查 |
| 工作流 | 08-workflow | 故事点估算、迭代待办 |
| 安全 | 09-security | 代码安全审计、API安全审查 |

## Prompt 设计规范

每个 Prompt 遵循统一结构：

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