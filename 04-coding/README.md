# 编码阶段资产

本目录包含编码阶段的 Prompt 资产，帮助开发人员进行代码编写、审查和优化。

## 目录结构

```
04-coding/
├── README.md                  # 本文件
├── backend/                   # 后端编码
│   ├── templates/
│   │   ├── java-code-style.md     # Java代码规范
│   │   ├── springcloud-boilerplate.md  # Spring Cloud模板
│   │   └── unit-test-guide.md     # 单元测试指南
│   └── prompts/
│       ├── code-generation.md         # 代码生成
│       ├── code-review.md             # 代码审查 (DataSphere)
│       ├── refactoring.md             # 重构建议 (DataSphere)
│       ├── git-commit.md              # Git 提交规范
│       ├── migration.md               # 迁移指南
│       ├── bug-fixing.md              # Bug修复
│       └── performance-optimization.md # 性能优化
└── frontend/                  # 前端编码
    ├── templates/
    │   ├── vue-code-style.md       # Vue代码规范
    │   ├── component-template.md   # 组件模板
    │   └── state-management.md     # 状态管理规范
    └── prompts/
        ├── vue-component-dev.md    # Vue组件开发 (DataSphere)
        ├── page-development.md     # 页面开发
        ├── frontend-review.md      # 前端代码审查 (DataSphere)
        └── frontend-optimization.md # 前端优化
```

## 后端编码资产

### 模板文件
- **java-code-style.md**: Java代码规范，遵循阿里巴巴Java开发手册
- **springcloud-boilerplate.md**: Spring Cloud项目模板和代码结构
- **unit-test-guide.md**: 单元测试编写指南

### Prompt文件
| 文件 | 用途 | 适用场景 |
|------|------|----------|
| code-generation.md | 根据接口设计生成代码 | 新增 CRUD 接口 |
| code-review.md | 代码审查和改进建议 | 代码评审阶段 |
| refactoring.md | 代码重构建议 | 代码优化阶段 |
| git-commit.md | Git 提交消息规范 | 提交代码阶段 |
| migration.md | 系统迁移指南 | 版本升级、技术迁移 |
| bug-fixing.md | Bug定位和修复 | 问题排查阶段 |
| performance-optimization.md | 性能优化建议 | 性能调优阶段 |

## 前端编码资产

### 模板文件
- **vue-code-style.md**: Vue代码规范，基于Vue 3最佳实践
- **component-template.md**: Vue组件标准模板
- **state-management.md**: Pinia状态管理规范

### Prompt文件
| 文件 | 用途 | 适用场景 |
|------|------|----------|
| vue-component-dev.md | Vue组件开发 | 开发新组件 |
| page-development.md | 页面开发 | 开发新页面 |
| frontend-review.md | 前端代码审查 | 代码评审阶段 |
| frontend-optimization.md | 前端性能优化 | 性能调优阶段 |

## DataSphere 项目专用

DataSphere 项目相关的 Prompt 已整合到上述文件中，包含：
- 技术栈上下文 (Java 21 + Spring Boot 3.5 + MyBatis-Plus, Vue 3 + TypeScript + wujie-vue3)
- 项目特定的代码规范
- CrudApi、CrudService 等项目组件使用指南

详细项目上下文见: `../projects/datasphere/project-context.md`

## 相关资产

- 设计阶段: `../02-design/`
- 测试阶段: `../05-testing/`
- 技术栈上下文: `../common/context-templates/tech-stack-context.md`