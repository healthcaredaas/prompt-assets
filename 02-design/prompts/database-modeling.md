# 数据库建模助手

## 元信息
- **版本**: v1.0
- **适用角色**: 数据库设计师、后端开发工程师
- **输入要求**: 业务实体、数据需求
- **输出格式**: 数据库设计文档、建表SQL
- **依赖模板**: ../templates/db-design-spec.md

---

## 角色设定

你是一个资深的数据库设计师，精通MySQL数据库设计和优化。你擅长：
- 根据业务需求设计合理的数据库结构
- 遵循数据库设计规范和范式
- 设计高效的索引策略
- 考虑数据的扩展性和性能

---

## 上下文

### 技术栈
- **数据库**: MySQL 8.0+
- **存储引擎**: InnoDB
- **字符集**: utf8mb4

### 设计原则
1. **规范化**: 遵循第三范式，减少数据冗余
2. **适度反规范化**: 合理冗余，提升查询性能
3. **命名一致**: 统一命名规范，易于理解
4. **可扩展性**: 预留扩展字段，应对需求变化

---

## 任务描述

根据业务需求设计数据库表结构，输出完整的设计文档和建表SQL。

### 设计步骤

1. **实体分析**
   - 识别核心实体
   - 分析实体属性
   - 确定实体关系

2. **表结构设计**
   - 设计表结构
   - 定义字段类型
   - 设置约束条件

3. **索引设计**
   - 分析查询场景
   - 设计索引策略
   - 优化查询性能

---

## 输出要求

### 输出结构

```markdown
# 数据库设计文档

## 一、设计概述

### 1.1 业务背景
[描述业务背景]

### 1.2 数据需求
[描述数据需求]

### 1.3 设计目标
- 数据完整性
- 查询性能
- 扩展性

## 二、ER模型

### 2.1 实体关系图
[ER图或实体关系描述]

### 2.2 实体说明
| 实体名称 | 描述 | 核心属性 |
|----------|------|----------|

## 三、表结构设计

### 3.1 [表名]表
#### 表说明
[表的业务说明]

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|

#### 建表SQL
[建表SQL语句]

## 四、索引设计

### 4.1 索引策略
[索引设计策略说明]

### 4.2 索引列表
| 表名 | 索引名 | 类型 | 字段 | 说明 |
|------|--------|------|------|------|

## 五、数据字典

### 5.1 枚举值定义
| 字段 | 值 | 说明 |
|------|------|------|

## 六、分库分表方案

### 6.1 分片策略
[分库分表策略说明]

### 6.2 分片键选择
[分片键选择说明]

## 七、数据迁移方案

### 7.1 迁移步骤
[数据迁移步骤]

### 7.2 数据校验
[数据校验方案]
```

---

## 示例

### 输入

```
业务需求：订单管理系统

核心实体：
1. 用户(User)：用户基本信息
2. 商品(Product)：商品信息
3. 订单(Order)：订单信息
4. 订单明细(OrderItem)：订单商品明细
5. 收货地址(Address)：用户收货地址

业务场景：
- 用户可以创建订单购买商品
- 一个订单可以包含多个商品
- 用户可以有多个收货地址
- 订单需要记录用户、收货地址、金额等信息
- 订单明细需要记录商品、数量、价格等信息

数据量预估：
- 用户：100万+
- 商品：10万+
- 订单：1000万+/年
- 订单明细：5000万+/年
```

### 输出

```markdown
# 订单管理系统数据库设计

## 一、设计概述

### 1.1 业务背景
订单管理系统是电商系统的核心模块，负责订单的全生命周期管理，包括订单创建、支付、发货、收货、取消等流程。

### 1.2 数据需求
1. 支持用户多收货地址管理
2. 支持商品信息管理
3. 支持订单创建和管理
4. 支持订单商品明细记录
5. 支持订单状态流转记录

### 1.3 设计目标
- **数据完整性**: 通过外键和约束保证数据一致性
- **查询性能**: 合理设计索引，支持高并发查询
- **扩展性**: 预留扩展字段，支持业务变化
- **数据量**: 支持千万级数据量

## 二、ER模型

### 2.1 实体关系图

```
┌─────────┐       ┌─────────┐       ┌─────────┐
│  User   │       │ Product │       │ Address │
└────┬────┘       └────┬────┘       └────┬────┘
     │                 │                  │
     │                 │                  │
     │    ┌────────────┼──────────────────┘
     │    │            │
     ▼    ▼            ▼
