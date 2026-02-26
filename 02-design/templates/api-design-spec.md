# API设计规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义RESTful API的设计规范，确保接口设计的一致性和易用性。

---

## 一、设计原则

### 1.1 RESTful原则

| 原则 | 说明 |
|------|------|
| **资源导向** | URL表示资源，HTTP方法表示操作 |
| **无状态** | 每个请求包含所有必要信息 |
| **统一接口** | 使用标准的HTTP方法和状态码 |
| **分层系统** | 客户端无需知道服务器架构 |

### 1.2 设计目标

- **易用性**: 接口清晰，易于理解和使用
- **一致性**: 遵循统一的设计规范
- **可维护性**: 版本管理，向后兼容
- **安全性**: 认证授权，数据校验

---

## 二、URL规范

### 2.1 URL格式

```
{协议}://{域名}/api/{版本}/{资源}/{资源ID}/{子资源}
```

**示例**:
```
https://api.example.com/api/v1/orders
https://api.example.com/api/v1/orders/123
https://api.example.com/api/v1/orders/123/items
```

### 2.2 URL命名规范

| 规则 | 说明 | 示例 |
|------|------|------|
| 使用小写 | URL全部小写 | `/orders` ✅ `/Orders` ❌ |
| 使用连字符 | 多个单词用连字符 | `/order-items` ✅ `/orderItems` ❌ |
| 使用名词 | 表示资源而非动作 | `/orders` ✅ `/getOrders` ❌ |
| 使用复数 | 资源使用复数形式 | `/orders` ✅ `/order` ❌ |

### 2.3 资源命名

```yaml
# 推荐命名
/users                  # 用户资源
/users/{id}             # 单个用户
/users/{id}/orders      # 用户的订单
/orders                 # 订单资源
/orders/{id}/items      # 订单明细
/products               # 商品资源
/products/{id}/reviews  # 商品评价

# 不推荐命名
/user                   # 单数形式
/getUsers               # 包含动词
/orderInfo              # 驼峰命名
/user-order             # 资源层级不清
```

### 2.4 查询参数

| 参数类型 | 用途 | 示例 |
|----------|------|------|
| 分页 | 分页查询 | `?page=1&size=20` |
| 排序 | 结果排序 | `?sort=createTime&order=desc` |
| 过滤 | 条件筛选 | `?status=1&category=2` |
| 字段 | 返回字段 | `?fields=id,name,price` |
| 搜索 | 关键词搜索 | `?keyword=phone` |

---

## 三、HTTP方法规范

### 3.1 方法定义

| 方法 | 用途 | 幂等性 | 安全性 | 示例 |
|------|------|--------|--------|------|
| GET | 查询资源 | 是 | 是 | `GET /orders` |
| POST | 创建资源 | 否 | 否 | `POST /orders` |
| PUT | 全量更新 | 是 | 否 | `PUT /orders/1` |
| PATCH | 部分更新 | 是 | 否 | `PATCH /orders/1` |
| DELETE | 删除资源 | 是 | 否 | `DELETE /orders/1` |

### 3.2 使用规范

```yaml
# 查询列表
GET /api/v1/orders

# 查询详情
GET /api/v1/orders/123

# 创建资源
POST /api/v1/orders
Content-Type: application/json

{
  "userId": 1,
  "items": [{"productId": 1, "quantity": 2}]
}

# 全量更新
PUT /api/v1/orders/123
Content-Type: application/json

{
  "status": 2,
  "remark": "用户取消"
}

# 部分更新
PATCH /api/v1/orders/123
Content-Type: application/json

{
  "status": 2
}

# 删除资源
DELETE /api/v1/orders/123
```

---

## 四、请求响应规范

### 4.1 请求头规范

```http
# 必需请求头
Content-Type: application/json
Accept: application/json

# 认证请求头
Authorization: Bearer {token}

# 租户请求头（多租户）
X-Tenant-Id: {tenantId}

# 追踪请求头
X-Request-Id: {uuid}
X-Trace-Id: {traceId}
```

### 4.2 统一响应格式

#### 成功响应

