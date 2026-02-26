# 数据库设计规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义数据库设计的统一规范，确保数据库结构的一致性和可维护性。

---

## 一、设计原则

### 1.1 核心原则

| 原则 | 说明 |
|------|------|
| **规范化** | 遵循数据库范式，减少数据冗余 |
| **适度反规范化** | 合理冗余，提升查询性能 |
| **命名一致** | 统一命名规范，易于理解 |
| **可扩展性** | 预留扩展字段，应对需求变化 |

### 1.2 设计目标

- **数据一致性**: 通过约束保证数据完整性
- **查询性能**: 合理设计索引，优化查询
- **可维护性**: 结构清晰，便于维护
- **安全性**: 敏感数据加密，权限控制

---

## 二、命名规范

### 2.1 表命名规范

| 规则 | 说明 | 示例 |
|------|------|------|
| 小写字母 | 表名全部小写 | `order` ✅ `Order` ❌ |
| 下划线分隔 | 多个单词用下划线 | `order_item` ✅ `orderitem` ❌ |
| 前缀标识 | 业务模块前缀 | `t_order`, `t_user` |
| 单数形式 | 表名使用单数 | `user` ✅ `users` ❌ |
| 禁止关键字 | 避免保留字 | `order` ❌ `t_order` ✅ |

**表名格式**: `t_{模块}_{实体}`

```sql
-- 推荐命名
t_user                -- 用户表
t_order               -- 订单表
t_order_item          -- 订单明细表
t_product             -- 商品表
t_product_category    -- 商品分类表

-- 不推荐命名
User                  -- 大写
orders                -- 复数
orderItem             -- 驼峰
item                  -- 无前缀
```

### 2.2 字段命名规范

| 规则 | 说明 | 示例 |
|------|------|------|
| 小写字母 | 字段名全部小写 | `user_name` ✅ `userName` ❌ |
| 下划线分隔 | 多个单词用下划线 | `create_time` ✅ `createtime` ❌ |
| 语义清晰 | 望文知义 | `order_amount` ✅ `amount` ❌ |
| 布尔类型 | is_ 前缀 | `is_deleted`, `is_enabled` |
| 时间类型 | _time 或 _date 后缀 | `create_time`, `birth_date` |

### 2.3 索引命名规范

| 类型 | 命名格式 | 示例 |
|------|----------|------|
| 主键 | `pk_{表名}` | `pk_order` |
| 唯一索引 | `uk_{字段名}` | `uk_order_no` |
| 普通索引 | `idx_{字段名}` | `idx_user_id` |
| 组合索引 | `idx_{字段1}_{字段2}` | `idx_user_status` |

```sql
-- 索引示例
PRIMARY KEY (`id`)                          -- 主键
UNIQUE KEY `uk_order_no` (`order_no`)       -- 唯一索引
KEY `idx_user_id` (`user_id`)               -- 普通索引
KEY `idx_user_status` (`user_id`, `status`) -- 组合索引
```

---

## 三、字段规范

### 3.1 主键规范

| 规范 | 说明 |
|------|------|
| 类型 | BIGINT |
| 生成策略 | 雪花算法（推荐）/ 自增 |
| 字段名 | id |

```sql
`id` BIGINT NOT NULL COMMENT '主键ID',
PRIMARY KEY (`id`)
```

### 3.2 必备字段

每个表必须包含以下字段：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | BIGINT | 主键ID |
| create_time | DATETIME | 创建时间 |
| update_time | DATETIME | 更新时间 |
| create_by | BIGINT | 创建人ID |
| update_by | BIGINT | 更新人ID |
| deleted | TINYINT | 删除标志（0未删除，1已删除） |

### 3.3 字段类型选择

#### 数值类型

| 类型 | 范围 | 用途 |
|------|------|------|
| TINYINT | -128 ~ 127 | 状态、标志 |
| SMALLINT | -32768 ~ 32767 | 计数、数量 |
| INT | -21亿 ~ 21亿 | 大计数 |
| BIGINT | -922亿亿 ~ 922亿亿 | 主键、ID |
| DECIMAL(p,s) | 精确小数 | 金额、百分比 |

#### 字符串类型

