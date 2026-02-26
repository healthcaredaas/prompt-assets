# 状态管理规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义Pinia状态管理的规范和最佳实践。

---

## 一、Store结构

### 1.1 目录结构

```
stores/
├── index.ts          # Store入口
├── user.ts           # 用户状态
├── app.ts            # 应用状态
├── permission.ts     # 权限状态
└── modules/          # 业务模块
    ├── order.ts
    └── product.ts
```

### 1.2 Store模板

```typescript
// stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { userApi } from '@/api/modules/user'
import type { UserInfo, LoginParams } from '@/types/user'

export const useUserStore = defineStore('user', () => {
  // 状态
  const token = ref<string>('')
  const userInfo = ref<UserInfo | null>(null)

  // 计算属性
  const isLoggedIn = computed(() => !!token.value)
  const userName = computed(() => userInfo.value?.name || '')
  const userId = computed(() => userInfo.value?.id || 0)

  // 方法
  const login = async (params: LoginParams) => {
    const res = await userApi.login(params)
    token.value = res.token
    userInfo.value = res.userInfo
    return res
  }

  const logout = () => {
    token.value = ''
    userInfo.value = null
  }

  const getUserInfo = async () => {
    const res = await userApi.getUserInfo()
    userInfo.value = res
    return res
  }

  const updateUserInfo = async (params: Partial<UserInfo>) => {
    const res = await userApi.updateUserInfo(params)
    userInfo.value = res
    return res
  }

  // 持久化
  const init = () => {
    const storedToken = localStorage.getItem('token')
    if (storedToken) {
      token.value = storedToken
    }
  }

  return {
    // 状态
    token,
    userInfo,
    // 计算属性
    isLoggedIn,
    userName,
    userId,
    // 方法
    login,
    logout,
    getUserInfo,
    updateUserInfo,
    init
  }
})
```

---

## 二、状态类型

### 2.1 简单状态

```typescript
// 简单值类型
const count = ref(0)
const name = ref('')
const isActive = ref(false)

// 对象类型
const user = ref<UserInfo | null>(null)

// 数组类型
const list = ref<UserInfo[]>([])
```

### 2.2 计算属性

```typescript
// 简单计算
const doubleCount = computed(() => count.value * 2)

// 条件计算
const isAdmin = computed(() => {
  return userInfo.value?.role === 'admin'
})

// 列表过滤
const activeList = computed(() => {
  return list.value.filter(item => item.active)
})
```

---

## 三、状态持久化

### 3.1 手动持久化

```typescript
export const useUserStore = defineStore('user', () => {
  const token = ref('')

  // 初始化时从localStorage读取
  const init = () => {
    const storedToken = localStorage.getItem('token')
    if (storedToken) {
      token.value = storedToken
    }
  }

  // 更新时保存到localStorage
  const setToken = (newToken: string) => {
    token.value = newToken
    localStorage.setItem('token', newToken)
  }

  // 清除时删除localStorage
  const clearToken = () => {
    token.value = ''
    localStorage.removeItem('token')
  }

  return {
    token,
    init,
    setToken,
    clearToken
  }
})
```

### 3.2 使用pinia-plugin-persistedstate

```typescript
// stores/user.ts
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    token: '',
    userInfo: null as UserInfo | null
  }),
  persist: {
    key: 'user',
    storage: localStorage,
    paths: ['token', 'userInfo']
  }
})
```

---

## 四、状态组合

### 4.1 组合多个Store

```typescript
// 在一个Store中使用另一个Store
export const useOrderStore = defineStore('order', () => {
  const userStore = useUserStore()

  const createOrder = async (params: OrderParams) => {
    // 使用userStore中的数据
    if (!userStore.isLoggedIn) {
      throw new Error('请先登录')
    }

    const res = await orderApi.create({
      ...params,
      userId: userStore.userId
    })
    return res
  }

  return {
    createOrder
  }
})
```

### 4.2 组合式函数

