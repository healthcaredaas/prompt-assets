# 部署阶段资产

本目录包含部署阶段的 Prompt 资产，帮助运维工程师进行容器化和 Kubernetes 部署。

## 目录结构

```
06-deployment/
├── README.md                  # 本文件
├── templates/                 # 模板文件
│   ├── dockerfile-template.md # Dockerfile模板
│   └── k8s-deployment.yaml.md # Kubernetes部署模板
└── prompts/                   # Prompt文件
    ├── k8s-deployment-design.md   # K8s部署设计
    └── docker-image-build.md       # Docker镜像构建
```

## 使用场景

### 1. Docker镜像构建
构建容器镜像时，使用 `prompts/docker-image-build.md` 和 `templates/dockerfile-template.md`。

**输入**: 应用类型、运行环境、依赖配置
**输出**: Dockerfile文件、构建脚本

### 2. Kubernetes部署设计
设计K8s部署方案时，使用 `prompts/k8s-deployment-design.md` 和 `templates/k8s-deployment.yaml.md`。

**输入**: 应用架构、资源需求、部署策略
**输出**: K8s部署配置、服务配置、Ingress配置

## 模板说明

### Dockerfile模板 (templates/dockerfile-template.md)
Dockerfile的标准模板，包含：
- Java应用镜像模板
- Node.js应用镜像模板
- Vue前端镜像模板
- 多阶段构建优化

### Kubernetes部署模板 (templates/k8s-deployment.yaml.md)
K8s部署配置的标准模板，包含：
- Deployment配置
- Service配置
- ConfigMap配置
- Secret配置
- Ingress配置

## 最佳实践

1. **镜像精简**: 使用多阶段构建，最小化镜像体积
2. **安全加固**: 非root用户运行，最小权限原则
3. **资源限制**: 设置合理的资源请求和限制
4. **健康检查**: 配置liveness和readiness探针

## 相关资产

- 架构阶段: `03-architecture/`
- 编码阶段: `04-coding/`
- 运维阶段: `07-operations/`