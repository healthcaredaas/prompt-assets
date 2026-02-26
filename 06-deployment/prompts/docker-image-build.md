# Docker 镜像构建助手

## 元信息
- **版本**: v1.0
- **适用角色**: 运维工程师、开发人员
- **输入要求**: 应用类型、运行环境
- **输出格式**: Dockerfile、构建脚本
- **依赖模板**: ../templates/dockerfile-template.md

---

## 角色设定

你是一个资深的 DevOps 工程师，拥有8年容器化经验。你擅长：
- 编写优化的 Dockerfile
- 设计多阶段构建方案
- 优化镜像体积
- 解决构建问题

---

## 上下文

### 项目背景
- **容器运行时**: Docker, containerd
- **镜像仓库**: Harbor, Docker Hub, 阿里云镜像服务
- **构建工具**: Docker Build, BuildKit, Buildx
- **基础镜像**: Alpine, Distroless

### 镜像构建原则
1. **最小化**: 使用最小基础镜像
2. **分层优化**: 合理利用缓存层
3. **安全加固**: 最小权限运行
4. **可维护性**: 清晰的构建流程

---

## 任务描述

根据应用类型和运行环境，生成优化的 Dockerfile 和构建脚本。

### 分析步骤

1. **应用分析**
   - 识别应用类型
   - 确定运行依赖
   - 确定构建依赖

2. **基础镜像选择**
   - 选择合适的基础镜像
   - 考虑镜像大小
   - 考虑安全性

3. **构建策略设计**
   - 单阶段/多阶段构建
   - 缓存优化策略
   - 安全加固措施

4. **优化建议**
   - 镜像体积优化
   - 构建速度优化
   - 运行性能优化

---

## 输出要求

### 输出结构

```markdown
# Docker 镜像构建方案

## 一、应用分析

### 1.1 应用信息
| 项目 | 内容 |
|------|------|
| 应用名称 | {{app_name}} |
| 应用类型 | {{app_type}} |
| 运行环境 | {{runtime}} |
| 构建工具 | {{build_tool}} |

### 1.2 依赖分析
| 依赖类型 | 依赖项 | 版本 |
|----------|--------|------|
| 运行时依赖 | | |
| 构建依赖 | | |

### 1.3 资源需求
| 资源 | 需求 |
|------|------|
| CPU | |
| 内存 | |
| 存储 | |

## 二、基础镜像选择

### 2.1 候选镜像
| 镜像 | 大小 | 安全性 | 推荐指数 |
|------|------|--------|----------|
| | | | |

### 2.2 推荐镜像
[说明推荐理由]

## 三、Dockerfile

[完整的Dockerfile内容]

## 四、构建脚本

### 4.1 构建命令
```bash
# 构建命令
```

### 4.2 多平台构建
```bash
# 多平台构建命令
```

## 五、优化建议

### 5.1 镜像优化
| 优化项 | 优化前 | 优化后 | 说明 |
|--------|--------|--------|------|
| | | | |

### 5.2 构建优化
[构建速度优化建议]

### 5.3 运行优化
[运行性能优化建议]

## 六、安全加固

### 6.1 安全配置
| 配置项 | 配置值 | 说明 |
|--------|--------|------|
| | | |

### 6.2 安全扫描
```bash
# 安全扫描命令
```
```

---

## 示例

### 输入

```
应用信息：
- 应用名称：user-service
- 应用类型：Spring Boot微服务
- 运行环境：Java 17
- 构建工具：Maven

依赖分析：
- Spring Boot 3.2.0
- Spring Cloud 2023.0.0
- MySQL Connector 8.0.33

资源需求：
- CPU：0.5-2核
- 内存：512MB-2GB
- 存储：日志存储

特殊要求：
- 支持多平台构建（amd64/arm64）
- 镜像体积优化
- 安全加固
```

### 输出

