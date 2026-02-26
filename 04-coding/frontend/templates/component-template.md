# Vue组件模板

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义Vue 3组件的标准模板。

---

## 一、基础组件模板

```vue
<template>
  <div class="{{component-name}}">
    <!-- 组件内容 -->
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

// Props
interface Props {
  modelValue?: string
  disabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: '',
  disabled: false
})

// Emits
const emit = defineEmits<{
  'update:modelValue': [value: string]
  'change': [value: string]
}>()

// State
const internalValue = ref(props.modelValue)

// Computed
const displayValue = computed(() => {
  return internalValue.value
})

// Watch
watch(() => props.modelValue, (val) => {
  internalValue.value = val
})

// Methods
const handleChange = (value: string) => {
  internalValue.value = value
  emit('update:modelValue', value)
  emit('change', value)
}

// Lifecycle
onMounted(() => {
  // 初始化
})
</script>

<style scoped>
.{{component-name}} {
  /* 样式 */
}
</style>
```

---

## 二、表单组件模板

```vue
<template>
  <el-form
    ref="formRef"
    :model="formData"
    :rules="formRules"
    :label-width="labelWidth"
    :disabled="disabled"
  >
    <el-form-item label="名称" prop="name">
      <el-input v-model="formData.name" placeholder="请输入名称" />
    </el-form-item>

    <el-form-item label="状态" prop="status">
      <el-select v-model="formData.status" placeholder="请选择状态">
        <el-option label="启用" :value="1" />
        <el-option label="禁用" :value="0" />
      </el-select>
    </el-form-item>

    <el-form-item label="描述" prop="description">
      <el-input
        v-model="formData.description"
        type="textarea"
        :rows="3"
        placeholder="请输入描述"
      />
    </el-form-item>

    <el-form-item>
      <el-button type="primary" @click="handleSubmit" :loading="loading">
        提交
      </el-button>
      <el-button @click="handleReset">重置</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus'

// Props
interface Props {
  modelValue?: any
  disabled?: boolean
  labelWidth?: string
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: null,
  disabled: false,
  labelWidth: '100px'
})

// Emits
const emit = defineEmits<{
  'update:modelValue': [value: any]
  'submit': [value: any]
}>()

// Refs
const formRef = ref<FormInstance>()
const loading = ref(false)

// Form Data
const formData = reactive({
  name: '',
  status: 1,
  description: ''
})

// Form Rules
const formRules: FormRules = {
  name: [
    { required: true, message: '请输入名称', trigger: 'blur' },
    { min: 2, max: 50, message: '长度在2到50个字符', trigger: 'blur' }
  ],
  status: [
    { required: true, message: '请选择状态', trigger: 'change' }
  ]
}

// Methods
const handleSubmit = async () => {
  if (!formRef.value) return

  await formRef.value.validate(async (valid) => {
    if (!valid) return

    loading.value = true
    try {
      emit('submit', { ...formData })
      ElMessage.success('提交成功')
    } finally {
      loading.value = false
    }
  })
}

const handleReset = () => {
  formRef.value?.resetFields()
}

// Expose
defineExpose({
  validate: () => formRef.value?.validate(),
  resetFields: () => formRef.value?.resetFields()
})
</script>

<style scoped>
/* 样式 */
</style>
```

---

## 三、表格组件模板

