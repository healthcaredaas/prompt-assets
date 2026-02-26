# 微服务设计模板

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义微服务设计的标准模板。

---

## 一、服务概述

### 1.1 服务基本信息

| 项目 | 内容 |
|------|------|
| 服务名称 | {{service_name}} |
| 服务描述 | {{service_description}} |
| 服务负责人 | {{owner}} |
| 创建日期 | {{date}} |

### 1.2 服务定位

[描述服务在整体架构中的定位和职责]

### 1.3 服务边界

**包含功能**:
- 功能1
- 功能2
- 功能3

**不包含功能**:
- 功能A（由服务X负责）
- 功能B（由服务Y负责）

---

## 二、服务设计

### 2.1 服务架构

```
┌─────────────────────────────────────────┐
│             {{service_name}}             │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────┐   ┌─────────┐   ┌────────┐│
│  │Controller│   │ Service │   │ Mapper ││
│  │  Layer  │──▶│  Layer  │──▶│ Layer ││
│  └─────────┘   └─────────┘   └────────┘│
│       │              │              │   │
│       ▼              ▼              ▼   │
│  ┌─────────┐   ┌─────────┐   ┌────────┐│
│  │  DTO    │   │  Entity │   │  DAO   ││
│  └─────────┘   └─────────┘   └────────┘│
│                                         │
└─────────────────────────────────────────┘
```

### 2.2 目录结构

```
{{service_name}}/
├── src/main/java/com/example/{{service}}/
│   ├── controller/         # 控制器层
│   ├── service/            # 服务层
│   │   ├── impl/          # 服务实现
│   ├── mapper/             # 数据访问层
│   ├── entity/             # 实体类
│   ├── dto/                # 数据传输对象
│   ├── vo/                 # 视图对象
│   ├── config/             # 配置类
│   ├── constant/           # 常量
│   ├── enums/              # 枚举
│   ├── exception/          # 异常
│   ├── util/               # 工具类
│   └── Application.java    # 启动类
├── src/main/resources/
│   ├── mapper/             # MyBatis映射文件
│   ├── application.yml     # 配置文件
│   └── application-dev.yml # 开发配置
├── src/test/               # 测试代码
├── Dockerfile              # Docker构建文件
├── pom.xml                 # Maven配置
└── README.md               # 服务文档
```

### 2.3 技术栈

| 类型 | 技术 | 版本 | 说明 |
|------|------|------|------|
| 开发语言 | Java | 17 | LTS版本 |
| 应用框架 | Spring Boot | 3.2.x | |
| ORM框架 | MyBatis-Plus | 3.5.x | |
| 数据库 | MySQL | 8.0 | |
| 缓存 | Redis | 7.0 | |
| 消息队列 | RocketMQ | 5.0 | |

---

## 三、接口设计

### 3.1 API列表

| 接口名称 | 方法 | 路径 | 说明 |
|----------|------|------|------|
| 创建{{entity}} | POST | /api/v1/{{entity}} | 创建 |
| 查询{{entity}}列表 | GET | /api/v1/{{entity}} | 分页查询 |
| 查询{{entity}}详情 | GET | /api/v1/{{entity}}/{id} | 详情查询 |
| 更新{{entity}} | PUT | /api/v1/{{entity}}/{id} | 更新 |
| 删除{{entity}} | DELETE | /api/v1/{{entity}}/{id} | 删除 |

### 3.2 接口文档

参考: `./api-interface-design.md`

---

## 四、数据设计

### 4.1 数据模型

```
┌─────────────────────────────────────────┐
│           {{table_name}}                │
├─────────────────────────────────────────┤
│ id (BIGINT)          - 主键ID           │
│ name (VARCHAR)       - 名称             │
│ status (TINYINT)     - 状态             │
│ create_time (DATETIME) - 创建时间        │
│ update_time (DATETIME) - 更新时间        │
│ deleted (TINYINT)    - 删除标志          │
└─────────────────────────────────────────┘
```

### 4.2 数据库设计

参考: `./database-modeling.md`

---

## 五、服务依赖

### 5.1 依赖服务

| 服务名称 | 依赖方式 | 用途 | 说明 |
|----------|----------|------|------|
| 用户服务 | Feign | 用户信息查询 | 同步调用 |
| 消息服务 | RocketMQ | 消息通知 | 异步调用 |

### 5.2 被依赖服务

| 服务名称 | 依赖方式 | 用途 |
|----------|----------|------|
| 订单服务 | Feign | 查询{{entity}}信息 |
| 支付服务 | Feign | 查询{{entity}}信息 |

### 5.3 外部依赖

