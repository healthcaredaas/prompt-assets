# Vue代码规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义Vue 3前端代码规范，基于Vue官方风格指南。

---

## 一、项目结构

```
src/
├── api/                  # API接口
│   ├── modules/         # 按模块划分
│   │   ├── user.ts
│   │   └── order.ts
│   └── request.ts       # Axios封装
├── assets/              # 静态资源
│   ├── images/
│   └── styles/
├── components/          # 公共组件
│   ├── common/          # 通用组件
│   └── business/        # 业务组件
├── composables/         # 组合式函数
├── directives/          # 自定义指令
├── hooks/               # 自定义Hooks
├── layouts/             # 布局组件
├── router/              # 路由配置
├── stores/              # 状态管理
├── styles/              # 样式文件
├── types/               # 类型定义
├── utils/               # 工具函数
├── views/               # 页面组件
├── App.vue              # 根组件
└── main.ts              # 入口文件
```

---

## 二、命名规范

### 2.1 组件命名

- 使用PascalCase命名
- 组件文件名使用PascalCase

```vue
<!-- 推荐 -->
<MyComponent />
<script setup lang="ts">
import MyComponent from './MyComponent.vue'
</script>

<!-- 不推荐 -->
<my-component />
<script setup lang="ts">
import myComponent from './my-component.vue'
</script>
```

### 2.2 变量命名

- 使用camelCase命名
- 布尔类型使用is/has前缀
- 常量使用UPPER_SNAKE_CASE

```typescript
// 推荐
const userName = ref('')
const isVisible = ref(false)
const MAX_COUNT = 100

// 不推荐
const user_name = ref('')
const visible = ref(false)
const maxCount = 100
```

### 2.3 事件命名

- 使用kebab-case命名
- 使用动词+名词形式

```vue
<!-- 推荐 -->
<template>
  <button @click="handleSubmit">提交</button>
</template>

<script setup lang="ts">
const emit = defineEmits<{
  'update:modelValue': [value: string]
  'submit-success': [data: any]
}>()
</script>

<!-- 不推荐 -->
<template>
  <button @click="handleSubmit">提交</button>
</template>

<script setup lang="ts">
const emit = defineEmits<{
  'updateModelValue': [value: string]
  'submitSuccess': [data: any]
}>()
</script>
```

---

## 三、组件规范

### 3.1 组件模板

```vue
<template>
  <div class="component-name">
    <!-- 模板内容 -->
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { PropType } from 'vue'

// Props定义
interface Props {
  title: string
  modelValue?: string
  options?: Array<{ label: string; value: string }>
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: '',
  options: () => []
})

// Emits定义
const emit = defineEmits<{
  'update:modelValue': [value: string]
  'change': [value: string]
}>()

// 响应式状态
const loading = ref(false)
const data = ref<any[]>([])

// 计算属性
const filteredData = computed(() => {
  return data.value.filter(item => item.active)
})

// 方法
const handleClick = () => {
  emit('change', props.modelValue)
}

// 生命周期
onMounted(() => {
  fetchData()
})

// 异步方法
const fetchData = async () => {
  loading.value = true
  try {
    const res = await api.getData()
    data.value = res.data
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.component-name {
  /* 组件样式 */
}
</style>
```

### 3.2 Props规范

```typescript
// 推荐：使用TypeScript接口
interface Props {
  title: string
  count?: number
  options?: Array<{ label: string; value: string }>
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
  options: () => []
})

// 不推荐：使用运行时声明
const props = defineProps({
  title: String,
  count: {
    type: Number,
    default: 0
  }
})
```

### 3.3 Emits规范

```typescript
// 推荐：使用TypeScript类型
const emit = defineEmits<{
  'update:modelValue': [value: string]
  'change': [value: string, event: Event]
}>()

// 不推荐：使用运行时声明
const emit = defineEmits(['update:modelValue', 'change'])
```

---

## 四、状态管理

### 4.1 Pinia Store模板

```typescript
// stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  // 状态
  const token = ref<string>('')
  const userInfo = ref<UserInfo | null>(null)

  // 计算属性
  const isLoggedIn = computed(() => !!token.value)
  const userName = computed(() => userInfo.value?.name || '')

  // 方法
  const login = async (username: string, password: string) => {
    const res = await userApi.login({ username, password })
    token.value = res.token
    userInfo.value = res.userInfo
  }

  const logout = () => {
    token.value = ''
    userInfo.value = null
  }

  return {
    token,
    userInfo,
    isLoggedIn,
    userName,
    login,
    logout
  }
})
```

