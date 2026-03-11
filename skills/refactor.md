# 重构指南

本文档提供 DataSphere 项目的代码重构指南，帮助开发者安全、有效地进行代码重构。

## 重构原则

### 何时重构

| 信号 | 说明 |
|------|------|
| 重复代码 | 相同逻辑出现多次 |
| 过长方法 | 方法超过 50 行 |
| 过大类 | 类承担过多职责 |
| 过长参数列表 | 参数超过 5 个 |
| 发散式变化 | 一个类因多种原因变化 |
| 霰弹式修改 | 一个变化导致多处修改 |
| 依恋情结 | 方法过度依赖其他类的数据 |

### 重构时机

- **添加新功能前**: 先重构使新功能更容易添加
- **修复 Bug 时**: 顺手优化相关代码
- **代码审查时**: 根据审查意见重构
- **理解代码时**: 通过重构加深理解

### 重构原则

1. **小步前进**: 每次只做一个小改动
2. **测试保障**: 重构前确保有测试覆盖
3. **持续集成**: 频繁提交，避免大爆炸式修改
4. **保持行为**: 重构不改变代码外部行为

## 后端重构模式

### 提取方法

**问题**: 方法过长或代码重复

**重构前**:
```java
public void processOrder(Order order) {
    // 验证订单
    if (order.getItems() == null || order.getItems().isEmpty()) {
        throw new IllegalArgumentException("订单项不能为空");
    }
    if (order.getTotalAmount() == null || order.getTotalAmount().compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("订单金额无效");
    }

    // 计算折扣
    BigDecimal discount = BigDecimal.ZERO;
    if (order.getCustomer().getLevel() == CustomerLevel.VIP) {
        discount = order.getTotalAmount().multiply(new BigDecimal("0.1"));
    } else if (order.getCustomer().getLevel() == CustomerLevel.GOLD) {
        discount = order.getTotalAmount().multiply(new BigDecimal("0.05"));
    }

    // 保存订单
    order.setDiscount(discount);
    order.setFinalAmount(order.getTotalAmount().subtract(discount));
    orderRepository.save(order);
}
```

**重构后**:
```java
public void processOrder(Order order) {
    validateOrder(order);
    BigDecimal discount = calculateDiscount(order);
    saveOrderWithDiscount(order, discount);
}

private void validateOrder(Order order) {
    if (order.getItems() == null || order.getItems().isEmpty()) {
        throw new IllegalArgumentException("订单项不能为空");
    }
    if (order.getTotalAmount() == null || order.getTotalAmount().compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("订单金额无效");
    }
}

private BigDecimal calculateDiscount(Order order) {
    return switch (order.getCustomer().getLevel()) {
        case VIP -> order.getTotalAmount().multiply(new BigDecimal("0.1"));
        case GOLD -> order.getTotalAmount().multiply(new BigDecimal("0.05"));
        default -> BigDecimal.ZERO;
    };
}

private void saveOrderWithDiscount(Order order, BigDecimal discount) {
    order.setDiscount(discount);
    order.setFinalAmount(order.getTotalAmount().subtract(discount));
    orderRepository.save(order);
}
```

### 提取接口

**问题**: 类依赖具体实现，难以测试和扩展

**重构前**:
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    public User findByUsername(String username) {
        return userRepository.findByUsername(username);
    }
}
```

**重构后**:
```java
public interface IUserRepository {
    User findByUsername(String username);
}

@Service
public class UserService {
    private final IUserRepository userRepository;

    public UserService(IUserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User findByUsername(String username) {
        return userRepository.findByUsername(username);
    }
}
```

### 以多态取代条件表达式

**问题**: 大量条件判断，难以维护

**重构前**:
```java
public BigDecimal calculatePrice(Product product, String customerType) {
    if ("VIP".equals(customerType)) {
        return product.getPrice().multiply(new BigDecimal("0.9"));
    } else if ("GOLD".equals(customerType)) {
        return product.getPrice().multiply(new BigDecimal("0.95"));
    } else {
        return product.getPrice();
    }
}
```

**重构后**:
```java
public interface PriceStrategy {
    BigDecimal calculate(Product product);
}

@Component("VIP")
public class VipPriceStrategy implements PriceStrategy {
    @Override
    public BigDecimal calculate(Product product) {
        return product.getPrice().multiply(new BigDecimal("0.9"));
    }
}

@Component("GOLD")
public class GoldPriceStrategy implements PriceStrategy {
    @Override
    public BigDecimal calculate(Product product) {
        return product.getPrice().multiply(new BigDecimal("0.95"));
    }
}

@Component("NORMAL")
public class NormalPriceStrategy implements PriceStrategy {
    @Override
    public BigDecimal calculate(Product product) {
        return product.getPrice();
    }
}

@Service
public class PriceService {
    private final Map<String, PriceStrategy> strategies;

