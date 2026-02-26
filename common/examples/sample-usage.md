# Prompt 使用示例

本文档提供 Prompt 资产库的使用示例，帮助快速上手。

---

## 示例1：需求分析

### 场景
用户需要开发一个订单管理系统，需要分析需求并生成标准化文档。

### 使用方式

**在 Claude Code 中输入：**

```
参考 ./prompt-assets/01-requirements/prompts/requirement-analysis.md

用户原始需求：
我需要一个订单管理系统，支持订单创建、查询、修改和取消。
系统需要对接库存服务，支持多种支付方式，并提供订单统计分析功能。
用户包括普通用户和客服人员，普通用户只能查看和操作自己的订单，
客服可以查看所有订单并协助处理问题订单。
```

### 期望输出

```
# 订单管理系统需求分析文档

## 一、需求概述

### 1.1 背景
开发一套订单管理系统，支持订单全生命周期管理...

### 1.2 目标
- 实现订单的创建、查询、修改、取消功能
- 对接库存服务实现库存扣减
- 支持多种支付方式...

### 1.3 范围
- 包含：订单管理、库存对接、支付集成、统计分析
- 不包含：商品管理、用户管理...

## 二、用户角色定义

| 角色 | 描述 | 权限范围 |
|------|------|----------|
| 普通用户 | 下单用户 | 仅操作自己的订单 |
| 客服人员 | 订单处理人员 | 查看所有订单，处理问题订单 |

## 三、功能需求

### 3.1 订单管理模块
- F001 订单创建
  - 支持多商品下单
  - 自动计算订单金额
  - 库存预占...

### 3.2 库存对接模块
...

## 四、非功能需求
...

## 五、验收标准
...

## 六、风险识别
...
```

---

## 示例2：代码生成

### 场景
根据API设计文档生成后端代码。

### 使用方式

**在 Claude Code 中输入：**

```
参考 ./prompt-assets/04-coding/backend/prompts/code-generation.md

接口设计：
- 接口路径: /api/v1/orders
- 功能: 订单管理CRUD
- 字段: id, orderNo, userId, amount, status, createTime, updateTime
- 操作: 创建订单、查询订单列表、查询订单详情、更新订单状态、取消订单

技术栈: Java 17 + Spring Boot 3 + MyBatis-Plus
```

### 期望输出

**OrderController.java**
```java
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

    @GetMapping("/{id}")
    @Operation(summary = "查询订单详情")
    public Result<OrderVO> getById(@PathVariable Long id) {
        return Result.success(orderService.getById(id));
    }

    @GetMapping
    @Operation(summary = "查询订单列表")
    public Result<PageResult<OrderVO>> list(OrderQueryDTO query) {
        return Result.success(orderService.list(query));
    }

    @PutMapping("/{id}/status")
    @Operation(summary = "更新订单状态")
    public Result<Void> updateStatus(@PathVariable Long id, @RequestBody OrderStatusDTO dto) {
        orderService.updateStatus(id, dto);
        return Result.success();
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "取消订单")
    public Result<Void> cancel(@PathVariable Long id) {
        orderService.cancel(id);
        return Result.success();
    }
}
```

**Order.java (Entity)**
```java
@Data
@TableName("t_order")
public class Order {

    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    private String orderNo;

    private Long userId;

    private BigDecimal amount;

    private Integer status;

    private LocalDateTime createTime;

    private LocalDateTime updateTime;

    @TableLogic
    private Integer deleted;
}
```

*(以及 Service、Mapper、DTO、VO 等完整代码)*

---

## 示例3：代码审查

### 场景
审查已有代码，提出改进建议。

### 使用方式

**在 Claude Code 中输入：**

```
参考 ./prompt-assets/04-coding/backend/prompts/code-review.md

请审查以下代码：

```java
public class OrderService {
    @Autowired
    private OrderMapper orderMapper;

    public Order getOrder(Long id) {
        return orderMapper.selectById(id);
    }

    public void createOrder(Order order) {
        orderMapper.insert(order);
    }

