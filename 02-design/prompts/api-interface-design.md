# API接口设计助手

## 元信息
- **版本**: v1.0
- **适用角色**: 后端开发工程师、架构师
- **输入要求**: 业务需求、数据模型
- **输出格式**: RESTful API接口文档
- **依赖模板**: ../templates/api-design-spec.md

---

## 角色设定

你是一个资深的API设计师，精通RESTful API设计规范。你擅长：
- 设计清晰、一致的API接口
- 遵循RESTful设计原则
- 编写完整的接口文档
- 考虑接口的版本管理和向后兼容

---

## 上下文

### 技术栈
- **框架**: Spring Boot 3.x
- **文档**: Swagger/OpenAPI 3.0
- **认证**: JWT Token
- **版本**: URL版本控制 (v1, v2)

### API设计原则
1. **资源导向**: URL表示资源，HTTP方法表示操作
2. **统一接口**: 使用标准的HTTP方法和状态码
3. **无状态**: 每个请求包含所有必要信息
4. **版本管理**: 向后兼容，版本号在URL中

---

## 任务描述

根据业务需求设计RESTful API接口，输出完整的接口文档。

### 设计步骤

1. **资源识别**
   - 识别核心资源
   - 确定资源关系
   - 设计资源层级

2. **接口设计**
   - 设计URL结构
   - 确定HTTP方法
   - 定义请求响应

3. **文档编写**
   - 编写接口文档
   - 定义数据模型
   - 编写示例

---

## 输出要求

### 输出结构

```markdown
# [模块名称] API接口文档

## 一、接口概览

| 接口名称 | 方法 | 路径 | 说明 |
|----------|------|------|------|
| 创建[资源] | POST | /api/v1/[资源] | 创建新[资源] |
| 查询[资源]列表 | GET | /api/v1/[资源] | 分页查询[资源]列表 |
| 查询[资源]详情 | GET | /api/v1/[资源]/{id} | 根据ID查询[资源]详情 |
| 更新[资源] | PUT | /api/v1/[资源]/{id} | 更新[资源]信息 |
| 删除[资源] | DELETE | /api/v1/[资源]/{id} | 删除[资源] |

## 二、接口详情

### 2.1 [接口名称]

#### 基本信息
- **接口路径**: [METHOD] /api/v1/[路径]
- **接口描述**: [描述]
- **认证方式**: Bearer Token
- **权限要求**: [权限]

#### 请求参数

##### Header参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|

##### Path参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|

##### Query参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|

##### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|

##### 错误响应
| 参数名 | 类型 | 说明 |
|--------|------|------|

#### 请求示例

```json
// 请求Body
{
  // JSON示例
}
```

#### 响应示例

```json
// 成功响应
{
  "code": 200,
  "message": "success",
  "data": {},
  "timestamp": 1709000000000
}

// 错误响应
{
  "code": 40001,
  "message": "参数错误",
  "data": null,
  "timestamp": 1709000000000
}
```

## 三、数据模型

### 3.1 [模型名称]DTO

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|

### 3.2 [模型名称]VO

| 字段名 | 类型 | 说明 | 示例 |
|--------|------|------|------|

## 四、错误码

| 错误码 | 说明 |
|--------|------|

## 五、代码示例

### 5.1 Controller代码

```java
// Controller代码示例
```

### 5.2 Service代码

```java
// Service代码示例
```
```

---

## 示例

### 输入

```
模块：订单管理
业务需求：
1. 创建订单
2. 查询订单列表（支持分页、筛选）
3. 查询订单详情
4. 更新订单状态
5. 取消订单
6. 订单支付

数据模型：
- 订单：ID、订单号、用户ID、金额、状态、收货地址、创建时间
- 订单明细：ID、订单ID、商品ID、数量、价格
```

### 输出

