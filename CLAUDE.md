# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

"轻记" - 移动端优先的个人财务记账单页应用（HTML + CSS + Vanilla JS），使用 localStorage 存储数据，通过 Chart.js 实现统计图表。

## 启动方式

直接在浏览器中打开 `index.html` 即可运行，无需构建。

## 架构特点

### 单文件结构
- 所有 HTML/CSS/JS 都在 `index.html` 中
- 路由使用 hash 模式（`#/login`, `#/add`, `#/records` 等）
- 页面通过 `.page.active` class 控制显示隐藏

### 数据层
- `accountService` - 账号服务（注册/登录/登出/数据隔离）
- `getRecords()` / `saveRecords()` - 读写当前用户的记账记录
- localStorage keys: `users`, `currentUser`, `records_{userId}`


### 路由守卫
- 未登录用户访问受保护页面会自动跳转到 `#/login`
- 登录/注册页面已登录则跳转到首页
- FAB（悬浮添加按钮）在登录/注册页面隐藏

### 关键函数
- `handleRoute()` - 路由处理，根据 hash 显示对应页面
- `navigate(hash)` - 路由跳转
- `showToast(msg)` - Toast 提示
- `md5(str)` - MD5 加密（用于密码存储）
- `uuid()` - 生成 UUID

### 消费分类（10个固定分类）
| ID | 名称 | 图标 |
|----|------|------|
| food | 餐饮 | 🍜 |
| transport | 交通 | 🚌 |
| shopping | 购物 | 🛒 |
| entertainment | 娱乐 | 🎬 |
| medical | 医疗 | 💊 |
| communication | 通讯 | 📱 |
| housing | 居住 | 🏠 |
| travel | 旅游 | ✈️ |
| education | 教育 | 📚 |
| other | 其他 | ☕ |

### UI 组件
- TabBar（底部导航，固定定位）
- FAB（悬浮添加按钮，登录/注册页面隐藏）
- Card（卡片容器）
- 数字键盘（金额输入）
- CategoryChip（分类选择）
- Toast（轻量提示）

### 样式变量
```
--primary: #8B7355       /* 奶茶棕，主色 */
--danger: #E8A0A0        /* 珊瑚粉，支出强调 */
--bg: #FDF8F3            /* 暖白，背景色 */
--card: #FFFAF5           /* 米白，卡片色 */
--text: #5D4037           /* 深棕，文字主色 */
--text-secondary: #A08070 /* 浅棕，文字次色 */
--radius-card: 16px
--radius-btn: 12px
```

## 页面结构

| 路由 | 页面 | 说明 |
|------|------|------|
| `#/login` | 登录页 | 用户登录 |
| `#/register` | 注册页 | 用户注册 |
| `#/` | 首页 | 今日概览 + 最近 5 条记录 |
| `#/add` | 记账页 | 快速记账表单 |
| `#/records` | 账单列表 | 按日分组的完整账单 |
| `#/stats` | 统计页 | 月度报告（环形图 + 折线图） |
| `#/record/:id` | 记录详情 | 查看/编辑/删除单笔记录 |
| `#/me` | 我的 | 账户统计 + 退出登录 |


## 数据结构

```js
User { id, username, password(MD5), createdAt }
Record { id, amount(分), categoryId, date, note, createdAt, updatedAt }
```

## 开发注意事项

- 金额以"分"为单位存储，显示时除以 100
- 左滑删除通过 touch 事件实现（`.swipe-wrapper`）
- 图表（pie/line）在切换路由时会 destroy 后重建
- 登录/注册页面的 FAB 通过 `document.querySelector('.fab').style.display = 'none'` 隐藏