```typescript
// composables/useAuth.ts
import { useUserStore } from '@/stores/user'
import { usePermissionStore } from '@/stores/permission'

export function useAuth() {
  const userStore = useUserStore()
  const permissionStore = usePermissionStore()

  const login = async (params: LoginParams) => {
    const res = await userStore.login(params)
    await permissionStore.getPermissions()
    return res
  }

  const logout = () => {
    userStore.logout()
    permissionStore.clearPermissions()
  }

  return {
    login,
    logout
  }
}
```

---

## 五、最佳实践

### 5.1 命名规范

```typescript
// 状态：名词
const user = ref<User | null>(null)
const isLoading = ref(false)

// 计算属性：名词或形容词
const userName = computed(() => user.value?.name || '')
const isLoggedIn = computed(() => !!token.value)

// 方法：动词开头
const fetchUser = async () => { }
const updateUser = async (params: Partial<User>) => { }
const clearUser = () => { }
```

### 5.2 异步处理

```typescript
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)

  const fetchUser = async (id: number) => {
    loading.value = true
    error.value = null
    try {
      const res = await userApi.getUser(id)
      user.value = res
      return res
    } catch (e) {
      error.value = e as Error
      throw e
    } finally {
      loading.value = false
    }
  }

  return {
    user,
    loading,
    error,
    fetchUser
  }
})
```

### 5.3 重置状态

```typescript
export const useUserStore = defineStore('user', () => {
  const initialState = {
    token: '',
    userInfo: null as UserInfo | null
  }

  const token = ref(initialState.token)
  const userInfo = ref(initialState.userInfo)

  const reset = () => {
    token.value = initialState.token
    userInfo.value = initialState.userInfo
  }

  return {
    token,
    userInfo,
    reset
  }
})
```

### 5.4 订阅状态变化

```typescript
// 订阅整个Store
const userStore = useUserStore()
userStore.$subscribe((mutation, state) => {
  console.log('状态变化:', mutation.type)
  console.log('新状态:', state)
})

// 订阅特定状态
watch(() => userStore.token, (newToken) => {
  if (newToken) {
    // 登录成功
  } else {
    // 退出登录
  }
})
```

---

## 六、示例

### 6.1 用户状态

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const token = ref('')
  const userInfo = ref<UserInfo | null>(null)

  const isLoggedIn = computed(() => !!token.value)
  const userName = computed(() => userInfo.value?.name || '')
  const userAvatar = computed(() => userInfo.value?.avatar || '/default-avatar.png')

  const login = async (params: LoginParams) => {
    const res = await userApi.login(params)
    token.value = res.token
    userInfo.value = res.userInfo
    localStorage.setItem('token', res.token)
    return res
  }

  const logout = async () => {
    await userApi.logout()
    token.value = ''
    userInfo.value = null
    localStorage.removeItem('token')
  }

  const getUserInfo = async () => {
    const res = await userApi.getUserInfo()
    userInfo.value = res
    return res
  }

  const init = () => {
    const storedToken = localStorage.getItem('token')
    if (storedToken) {
      token.value = storedToken
      getUserInfo()
    }
  }

  return {
    token,
    userInfo,
    isLoggedIn,
    userName,
    userAvatar,
    login,
    logout,
    getUserInfo,
    init
  }
})
```

### 6.2 应用状态

```typescript
// stores/app.ts
export const useAppStore = defineStore('app', () => {
  const sidebar = ref({
    collapsed: false
  })

  const device = ref<'desktop' | 'mobile'>('desktop')

  const theme = ref<'light' | 'dark'>('light')

  const toggleSidebar = () => {
    sidebar.value.collapsed = !sidebar.value.collapsed
  }

  const toggleTheme = () => {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
    document.documentElement.setAttribute('data-theme', theme.value)
  }

  const setDevice = (newDevice: 'desktop' | 'mobile') => {
    device.value = newDevice
    if (newDevice === 'mobile') {
      sidebar.value.collapsed = true
    }
  }

  return {
    sidebar,
    device,
    theme,
    toggleSidebar,
    toggleTheme,
    setDevice
  }
})
```