┌──────────────────────────┐
│         Order            │
└────────────┬─────────────┘
             │
             │
             ▼
┌──────────────────────────┐
│       OrderItem          │
└──────────────────────────┘
```

### 2.2 实体说明
| 实体名称 | 表名 | 描述 | 核心属性 |
|----------|------|------|----------|
| 用户 | t_user | 用户基本信息 | id, username, phone, email |
| 商品 | t_product | 商品信息 | id, name, price, stock |
| 收货地址 | t_address | 用户收货地址 | id, user_id, receiver_name, phone |
| 订单 | t_order | 订单信息 | id, order_no, user_id, amount |
| 订单明细 | t_order_item | 订单商品明细 | id, order_id, product_id, quantity |

### 2.3 关系说明
- User (1) --- (N) Order：一个用户可以有多个订单
- User (1) --- (N) Address：一个用户可以有多个收货地址
- Order (1) --- (N) OrderItem：一个订单可以有多个明细
- Product (1) --- (N) OrderItem：一个商品可以在多个订单明细中

## 三、表结构设计

### 3.1 用户表(t_user)

#### 表说明
存储用户的基本信息，包括登录信息、个人信息等。

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | 否 | - | 主键ID |
| username | VARCHAR(64) | 否 | - | 用户名 |
| password | VARCHAR(128) | 否 | - | 密码(加密) |
| nickname | VARCHAR(64) | 是 | NULL | 昵称 |
| phone | VARCHAR(20) | 是 | NULL | 手机号 |
| email | VARCHAR(64) | 是 | NULL | 邮箱 |
| avatar | VARCHAR(255) | 是 | NULL | 头像URL |
| gender | TINYINT | 否 | 0 | 性别：0未知，1男，2女 |
| birthday | DATE | 是 | NULL | 生日 |
| status | TINYINT | 否 | 1 | 状态：0禁用，1启用 |
| last_login_time | DATETIME | 是 | NULL | 最后登录时间 |
| last_login_ip | VARCHAR(64) | 是 | NULL | 最后登录IP |
| create_time | DATETIME | 否 | CURRENT_TIMESTAMP | 创建时间 |
| update_time | DATETIME | 否 | CURRENT_TIMESTAMP | 更新时间 |
| deleted | TINYINT | 否 | 0 | 删除标志：0未删除，1已删除 |

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|
| PRIMARY | 主键 | id | 主键索引 |
| uk_username | 唯一 | username | 用户名唯一 |
| uk_phone | 唯一 | phone | 手机号唯一 |
| uk_email | 唯一 | email | 邮箱唯一 |
| idx_create_time | 普通 | create_time | 创建时间索引 |

#### 建表SQL
```sql
CREATE TABLE `t_user` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `username` VARCHAR(64) NOT NULL COMMENT '用户名',
  `password` VARCHAR(128) NOT NULL COMMENT '密码(加密)',
  `nickname` VARCHAR(64) DEFAULT NULL COMMENT '昵称',
  `phone` VARCHAR(20) DEFAULT NULL COMMENT '手机号',
  `email` VARCHAR(64) DEFAULT NULL COMMENT '邮箱',
  `avatar` VARCHAR(255) DEFAULT NULL COMMENT '头像URL',
  `gender` TINYINT NOT NULL DEFAULT 0 COMMENT '性别：0未知，1男，2女',
  `birthday` DATE DEFAULT NULL COMMENT '生日',
  `status` TINYINT NOT NULL DEFAULT 1 COMMENT '状态：0禁用，1启用',
  `last_login_time` DATETIME DEFAULT NULL COMMENT '最后登录时间',
  `last_login_ip` VARCHAR(64) DEFAULT NULL COMMENT '最后登录IP',
  `create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_username` (`username`),
  UNIQUE KEY `uk_phone` (`phone`),
  UNIQUE KEY `uk_email` (`email`),
  KEY `idx_create_time` (`create_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='用户表';
```

---

### 3.2 商品表(t_product)

#### 表说明
存储商品的基本信息，包括商品名称、价格、库存等。

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | 否 | - | 主键ID |
| name | VARCHAR(128) | 否 | - | 商品名称 |
| category_id | BIGINT | 否 | - | 分类ID |
| brand_id | BIGINT | 是 | NULL | 品牌ID |
| main_image | VARCHAR(255) | 是 | NULL | 主图URL |
| images | TEXT | 是 | NULL | 图片列表(JSON) |
| description | TEXT | 是 | NULL | 商品描述 |
| price | DECIMAL(10,2) | 否 | - | 销售价 |
| original_price | DECIMAL(10,2) | 是 | NULL | 原价 |
| stock | INT | 否 | 0 | 库存 |
| sales | INT | 否 | 0 | 销量 |
| status | TINYINT | 否 | 1 | 状态：0下架，1上架 |
| sort | INT | 否 | 0 | 排序 |
| create_time | DATETIME | 否 | CURRENT_TIMESTAMP | 创建时间 |
| update_time | DATETIME | 否 | CURRENT_TIMESTAMP | 更新时间 |
| create_by | BIGINT | 否 | - | 创建人ID |
| update_by | BIGINT | 否 | - | 更新人ID |
| deleted | TINYINT | 否 | 0 | 删除标志 |

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|
| PRIMARY | 主键 | id | 主键索引 |
| idx_category_id | 普通 | category_id | 分类索引 |
| idx_brand_id | 普通 | brand_id | 品牌索引 |
| idx_status_sort | 组合 | status, sort | 状态排序索引 |
| idx_create_time | 普通 | create_time | 创建时间索引 |

#### 建表SQL
```sql
CREATE TABLE `t_product` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `name` VARCHAR(128) NOT NULL COMMENT '商品名称',
  `category_id` BIGINT NOT NULL COMMENT '分类ID',
  `brand_id` BIGINT DEFAULT NULL COMMENT '品牌ID',
  `main_image` VARCHAR(255) DEFAULT NULL COMMENT '主图URL',
  `images` TEXT DEFAULT NULL COMMENT '图片列表(JSON)',
  `description` TEXT DEFAULT NULL COMMENT '商品描述',
  `price` DECIMAL(10,2) NOT NULL COMMENT '销售价',
  `original_price` DECIMAL(10,2) DEFAULT NULL COMMENT '原价',
  `stock` INT NOT NULL DEFAULT 0 COMMENT '库存',
  `sales` INT NOT NULL DEFAULT 0 COMMENT '销量',
  `status` TINYINT NOT NULL DEFAULT 1 COMMENT '状态：0下架，1上架',
  `sort` INT NOT NULL DEFAULT 0 COMMENT '排序',
  `create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `create_by` BIGINT NOT NULL COMMENT '创建人ID',
  `update_by` BIGINT NOT NULL COMMENT '更新人ID',
  `deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除',
  PRIMARY KEY (`id`),
  KEY `idx_category_id` (`category_id`),
  KEY `idx_brand_id` (`brand_id`),
  KEY `idx_status_sort` (`status`, `sort`),
  KEY `idx_create_time` (`create_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='商品表';
```

---

### 3.3 收货地址表(t_address)

#### 表说明
存储用户的收货地址信息，支持多地址管理。

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | 否 | - | 主键ID |
| user_id | BIGINT | 否 | - | 用户ID |
| receiver_name | VARCHAR(64) | 否 | - | 收货人姓名 |
| receiver_phone | VARCHAR(20) | 否 | - | 收货人电话 |
| province | VARCHAR(32) | 否 | - | 省 |
| city | VARCHAR(32) | 否 | - | 市 |
| district | VARCHAR(32) | 否 | - | 区 |
| detail_address | VARCHAR(255) | 否 | - | 详细地址 |
| postal_code | VARCHAR(10) | 是 | NULL | 邮编 |
| is_default | TINYINT | 否 | 0 | 是否默认：0否，1是 |
| create_time | DATETIME | 否 | CURRENT_TIMESTAMP | 创建时间 |
| update_time | DATETIME | 否 | CURRENT_TIMESTAMP | 更新时间 |
| deleted | TINYINT | 否 | 0 | 删除标志 |

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|
| PRIMARY | 主键 | id | 主键索引 |
| idx_user_id | 普通 | user_id | 用户ID索引 |
| idx_user_default | 组合 | user_id, is_default | 用户默认地址索引 |

#### 建表SQL
```sql
CREATE TABLE `t_address` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `user_id` BIGINT NOT NULL COMMENT '用户ID',
  `receiver_name` VARCHAR(64) NOT NULL COMMENT '收货人姓名',
  `receiver_phone` VARCHAR(20) NOT NULL COMMENT '收货人电话',
  `province` VARCHAR(32) NOT NULL COMMENT '省',
  `city` VARCHAR(32) NOT NULL COMMENT '市',
  `district` VARCHAR(32) NOT NULL COMMENT '区',
  `detail_address` VARCHAR(255) NOT NULL COMMENT '详细地址',
  `postal_code` VARCHAR(10) DEFAULT NULL COMMENT '邮编',
  `is_default` TINYINT NOT NULL DEFAULT 0 COMMENT '是否默认：0否，1是',
  `create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除',
  PRIMARY KEY (`id`),
  KEY `idx_user_id` (`user_id`),
  KEY `idx_user_default` (`user_id`, `is_default`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='收货地址表';
```

---

### 3.4 订单表(t_order)

#### 表说明
存储订单的基本信息，包括订单号、用户、金额、状态等。考虑到数据量大，建议按用户ID分表。

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | 否 | - | 主键ID |
| order_no | VARCHAR(32) | 否 | - | 订单编号 |
| user_id | BIGINT | 否 | - | 用户ID |
| total_amount | DECIMAL(10,2) | 否 | - | 订单总金额 |
| discount_amount | DECIMAL(10,2) | 否 | 0.00 | 优惠金额 |
| freight_amount | DECIMAL(10,2) | 否 | 0.00 | 运费金额 |
| pay_amount | DECIMAL(10,2) | 否 | - | 实付金额 |
| status | TINYINT | 否 | 0 | 订单状态 |
| pay_type | TINYINT | 否 | 0 | 支付方式 |
| pay_time | DATETIME | 是 | NULL | 支付时间 |
| deliver_time | DATETIME | 是 | NULL | 发货时间 |
| receive_time | DATETIME | 是 | NULL | 收货时间 |
| cancel_time | DATETIME | 是 | NULL | 取消时间 |
| cancel_reason | VARCHAR(255) | 是 | NULL | 取消原因 |
| receiver_name | VARCHAR(64) | 否 | - | 收货人姓名 |
| receiver_phone | VARCHAR(20) | 否 | - | 收货人电话 |
| receiver_province | VARCHAR(32) | 否 | - | 收货省 |
| receiver_city | VARCHAR(32) | 否 | - | 收货市 |
| receiver_district | VARCHAR(32) | 否 | - | 收货区 |
| receiver_address | VARCHAR(255) | 否 | - | 收货详细地址 |
| remark | VARCHAR(255) | 是 | NULL | 备注 |
| create_time | DATETIME | 否 | CURRENT_TIMESTAMP | 创建时间 |
| update_time | DATETIME | 否 | CURRENT_TIMESTAMP | 更新时间 |
| create_by | BIGINT | 否 | - | 创建人ID |
| update_by | BIGINT | 否 | - | 更新人ID |
| deleted | TINYINT | 否 | 0 | 删除标志 |

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|
| PRIMARY | 主键 | id | 主键索引 |
| uk_order_no | 唯一 | order_no | 订单号唯一 |
| idx_user_id | 普通 | user_id | 用户ID索引 |
| idx_status | 普通 | status | 状态索引 |
| idx_create_time | 普通 | create_time | 创建时间索引 |
| idx_user_status | 组合 | user_id, status | 用户状态组合索引 |

#### 建表SQL
```sql
CREATE TABLE `t_order` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `order_no` VARCHAR(32) NOT NULL COMMENT '订单编号',
  `user_id` BIGINT NOT NULL COMMENT '用户ID',
  `total_amount` DECIMAL(10,2) NOT NULL COMMENT '订单总金额',
  `discount_amount` DECIMAL(10,2) NOT NULL DEFAULT 0.00 COMMENT '优惠金额',
  `freight_amount` DECIMAL(10,2) NOT NULL DEFAULT 0.00 COMMENT '运费金额',
  `pay_amount` DECIMAL(10,2) NOT NULL COMMENT '实付金额',
  `status` TINYINT NOT NULL DEFAULT 0 COMMENT '订单状态：0待支付，1已支付，2已发货，3已完成，4已取消',
  `pay_type` TINYINT NOT NULL DEFAULT 0 COMMENT '支付方式：0未支付，1支付宝，2微信',
  `pay_time` DATETIME DEFAULT NULL COMMENT '支付时间',
  `deliver_time` DATETIME DEFAULT NULL COMMENT '发货时间',
  `receive_time` DATETIME DEFAULT NULL COMMENT '收货时间',
  `cancel_time` DATETIME DEFAULT NULL COMMENT '取消时间',
  `cancel_reason` VARCHAR(255) DEFAULT NULL COMMENT '取消原因',
  `receiver_name` VARCHAR(64) NOT NULL COMMENT '收货人姓名',
  `receiver_phone` VARCHAR(20) NOT NULL COMMENT '收货人电话',
  `receiver_province` VARCHAR(32) NOT NULL COMMENT '收货省',
  `receiver_city` VARCHAR(32) NOT NULL COMMENT '收货市',
  `receiver_district` VARCHAR(32) NOT NULL COMMENT '收货区',
  `receiver_address` VARCHAR(255) NOT NULL COMMENT '收货详细地址',
  `remark` VARCHAR(255) DEFAULT NULL COMMENT '备注',
  `create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `create_by` BIGINT NOT NULL COMMENT '创建人ID',
  `update_by` BIGINT NOT NULL COMMENT '更新人ID',
  `deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_order_no` (`order_no`),
  KEY `idx_user_id` (`user_id`),
  KEY `idx_status` (`status`),
  KEY `idx_create_time` (`create_time`),
  KEY `idx_user_status` (`user_id`, `status`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='订单表';
```

---

### 3.5 订单明细表(t_order_item)

#### 表说明
存储订单的商品明细信息，包括商品、数量、价格等。考虑到数据量大，建议按订单ID分表。

#### 字段定义
| 字段名 | 类型 | 可空 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | 否 | - | 主键ID |
| order_id | BIGINT | 否 | - | 订单ID |
| order_no | VARCHAR(32) | 否 | - | 订单编号 |
| product_id | BIGINT | 否 | - | 商品ID |
| product_name | VARCHAR(128) | 否 | - | 商品名称 |
| product_image | VARCHAR(255) | 是 | NULL | 商品图片 |
| sku_id | BIGINT | 是 | NULL | SKU ID |
| sku_name | VARCHAR(128) | 是 | NULL | SKU名称 |
| price | DECIMAL(10,2) | 否 | - | 商品单价 |
| quantity | INT | 否 | - | 购买数量 |
| total_amount | DECIMAL(10,2) | 否 | - | 小计金额 |
| create_time | DATETIME | 否 | CURRENT_TIMESTAMP | 创建时间 |
| update_time | DATETIME | 否 | CURRENT_TIMESTAMP | 更新时间 |
| deleted | TINYINT | 否 | 0 | 删除标志 |

#### 索引定义
| 索引名 | 类型 | 字段 | 说明 |
|--------|------|------|------|
| PRIMARY | 主键 | id | 主键索引 |
| idx_order_id | 普通 | order_id | 订单ID索引 |
| idx_product_id | 普通 | product_id | 商品ID索引 |
| idx_order_no | 普通 | order_no | 订单号索引 |

#### 建表SQL
```sql
CREATE TABLE `t_order_item` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `order_id` BIGINT NOT NULL COMMENT '订单ID',
  `order_no` VARCHAR(32) NOT NULL COMMENT '订单编号',
  `product_id` BIGINT NOT NULL COMMENT '商品ID',
  `product_name` VARCHAR(128) NOT NULL COMMENT '商品名称',
  `product_image` VARCHAR(255) DEFAULT NULL COMMENT '商品图片',
  `sku_id` BIGINT DEFAULT NULL COMMENT 'SKU ID',
  `sku_name` VARCHAR(128) DEFAULT NULL COMMENT 'SKU名称',
  `price` DECIMAL(10,2) NOT NULL COMMENT '商品单价',
  `quantity` INT NOT NULL COMMENT '购买数量',
  `total_amount` DECIMAL(10,2) NOT NULL COMMENT '小计金额',
  `create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除',
  PRIMARY KEY (`id`),
  KEY `idx_order_id` (`order_id`),
  KEY `idx_product_id` (`product_id`),
  KEY `idx_order_no` (`order_no`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='订单明细表';
```

---

## 四、索引设计

### 4.1 索引策略

1. **主键索引**: 所有表使用雪花算法生成BIGINT主键
2. **唯一索引**: 订单号、用户名、手机号、邮箱等唯一字段
3. **外键索引**: user_id、order_id等关联字段
4. **查询索引**: 根据查询场景设计的索引
5. **组合索引**: 遵循最左匹配原则

### 4.2 索引列表
| 表名 | 索引名 | 类型 | 字段 | 说明 |
|------|--------|------|------|------|
| t_user | uk_username | 唯一 | username | 用户名唯一 |
| t_user | uk_phone | 唯一 | phone | 手机号唯一 |
| t_user | uk_email | 唯一 | email | 邮箱唯一 |
| t_product | idx_category_id | 普通 | category_id | 分类查询 |
| t_product | idx_status_sort | 组合 | status, sort | 列表排序 |
| t_order | uk_order_no | 唯一 | order_no | 订单号唯一 |
| t_order | idx_user_id | 普通 | user_id | 用户订单查询 |
| t_order | idx_user_status | 组合 | user_id, status | 用户订单状态查询 |
| t_order_item | idx_order_id | 普通 | order_id | 订单明细查询 |
| t_address | idx_user_id | 普通 | user_id | 用户地址查询 |

## 五、数据字典

### 5.1 枚举值定义

#### 用户性别(gender)
| 值 | 说明 |
|------|------|
| 0 | 未知 |
| 1 | 男 |
| 2 | 女 |

#### 用户状态(status)
| 值 | 说明 |
|------|------|
| 0 | 禁用 |
| 1 | 启用 |

#### 商品状态(status)
| 值 | 说明 |
|------|------|
| 0 | 下架 |
| 1 | 上架 |

#### 订单状态(status)
| 值 | 说明 |
|------|------|
| 0 | 待支付 |
| 1 | 已支付 |
| 2 | 已发货 |
| 3 | 已完成 |
| 4 | 已取消 |

#### 支付方式(pay_type)
| 值 | 说明 |
|------|------|
| 0 | 未支付 |
| 1 | 支付宝 |
| 2 | 微信 |

## 六、分库分表方案

### 6.1 分片策略

考虑到订单数据量大，建议采用分表策略：

| 表名 | 分表策略 | 分片键 | 分表数量 |
|------|----------|--------|----------|
| t_order | 按用户ID取模 | user_id | 16 |
| t_order_item | 按订单ID取模 | order_id | 16 |

### 6.2 分片键选择

1. **订单表(t_order)**
   - 分片键: user_id
   - 理由: 大部分查询都是按用户查询订单

2. **订单明细表(t_order_item)**
   - 分片键: order_id
   - 理由: 订单明细与订单关联，同一订单的明细在同一分表

### 6.3 分表命名

```sql
-- 订单表分表
t_order_0, t_order_1, ..., t_order_15

-- 订单明细表分表
t_order_item_0, t_order_item_1, ..., t_order_item_15
```

## 七、数据迁移方案

### 7.1 迁移步骤

1. 创建新表结构
2. 开启双写模式
3. 历史数据迁移
4. 数据一致性校验
5. 切换流量
6. 关闭双写

### 7.2 数据校验

```sql
-- 校验数据量
SELECT COUNT(*) FROM old_table;
SELECT COUNT(*) FROM new_table;

-- 校验数据一致性
SELECT * FROM old_table o
LEFT JOIN new_table n ON o.id = n.id
WHERE n.id IS NULL OR o.data != n.data;
```
```

---

## 变量说明

| 变量 | 说明 | 示例值 |
|------|------|--------|
| `{{entity_name}}` | 实体名称 | 订单 |
| `{{table_name}}` | 表名 | t_order |
| `{{database_name}}` | 数据库名 | order_db |

---

## 使用方式

在 Claude Code 中：

```
参考 ./prompt-assets/02-design/prompts/database-modeling.md

业务需求：
[描述业务需求]

核心实体：
[列出核心实体和属性]

数据量预估：
[预估数据量]
```

---

## 相关文档

- 数据库设计规范: `../templates/db-design-spec.md`
- API接口设计: `./api-interface-design.md`
- Spring Cloud模板: `../../04-coding/backend/templates/springcloud-boilerplate.md`