| 类型 | 最大长度 | 用途 |
|------|----------|------|
| CHAR(n) | 255 | 定长字符串（如手机号） |
| VARCHAR(n) | 65535 | 变长字符串 |
| TEXT | 65535 | 长文本 |
| LONGTEXT | 4GB | 超长文本 |

#### 时间类型

| 类型 | 格式 | 用途 |
|------|------|------|
| DATE | YYYY-MM-DD | 日期 |
| TIME | HH:MM:SS | 时间 |
| DATETIME | YYYY-MM-DD HH:MM:SS | 日期时间 |
| TIMESTAMP | 时间戳 | 时间戳（自动更新） |

### 3.4 字段默认值

| 字段类型 | 默认值 | 说明 |
|----------|--------|------|
| 字符串 | 空字符串 | 不使用NULL |
| 数值 | 0 | 不使用NULL |
| 布尔 | 0 | false |
| 时间 | 当前时间 | CURRENT_TIMESTAMP |

```sql
`status` TINYINT NOT NULL DEFAULT 0 COMMENT '状态：0禁用，1启用',
`create_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
`update_time` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
`deleted` TINYINT NOT NULL DEFAULT 0 COMMENT '删除标志：0未删除，1已删除'
```

---

## 四、索引规范

### 4.1 索引原则

| 原则 | 说明 |
|------|------|
| 合理创建 | 根据查询条件创建索引 |
| 避免过多 | 单表索引不超过5个 |
| 组合优先 | 组合索引优于多个单列索引 |
| 最左匹配 | 组合索引遵循最左匹配原则 |

### 4.2 索引场景

#### 需要创建索引的场景

```sql
-- WHERE条件字段
SELECT * FROM t_order WHERE user_id = ?;
-- 创建索引
KEY `idx_user_id` (`user_id`)

-- JOIN关联字段
SELECT * FROM t_order o JOIN t_user u ON o.user_id = u.id;
-- 创建索引
KEY `idx_user_id` (`user_id`)

-- ORDER BY排序字段
SELECT * FROM t_order ORDER BY create_time DESC;
-- 创建索引
KEY `idx_create_time` (`create_time`)

-- 组合查询
SELECT * FROM t_order WHERE user_id = ? AND status = ?;
-- 创建组合索引
KEY `idx_user_status` (`user_id`, `status`)
```

#### 不需要创建索引的场景

- 数据量小的表（<1000行）
- 频繁更新的字段
- 区分度低的字段（如性别）
- 很少查询的字段

### 4.3 索引优化

```sql
-- 覆盖索引（避免回表）
SELECT user_id, status FROM t_order WHERE user_id = ?;
KEY `idx_user_status` (`user_id`, `status`)

-- 索引下推
SELECT * FROM t_order WHERE user_id = ? AND status = ?;
KEY `idx_user_status` (`user_id`, `status`)