```json
{
  "code": 200,
  "message": "success",
  "data": {
    // 业务数据
  },
  "timestamp": 1709000000000
}
```

#### 分页响应

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "list": [
      // 数据列表
    ],
    "total": 100,
    "page": 1,
    "size": 20
  },
  "timestamp": 1709000000000
}
```

#### 错误响应

```json
{
  "code": 40001,
  "message": "参数校验失败",
  "data": {
    "errors": [
      {
        "field": "username",
        "message": "用户名不能为空"
      }
    ]
  },
  "timestamp": 1709000000000
}
```

### 4.3 响应状态码

#### HTTP状态码

| 状态码 | 说明 | 使用场景 |
|--------|------|----------|
| 200 | 成功 | 请求成功 |
| 201 | 创建成功 | 资源创建成功 |
| 204 | 无内容 | 删除成功 |
| 400 | 请求错误 | 参数校验失败 |
| 401 | 未授权 | 未登录或Token失效 |
| 403 | 禁止访问 | 无权限访问 |
| 404 | 未找到 | 资源不存在 |
| 500 | 服务器错误 | 系统异常 |

#### 业务状态码

| 状态码 | 说明 |
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

---

## 五、接口文档规范

### 5.1 Swagger注解规范

```java
@RestController
@RequestMapping("/api/v1/orders")
@Tag(name = "订单管理", description = "订单相关接口")
public class OrderController {

    @PostMapping
    @Operation(summary = "创建订单", description = "创建新订单")
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "创建成功"),
        @ApiResponse(responseCode = "400", description = "参数错误")
    })
    public Result<Long> create(
        @Parameter(description = "订单创建请求")
        @Valid @RequestBody OrderCreateDTO dto
    ) {
        return Result.success(orderService.create(dto));
    }
}
```

### 5.2 接口文档结构

```markdown
# 订单创建接口

## 接口信息
- **接口路径**: POST /api/v1/orders
- **接口描述**: 创建新订单
- **认证方式**: Bearer Token

## 请求参数

### Header参数
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| Authorization | String | 是 | Bearer {token} |
| Content-Type | String | 是 | application/json |

### Body参数
| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| userId | Long | 是 | 用户ID | 10001 |
| items | Array | 是 | 商品列表 | - |
| items[].productId | Long | 是 | 商品ID | 1 |
| items[].quantity | Integer | 是 | 数量 | 2 |
| addressId | Long | 是 | 收货地址ID | 1 |
| couponId | Long | 否 | 优惠券ID | 1 |

## 响应参数

### 成功响应
| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| message | String | 提示信息 |
| data | Long | 订单ID |
| timestamp | Long | 时间戳 |

## 请求示例

```json
{
  "userId": 10001,
  "items": [
    {
      "productId": 1,
      "quantity": 2
    }
  ],
  "addressId": 1,
  "couponId": 1
}
```

## 响应示例

### 成功响应
```json
{
  "code": 200,
  "message": "success",
  "data": 12345,
  "timestamp": 1709000000000
}
```

### 错误响应
```json
{
  "code": 40001,
  "message": "商品库存不足",
  "data": null,
  "timestamp": 1709000000000
}
```
```

---

## 六、参数校验规范

### 6.1 校验注解

```java
@Data
public class OrderCreateDTO {

    @NotNull(message = "用户ID不能为空")
    private Long userId;

    @NotEmpty(message = "商品列表不能为空")
    @Valid
    private List<OrderItemDTO> items;

    @NotNull(message = "收货地址不能为空")
    private Long addressId;

    @DecimalMin(value = "0.01", message = "订单金额必须大于0")
    private BigDecimal amount;
}

@Data
public class OrderItemDTO {

    @NotNull(message = "商品ID不能为空")
    private Long productId;

