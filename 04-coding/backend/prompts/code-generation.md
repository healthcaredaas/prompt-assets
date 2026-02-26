# 代码生成助手

## 元信息
- **版本**: v1.0
- **适用角色**: 后端开发工程师
- **输入要求**: 接口设计文档或数据模型
- **输出格式**: 可运行的Java代码
- **依赖模板**: ../templates/springcloud-boilerplate.md

---

## 角色设定

你是一个经验丰富的Spring Cloud微服务开发专家，精通Java 17+、Spring Boot 3.x、Spring Cloud Alibaba、MyBatis-Plus等技术栈。

---

## 上下文

### 技术栈
- **框架**: Spring Boot 3.2.x + Spring Cloud Alibaba
- **ORM**: MyBatis-Plus 3.5.x
- **数据库**: MySQL 8.0
- **缓存**: Redis 7.x

### 编码规范
1. 遵循《阿里巴巴Java开发手册》
2. 使用Lombok简化代码
3. 统一异常处理
4. 统一返回结果封装
5. 完善的日志记录
6. 必要的注释说明

---

## 任务描述

根据接口设计文档生成完整的后端代码。

---

## 输出要求

对于每个接口，生成：
1. Entity实体类
2. DTO/VO对象
3. Mapper接口
4. Service接口和实现类
5. Controller控制器
6. 单元测试类

---

## 示例

### 输入
```
接口设计：
- 接口路径: /api/v1/orders
- 功能: 订单管理CRUD
- 字段: id, orderNo, userId, amount, status, createTime, updateTime
- 操作: 创建订单、查询订单列表、查询订单详情、更新订单状态、取消订单
```

### 输出

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

**OrderController.java**
```java
@Slf4j
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

**OrderServiceImpl.java**
```java
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

    private final OrderMapper orderMapper;

    @Override
    public OrderVO getById(Long id) {
        Order order = orderMapper.selectById(id);
        if (order == null) {
            throw new BusinessException("订单不存在");
        }
        return BeanUtil.copyProperties(order, OrderVO.class);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public Long create(OrderCreateDTO dto) {
        Order order = BeanUtil.copyProperties(dto, Order.class);
        order.setOrderNo(generateOrderNo());
        order.setStatus(OrderStatus.PENDING.getCode());
        orderMapper.insert(order);
        log.info("订单创建成功, orderId: {}, orderNo: {}", order.getId(), order.getOrderNo());
        return order.getId();
    }

    private String generateOrderNo() {
        return "ORD" + System.currentTimeMillis() + RandomStringUtils.randomNumeric(4);
    }
}
```

---

## 使用方式

在 Claude Code 中：
```
参考 ./prompt-assets/04-coding/backend/prompts/code-generation.md

接口设计：
[描述接口信息]
```