    public BigDecimal calculatePrice(Product product, String customerType) {
        return strategies.get(customerType).calculate(product);
    }
}
```

## 前端重构模式

### 提取组合式函数

**问题**: 组件逻辑复杂，难以复用

**重构前**:
```vue
<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'

const users = ref<User[]>([])
const loading = ref(false)
const page = ref({ current: 1, size: 10, total: 0 })
const searchParams = ref({})

const fetchUsers = async () => {
  loading.value = true
  try {
    const result = await userApi.page({
      ...searchParams.value,
      current: page.value.current,
      size: page.value.size
    })
    users.value = result.records
    page.value.total = result.total
  } finally {
    loading.value = false
  }
}

const handleSearch = (params: any) => {
  searchParams.value = params
  page.value.current = 1
  fetchUsers()
}

const handlePageChange = (current: number) => {
  page.value.current = current
  fetchUsers()
}

onMounted(() => {
  fetchUsers()
})
</script>
```

**重构后**:
```vue
<script setup lang="ts">
import { useUserList } from './composables/useUserList'

const {
  users,
  loading,
  page,
  fetchUsers,
  handleSearch,
  handlePageChange
} = useUserList()
</script>
```

```typescript
// composables/useUserList.ts
import { ref, onMounted } from 'vue'
import { UserApi } from '@/api'

export function useUserList() {
  const userApi = new UserApi()
  const users = ref<User[]>([])
  const loading = ref(false)
  const page = ref({ current: 1, size: 10, total: 0 })
  const searchParams = ref({})

  const fetchUsers = async () => {
    loading.value = true
    try {
      const result = await userApi.page({
        ...searchParams.value,
        current: page.value.current,
        size: page.value.size
      })
      users.value = result.records
      page.value.total = result.total
    } finally {
      loading.value = false
    }
  }

  const handleSearch = (params: any) => {
    searchParams.value = params
    page.value.current = 1
    fetchUsers()
  }

  const handlePageChange = (current: number) => {
    page.value.current = current
    fetchUsers()
  }

  onMounted(() => {
    fetchUsers()
  })

  return {
    users,
    loading,
    page,
    fetchUsers,
    handleSearch,
    handlePageChange
  }
}
```

### 提取组件

**问题**: 组件过大，职责不清

**重构前**:
```vue
<template>
  <div class="user-management">
    <div class="search-form">
      <el-input v-model="searchForm.name" placeholder="姓名" />
      <el-select v-model="searchForm.status" placeholder="状态">
        <el-option label="有效" value="1" />
        <el-option label="无效" value="0" />
      </el-select>
      <el-button @click="handleSearch">搜索</el-button>
    </div>

    <el-table :data="users">
      <!-- 表格列 -->
    </el-table>

    <el-pagination
      :current-page="page.current"
      :page-size="page.size"
      :total="page.total"
      @current-change="handlePageChange"
    />
  </div>
</template>
```

**重构后**:
```vue
<template>
  <div class="user-management">
    <UserSearchForm @search="handleSearch" />
    <UserTable :data="users" :loading="loading" />
    <UserPagination :page="page" @change="handlePageChange" />
  </div>
</template>
```

## 重构步骤

### 1. 准备阶段

```bash
# 1. 确保代码可编译/运行
pnpm build
pnpm test

# 2. 创建功能分支
git checkout -b refactor/xxx

# 3. 确保有测试覆盖
pnpm test:coverage
```

### 2. 重构阶段

```bash
# 1. 小步重构
# 每完成一个小改动就提交

git add .
git commit -m "refactor: 提取xxx方法"

# 2. 频繁运行测试
pnpm test

# 3. 保持代码可运行
```

### 3. 验证阶段

```bash
# 1. 运行完整测试
pnpm test

# 2. 代码审查
# 提交 Merge Request

# 3. 合并代码
```

## 重构清单

```markdown
## 重构前检查

- [ ] 代码有测试覆盖
- [ ] 理解代码当前行为
- [ ] 有明确的重构目标
- [ ] 创建功能分支

## 重构过程

- [ ] 小步前进
- [ ] 频繁运行测试
- [ ] 频繁提交
- [ ] 保持代码可运行

## 重构后检查

- [ ] 所有测试通过
- [ ] 代码更清晰
- [ ] 没有引入新 Bug
- [ ] 更新相关文档
```

## 注意事项

1. **不要混合重构和功能开发**: 分开提交
2. **不要一次重构太多**: 小步前进
3. **不要忽视测试**: 测试是重构的安全网
4. **不要过度设计**: 只解决当前问题