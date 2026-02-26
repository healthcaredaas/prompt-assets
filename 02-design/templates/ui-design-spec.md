# UI设计规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义前端UI设计的统一规范，确保界面风格一致性。

---

## 一、设计原则

### 1.1 核心原则

| 原则 | 说明 |
|------|------|
| **一致性** | 相同功能使用相同的交互方式 |
| **简洁性** | 减少视觉干扰，突出核心内容 |
| **可用性** | 优先考虑用户体验 |
| **响应式** | 适配多种屏幕尺寸 |

### 1.2 设计目标

- **用户目标**: 高效完成任务，减少认知负担
- **业务目标**: 提升转化率，降低用户流失
- **技术目标**: 组件复用，降低开发成本

---

## 二、颜色规范

### 2.1 主色板

| 颜色名称 | 色值 | 用途 |
|----------|------|------|
| 主色 | #409EFF | 主要按钮、链接、强调元素 |
| 成功色 | #67C23A | 成功状态、正确提示 |
| 警告色 | #E6A23C | 警告状态、注意提示 |
| 危险色 | #F56C6C | 错误状态、危险操作 |
| 信息色 | #909399 | 信息提示、次要文本 |

### 2.2 中性色板

| 颜色名称 | 色值 | 用途 |
|----------|------|------|
| 主文本 | #303133 | 主要文本、标题 |
| 常规文本 | #606266 | 正文、段落 |
| 次要文本 | #909399 | 辅助信息、占位符 |
| 禁用文本 | #C0C4CC | 禁用状态文本 |
| 边框色 | #DCDFE6 | 边框、分割线 |
| 背景色 | #F2F6FC | 页面背景 |

### 2.3 颜色使用规范

```css
/* 推荐使用CSS变量 */
:root {
  --color-primary: #409EFF;
  --color-success: #67C23A;
  --color-warning: #E6A23C;
  --color-danger: #F56C6C;
  --color-info: #909399;

  --text-primary: #303133;
  --text-regular: #606266;
  --text-secondary: #909399;
  --text-placeholder: #C0C4CC;

  --border-color: #DCDFE6;
  --background-color: #F2F6FC;
}
```

---

## 三、字体规范

### 3.1 字体家族

```css
/* 中文字体 */
font-family: "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif;

/* 英文字体 */
font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;

/* 代码字体 */
font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
```

### 3.2 字号规范

| 级别 | 字号 | 行高 | 用途 |
|------|------|------|------|
| H1 | 24px | 32px | 页面标题 |
| H2 | 20px | 28px | 区块标题 |
| H3 | 18px | 26px | 小节标题 |
| H4 | 16px | 24px | 卡片标题 |
| Body | 14px | 22px | 正文内容 |
| Small | 12px | 20px | 辅助信息 |
| Mini | 10px | 18px | 极小文字 |

### 3.3 字重规范

| 字重 | 数值 | 用途 |
|------|------|------|
| 正常 | 400 | 正文内容 |
| 中等 | 500 | 强调内容 |
| 粗体 | 700 | 标题、重要信息 |

---

## 四、间距规范

### 4.1 基础间距

基于 4px 网格系统：

| 变量 | 数值 | 用途 |
|------|------|------|
| spacing-xs | 4px | 极小间距 |
| spacing-sm | 8px | 小间距 |
| spacing-md | 16px | 中等间距 |
| spacing-lg | 24px | 大间距 |
| spacing-xl | 32px | 极大间距 |

### 4.2 内边距规范

```css
/* 按钮内边距 */
.btn-sm { padding: 8px 16px; }
.btn-md { padding: 12px 20px; }
.btn-lg { padding: 16px 24px; }

/* 卡片内边距 */
.card { padding: 16px; }
.card-lg { padding: 24px; }

/* 表单项内边距 */
.form-item { padding: 16px 0; }
```

### 4.3 外边距规范

```css
/* 区块间距 */
.section { margin-bottom: 24px; }

/* 列表项间距 */
.list-item { margin-bottom: 16px; }

/* 组件间距 */
.component { margin-bottom: 16px; }
```

---

## 五、布局规范

### 5.1 栅格系统

使用24栅格系统：

```css
/* 栅格容器 */
.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 16px;
}

/* 栅格行 */
.row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -8px;
}

/* 栅格列 */
.col-6 { flex: 0 0 25%; padding: 0 8px; }
.col-8 { flex: 0 0 33.33%; padding: 0 8px; }
.col-12 { flex: 0 0 50%; padding: 0 8px; }
.col-24 { flex: 0 0 100%; padding: 0 8px; }
```

### 5.2 页面布局

```
┌────────────────────────────────────────┐
│                 Header                 │
├────────────────────────────────────────┤
│         │                              │
│  Side   │          Content            │
│  Bar    │                              │
│         │                              │
├────────────────────────────────────────┤
│                 Footer                 │
└────────────────────────────────────────┘
```

### 5.3 响应式断点

| 设备类型 | 断点 | 容器宽度 |
|----------|------|----------|
| 手机 | < 576px | 100% |
| 平板 | ≥ 576px | 540px |
| 小屏电脑 | ≥ 768px | 720px |
| 中屏电脑 | ≥ 992px | 960px |
| 大屏电脑 | ≥ 1200px | 1140px |
| 超大屏 | ≥ 1920px | 1800px |

---

## 六、组件规范

### 6.1 按钮规范

#### 按钮类型

| 类型 | 样式 | 用途 |
|------|------|------|
| 主要按钮 | 蓝色背景 | 主要操作 |
| 成功按钮 | 绿色背景 | 成功操作 |
| 警告按钮 | 橙色背景 | 警告操作 |
| 危险按钮 | 红色背景 | 危险操作 |
| 默认按钮 | 白色背景边框 | 次要操作 |
| 文字按钮 | 无背景 | 链接跳转 |

