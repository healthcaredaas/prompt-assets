# 版本变更记录

本文件记录 Prompt 资产库的所有版本变更。

## 版本命名规范

采用语义化版本控制 (SemVer): `MAJOR.MINOR.PATCH`

- **MAJOR**: 不兼容的API变更
- **MINOR**: 向后兼容的功能新增
- **PATCH**: 向后兼容的问题修复

---

## [1.0.0] - 2026-02-26

### 新增

#### 核心资产库结构
- 建立完整的目录结构，覆盖9个研发阶段
- 制定Prompt设计规范和模板设计规范

#### 需求阶段 (01-requirements)
- PRD文档模板
- 用户故事模板
- 验收标准模板
- 需求分析Prompt
- 用户故事生成Prompt
- 优先级评估Prompt

#### 设计阶段 (02-design)
- UI设计规范模板
- API设计规范模板
- 数据库设计规范模板
- UI组件设计Prompt
- API接口设计Prompt
- 数据库建模Prompt
- 架构评审Prompt

#### 架构阶段 (03-architecture)
- 技术选型模板
- 系统架构模板
- 微服务设计模板
- Spring Cloud架构设计Prompt
- Vue架构设计Prompt
- 微服务拆分Prompt
- 分库分表设计Prompt
- 安全架构设计Prompt

#### 编码阶段 (04-coding)

**后端资产:**
- Java代码规范
- Spring Cloud模板
- 单元测试指南
- 代码生成Prompt
- 代码审查Prompt
- 重构建议Prompt
- Bug修复Prompt
- 性能优化Prompt

**前端资产:**
- Vue代码规范
- 组件模板
- 状态管理规范
- Vue组件开发Prompt
- 页面开发Prompt
- 前端优化Prompt

#### 测试阶段 (05-testing)
- 测试计划模板
- 测试用例模板
- 测试报告模板
- 测试用例生成Prompt
- 集成测试设计Prompt
- E2E测试设计Prompt
- 覆盖率分析Prompt

#### 部署阶段 (06-deployment)
- Dockerfile模板
- K8s部署模板
- CI/CD流水线模板
- Docker镜像构建Prompt
- K8s部署设计Prompt
- Helm Chart开发Prompt
- GitOps工作流Prompt

#### 运维阶段 (07-operations)
- 告警规则模板
- 运维手册模板
- 故障报告模板
- 日志分析Prompt
- 性能监控Prompt
- 故障排查Prompt
- 容量规划Prompt

#### 工作流阶段 (08-workflow)
- 迭代计划模板
- 站会模板
- 复盘模板
- 故事点估算Prompt
- 迭代待办整理Prompt
- 速率分析Prompt

#### 安全阶段 (09-security)
- 安全检查清单模板
- 漏洞报告模板
- 代码安全审计Prompt
- API安全审查Prompt
- 依赖扫描分析Prompt

#### 通用资产 (common)
- Prompt语法指南
- 项目上下文模板
- 技术栈上下文模板
- 示例使用文档

#### 辅助工具 (tools)
- Prompt验证工具
- 版本同步脚本

---

## 变更类型说明

- **新增 (Added)**: 新功能或新文件
- **变更 (Changed)**: 现有功能的变更
- **弃用 (Deprecated)**: 即将移除的功能
- **移除 (Removed)**: 已移除的功能
- **修复 (Fixed)**: 问题修复
- **安全 (Security)**: 安全相关的修复