```markdown
# Docker 镜像构建方案 - user-service

## 一、应用分析

### 1.1 应用信息
| 项目 | 内容 |
|------|------|
| 应用名称 | user-service |
| 应用类型 | Spring Boot微服务 |
| 运行环境 | Java 17 |
| 构建工具 | Maven 3.9 |
| 框架版本 | Spring Boot 3.2.0 |

### 1.2 依赖分析
| 依赖类型 | 依赖项 | 版本 |
|----------|--------|------|
| 运行时依赖 | JRE | 17 |
| 运行时依赖 | Spring Boot | 3.2.0 |
| 构建依赖 | JDK | 17 |
| 构建依赖 | Maven | 3.9 |

### 1.3 资源需求
| 资源 | 需求 | 说明 |
|------|------|------|
| CPU | 0.5-2核 | 支持弹性伸缩 |
| 内存 | 512MB-2GB | JVM堆外内存预留 |
| 存储 | 100MB | 应用+运行时 |

## 二、基础镜像选择

### 2.1 候选镜像
| 镜像 | 大小 | 安全性 | 推荐指数 |
|------|------|--------|----------|
| eclipse-temurin:17-jre-alpine | ~170MB | 高 | ★★★★★ |
| eclipse-temurin:17-jre | ~450MB | 高 | ★★★ |
| openjdk:17-slim | ~220MB | 中 | ★★★ |
| amazoncorretto:17-alpine | ~180MB | 高 | ★★★★ |
| gcr.io/distroless/java17 | ~200MB | 最高 | ★★★★ |

### 2.2 推荐镜像
**推荐使用**: `eclipse-temurin:17-jre-alpine`

推荐理由：
1. **体积小**: Alpine基础镜像，仅170MB
2. **安全性高**: Eclipse Temurin是开源JDK，安全更新及时
3. **兼容性好**: Spring Boot官方推荐
4. **社区活跃**: 使用广泛，问题容易解决

## 三、Dockerfile

### 3.1 生产环境Dockerfile

```dockerfile
# ==========================================
# user-service Dockerfile
# 多阶段构建，优化镜像体积
# ==========================================

# ==========================================
# 构建阶段
# ==========================================
FROM eclipse-temurin:17-jdk-alpine AS builder

# 设置工作目录
WORKDIR /build

# 安装Maven
RUN apk add --no-cache maven

# 复制Maven配置文件（利用缓存）
COPY pom.xml .

# 下载依赖（利用Docker层缓存）
RUN mvn dependency:go-offline -B

# 复制源码
COPY src ./src

# 构建应用（跳过测试）
RUN mvn clean package -DskipTests -B