```markdown
# 订单管理 API接口文档

## 一、接口概览

| 接口名称 | 方法 | 路径 | 说明 |
|----------|------|------|------|
| 创建订单 | POST | /api/v1/orders | 创建新订单 |
| 查询订单列表 | GET | /api/v1/orders | 分页查询订单列表 |
| 查询订单详情 | GET | /api/v1/orders/{id} | 根据ID查询订单详情 |
| 更新订单状态 | PUT | /api/v1/orders/{id}/status | 更新订单状态 |
| 取消订单 | POST | /api/v1/orders/{id}/cancel | 取消订单 |
| 订单支付 | POST | /api/v1/orders/{id}/pay | 订单支付 |

## 二、接口详情

### 2.1 创建订单

#### 基本信息
- **接口路径**: POST /api/v1/orders
- **接口描述**: 创建新订单，同时预占库存
- **认证方式**: Bearer Token
- **权限要求**: user

#### 请求参数

##### Header参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| Authorization | String | 是 | Bearer {token} |
| Content-Type | String | 是 | application/json |

##### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| items | Array | 是 | 商品列表 | - |
| items[].productId | Long | 是 | 商品ID | 1 |
| items[].skuId | Long | 否 | SKU ID | 1 |
| items[].quantity | Integer | 是 | 数量 | 2 |
| addressId | Long | 是 | 收货地址ID | 1 |
| couponId | Long | 否 | 优惠券ID | 1 |
| remark | String | 否 | 备注 | "尽快发货" |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Object | 订单信息 |
| data.orderId | Long | 订单ID |
| data.orderNo | String | 订单号 |
| data.totalAmount | Decimal | 订单总金额 |
| data.payAmount | Decimal | 实付金额 |
| timestamp | Long | 时间戳 |

##### 错误响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 错误码 |
| message | String | 错误信息 |
| data | Object | 错误详情 |
| timestamp | Long | 时间戳 |

#### 请求示例

```json
{
  "items": [
    {
      "productId": 10001,
      "skuId": 20001,
      "quantity": 2
    },
    {
      "productId": 10002,
      "skuId": 20002,
      "quantity": 1
    }
  ],
  "addressId": 1,
  "couponId": 100,
  "remark": "尽快发货"
}
```

#### 响应示例

```json
// 成功响应
{
  "code": 200,
  "message": "success",
  "data": {
    "orderId": 123456789,
    "orderNo": "20260226000001",
    "totalAmount": 299.00,
    "discountAmount": 30.00,
    "freightAmount": 0.00,
    "payAmount": 269.00,
    "payUrl": "https://pay.example.com/order/123456789"
  },
  "timestamp": 1709000000000
}

// 错误响应 - 库存不足
{
  "code": 40001,
  "message": "商品库存不足",
  "data": {
    "productId": 10001,
    "productName": "iPhone 15 Pro",
    "stock": 0,
    "required": 2
  },
  "timestamp": 1709000000000
}

// 错误响应 - 参数校验失败
{
  "code": 40002,
  "message": "参数校验失败",
  "data": {
    "errors": [
      {
        "field": "items[0].productId",
        "message": "商品ID不能为空"
      },
      {
        "field": "addressId",
        "message": "收货地址不能为空"
      }
    ]
  },
  "timestamp": 1709000000000
}
```

---

### 2.2 查询订单列表

#### 基本信息
- **接口路径**: GET /api/v1/orders
- **接口描述**: 分页查询订单列表，支持多条件筛选
- **认证方式**: Bearer Token
- **权限要求**: user

#### 请求参数

##### Header参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| Authorization | String | 是 | Bearer {token} |

##### Query参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| page | Integer | 否 | 页码，默认1 | 1 |
| size | Integer | 否 | 每页数量，默认20 | 20 |
| status | Integer | 否 | 订单状态 | 1 |
| orderNo | String | 否 | 订单号 | "20260226000001" |
| startTime | String | 否 | 开始时间 | "2026-02-01" |
| endTime | String | 否 | 结束时间 | "2026-02-28" |
| sort | String | 否 | 排序字段 | "createTime" |
| order | String | 否 | 排序方向 | "desc" |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Object | 分页数据 |
| data.list | Array | 订单列表 |
| data.total | Long | 总数 |
| data.page | Integer | 当前页 |
| data.size | Integer | 每页数量 |
| data.pages | Integer | 总页数 |
| timestamp | Long | 时间戳 |

#### 请求示例

```
GET /api/v1/orders?page=1&size=20&status=1&sort=createTime&order=desc
```

#### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "list": [
      {
        "id": 123456789,
        "orderNo": "20260226000001",
        "totalAmount": 299.00,
        "payAmount": 269.00,
        "status": 1,
        "statusName": "已支付",
        "itemCount": 3,
        "createTime": "2026-02-26 10:00:00"
      }
    ],
    "total": 100,
    "page": 1,
    "size": 20,
    "pages": 5
  },
  "timestamp": 1709000000000
}
```

---

### 2.3 查询订单详情

#### 基本信息
- **接口路径**: GET /api/v1/orders/{id}
- **接口描述**: 根据订单ID查询订单详情
- **认证方式**: Bearer Token
- **权限要求**: user

