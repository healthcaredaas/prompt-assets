# 技术栈上下文模板

本文档定义技术栈的标准上下文信息，可在代码生成、架构设计等 Prompt 中引用。

---

## 后端技术栈

### 核心框架
| 技术 | 版本 | 用途 |
|------|------|------|
| Java | 17+ | 开发语言 |
| Spring Boot | 3.2.x | 应用框架 |
| Spring Cloud Alibaba | 2022.x | 微服务框架 |
| Spring Security | 6.x | 安全框架 |

### 数据访问
| 技术 | 版本 | 用途 |
|------|------|------|
| MyBatis-Plus | 3.5.x | ORM框架 |
| MySQL | 8.0+ | 关系数据库 |
| Redis | 7.x | 缓存数据库 |
| Elasticsearch | 8.x | 搜索引擎 |
| MongoDB | 6.x | 文档数据库 |

### 消息中间件
| 技术 | 版本 | 用途 |
|------|------|------|
| RocketMQ | 5.x | 消息队列 |
| Kafka | 3.x | 流处理 |

### 微服务组件
| 技术 | 版本 | 用途 |
|------|------|------|
| Nacos | 2.x | 注册中心/配置中心 |
| Sentinel | 1.8.x | 流量控制 |
| Seata | 2.x | 分布式事务 |
| Gateway | 4.x | API网关 |

### 工具库
| 技术 | 版本 | 用途 |
|------|------|------|
| Lombok | 1.18.x | 代码简化 |
| MapStruct | 1.5.x | 对象映射 |
| Hutool | 5.x | 工具类库 |
| Guava | 32.x | Google核心库 |

---

## 前端技术栈

### 核心框架
| 技术 | 版本 | 用途 |
|------|------|------|
| Vue | 3.4.x | 前端框架 |
| TypeScript | 5.x | 类型支持 |
| Vite | 5.x | 构建工具 |

### UI组件
| 技术 | 版本 | 用途 |
|------|------|------|
| Element Plus | 2.5.x | UI组件库 |
| TailwindCSS | 3.x | 原子化CSS |

### 状态管理
| 技术 | 版本 | 用途 |
|------|------|------|
| Pinia | 2.x | 状态管理 |
| VueUse | 10.x | 组合式工具 |

### 网络请求
| 技术 | 版本 | 用途 |
|------|------|------|
| Axios | 1.x | HTTP客户端 |
| VueRequest | 2.x | 请求管理 |

### 工具库
| 技术 | 版本 | 用途 |
|------|------|------|
| Day.js | 1.x | 日期处理 |
| Lodash-es | 4.x | 工具函数 |
| VueI18n | 9.x | 国际化 |

---

## 基础设施

### 容器化
| 技术 | 版本 | 用途 |
|------|------|------|
| Docker | 24.x | 容器引擎 |
| Docker Compose | 2.x | 本地编排 |
| Kubernetes | 1.28+ | 容器编排 |
| Helm | 3.x | K8s包管理 |

### CI/CD
| 技术 | 用途 |
|------|------|
| GitLab CI | 持续集成 |
| Jenkins | 流水线 |
| ArgoCD | GitOps |

### 监控运维
| 技术 | 用途 |
|------|------|
| Prometheus | 指标采集 |
| Grafana | 可视化监控 |
| ELK Stack | 日志分析 |
| SkyWalking | 链路追踪 |
| AlertManager | 告警管理 |

---

## 开发规范

### 代码规范

#### Java代码规范
- 遵循《阿里巴巴Java开发手册》
- 使用CheckStyle进行代码检查
- 单个方法长度不超过80行
- 单个类文件不超过500行

```java
// 示例：Controller标准结构
@RestController
@RequestMapping("/api/v1/orders")
@Tag(name = "订单管理", description = "订单相关接口")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @PostMapping
    @Operation(summary = "创建订单")
    public Result<Long> create(@Valid @RequestBody OrderCreateDTO dto) {
        return Result.success(orderService.create(dto));
    }
}
```

#### Vue代码规范
- 遵循Vue官方风格指南
- 使用ESLint + Prettier格式化
- 组件命名采用PascalCase
- 使用Composition API

```vue
<!-- 示例：组件标准结构 -->
<template>
  <div class="order-list">
    <!-- 模板内容 -->
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
// 组合式API实现
</script>

<style scoped>
/* 组件样式 */
</style>
```

### API设计规范
- RESTful风格
- 统一返回格式
- 版本控制
- 接口文档

```json
// 统一返回格式
{
  "code": 200,
  "message": "success",
  "data": {},
  "timestamp": 1709000000000
}
```

### 数据库规范
- 表名使用下划线命名
- 主键使用雪花算法ID
- 必备字段：id, create_time, update_time, deleted
- 索引命名：idx_字段名, uk_字段名

```sql
-- 示例：表结构
CREATE TABLE `t_order` (
  `id` bigint NOT NULL COMMENT '主键ID',
  `order_no` varchar(32) NOT NULL COMMENT '订单编号',
  `user_id` bigint NOT NULL COMMENT '用户ID',
  `amount` decimal(10,2) NOT NULL COMMENT '订单金额',
  `status` tinyint NOT NULL COMMENT '订单状态',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间',
  `deleted` tinyint NOT NULL DEFAULT '0' COMMENT '删除标志',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_order_no` (`order_no`),
  KEY `idx_user_id` (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单表';
```

---

## 安全规范

### 认证授权
- JWT Token认证
- RBAC权限模型
- 接口权限控制

### 数据安全
- 敏感数据加密存储
- SQL注入防护
- XSS攻击防护
- CSRF防护

### 接口安全
- 接口签名验证
- 请求频率限制
- 参数校验

---

## 性能标准

### 响应时间
| 接口类型 | 目标值 |
|----------|--------|
| 查询接口 | <200ms (P99) |
| 写入接口 | <500ms (P99) |
| 复杂计算 | <1s (P99) |

### 并发能力
| 指标 | 目标值 |
|------|--------|
| QPS | >1000 |
| 并发用户 | >10000 |

### 资源利用率
| 资源 | 上限 |
|------|------|
| CPU | <70% |
| 内存 | <80% |
| 磁盘IO | <70% |

---

## 使用说明

在 Prompt 中引用技术栈上下文：

```markdown
## 上下文
参考: ./common/context-templates/tech-stack-context.md

项目使用 Java + Spring Cloud 后端技术栈，Vue 前端技术栈，
部署在 Kubernetes 环境中。
```

---

## 更新记录

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.0 | 2026-02-26 | 初始版本 |