    public List<Order> getOrdersByUser(Long userId) {
        return orderMapper.selectList(
            new QueryWrapper<Order>().eq("user_id", userId)
        );
    }
}
```

代码背景：这是订单服务的核心代码，需要满足高并发场景。
```

### 期望输出

```
# 代码审查报告

## 一、审查摘要

| 指标 | 评分 |
|------|------|
| 代码质量 | C |
| 可维护性 | B- |
| 性能 | D |
| 安全性 | C+ |

**总体评价**: 代码存在多处问题，需要进行重构优化。

## 二、问题清单

### 严重问题 (P0)

#### 1. 缺少事务控制
**位置**: `createOrder` 方法
**问题**: 订单创建没有事务控制，可能导致数据不一致
**建议**: 添加 `@Transactional` 注解

```java
@Transactional(rollbackFor = Exception.class)
public void createOrder(Order order) {
    orderMapper.insert(order);
}
```

#### 2. 存在N+1查询风险
**位置**: `getOrdersByUser` 方法
**问题**: 如果在循环中调用会导致N+1问题
**建议**: 使用JOIN查询或批量查询

### 一般问题 (P1)

#### 3. 使用字段注入
**位置**: `@Autowired` 字段注入
**问题**: 不推荐使用字段注入，应使用构造器注入
**建议**:

```java
@RequiredArgsConstructor
public class OrderService {
    private final OrderMapper orderMapper;
}
```

#### 4. 缺少参数校验
**位置**: 所有公开方法
**问题**: 缺少参数非空校验
**建议**: 添加参数校验逻辑

### 优化建议 (P2)

#### 5. 缺少日志记录
**建议**: 添加关键操作日志

#### 6. 缺少缓存
**建议**: 热点数据添加Redis缓存

## 三、重构建议

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderMapper orderMapper;
    private final RedisTemplate<String, Object> redisTemplate;

    public Order getOrder(Long id) {
        Assert.notNull(id, "订单ID不能为空");
        String cacheKey = "order:" + id;

        // 缓存查询
        Order order = (Order) redisTemplate.opsForValue().get(cacheKey);
        if (order != null) {
            return order;
        }

        // 数据库查询
        order = orderMapper.selectById(id);
        if (order != null) {
            redisTemplate.opsForValue().set(cacheKey, order, 1, TimeUnit.HOURS);
        }

        return order;
    }

    @Transactional(rollbackFor = Exception.class)
    public Long createOrder(OrderCreateDTO dto) {
        log.info("创建订单开始, userId: {}", dto.getUserId());

        Order order = new Order();
        BeanUtils.copyProperties(dto, order);
        order.setOrderNo(generateOrderNo());
        order.setStatus(OrderStatus.PENDING.getCode());
        order.setCreateTime(LocalDateTime.now());

        orderMapper.insert(order);

        log.info("订单创建成功, orderId: {}, orderNo: {}", order.getId(), order.getOrderNo());
        return order.getId();
    }

    // ... 其他方法
}
```

## 四、改进优先级

1. **立即修复**: 事务控制、参数校验
2. **本周完成**: 依赖注入优化、日志添加
3. **下个迭代**: 缓存优化、批量查询优化
```

---

## 示例4：K8s部署

### 场景
为订单服务设计Kubernetes部署方案。

### 使用方式

**在 Claude Code 中输入：**

```
参考 ./prompt-assets/06-deployment/prompts/k8s-deployment-design.md

