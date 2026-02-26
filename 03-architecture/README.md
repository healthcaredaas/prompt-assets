# 技术架构阶段资产

本目录包含技术架构阶段的 Prompt 资产，帮助架构师进行系统架构设计和技术选型。

## 目录结构

```
03-architecture/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   ├── tech-stack-selection.md    # 技术选型模板
│   ├── system-architecture.md     # 系统架构模板
│   └── microservice-design.md     # 微服务设计模板
└── prompts/                   # Prompt文件
    ├── springcloud-architecture.md    # Spring Cloud架构设计
    ├── vue-architecture.md            # Vue架构设计
    ├── microservice-split.md          # 微服务拆分
    ├── database-sharding.md           # 分库分表设计
    └── security-architecture.md       # 安全架构设计
```

## 使用场景

### 1. 技术选型
使用 `templates/tech-stack-selection.md` 进行技术选型决策。

### 2. 系统架构设计
使用 `prompts/springcloud-architecture.md` 和 `prompts/vue-architecture.md` 设计系统架构。

### 3. 微服务拆分
使用 `prompts/microservice-split.md` 进行微服务拆分设计。

### 4. 数据库架构
使用 `prompts/database-sharding.md` 设计分库分表方案。

### 5. 安全架构
使用 `prompts/security-architecture.md` 设计安全架构。

## 相关资产

- 设计阶段: `02-design/`
- 编码阶段: `04-coding/`
- 技术栈上下文: `common/context-templates/tech-stack-context.md`