| 依赖名称 | 类型 | 用途 | 说明 |
|----------|------|------|------|
| MySQL | 数据库 | 数据存储 | 主从架构 |
| Redis | 缓存 | 缓存/会话 | 集群模式 |
| RocketMQ | 消息队列 | 异步消息 | 集群模式 |

---

## 六、配置管理

### 6.1 应用配置

```yaml
server:
  port: 8080

spring:
  application:
    name: {{service_name}}
  datasource:
    url: jdbc:mysql://localhost:3306/{{database}}
    username: root
    password: ${DB_PASSWORD}
  redis:
    host: ${REDIS_HOST}
    port: 6379

mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  configuration:
    map-underscore-to-camel-case: true

rocketmq:
  name-server: ${ROCKETMQ_NAME_SERVER}
```

### 6.2 环境配置

| 环境 | 配置文件 | 说明 |
|------|----------|------|
| 开发 | application-dev.yml | 开发环境配置 |
| 测试 | application-test.yml | 测试环境配置 |
| 预发布 | application-staging.yml | 预发布环境配置 |
| 生产 | application-prod.yml | 生产环境配置 |

---

## 七、部署配置

### 7.1 Dockerfile

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/{{service_name}}.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 7.2 Kubernetes配置

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{service_name}}
spec:
  replicas: 2
  selector:
    matchLabels:
      app: {{service_name}}
  template:
    metadata:
      labels:
        app: {{service_name}}
    spec:
      containers:
      - name: {{service_name}}
        image: {{image}}:{{tag}}
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: "500m"
            memory: "1Gi"
          limits:
            cpu: "1000m"
            memory: "2Gi"
```

---

## 八、监控告警

### 8.1 监控指标

| 指标类型 | 指标名称 | 阈值 | 说明 |
|----------|----------|------|------|
| 基础监控 | CPU使用率 | >70% | 触发告警 |
| 基础监控 | 内存使用率 | >80% | 触发告警 |
| 应用监控 | 接口响应时间 | >500ms | 触发告警 |
| 应用监控 | 错误率 | >1% | 触发告警 |

### 8.2 日志规范

| 日志级别 | 使用场景 |
|----------|----------|
| ERROR | 错误日志，需要处理 |
| WARN | 警告日志，需要关注 |
| INFO | 关键业务日志 |
| DEBUG | 调试日志，生产环境关闭 |

---

## 九、安全设计

### 9.1 认证授权

| 接口类型 | 认证方式 | 说明 |
|----------|----------|------|
| 开放接口 | 无 | 无需认证 |
| 用户接口 | JWT | 用户Token认证 |
| 管理接口 | JWT + RBAC | 角色权限控制 |

### 9.2 数据安全

| 安全措施 | 说明 |
|----------|------|
| 敏感数据加密 | AES加密存储 |
| 传输加密 | HTTPS |
| SQL注入防护 | 参数化查询 |
| XSS防护 | 输入输出转义 |

---

## 十、性能设计

### 10.1 性能指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| 响应时间 | <200ms | P99 |
| QPS | >1000 | 峰值 |
| 可用性 | 99.9% | 年度可用性 |

### 10.2 性能优化

| 优化项 | 方案 | 说明 |
|--------|------|------|
| 缓存 | Redis缓存热点数据 | 减少数据库压力 |
| 异步 | 消息队列异步处理 | 削峰填谷 |
| 分页 | 分页查询 | 减少数据量 |
| 索引 | 数据库索引优化 | 提升查询性能 |

---

## 十一、容灾设计

### 11.1 容灾方案

| 场景 | 方案 | RTO |
|------|------|-----|
| 服务故障 | K8s自动重启 | <1min |
| 数据库故障 | 主从切换 | <5min |
| 机房故障 | 多机房部署 | <30min |

### 11.2 降级熔断

| 场景 | 降级策略 | 说明 |
|------|----------|------|
| 依赖服务不可用 | 熔断降级 | 返回默认值 |
| 数据库压力过大 | 限流 | 拒绝部分请求 |
| 缓存失效 | 缓存降级 | 查询数据库 |

---

## 十二、开发规范

### 12.1 代码规范

- 遵循《阿里巴巴Java开发手册》
- 使用CheckStyle进行代码检查
- 单元测试覆盖率 > 80%

### 12.2 Git规范

| 分支类型 | 说明 |
|----------|------|
| main | 生产分支 |
| develop | 开发分支 |
| feature/* | 功能分支 |
| hotfix/* | 热修复分支 |

### 12.3 发布规范

1. 代码合并到develop分支
2. 通过CI/CD流水线
3. 部署到测试环境验证
4. 部署到生产环境