#### 请求参数

##### Path参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 订单ID |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Object | 订单详情 |
| data.id | Long | 订单ID |
| data.orderNo | String | 订单号 |
| data.status | Integer | 订单状态 |
| data.statusName | String | 状态名称 |
| data.totalAmount | Decimal | 订单总金额 |
| data.discountAmount | Decimal | 优惠金额 |
| data.freightAmount | Decimal | 运费金额 |
| data.payAmount | Decimal | 实付金额 |
| data.payType | Integer | 支付方式 |
| data.payTime | String | 支付时间 |
| data.receiverName | String | 收货人 |
| data.receiverPhone | String | 收货电话 |
| data.receiverAddress | String | 收货地址 |
| data.items | Array | 订单明细 |
| data.items[].productId | Long | 商品ID |
| data.items[].productName | String | 商品名称 |
| data.items[].productImage | String | 商品图片 |
| data.items[].skuId | Long | SKU ID |
| data.items[].skuName | String | SKU名称 |
| data.items[].price | Decimal | 单价 |
| data.items[].quantity | Integer | 数量 |
| data.items[].totalAmount | Decimal | 小计 |
| data.createTime | String | 创建时间 |
| timestamp | Long | 时间戳 |

#### 请求示例

```
GET /api/v1/orders/123456789
```

#### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 123456789,
    "orderNo": "20260226000001",
    "status": 1,
    "statusName": "已支付",
    "totalAmount": 299.00,
    "discountAmount": 30.00,
    "freightAmount": 0.00,
    "payAmount": 269.00,
    "payType": 1,
    "payTypeName": "支付宝",
    "payTime": "2026-02-26 10:05:00",
    "receiverName": "张三",
    "receiverPhone": "13800138000",
    "receiverAddress": "北京市朝阳区xxx街道xxx号",
    "items": [
      {
        "productId": 10001,
        "productName": "iPhone 15 Pro",
        "productImage": "https://xxx.com/iphone.jpg",
        "skuId": 20001,
        "skuName": "深空黑色 256GB",
        "price": 199.00,
        "quantity": 1,
        "totalAmount": 199.00
      },
      {
        "productId": 10002,
        "productName": "iPhone手机壳",
        "productImage": "https://xxx.com/case.jpg",
        "skuId": 20002,
        "skuName": "透明款",
        "price": 50.00,
        "quantity": 2,
        "totalAmount": 100.00
      }
    ],
    "createTime": "2026-02-26 10:00:00"
  },
  "timestamp": 1709000000000
}
```

---

### 2.4 更新订单状态

#### 基本信息
- **接口路径**: PUT /api/v1/orders/{id}/status
- **接口描述**: 更新订单状态（管理员使用）
- **认证方式**: Bearer Token
- **权限要求**: admin

#### 请求参数

##### Path参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 订单ID |

##### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| status | Integer | 是 | 订单状态 | 2 |
| remark | String | 否 | 备注 | "已发货" |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Boolean | 是否成功 |
| timestamp | Long | 时间戳 |

#### 请求示例

```json
{
  "status": 2,
  "remark": "已发货，快递单号：SF1234567890"
}
```

#### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": true,
  "timestamp": 1709000000000
}
```

---

### 2.5 取消订单

#### 基本信息
- **接口路径**: POST /api/v1/orders/{id}/cancel
- **接口描述**: 取消订单，释放库存
- **认证方式**: Bearer Token
- **权限要求**: user

#### 请求参数

##### Path参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 订单ID |

##### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| reason | String | 否 | 取消原因 | "不想买了" |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Boolean | 是否成功 |
| timestamp | Long | 时间戳 |

#### 请求示例

```json
{
  "reason": "不想买了"
}
```

#### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": true,
  "timestamp": 1709000000000
}
```

---

### 2.6 订单支付

#### 基本信息
- **接口路径**: POST /api/v1/orders/{id}/pay
- **接口描述**: 发起订单支付，返回支付链接
- **认证方式**: Bearer Token
- **权限要求**: user

#### 请求参数

##### Path参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Long | 是 | 订单ID |

##### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| payType | Integer | 是 | 支付方式：1支付宝，2微信 | 1 |
| returnUrl | String | 否 | 支付完成跳转地址 | "https://xxx.com/pay/success" |

#### 响应参数

##### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Object | 支付信息 |
| data.payUrl | String | 支付链接 |
| data.qrCode | String | 支付二维码 |
| timestamp | Long | 时间戳 |

#### 请求示例

```json
{
  "payType": 1,
  "returnUrl": "https://www.example.com/pay/success"
}
```

#### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "payUrl": "https://openapi.alipay.com/gateway.do?xxx",
    "qrCode": "https://qr.alipay.com/xxx"
  },
  "timestamp": 1709000000000
}
```

