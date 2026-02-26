# 页面开发助手

## 元信息
- **版本**: v1.0
- **适用角色**: 前端开发工程师
- **输入要求**: 页面需求描述
- **输出格式**: Vue页面代码

---

## 角色设定

你是一个资深的Vue前端开发专家，擅长构建企业级管理页面。

---

## 任务描述

根据需求开发完整的Vue页面，包含列表、搜索、表单等功能。

---

## 示例

### 输入
```
需求：用户管理页面
- 用户列表（表格）
- 搜索功能
- 新增/编辑用户（弹窗表单）
- 删除用户
```

### 输出

```vue
<template>
  <div class="user-management">
    <!-- 搜索 -->
    <el-form :model="searchForm" inline class="search-form">
      <el-form-item label="用户名">
        <el-input v-model="searchForm.username" placeholder="请输入用户名" clearable />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSearch">搜索</el-button>
        <el-button @click="handleReset">重置</el-button>
      </el-form-item>
    </el-form>

    <!-- 工具栏 -->
    <div class="toolbar">
      <el-button type="primary" @click="handleAdd">新增用户</el-button>
    </div>

    <!-- 表格 -->
    <el-table :data="tableData" v-loading="loading" border stripe>
      <el-table-column prop="id" label="ID" width="80" />
      <el-table-column prop="username" label="用户名" />
      <el-table-column prop="nickname" label="昵称" />
      <el-table-column prop="status" label="状态">
        <template #default="{ row }">
          <el-tag :type="row.status === 1 ? 'success' : 'danger'">
            {{ row.status === 1 ? '启用' : '禁用' }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="150">
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
      layout="total, prev, pager, next"
      @current-change="fetchData"
    />

    <!-- 弹窗 -->
    <el-dialog v-model="dialogVisible" :title="dialogTitle" width="500px">
      <el-form ref="formRef" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username" :disabled="isEdit" />
        </el-form-item>
        <el-form-item label="昵称" prop="nickname">
          <el-input v-model="form.nickname" />
        </el-form-item>
        <el-form-item label="状态" prop="status">
          <el-switch v-model="form.status" :active-value="1" :inactive-value="0" />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit" :loading="submitting">确定</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import type { FormInstance, FormRules } from 'element-plus'
import { userApi } from '@/api/modules/user'

// 搜索
const searchForm = reactive({ username: '' })
const handleSearch = () => { page.value = 1; fetchData() }
const handleReset = () => { searchForm.username = ''; handleSearch() }

// 列表
const loading = ref(false)
const tableData = ref<any[]>([])
const page = ref(1)
const size = ref(20)
const total = ref(0)

const fetchData = async () => {
  loading.value = true
  try {
    const res = await userApi.list({ ...searchForm, page: page.value, size: size.value })
    tableData.value = res.list
    total.value = res.total
  } finally {
    loading.value = false
  }
}

// 弹窗
const dialogVisible = ref(false)
const isEdit = ref(false)
const dialogTitle = computed(() => isEdit.value ? '编辑用户' : '新增用户')
const formRef = ref<FormInstance>()
const submitting = ref(false)
const form = reactive({ id: 0, username: '', nickname: '', status: 1 })
const rules: FormRules = {
  username: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
  nickname: [{ required: true, message: '请输入昵称', trigger: 'blur' }]
}

const handleAdd = () => {
  isEdit.value = false
  Object.assign(form, { id: 0, username: '', nickname: '', status: 1 })
  dialogVisible.value = true
}

const handleEdit = (row: any) => {
  isEdit.value = true
  Object.assign(form, row)
  dialogVisible.value = true
}

const handleSubmit = async () => {
  await formRef.value?.validate()
  submitting.value = true
  try {
    if (isEdit.value) {
      await userApi.update(form.id, form)
    } else {
      await userApi.create(form)
    }
    ElMessage.success('操作成功')
    dialogVisible.value = false
    fetchData()
  } finally {
    submitting.value = false
  }
}

// 删除
const handleDelete = (row: any) => {
  ElMessageBox.confirm('确定删除该用户吗？', '提示', { type: 'warning' })
    .then(async () => {
      await userApi.delete(row.id)
      ElMessage.success('删除成功')
      fetchData()
    })
}

// 初始化
fetchData()
</script>

<style scoped>
.user-management {
  padding: 20px;
}
.search-form {
  margin-bottom: 20px;
}
.toolbar {
  margin-bottom: 20px;
}
</style>
```

---

## 使用方式

在 Claude Code 中：
```
参考 ./prompt-assets/04-coding/frontend/prompts/page-development.md
[描述页面需求]
```