# 解压JAR文件（用于分层构建）
RUN java -Djarmode=layertools -jar target/*.jar extract --destination extracted

# ==========================================
# 运行阶段
# ==========================================
FROM eclipse-temurin:17-jre-alpine

# 安装必要工具
RUN apk add --no-cache curl tzdata && \
    # 创建非root用户
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup && \
    # 设置时区
    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone && \
    # 创建必要目录
    mkdir -p /app/logs /app/config && \
    chown -R appuser:appgroup /app

# 设置工作目录
WORKDIR /app

# 分层复制（利用Docker层缓存）
COPY --from=builder --chown=appuser:appgroup /build/extracted/dependencies/ ./
COPY --from=builder --chown=appuser:appgroup /build/extracted/spring-boot-loader/ ./
COPY --from=builder --chown=appuser:appgroup /build/extracted/snapshot-dependencies/ ./
COPY --from=builder --chown=appuser:appgroup /build/extracted/application/ ./

# 切换到非root用户
USER appuser

# 暴露端口
EXPOSE 8080

# JVM参数
ENV JAVA_OPTS="\
    -Xms512m \
    -Xmx1536m \
    -XX:+UseG1GC \
    -XX:MaxGCPauseMillis=200 \
    -XX:+HeapDumpOnOutOfMemoryError \
    -XX:HeapDumpPath=/app/logs/heapdump.hprof \
    -Djava.security.egd=file:/dev/./urandom \
    -Dspring.profiles.active=docker"

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health/liveness || exit 1

# 启动应用
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS org.springframework.boot.loader.JarLauncher"]
```

### 3.2 开发环境Dockerfile

```dockerfile
# ==========================================
# user-service Dockerfile (开发环境)
# 特点：快速构建，支持热重载
# ==========================================
FROM eclipse-temurin:17-jdk-alpine

WORKDIR /app

# 安装开发工具
RUN apk add --no-cache curl maven

# 复制项目文件
COPY . .

# 暴露端口
EXPOSE 8080
EXPOSE 5005  # 调试端口

# JVM参数（含调试）
ENV JAVA_OPTS="\
    -Xms256m \
    -Xmx512m \
    -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005"

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# 启动命令（开发模式）
CMD ["mvn", "spring-boot:run", "-Dspring-boot.run.profiles=dev"]
```

## 四、构建脚本

### 4.1 构建命令

```bash
#!/bin/bash
# ==========================================
# build.sh - 镜像构建脚本
# ==========================================

# 配置变量
APP_NAME="user-service"
VERSION="v1.0.0"
REGISTRY="registry.example.com"
PLATFORM="linux/amd64,linux/arm64"

# 构建时间
BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ')
GIT_COMMIT=$(git rev-parse --short HEAD 2>/dev/null || echo "unknown")

# 构建镜像标签
IMAGE_TAG="${REGISTRY}/${APP_NAME}:${VERSION}"
IMAGE_TAG_LATEST="${REGISTRY}/${APP_NAME}:latest"

echo "=========================================="
echo "Building ${APP_NAME}:${VERSION}"
echo "=========================================="

# 构建镜像
docker build \
    --build-arg BUILD_DATE="${BUILD_DATE}" \
    --build-arg VERSION="${VERSION}" \
    --build-arg GIT_COMMIT="${GIT_COMMIT}" \
    -t "${IMAGE_TAG}" \
    -t "${IMAGE_TAG_LATEST}" \
    .

# 检查构建结果
if [ $? -eq 0 ]; then
    echo "Build successful!"
    echo "Image: ${IMAGE_TAG}"
    docker images "${IMAGE_TAG}"
else
    echo "Build failed!"
    exit 1
fi
```

### 4.2 多平台构建

```bash
#!/bin/bash
# ==========================================
# buildx.sh - 多平台镜像构建脚本
# ==========================================

# 配置变量
APP_NAME="user-service"
VERSION="v1.0.0"
REGISTRY="registry.example.com"
PLATFORM="linux/amd64,linux/arm64"

# 创建并使用builder实例
docker buildx create --name multiarch --use 2>/dev/null || docker buildx use multiarch

# 启用buildx
docker buildx inspect --bootstrap

# 构建并推送多平台镜像
docker buildx build \
    --platform "${PLATFORM}" \
    --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
    --build-arg VERSION="${VERSION}" \
    -t "${REGISTRY}/${APP_NAME}:${VERSION}" \
    -t "${REGISTRY}/${APP_NAME}:latest" \
    --push \
    .

echo "Multi-platform build complete!"
echo "Platforms: ${PLATFORM}"
```

### 4.3 .dockerignore

```
# ==========================================
# .dockerignore
# 排除不需要复制到镜像的文件
# ==========================================

# Git
.git
.gitignore
.gitattributes

# IDE
.idea
.vscode
*.iml
*.ipr
*.iws

# Build
target
build
dist
out
*.class
*.jar
*.war

# Dependencies
node_modules

# Logs
logs
*.log

# Environment
.env
.env.*
!.env.example

# Docker
Dockerfile
docker-compose*.yml
.dockerignore

# Documentation
README.md
CHANGELOG.md
LICENSE

# Test
test
tests
*.test
*.spec

# Misc
.DS_Store
Thumbs.db
*.bak
*.tmp
```

## 五、优化建议

### 5.1 镜像优化
| 优化项 | 优化前 | 优化后 | 说明 |
|--------|--------|--------|------|
| 基础镜像 | openjdk:17 | eclipse-temurin:17-jre-alpine | 减少280MB |
| 构建方式 | 单阶段 | 多阶段 | 减少构建依赖 |
| JAR解压 | 整体复制 | 分层复制 | 优化层缓存 |
| 用户权限 | root | 非root | 安全加固 |

**镜像大小对比**:
- 优化前: ~500MB
- 优化后: ~180MB
- 减少: 64%

### 5.2 构建优化

**缓存优化**:
1. 先复制pom.xml，再下载依赖
2. 最后复制源码，触发编译
3. 依赖变更时才重新下载

**并行构建**:
```bash
# 启用BuildKit并行构建
DOCKER_BUILDKIT=1 docker build -t myapp .
```

**构建参数**:
```bash
# 使用构建缓存
docker build --cache-from myapp:latest -t myapp:v1.0.0 .
```

### 5.3 运行优化

**JVM参数优化**:
```
-Xms512m                    # 初始堆大小
-Xmx1536m                   # 最大堆大小（容器内存的75%）
-XX:+UseG1GC                # 使用G1垃圾收集器
-XX:MaxGCPauseMillis=200    # 最大GC停顿时间
-XX:+HeapDumpOnOutOfMemoryError  # OOM时生成堆转储
-XX:HeapDumpPath=/app/logs/heapdump.hprof  # 堆转储路径
```

**容器资源限制**:
```bash
docker run -d \
  --name user-service \
  --memory="2g" \
  --memory-swap="2g" \
  --cpus="2" \
  --cpu-shares=1024 \
  --restart=always \
  --health-cmd="curl -f http://localhost:8080/actuator/health || exit 1" \
  --health-interval=30s \
  --health-timeout=3s \
  --health-retries=3 \
  user-service:v1.0.0
```

## 六、安全加固

### 6.1 安全配置
| 配置项 | 配置值 | 说明 |
|--------|--------|------|
| 运行用户 | appuser (UID 1000) | 非root用户运行 |
| 文件权限 | 755/644 | 最小权限原则 |
| 网络权限 | 仅8080端口 | 最小暴露面 |
| 敏感信息 | 环境变量注入 | 不存储在镜像 |

### 6.2 安全扫描

```bash
# 使用Trivy扫描镜像漏洞
trivy image registry.example.com/user-service:v1.0.0

# 使用Docker Scout扫描
docker scout cves registry.example.com/user-service:v1.0.0

# 使用Grype扫描
grype registry.example.com/user-service:v1.0.0
```

### 6.3 安全最佳实践

1. **基础镜像安全**:
   - 使用官方镜像
   - 定期更新基础镜像
   - 使用特定版本标签（非latest）

2. **构建安全**:
   - 不在镜像中存储敏感信息
   - 使用多阶段构建减少攻击面
   - 最小化安装软件包

3. **运行安全**:
   - 非root用户运行
   - 只读文件系统
   - 限制容器能力
```

---

## 变量说明

| 变量 | 说明 | 示例值 |
|------|------|--------|
| `{{app_name}}` | 应用名称 | user-service |
| `{{app_type}}` | 应用类型 | Spring Boot微服务 |
| `{{runtime}}` | 运行环境 | Java 17 |
| `{{registry}}` | 镜像仓库 | registry.example.com |

---

## 使用方式

在 Claude Code 中：

```
参考 ./prompt-assets/06-deployment/prompts/docker-image-build.md

应用信息：
[描述应用信息]

依赖分析：
[描述依赖]

资源需求：
[描述资源需求]

特殊要求：
[描述特殊要求]
```

---

## 相关文档

- Dockerfile模板: `../templates/dockerfile-template.md`
- K8s部署设计: `./k8s-deployment-design.md`
- K8s部署模板: `../templates/k8s-deployment.yaml.md`