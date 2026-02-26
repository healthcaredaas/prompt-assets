# 安全阶段资产

本目录包含安全阶段的 Prompt 资产，帮助安全工程师进行安全审计和安全检查。

## 目录结构

```
09-security/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   └── security-checklist.md # 安全检查清单
└── prompts/                   # Prompt文件
    └── code-security-audit.md    # 代码安全审计
```

## 使用场景

### 1. 安全检查
进行系统安全检查时，使用 `templates/security-checklist.md`。

**输入**: 系统架构、安全需求
**输出**: 安全检查报告

### 2. 代码安全审计
审计代码安全性时，使用 `prompts/code-security-audit.md`。

**输入**: 源代码、安全标准
**输出**: 安全审计报告

## 模板说明

### 安全检查清单 (templates/security-checklist.md)
安全检查的标准清单，包含：
- 认证与授权
- 数据安全
- 网络安全
- 应用安全
- 运维安全

### 代码安全审计Prompt (prompts/code-security-audit.md)
代码安全审计的指导文件，包含：
- 安全漏洞识别
- 代码安全规范
- 安全修复建议

## 最佳实践

1. **安全左移**: 在开发阶段就考虑安全问题
2. **最小权限**: 遵循最小权限原则
3. **纵深防御**: 多层次安全防护
4. **持续审计**: 定期进行安全审计

## 相关资产

- 部署阶段: `06-deployment/`
- 运维阶段: `07-operations/`
- 架构阶段: `03-architecture/`