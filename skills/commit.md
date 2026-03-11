# Git 提交规范

本文档定义 DataSphere 项目的 Git 提交规范，确保提交历史清晰、可追溯。

## 提交消息格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type 类型说明

| Type | 说明 | 示例 |
|------|------|------|
| feat | 新功能 | feat(backend): 添加质量规则测试接口 |
| fix | Bug 修复 | fix(frontend): 修复用户管理页面分页问题 |
| docs | 文档更新 | docs: 更新 API 文档 |
| style | 代码格式（不影响功能） | style: 格式化代码 |
| refactor | 重构（不是新功能也不是修复） | refactor(backend): 重构用户服务 |
| test | 测试相关 | test: 添加单元测试 |
| chore | 构建/工具相关 | chore: 更新依赖版本 |
| perf | 性能优化 | perf(database): 优化查询性能 |
| ci | CI/CD 相关 | ci: 添加 GitHub Actions 配置 |

### Scope 范围说明

| Scope | 说明 |
|-------|------|
| backend | 后端代码 |
| frontend | 前端代码 |
| database | 数据库相关 |
| docs | 文档 |
| config | 配置文件 |
| api | API 接口 |
| security | 安全相关 |

### Subject 主题说明

- 使用简短的描述（不超过 50 字符）
- 使用祈使语气（如 "add" 而不是 "added"）
- 不以句号结尾

## 提交前检查清单

- [ ] 代码已通过本地测试
- [ ] 代码符合编码规范
- [ ] 提交消息符合规范
- [ ] 不包含敏感信息（密码、密钥等）
- [ ] 不包含不必要的文件（IDE 配置、日志等）

## 提交示例

### 新功能
```
feat(backend): 添加质量规则测试接口

- 支持通过规则ID测试SQL
- 返回测试结果和错误信息
- 支持模板参数替换

Closes #123
```

### Bug 修复
```
fix(frontend): 修复用户管理页面分页问题

修复了切换页码后数据未刷新的问题，原因是分页参数未正确传递。

Fixes #456
```

### 重构
```
refactor(backend): 重构用户服务

- 抽取公共方法到基类
- 优化查询性能
- 添加缓存支持
```

### 文档更新
```
docs: 更新 API 文档

- 添加质量规则接口文档
- 更新用户管理接口示例
```

## 分支命名规范

| 分支类型 | 命名格式 | 示例 |
|----------|----------|------|
| 功能分支 | feature/xxx | feature/quality-rule |
| 修复分支 | fix/xxx | fix/pagination-bug |
| 发布分支 | release/x.x.x | release/1.0.0 |
| 热修复分支 | hotfix/xxx | hotfix/security-patch |

## 合并请求规范

### 标题格式
```
[Type] 简短描述
```

示例：
```
[Feat] 添加质量规则测试接口
[Fix] 修复用户管理页面分页问题
```

### 描述模板
```markdown
## 变更说明
简要描述本次变更的内容和原因

## 变更类型
- [ ] 新功能
- [ ] Bug 修复
- [ ] 重构
- [ ] 文档更新
- [ ] 其他

## 测试说明
- 测试步骤
- 预期结果

## 相关 Issue
Closes #xxx
```

## 注意事项

1. **原子提交**: 每次提交只做一件事
2. **频繁提交**: 小步快跑，便于回滚
3. **有意义的消息**: 避免无意义的提交消息如 "fix", "update"
4. **关联 Issue**: 使用 `Closes #xxx` 或 `Fixes #xxx` 关联 Issue
5. **不提交敏感信息**: 密码、密钥、Token 等不应提交