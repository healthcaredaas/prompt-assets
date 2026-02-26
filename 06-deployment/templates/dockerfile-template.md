# Dockerfile 模板

> 版本: v1.0 | 最后更新: 2026-02-26

本文档提供各类应用的 Dockerfile 标准模板，帮助快速构建容器镜像。

---

## 一、Java应用镜像模板

### 1.1 Spring Boot应用（标准版）

```dockerfile
# ==========================================
# Spring Boot应用 Dockerfile
# 适用: Spring Boot 2.x/3.x 应用
# ==========================================

# 构建阶段
FROM eclipse-temurin:17-jdk-alpine AS builder

WORKDIR /app

# 复制Maven配置和源码
COPY pom.xml .
COPY src ./src

# 构建应用（需要Maven）
RUN apk add --no-cache maven && \
    mvn clean package -DskipTests

# 运行阶段
FROM eclipse-temurin:17-jre-alpine

# 安装必要工具
RUN apk add --no-cache curl && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# 复制构建产物
COPY --from=builder /app/target/*.jar app.jar

# 设置权限
RUN chown -R appuser:appgroup /app

# 切换用户
USER appuser

# 暴露端口
EXPOSE 8080

# JVM参数
ENV JAVA_OPTS="-Xms512m -Xmx1024m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# 启动命令
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```

### 1.2 Spring Boot应用（优化版）

```dockerfile
# ==========================================
# Spring Boot应用 Dockerfile（优化版）
# 特点: 分层构建、AOT编译、GraalVM
# ==========================================

# 构建阶段
FROM ghcr.io/graalvm/native-image-community:17-muslib AS builder

WORKDIR /app

# 复制源码
COPY . .

# 构建原生镜像
RUN ./mvnw -Pnative package -DskipTests

# 运行阶段（使用最小基础镜像）
FROM alpine:3.19

RUN apk add --no-cache libstdc++ && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# 复制原生可执行文件
COPY --from=builder /app/target/*.exe app

RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 8080

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget -q --spider http://localhost:8080/actuator/health || exit 1

ENTRYPOINT ["./app"]
```

### 1.3 Spring Cloud微服务

```dockerfile
# ==========================================
# Spring Cloud微服务 Dockerfile
# 特点: 配置中心、服务注册、链路追踪
# ==========================================

FROM eclipse-temurin:17-jre-alpine

# 安装工具
RUN apk add --no-cache curl tzdata && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# 复制应用
COPY target/*.jar app.jar

# 设置时区
ENV TZ=Asia/Shanghai

# 创建日志目录
RUN mkdir -p /app/logs && chown -R appuser:appgroup /app

USER appuser

EXPOSE 8080

# 微服务特有参数
ENV JAVA_OPTS="\
    -Xms512m -Xmx1024m \
    -XX:+UseG1GC \
    -XX:MaxGCPauseMillis=200 \
    -Djava.security.egd=file:/dev/./urandom \
    -Dspring.profiles.active=k8s"

# 配置中心和服务发现参数（运行时注入）
ENV SPRING_CONFIG_IMPORT="" \
    SPRING_CLOUD_CONFIG_URI="" \
    SPRING_CLOUD_NACOS_DISCOVERY_SERVER_ADDR=""

HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```

---

## 二、Node.js应用镜像模板

### 2.1 Node.js应用（标准版）

```dockerfile
# ==========================================
# Node.js应用 Dockerfile
# 适用: Express/Koa/NestJS 应用
# ==========================================

# 构建阶段
FROM node:20-alpine AS builder

WORKDIR /app

# 复制依赖文件
COPY package*.json ./

# 安装依赖
RUN npm ci --only=production

# 复制源码
COPY . .

# 构建（如需要）
RUN npm run build

# 运行阶段
FROM node:20-alpine

# 安装必要工具
RUN apk add --no-cache curl && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# 复制依赖和构建产物
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./

RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 3000

# 环境变量
ENV NODE_ENV=production \
    PORT=3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD curl -f http://localhost:3000/health || exit 1

CMD ["node", "dist/main.js"]
```

### 2.2 Node.js应用（PNPM版）