---

## 三、数据模型

### 3.1 OrderCreateDTO

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| items | Array<OrderItemDTO> | 是 | 商品列表 | - |
| addressId | Long | 是 | 收货地址ID | 1 |
| couponId | Long | 否 | 优惠券ID | 1 |
| remark | String | 否 | 备注 | "尽快发货" |

### 3.2 OrderItemDTO

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| productId | Long | 是 | 商品ID | 1 |
| skuId | Long | 否 | SKU ID | 1 |
| quantity | Integer | 是 | 数量 | 2 |

### 3.3 OrderQueryDTO

| 字段名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| page | Integer | 否 | 页码 | 1 |
| size | Integer | 否 | 每页数量 | 20 |
| status | Integer | 否 | 订单状态 | 1 |
| orderNo | String | 否 | 订单号 | "20260226000001" |
| startTime | String | 否 | 开始时间 | "2026-02-01" |
| endTime | String | 否 | 结束时间 | "2026-02-28" |
| sort | String | 否 | 排序字段 | "createTime" |
| order | String | 否 | 排序方向 | "desc" |

### 3.4 OrderVO

| 字段名 | 类型 | 说明 | 示例 |
|--------|------|------|------|
| id | Long | 订单ID | 123456789 |
| orderNo | String | 订单号 | "20260226000001" |
| status | Integer | 订单状态 | 1 |
| statusName | String | 状态名称 | "已支付" |
| totalAmount | Decimal | 订单总金额 | 299.00 |
| discountAmount | Decimal | 优惠金额 | 30.00 |
| freightAmount | Decimal | 运费金额 | 0.00 |
| payAmount | Decimal | 实付金额 | 269.00 |
| payType | Integer | 支付方式 | 1 |
| payTime | String | 支付时间 | "2026-02-26 10:05:00" |
| receiverName | String | 收货人 | "张三" |
| receiverPhone | String | 收货电话 | "13800138000" |
| receiverAddress | String | 收货地址 | "北京市朝阳区xxx" |
| items | Array<OrderItemVO> | 订单明细 | - |
| createTime | String | 创建时间 | "2026-02-26 10:00:00" |

### 3.5 OrderItemVO

| 字段名 | 类型 | 说明 | 示例 |
|--------|------|------|------|
| productId | Long | 商品ID | 1 |
| productName | String | 商品名称 | "iPhone 15 Pro" |
| productImage | String | 商品图片 | "https://xxx.com/iphone.jpg" |
| skuId | Long | SKU ID | 1 |
| skuName | String | SKU名称 | "深空黑色 256GB" |
| price | Decimal | 单价 | 199.00 |
| quantity | Integer | 数量 | 1 |
| totalAmount | Decimal | 小计 | 199.00 |

## 四、错误码

| 错误码 | 说明 |
|--------|------|
| 200 | 成功 |
| 40001 | 参数校验失败 |
| 40002 | 资源不存在 |
| 40003 | 资源已存在 |
| 40101 | 未登录 |
| 40102 | Token失效 |
| 40301 | 无权限 |
| 50001 | 系统异常 |
| 50002 | 服务不可用 |
| 60001 | 库存不足 |
| 60002 | 订单状态异常 |
| 60003 | 订单已取消 |
| 60004 | 订单已支付 |

## 五、代码示例

### 5.1 Controller代码

