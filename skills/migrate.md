# 迁移指南

本文档提供 DataSphere 项目的代码迁移指南，帮助开发者安全、高效地进行系统迁移。

## 迁移类型

| 迁移类型 | 说明 | 风险等级 |
|----------|------|----------|
| 技术栈迁移 | 框架版本升级、技术替换 | 高 |
| 架构迁移 | 单体到微服务、模块拆分 | 高 |
| 数据迁移 | 数据库迁移、数据结构变更 | 中 |
| 接口迁移 | API 版本升级、接口重构 | 中 |
| 组件迁移 | 前端组件库升级 | 低 |

## 迁移流程

```
1. 评估阶段 - 分析迁移范围和风险
2. 规划阶段 - 制定迁移计划和回滚方案
3. 准备阶段 - 搭建环境、准备工具
4. 执行阶段 - 分阶段迁移
5. 验证阶段 - 测试验证
6. 清理阶段 - 清理旧代码
```

## 技术栈迁移

### Spring Boot 版本升级

#### 2.x 到 3.x 迁移要点

| 变更项 | Spring Boot 2.x | Spring Boot 3.x |
|--------|-----------------|-----------------|
| JDK 版本 | JDK 8/11 | JDK 17+ |
| Jakarta EE | javax.* | jakarta.* |
| Spring Security | 旧配置方式 | 新配置方式 |

#### 迁移步骤

```bash
# 1. 更新 pom.xml 中的 Spring Boot 版本
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.11</version>
</parent>

# 2. 替换 javax 为 jakarta
# 全局搜索替换 javax.* -> jakarta.*

# 3. 更新依赖版本
mvn versions:display-dependency-updates

# 4. 运行测试
mvn test

# 5. 解决编译错误
```

#### 常见问题

**问题1**: javax.servlet 找不到
```java
// 旧代码
import javax.servlet.http.HttpServletRequest;

// 新代码
import jakarta.servlet.http.HttpServletRequest;
```

**问题2**: Spring Security 配置方式变更
```java
// 旧配置
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/public/**").permitAll();
    }
}

// 新配置
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
        );
        return http.build();
    }
}
```

### Vue 2 到 Vue 3 迁移

#### 迁移要点

| 变更项 | Vue 2 | Vue 3 |
|--------|-------|-------|
| API 风格 | Options API | Composition API |
| 组件注册 | Vue.component | app.component |
| 生命周期 | mounted | onMounted |
| 响应式 | data() | ref/reactive |

#### 迁移步骤

```bash
# 1. 更新 package.json
npm install vue@3 vue-router@4 pinia

# 2. 使用迁移构建版本
npm install @vue/compat

# 3. 配置兼容模式
# vite.config.js
export default {
  resolve: {
    alias: {
      vue: '@vue/compat'
    }
  }
}

# 4. 逐步迁移组件
# 5. 移除兼容模式
```

#### 组件迁移示例

**Options API 到 Composition API**

```vue
<!-- Vue 2 Options API -->
<script>
export default {
  data() {
    return {
      count: 0,
      user: null
    }
  },
  computed: {
    doubleCount() {
      return this.count * 2
    }
  },
  mounted() {
    this.fetchUser()
  },
  methods: {
    fetchUser() {
      // ...
    },
    increment() {
      this.count++
    }
  }
}
</script>

<!-- Vue 3 Composition API -->
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

const count = ref(0)
const user = ref(null)

const doubleCount = computed(() => count.value * 2)

const fetchUser = () => {
  // ...
}

const increment = () => {
  count.value++
}

onMounted(() => {
  fetchUser()
})
</script>
```

## 数据库迁移

### Flyway 迁移

#### 迁移脚本命名规范

```
V{版本号}__{描述}.sql

示例:
V1.0.0__init_schema.sql
V1.0.1__add_user_table.sql
V1.0.2__add_order_table.sql
```

#### 迁移脚本示例

```sql
-- V1.0.0__init_schema.sql
CREATE TABLE `user` (
  `id` varchar(32) NOT NULL COMMENT '主键ID',
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(100) NOT NULL COMMENT '密码',
  `status` char(1) DEFAULT '1' COMMENT '状态',
  `create_by` varchar(32) COMMENT '创建人',
  `create_time` datetime COMMENT '创建时间',
  `update_by` varchar(32) COMMENT '更新人',
  `update_time` datetime COMMENT '更新时间',
  `delete_flag` char(1) DEFAULT '0' COMMENT '删除标识',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

#### 执行迁移

```bash
# 查看迁移状态
mvn flyway:info