应用信息：
- 服务名称: order-service
- 镜像: registry.example.com/order-service:v1.0.0
- 端口: 8080
- 资源需求: CPU 500m, 内存 1Gi
- 最小副本: 2
- 最大副本: 5
- 需要配置: 数据库连接、Redis连接、消息队列配置
- 需要健康检查
```

### 期望输出

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  labels:
    app: order-service
    version: v1.0.0
spec:
  replicas: 2
  selector:
    matchLabels:
      app: order-service
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: order-service
        version: v1.0.0
    spec:
      containers:
      - name: order-service
        image: registry.example.com/order-service:v1.0.0
        ports:
        - containerPort: 8080
          name: http
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
          limits:
            cpu: 1000m
            memory: 2Gi
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "prod"
        envFrom:
        - configMapRef:
            name: order-service-config
        - secretRef:
            name: order-service-secret
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: order-service
              topologyKey: kubernetes.io/hostname

---
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  type: ClusterIP
  selector:
    app: order-service
  ports:
  - port: 8080
    targetPort: 8080
    name: http

---
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
data:
  SPRING_DATASOURCE_URL: "jdbc:mysql://mysql:3306/order_db"
  SPRING_REDIS_HOST: "redis"
  SPRING_REDIS_PORT: "6379"
  SPRING_ROCKETMQ_NAME_SERVER: "rocketmq:9876"

---
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

---

## 示例5：故障排查

### 场景
线上服务响应缓慢，需要进行故障排查。

### 使用方式

**在 Claude Code 中输入：**

```
参考 ./prompt-assets/07-operations/prompts/troubleshooting.md

故障现象：
- 服务名称: order-service
- 现象: 接口响应时间从200ms上升到5s+
- 时间: 今天上午10:00开始
- 监控数据: CPU使用率90%，内存使用率70%，数据库连接数接近上限

日志片段：
2026-02-26 10:05:23 ERROR [order-service] c.e.o.s.OrderService - Database connection timeout
2026-02-26 10:05:45 WARN [order-service] c.e.o.s.OrderService - Slow query detected: SELECT * FROM t_order WHERE user_id = ?
```

### 期望输出

```
# 故障排查报告

## 一、故障概述

| 项目 | 内容 |
|------|------|
| 故障服务 | order-service |
| 故障现象 | 接口响应时间严重超时 (200ms → 5s+) |
| 故障时间 | 2026-02-26 10:00 开始 |
| 影响范围 | 订单相关接口全部受影响 |
| 故障等级 | P1 (严重) |

## 二、根因分析

### 2.1 问题链路

```
接口响应慢 → 数据库连接池耗尽 → 慢SQL → 全表扫描 → 索引缺失
```

### 2.2 根本原因

**主要原因**:
1. `t_order.user_id` 字段缺少索引，导致全表扫描
2. 数据库连接池配置过小，高峰期连接数不足
3. 慢查询堆积，导致连接池耗尽

**次要原因**:
1. 缺少SQL慢查询监控告警
2. 数据库连接池监控缺失

## 三、解决方案

### 3.1 立即处理 (紧急)

```sql
-- 添加索引
ALTER TABLE t_order ADD INDEX idx_user_id (user_id);
```

```yaml
# 临时扩容数据库连接池
spring:
  datasource:
    hikari:
      maximum-pool-size: 50  # 原值20
```

### 3.2 永久修复

**1. 数据库优化**
```sql
-- 检查并补充必要索引
SELECT * FROM sys_indexes WHERE table_name = 't_order';
ALTER TABLE t_order ADD INDEX idx_user_id (user_id);
ALTER TABLE t_order ADD INDEX idx_status_create_time (status, create_time);
```

**2. 连接池配置优化**
```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 30
      minimum-idle: 10
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

**3. 添加监控告警**
```yaml
# Prometheus告警规则
groups:
- name: order-service
  rules:
  - alert: SlowQueryDetected
    expr: mysql_slow_queries_total > 10
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: "慢查询检测"
```

## 四、预防措施

1. **SQL审核**: 上线前强制SQL审核，检查索引
2. **监控完善**: 添加慢查询、连接池监控告警
3. **容量规划**: 定期评估数据库连接池容量
4. **压测验证**: 上线前进行性能压测

## 五、跟进事项

- [ ] 执行索引添加
- [ ] 调整连接池配置
- [ ] 添加监控告警
- [ ] 编写故障复盘报告
```

---

## 更多示例

更多详细示例请参考各阶段目录下的具体 Prompt 文件。