### 4.2 Store使用规范

```vue
<script setup lang="ts">
import { useUserStore } from '@/stores/user'

const userStore = useUserStore()

// 读取状态
const isLoggedIn = computed(() => userStore.isLoggedIn)
const userName = computed(() => userStore.userName)

// 调用方法
const handleLogin = async () => {
  await userStore.login(username.value, password.value)
}
</script>
```

---

## 五、API规范

### 5.1 API封装

```typescript
// api/request.ts
import axios from 'axios'
import type { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios'
import { useUserStore } from '@/stores/user'
import { ElMessage } from 'element-plus'

const request: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000
})

// 请求拦截器
request.interceptors.request.use(
  (config) => {
    const userStore = useUserStore()
    if (userStore.token) {
      config.headers.Authorization = `Bearer ${userStore.token}`
    }
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

// 响应拦截器
request.interceptors.response.use(
  (response: AxiosResponse) => {
    const { code, message, data } = response.data
    if (code === 200) {
      return data
    }
    ElMessage.error(message || '请求失败')
    return Promise.reject(new Error(message || '请求失败'))
  },
  (error) => {
    const { response } = error
    if (response?.status === 401) {
      const userStore = useUserStore()
      userStore.logout()
      window.location.href = '/login'
    }
    ElMessage.error(response?.data?.message || '网络错误')
    return Promise.reject(error)
  }
)

export default request
```

### 5.2 API模块

```typescript
// api/modules/user.ts
import request from '../request'
import type { LoginParams, LoginResult, UserInfo } from '@/types/user'

export const userApi = {
  login: (params: LoginParams) =>
    request.post<LoginResult>('/api/v1/users/login', params),

  logout: () =>
    request.post('/api/v1/users/logout'),

  getUserInfo: () =>
    request.get<UserInfo>('/api/v1/users/info'),

  updateUserInfo: (params: Partial<UserInfo>) =>
    request.put<UserInfo>('/api/v1/users/info', params)
}
```

---

## 六、样式规范

### 6.1 样式组织

```vue
<template>
  <div class="user-list">
    <div class="user-list-header">
      <h3 class="title">用户列表</h3>
    </div>
    <div class="user-list-content">
      <!-- 内容 -->
    </div>
  </div>
</template>

<style scoped>
.user-list {
  padding: 16px;
}

.user-list-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.user-list-header .title {
  font-size: 16px;
  font-weight: 500;
}

.user-list-content {
  /* 样式 */
}
</style>
```

### 6.2 CSS变量

```css
/* styles/variables.css */
:root {
  --color-primary: #409eff;
  --color-success: #67c23a;
  --color-warning: #e6a23c;
  --color-danger: #f56c6c;
  --color-info: #909399;

  --text-primary: #303133;
  --text-regular: #606266;
  --text-secondary: #909399;

  --border-color: #dcdfe6;
  --background-color: #f2f6fc;
}
```

---

## 七、最佳实践

### 7.1 组合式函数

```typescript
// composables/useTable.ts
import { ref, computed } from 'vue'

export function useTable<T>(api: (params: any) => Promise<any>) {
  const loading = ref(false)
  const data = ref<T[]>([])
  const total = ref(0)
  const page = ref(1)
  const size = ref(20)

  const fetchData = async (params?: any) => {
    loading.value = true
    try {
      const res = await api({
        page: page.value,
        size: size.value,
        ...params
      })
      data.value = res.list
      total.value = res.total
    } finally {
      loading.value = false
    }
  }

  const handlePageChange = (newPage: number) => {
    page.value = newPage
    fetchData()
  }

  const handleSizeChange = (newSize: number) => {
    size.value = newSize
    page.value = 1
    fetchData()
  }

  return {
    loading,
    data,
    total,
    page,
    size,
    fetchData,
    handlePageChange,
    handleSizeChange
  }
}
```

### 7.2 类型定义

```typescript
// types/user.ts
export interface UserInfo {
  id: number
  username: string
  nickname: string
  email: string
  phone: string
  avatar: string
  status: number
  createTime: string
}

export interface LoginParams {
  username: string
  password: string
  captcha?: string
}

export interface LoginResult {
  token: string
  userInfo: UserInfo
}
```