```vue
<template>
  <div class="table-container">
    <!-- 搜索栏 -->
    <div class="search-bar" v-if="showSearch">
      <el-form :model="searchForm" inline>
        <el-form-item label="关键词">
          <el-input
            v-model="searchForm.keyword"
            placeholder="请输入关键词"
            clearable
            @keyup.enter="handleSearch"
          />
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="searchForm.status" placeholder="请选择状态" clearable>
            <el-option label="启用" :value="1" />
            <el-option label="禁用" :value="0" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">搜索</el-button>
          <el-button @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>

    <!-- 工具栏 -->
    <div class="toolbar">
      <el-button type="primary" @click="handleAdd" v-if="showAdd">
        <el-icon><Plus /></el-icon>
        新增
      </el-button>
      <el-button @click="handleRefresh">
        <el-icon><Refresh /></el-icon>
        刷新
      </el-button>
    </div>

    <!-- 表格 -->
    <el-table
      :data="tableData"
      :loading="loading"
      border
      stripe
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column prop="id" label="ID" width="80" />
      <el-table-column prop="name" label="名称" min-width="120" />
      <el-table-column prop="status" label="状态" width="100">
        <template #default="{ row }">
          <el-tag :type="row.status === 1 ? 'success' : 'danger'">
            {{ row.status === 1 ? '启用' : '禁用' }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" label="创建时间" width="180" />
      <el-table-column label="操作" width="200" fixed="right">
        <template #default="{ row }">
          <el-button type="primary" link @click="handleEdit(row)">编辑</el-button>
          <el-button type="danger" link @click="handleDelete(row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
    <el-pagination
      v-model:current-page="page"
      v-model:page-size="size"
      :total="total"
      :page-sizes="[10, 20, 50, 100]"
      layout="total, sizes, prev, pager, next, jumper"
      @size-change="handleSizeChange"
      @current-change="handlePageChange"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import { Plus, Refresh } from '@element-plus/icons-vue'
import { ElMessage, ElMessageBox } from 'element-plus'

// Props
interface Props {
  showSearch?: boolean
  showAdd?: boolean
}

withDefaults(defineProps<Props>(), {
  showSearch: true,
  showAdd: true
})

// Emits
const emit = defineEmits<{
  'add': []
  'edit': [row: any]
  'delete': [row: any]
}>()

// State
const loading = ref(false)
const tableData = ref<any[]>([])
const total = ref(0)
const page = ref(1)
const size = ref(20)

const searchForm = reactive({
  keyword: '',
  status: undefined as number | undefined
})

const selectedRows = ref<any[]>([])

// Methods
const fetchData = async () => {
  loading.value = true
  try {
    // 调用API获取数据
    // const res = await api.getList({
    //   page: page.value,
    //   size: size.value,
    //   ...searchForm
    // })
    // tableData.value = res.list
    // total.value = res.total
  } finally {
    loading.value = false
  }
}

const handleSearch = () => {
  page.value = 1
  fetchData()
}

const handleReset = () => {
  searchForm.keyword = ''
  searchForm.status = undefined
  handleSearch()
}

const handleRefresh = () => {
  fetchData()
}

const handleAdd = () => {
  emit('add')
}

const handleEdit = (row: any) => {
  emit('edit', row)
}

const handleDelete = (row: any) => {
  ElMessageBox.confirm('确定要删除吗？', '提示', {
    type: 'warning'
  }).then(async () => {
    // await api.delete(row.id)
    ElMessage.success('删除成功')
    fetchData()
  })
}

const handleSelectionChange = (rows: any[]) => {
  selectedRows.value = rows
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

// Lifecycle
onMounted(() => {
  fetchData()
})
</script>

<style scoped>
.table-container {
  padding: 16px;
}

.search-bar {
  margin-bottom: 16px;
}

.toolbar {
  margin-bottom: 16px;
}

.el-pagination {
  margin-top: 16px;
  justify-content: flex-end;
}
</style>
```

---

## 四、对话框组件模板

```vue
<template>
  <el-dialog
    v-model="visible"
    :title="title"
    :width="width"
    :close-on-click-modal="false"
    @close="handleClose"
  >
    <el-form
      ref="formRef"
      :model="formData"
      :rules="formRules"
      label-width="100px"
    >
      <el-form-item label="名称" prop="name">
        <el-input v-model="formData.name" placeholder="请输入名称" />
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-radio-group v-model="formData.status">
          <el-radio :label="1">启用</el-radio>
          <el-radio :label="0">禁用</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>

    <template #footer>
      <el-button @click="handleClose">取消</el-button>
      <el-button type="primary" @click="handleSubmit" :loading="loading">
        确定
      </el-button>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref, reactive, computed } from 'vue'
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus'

// Props
interface Props {
  modelValue: boolean
  title?: string
  width?: string
  data?: any
}

const props = withDefaults(defineProps<Props>(), {
  title: '编辑',
  width: '500px',
  data: null
})

// Emits
const emit = defineEmits<{
  'update:modelValue': [value: boolean]
  'submit': [value: any]
}>()

// State
const visible = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val)
})

const formRef = ref<FormInstance>()
const loading = ref(false)

const formData = reactive({
  name: '',
  status: 1
})

const formRules: FormRules = {
  name: [
    { required: true, message: '请输入名称', trigger: 'blur' }
  ]
}

// Methods
const handleSubmit = async () => {
  if (!formRef.value) return

  await formRef.value.validate(async (valid) => {
    if (!valid) return

    loading.value = true
    try {
      emit('submit', { ...formData })
      ElMessage.success('操作成功')
      handleClose()
    } finally {
      loading.value = false
    }
  })
}

const handleClose = () => {
  formRef.value?.resetFields()
  visible.value = false
}
</script>

<style scoped>
/* 样式 */
</style>
```