# 执行迁移
mvn flyway:migrate

# 回滚（需要 Flyway Teams 版本）
mvn flyway:undo
```

### 数据迁移脚本

```sql
-- 数据迁移脚本示例
-- 迁移用户数据从旧表到新表

INSERT INTO `user_new` (id, username, email, status, create_time)
SELECT
    id,
    username,
    email,
    CASE WHEN is_active = 1 THEN '1' ELSE '0' END,
    created_at
FROM `user_old`
WHERE deleted_at IS NULL;

-- 验证迁移结果
SELECT COUNT(*) FROM `user_old` WHERE deleted_at IS NULL;
SELECT COUNT(*) FROM `user_new`;
```

## API 版本迁移

### API 版本策略

```
/api/v1/users  -> 旧版本
/api/v2/users  -> 新版本

策略:
1. 保留旧版本一段时间
2. 新版本并行运行
3. 逐步迁移客户端
4. 废弃旧版本
```

### 兼容性处理

```java
// 旧版本 API
@RestController
@RequestMapping("/api/v1/users")
public class UserV1Controller {
    @GetMapping
    public List<UserV1> list() {
        return userService.listV1();
    }
}

// 新版本 API
@RestController
@RequestMapping("/api/v2/users")
public class UserV2Controller {
    @GetMapping
    public PageResult<UserV2> page(PageParams params) {
        return userService.pageV2(params);
    }
}

// 版本适配器
public class UserAdapter {
    public UserV2 toV2(UserV1 v1) {
        UserV2 v2 = new UserV2();
        // 属性映射
        return v2;
    }
}
```

## 微前端迁移

### 模块迁移步骤

```bash
# 1. 创建新的子应用
cd datasphere/apps
pnpm create vite new-app --template vue-ts

# 2. 配置微前端
# 修改 src/main.ts
import { setupMicroApp } from '@datasphere/micro-app'

setupMicroApp({
  name: 'new-app',
  entry: '/new-app/'
})

# 3. 迁移业务代码
# 从旧应用逐步迁移

# 4. 配置路由
# portal 应用中配置子应用路由
```

### 状态迁移

```typescript
// 从 Vuex 迁移到 Pinia

// Vuex (旧)
const store = new Vuex.Store({
  state: {
    user: null
  },
  mutations: {
    SET_USER(state, user) {
      state.user = user
    }
  },
  actions: {
    fetchUser({ commit }) {
      return api.getUser().then(user => {
        commit('SET_USER', user)
      })
    }
  }
})

// Pinia (新)
export const useUserStore = defineStore('user', {
  state: () => ({
    user: null
  }),
  actions: {
    async fetchUser() {
      this.user = await api.getUser()
    }
  }
})
```

## 迁移检查清单

```markdown
## 迁移前准备

- [ ] 备份数据/代码
- [ ] 制定回滚方案
- [ ] 通知相关团队
- [ ] 准备迁移环境

## 迁移执行

- [ ] 按计划执行迁移
- [ ] 监控迁移进度
- [ ] 记录迁移日志
- [ ] 处理迁移错误

## 迁移验证

- [ ] 功能测试通过
- [ ] 性能测试通过
- [ ] 数据一致性验证
- [ ] 用户验收测试

## 迁移完成

- [ ] 清理旧代码/数据
- [ ] 更新文档
- [ ] 通知相关团队
- [ ] 归档迁移记录
```

## 回滚方案

### 代码回滚

```bash
# Git 回滚
git revert HEAD
git push origin main

# 回滚到指定版本
git reset --hard <commit-hash>
git push origin main --force
```

### 数据库回滚

```sql
-- 执行回滚脚本
-- V1.0.1__rollback.sql
DROP TABLE IF EXISTS `new_table`;
ALTER TABLE `user` DROP COLUMN `new_column`;
```

### 配置回滚

```yaml
# Kubernetes 回滚
kubectl rollout undo deployment/app-name

# Docker 回滚
docker service rollback service-name
```

## 注意事项

1. **充分测试**: 迁移前在测试环境充分验证
2. **数据备份**: 迁移前备份所有数据
3. **分批迁移**: 大规模迁移分批进行
4. **监控告警**: 迁移过程中加强监控
5. **文档更新**: 迁移完成后更新文档