```dockerfile
# ==========================================
# Node.js应用 Dockerfile（PNPM版）
# 特点: 使用PNPM管理依赖，更快的安装速度
# ==========================================

FROM node:20-alpine AS builder

# 安装PNPM
RUN corepack enable && corepack prepare pnpm@latest --activate

WORKDIR /app

# 复制依赖文件
COPY pnpm-lock.yaml package.json ./

# 安装依赖
RUN pnpm install --frozen-lockfile

# 复制源码并构建
COPY . .
RUN pnpm build

# 运行阶段
FROM node:20-alpine

RUN corepack enable && corepack prepare pnpm@latest --activate && \
    apk add --no-cache curl && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# 复制必要文件
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./

RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 3000

ENV NODE_ENV=production

HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:3000/health || exit 1

CMD ["node", "dist/main.js"]
```

---

## 三、前端应用镜像模板

### 3.1 Vue应用

```dockerfile
# ==========================================
# Vue应用 Dockerfile
# 适用: Vue 2.x/3.x 应用
# ==========================================

# 构建阶段
FROM node:20-alpine AS builder

WORKDIR /app

# 复制依赖文件
COPY package*.json ./

# 安装依赖
RUN npm ci

# 复制源码
COPY . .

# 构建生产版本
RUN npm run build

# 运行阶段（使用Nginx）
FROM nginx:alpine

# 复制自定义Nginx配置
COPY nginx.conf /etc/nginx/nginx.conf

# 复制构建产物
COPY --from=builder /app/dist /usr/share/nginx/html

# 暴露端口
EXPOSE 80

# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
```

### 3.2 Vue应用Nginx配置

```nginx
# nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
    gzip_min_length 1024;

    server {
        listen 80;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;

        # 安全头
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;

        # 静态资源缓存
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }

        # SPA路由支持
        location / {
            try_files $uri $uri/ /index.html;
        }

        # API代理
        location /api/ {
            proxy_pass http://backend-service:8080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # 健康检查
        location /health {
            access_log off;
            return 200 'healthy';
            add_header Content-Type text/plain;
        }
    }
}
```

### 3.3 React应用

```dockerfile
# ==========================================
# React应用 Dockerfile
# 适用: React 17/18 应用
# ==========================================

# 构建阶段
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# 运行阶段
FROM nginx:alpine

COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=builder /app/build /usr/share/nginx/html

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
```

---

## 四、多应用组合模板

### 4.1 前后端分离部署

```dockerfile
# ==========================================
# 前后端分离 - Docker Compose
# ==========================================

# docker-compose.yml
version: '3.8'

services:
  # 后端服务
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: app-backend
    restart: always
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/app
    depends_on:
      - mysql
      - redis
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/actuator/health"]
      interval: 30s
      timeout: 3s
      retries: 3

  # 前端服务
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: app-frontend
    restart: always
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - app-network

  # MySQL
  mysql:
    image: mysql:8.0
    container_name: app-mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=root123
      - MYSQL_DATABASE=app
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - app-network

  # Redis
  redis:
    image: redis:7-alpine
    container_name: app-redis
    restart: always
    volumes:
      - redis-data:/data
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  mysql-data:
  redis-data:
```

---

## 五、构建最佳实践

### 5.1 镜像优化

| 优化项 | 说明 | 建议 |
|--------|------|------|
| 基础镜像 | 选择Alpine版本 | 减少50%+体积 |
| 多阶段构建 | 分离构建和运行环境 | 减少构建依赖 |
| 层合并 | 合并RUN命令 | 减少镜像层数 |
| .dockerignore | 排除不必要的文件 | 减少构建上下文 |

### 5.2 .dockerignore示例

```
# .dockerignore
node_modules
npm-debug.log
Dockerfile
docker-compose*.yml
.git
.gitignore
README.md
.env
.env.*
*.log
target
dist
build
```

### 5.3 构建命令

```bash
# 构建镜像
docker build -t myapp:v1.0.0 .

# 构建时传递参数
docker build \
  --build-arg APP_VERSION=v1.0.0 \
  --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
  -t myapp:v1.0.0 .

# 多平台构建
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myapp:v1.0.0 \
  --push .
```

---

## 使用说明

1. **选择模板**: 根据应用类型选择对应模板
2. **修改参数**: 根据实际情况调整端口、环境变量
3. **优化镜像**: 遵循最佳实践优化镜像体积
4. **安全加固**: 非root用户运行，最小权限原则

## 相关文档

- Docker镜像构建: `../prompts/docker-image-build.md`
- K8s部署设计: `../prompts/k8s-deployment-design.md`
- K8s部署模板: `./k8s-deployment.yaml.md`