#### 按钮尺寸

| 尺寸 | 高度 | 内边距 | 字号 |
|------|------|--------|------|
| Small | 28px | 8px 16px | 12px |
| Medium | 36px | 12px 20px | 14px |
| Large | 44px | 16px 24px | 16px |

```vue
<template>
  <button class="btn btn-primary btn-md">
    主要按钮
  </button>
</template>
```

### 6.2 表单规范

#### 输入框

| 状态 | 边框颜色 | 说明 |
|------|----------|------|
| 默认 | #DCDFE6 | 正常状态 |
| 聚焦 | #409EFF | 输入状态 |
| 禁用 | #E4E7ED | 禁用状态 |
| 错误 | #F56C6C | 校验失败 |
| 成功 | #67C23A | 校验成功 |

#### 输入框尺寸

| 尺寸 | 高度 | 字号 |
|------|------|------|
| Small | 28px | 12px |
| Medium | 36px | 14px |
| Large | 44px | 16px |

```vue
<template>
  <div class="form-item">
    <label class="form-label">用户名</label>
    <input class="input input-md" placeholder="请输入用户名" />
    <span class="form-error">用户名不能为空</span>
  </div>
</template>
```

### 6.3 表格规范

```vue
<template>
  <table class="table">
    <thead>
      <tr>
        <th>列标题</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>数据内容</td>
      </tr>
    </tbody>
  </table>
</template>

<style>
.table {
  width: 100%;
  border-collapse: collapse;
}

.table th {
  background: #F5F7FA;
  padding: 12px 16px;
  text-align: left;
  font-weight: 500;
  border-bottom: 1px solid #EBEEF5;
}

.table td {
  padding: 12px 16px;
  border-bottom: 1px solid #EBEEF5;
}

.table tr:hover {
  background: #F5F7FA;
}
</style>
```

### 6.4 卡片规范

```vue
<template>
  <div class="card">
    <div class="card-header">
      <span class="card-title">卡片标题</span>
    </div>
    <div class="card-body">
      卡片内容
    </div>
    <div class="card-footer">
      <button class="btn btn-primary btn-sm">操作</button>
    </div>
  </div>
</template>

<style>
.card {
  background: #FFFFFF;
  border-radius: 4px;
  border: 1px solid #EBEEF5;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.card-header {
  padding: 16px 20px;
  border-bottom: 1px solid #EBEEF5;
}

.card-title {
  font-size: 16px;
  font-weight: 500;
}

.card-body {
  padding: 20px;
}

.card-footer {
  padding: 16px 20px;
  border-top: 1px solid #EBEEF5;
  text-align: right;
}
</style>
```

---

## 七、交互规范

### 7.1 状态反馈

| 状态 | 反馈方式 |
|------|----------|
| 加载中 | Loading动画、骨架屏 |
| 成功 | 成功提示、绿色勾选 |
| 失败 | 错误提示、红色警告 |
| 空状态 | 空状态插图、引导文案 |

### 7.2 操作确认

| 操作类型 | 确认方式 |
|----------|----------|
| 删除 | 二次确认弹窗 |
| 批量操作 | 二次确认弹窗 |
| 重要修改 | 二次确认弹窗 |
| 普通操作 | Toast提示 |

### 7.3 加载状态

```vue
<template>
  <!-- 按钮加载 -->
  <button class="btn btn-primary" :disabled="loading">
    <i v-if="loading" class="loading-icon"></i>
    <span>{{ loading ? '提交中...' : '提交' }}</span>
  </button>

  <!-- 页面加载 -->
  <div v-loading="loading" class="page-container">
    <!-- 内容 -->
  </div>
</template>
```

---

## 八、动效规范

### 8.1 过渡时间

| 类型 | 时长 | 用途 |
|------|------|------|
| 快速 | 100ms | 按钮状态变化 |
| 常规 | 200ms | 弹窗显示隐藏 |
| 慢速 | 300ms | 页面切换 |

### 8.2 缓动函数

```css
/* 常用缓动函数 */
.ease-in-out { transition-timing-function: ease-in-out; }
.ease-out { transition-timing-function: ease-out; }
.linear { transition-timing-function: linear; }

/* 示例 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 200ms ease-in-out;
}
```

---

## 九、图标规范

### 9.1 图标尺寸

| 尺寸 | 数值 | 用途 |
|------|------|------|
| 小 | 12px | 内联图标 |
| 中 | 16px | 按钮图标 |
| 大 | 24px | 导航图标 |
| 超大 | 32px | 功能图标 |

### 9.2 图标库

推荐使用：
- Element Plus Icons
- Font Awesome
- 自定义SVG图标

---

## 十、暗黑模式

### 10.1 暗黑模式颜色

| 元素 | 亮色模式 | 暗黑模式 |
|------|----------|----------|
| 背景 | #FFFFFF | #1D1E1F |
| 文本 | #303133 | #E5EAF3 |
| 边框 | #DCDFE6 | #414243 |
| 卡片 | #FFFFFF | #252526 |

### 10.2 暗黑模式实现

```css
/* CSS变量实现 */
:root {
  --bg-color: #FFFFFF;
  --text-color: #303133;
}

[data-theme='dark'] {
  --bg-color: #1D1E1F;
  --text-color: #E5EAF3;
}

/* 使用 */
body {
  background-color: var(--bg-color);
  color: var(--text-color);
}
```

---

## 相关文档

- Vue组件开发: `../../04-coding/frontend/prompts/vue-component-dev.md`
- 前端代码规范: `../../04-coding/frontend/templates/vue-code-style.md`