```java
@RestController
@RequestMapping("/api/v1/orders")
@Tag(name = "订单管理", description = "订单相关接口")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @PostMapping
    @Operation(summary = "创建订单")
    public Result<OrderCreateVO> create(@Valid @RequestBody OrderCreateDTO dto) {
        return Result.success(orderService.create(dto));
    }

    @GetMapping
    @Operation(summary = "查询订单列表")
    public Result<PageResult<OrderVO>> list(OrderQueryDTO query) {
        return Result.success(orderService.list(query));
    }

    @GetMapping("/{id}")
    @Operation(summary = "查询订单详情")
    public Result<OrderVO> getById(@PathVariable Long id) {
        return Result.success(orderService.getById(id));
    }

    @PutMapping("/{id}/status")
    @Operation(summary = "更新订单状态")
    public Result<Boolean> updateStatus(
            @PathVariable Long id,
            @Valid @RequestBody OrderStatusDTO dto) {
        return Result.success(orderService.updateStatus(id, dto));
    }

    @PostMapping("/{id}/cancel")
    @Operation(summary = "取消订单")
    public Result<Boolean> cancel(
            @PathVariable Long id,
            @RequestBody(required = false) OrderCancelDTO dto) {
        return Result.success(orderService.cancel(id, dto));
    }

    @PostMapping("/{id}/pay")
    @Operation(summary = "订单支付")
    public Result<OrderPayVO> pay(
            @PathVariable Long id,
            @Valid @RequestBody OrderPayDTO dto) {
        return Result.success(orderService.pay(id, dto));
    }
}
```

### 5.2 Service代码

```java
@Service
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

    private final OrderMapper orderMapper;
    private final OrderItemMapper orderItemMapper;
    private final InventoryService inventoryService;
    private final ProductService productService;
    private final CouponService couponService;

    @Transactional(rollbackFor = Exception.class)
    @Override
    public OrderCreateVO create(OrderCreateDTO dto) {
        // 1. 校验商品库存
        for (OrderItemDTO item : dto.getItems()) {
            Integer stock = inventoryService.getStock(item.getProductId(), item.getSkuId());
            if (stock < item.getQuantity()) {
                throw new BusinessException("商品库存不足");
            }
        }

        // 2. 计算订单金额
        BigDecimal totalAmount = BigDecimal.ZERO;
        List<OrderItem> items = new ArrayList<>();
        for (OrderItemDTO item : dto.getItems()) {
            ProductVO product = productService.getById(item.getProductId());
            BigDecimal itemAmount = product.getPrice().multiply(new BigDecimal(item.getQuantity()));
            totalAmount = totalAmount.add(itemAmount);

            OrderItem orderItem = new OrderItem();
            orderItem.setProductId(item.getProductId());
            orderItem.setSkuId(item.getSkuId());
            orderItem.setProductName(product.getName());
            orderItem.setPrice(product.getPrice());
            orderItem.setQuantity(item.getQuantity());
            orderItem.setTotalAmount(itemAmount);
            items.add(orderItem);
        }

        // 3. 计算优惠
        BigDecimal discountAmount = BigDecimal.ZERO;
        if (dto.getCouponId() != null) {
            discountAmount = couponService.calculateDiscount(dto.getCouponId(), totalAmount);
        }

        // 4. 创建订单
        Order order = new Order();
        order.setOrderNo(generateOrderNo());
        order.setUserId(UserContext.getUserId());
        order.setTotalAmount(totalAmount);
        order.setDiscountAmount(discountAmount);
        order.setFreightAmount(BigDecimal.ZERO);
        order.setPayAmount(totalAmount.subtract(discountAmount));
        order.setStatus(OrderStatus.PENDING.getCode());
        orderMapper.insert(order);

        // 5. 创建订单明细
        for (OrderItem item : items) {
            item.setOrderId(order.getId());
            item.setOrderNo(order.getOrderNo());
            orderItemMapper.insert(item);
        }

        // 6. 预占库存
        for (OrderItemDTO item : dto.getItems()) {
            inventoryService.preDeduct(item.getProductId(), item.getSkuId(), item.getQuantity());
        }

        return OrderCreateVO.builder()
                .orderId(order.getId())
                .orderNo(order.getOrderNo())
                .totalAmount(order.getTotalAmount())
                .payAmount(order.getPayAmount())
                .build();
    }

    private String generateOrderNo() {
        return "ORD" + System.currentTimeMillis() + RandomStringUtils.randomNumeric(4);
    }
}
```
```

---

## 变量说明

| 变量 | 说明 | 示例值 |
|------|------|--------|
| `{{module_name}}` | 模块名称 | 订单管理 |
| `{{resource_name}}` | 资源名称 | 订单 |
| `{{api_version}}` | API版本 | v1 |

---

## 使用方式

在 Claude Code 中：

```
参考 ./prompt-assets/02-design/prompts/api-interface-design.md

模块：[模块名称]
业务需求：
[描述业务需求]

数据模型：
[描述数据模型]
```

---

## 相关文档

- API设计规范: `../templates/api-design-spec.md`
- 数据库设计规范: `../templates/db-design-spec.md`
- Spring Cloud模板: `../../04-coding/backend/templates/springcloud-boilerplate.md`