-- 索引顺序
-- 区分度高的字段放前面
-- 等值查询字段放前面
-- 范围查询字段放后面
KEY `idx_user_status_create_time` (`user_id`, `status`, `create_time`)
```

---

## 五、表设计示例

### 5.1 订单表

```sql
CREATE TABLE `t_order` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `order_no` VARCHAR(32) NOT NULL COMMENT '订单编号',
  `user_id` BIGINT NOT NULL COMMENT '用户ID',
  `total_amount` DECIMAL(10,2) NOT NULL COMMENT '订单总金额',
  `pay_amount` DECIMAL(10,2) NOT NULL COMMENT '实付金额',
  `freight_amount` DECIMAL(10,2) NOT NULL DEFAULT 0.00 COMMENT '运费金额',
  `discount_amount` DECIMAL(10,2) NOT NULL DEFAULT 0.00 COMMENT '优惠金额',
  `status` TINYINT NOT NULL DEFAULT 0 COMMENT '订单状态：0待支付，1已支付，2已发货，3已完成，4已取消',
  `pay_type` TINYINT NOT NULL DEFAULT 0 COMMENT '支付方式：0未支付，1支付宝，2微信',
  `pay_time` DATETIME DEFAULT NULL COMMENT '支付时间',
  `deliver_time` DATETIME DEFAULT NULL COMMENT '发货时间',
  `receive_time` DATETIME DEFAULT NULL COMMENT '收货时间',
  `cancel_time` DATETIME DEFAULT NULL COMMENT '取消时间',
  `receiver_name` VARCHAR(64) NOT NULL COMMENT '收货人姓名',
  `receiver_phone` VARCHAR(20) NOT NULL COMMENT '收货人电话',
  `receiver_address` VARCHAR(255) NOT NULL COMMENT '收货地址',
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
  KEY `idx_create_time` (`create_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='订单表';
```

### 5.2 订单明细表

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
  KEY `idx_product_id` (`product_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='订单明细表';
```

### 5.3 用户表

```sql
CREATE TABLE `t_user` (
  `id` BIGINT NOT NULL COMMENT '主键ID',
  `username` VARCHAR(64) NOT NULL COMMENT '用户名',
  `password` VARCHAR(128) NOT NULL COMMENT '密码（加密）',
  `nickname` VARCHAR(64) DEFAULT NULL COMMENT '昵称',
  `email` VARCHAR(64) DEFAULT NULL COMMENT '邮箱',
  `phone` VARCHAR(20) DEFAULT NULL COMMENT '手机号',
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
  UNIQUE KEY `uk_email` (`email`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='用户表';
```

---

## 六、分库分表规范

### 6.1 分库策略

| 策略 | 说明 | 适用场景 |
|------|------|----------|
| 垂直分库 | 按业务拆分 | 业务独立、耦合度低 |
| 水平分库 | 按数据拆分 | 单库数据量大 |

### 6.2 分表策略

| 策略 | 说明 | 适用场景 |
|------|------|----------|
| 垂直分表 | 字段拆分 | 大字段、冷热数据 |
| 水平分表 | 数据拆分 | 单表数据量大 |

### 6.3 分片键选择

| 选择原则 | 说明 |
|----------|------|
| 数据均匀 | 数据分布均匀 |
| 查询高效 | 满足大部分查询 |
| 扩展性好 | 便于后续扩展 |

**常用分片键**:
- 用户ID
- 订单ID
- 时间

### 6.4 分表命名

```sql
-- 按用户ID分表（16张表）
t_order_0
t_order_1
...
t_order_15

-- 按时间分表（按月）
t_order_202601
t_order_202602
...
```

---

## 七、数据安全

### 7.1 敏感字段加密

| 字段 | 加密方式 | 说明 |
|------|----------|------|
| 密码 | BCrypt | 单向加密，不可逆 |
| 手机号 | AES | 可逆加密，脱敏展示 |
| 身份证 | AES | 可逆加密，脱敏展示 |
| 银行卡 | AES | 可逆加密，脱敏展示 |

### 7.2 数据备份

| 备份类型 | 频率 | 保留时间 |
|----------|------|----------|
| 全量备份 | 每天 | 30天 |
| 增量备份 | 每小时 | 7天 |
| Binlog | 实时 | 7天 |

---

## 八、性能优化

### 8.1 查询优化

```sql
-- 避免 SELECT *
SELECT id, order_no, user_id FROM t_order WHERE id = ?;

-- 使用索引覆盖
SELECT user_id, status FROM t_order WHERE user_id = ?;

-- 避免 OR 条件
-- 不推荐
SELECT * FROM t_order WHERE user_id = ? OR status = ?;
-- 推荐
SELECT * FROM t_order WHERE user_id = ?
UNION
SELECT * FROM t_order WHERE status = ?;

-- 使用 LIMIT
SELECT * FROM t_order WHERE user_id = ? LIMIT 20;

-- 避免 LIKE 前置通配符
-- 不推荐
SELECT * FROM t_user WHERE name LIKE '%张%';
-- 推荐
SELECT * FROM t_user WHERE name LIKE '张%';
```

### 8.2 表结构优化

```sql
-- 选择合适的存储引擎
ENGINE=InnoDB  -- 支持事务
ENGINE=MyISAM  -- 不支持事务，读取快

-- 选择合适的字符集
CHARSET=utf8mb4  -- 支持emoji
CHARSET=utf8     -- 不支持emoji

-- 选择合适的排序规则
COLLATE=utf8mb4_unicode_ci  -- 不区分大小写
COLLATE=utf8mb4_bin         -- 区分大小写
```

---

## 相关文档

- API设计规范: `./api-design-spec.md`
- 数据库建模Prompt: `../prompts/database-modeling.md`
- Spring Cloud模板: `../../04-coding/backend/templates/springcloud-boilerplate.md`