    @Min(value = 1, message = "数量必须大于0")
    private Integer quantity;
}
```

### 6.2 校验规则

| 字段类型 | 校验规则 |
|----------|----------|
| 字符串 | 非空、长度、格式、正则 |
| 数字 | 非空、范围、精度 |
| 日期 | 非空、格式、范围 |
| 枚举 | 非空、有效值 |
| 关联 | 非空、存在性 |

---

## 七、版本管理规范

### 7.1 版本策略

| 策略 | 说明 | 示例 |
|------|------|------|
| URL版本 | 在URL中包含版本号 | `/api/v1/orders` |
| Header版本 | 在Header中指定版本 | `Accept: application/vnd.api+json;version=1` |
| 参数版本 | 在查询参数中指定版本 | `/api/orders?version=1` |

**推荐**: URL版本策略

### 7.2 版本兼容

| 变更类型 | 版本变更 | 说明 |
|----------|----------|------|
| 新增接口 | 小版本 | 新增功能 |
| 新增字段 | 小版本 | 向后兼容 |
| 删除接口 | 大版本 | 不兼容变更 |
| 修改字段 | 大版本 | 不兼容变更 |

---

## 八、安全规范

### 8.1 认证授权

```yaml
# JWT认证
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# API Key认证
X-API-Key: your-api-key

# OAuth2认证
Authorization: Bearer access_token
```

### 8.2 接口安全

| 安全措施 | 说明 |
|----------|------|
| HTTPS | 所有接口使用HTTPS |
| 参数加密 | 敏感参数加密传输 |
| 签名验证 | 接口签名防篡改 |
| 频率限制 | 接口调用频率限制 |
| SQL注入防护 | 参数化查询 |
| XSS防护 | 输入输出转义 |

---

## 九、性能规范

### 9.1 缓存策略

```http
# 响应头设置缓存
Cache-Control: max-age=3600
ETag: "abc123"
Last-Modified: Wed, 21 Oct 2025 07:28:00 GMT

# 客户端缓存验证
If-None-Match: "abc123"
If-Modified-Since: Wed, 21 Oct 2025 07:28:00 GMT
```

### 9.2 分页查询

```yaml
# 请求参数
page: 1        # 页码，从1开始
size: 20       # 每页数量，默认20，最大100
sort: createTime  # 排序字段
order: desc    # 排序方向

# 响应结构
{
  "list": [],
  "total": 100,
  "page": 1,
  "size": 20,
  "pages": 5
}
```

---

## 十、接口设计示例

### 10.1 用户接口

```yaml
# 用户注册
POST /api/v1/users/register
Request:
  - username: 用户名
  - password: 密码
  - email: 邮箱
Response:
  - userId: 用户ID

# 用户登录
POST /api/v1/users/login
Request:
  - username: 用户名
  - password: 密码
Response:
  - token: 访问令牌
  - refreshToken: 刷新令牌

# 获取用户信息
GET /api/v1/users/{id}
Response:
  - id: 用户ID
  - username: 用户名
  - email: 邮箱
  - createTime: 创建时间

# 更新用户信息
PUT /api/v1/users/{id}
Request:
  - nickname: 昵称
  - avatar: 头像
Response:
  - success: 是否成功

# 删除用户
DELETE /api/v1/users/{id}
Response:
  - success: 是否成功
```

### 10.2 订单接口

```yaml
# 创建订单
POST /api/v1/orders
Request:
  - items: 商品列表
  - addressId: 地址ID
  - couponId: 优惠券ID
Response:
  - orderId: 订单ID
  - orderNo: 订单号

# 查询订单列表
GET /api/v1/orders
Params:
  - status: 订单状态
  - page: 页码
  - size: 每页数量
Response:
  - list: 订单列表
  - total: 总数

# 查询订单详情
GET /api/v1/orders/{id}
Response:
  - id: 订单ID
  - orderNo: 订单号
  - status: 订单状态
  - items: 商品列表
  - amount: 订单金额

# 取消订单
POST /api/v1/orders/{id}/cancel
Request:
  - reason: 取消原因
Response:
  - success: 是否成功

# 订单支付
POST /api/v1/orders/{id}/pay
Request:
  - payMethod: 支付方式
Response:
  - payUrl: 支付链接
```

---

## 相关文档

- 数据库设计规范: `./db-design-spec.md`
- API接口设计Prompt: `../prompts/api-interface-design.md`
- 后端代码规范: `../../04-coding/backend/templates/java-code-style.md`