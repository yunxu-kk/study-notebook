# 测试报告：TC-OVERVIEW-002 课程完成率卡片点击滚动到表格

## 1. 测试用例信息
- **用例 ID**: TC-OVERVIEW-002
- **用例名称**: 课程完成率卡片点击滚动到表格
- **优先级**: P2
- **类型**: 功能
- **预计耗时**: 1min

## 2. 测试环境
- **测试系统**: 测机管理系统 - 我的培训
- **测试版本**: v1.0
- **测试时间**: 2026-05-20 09:15 - 09:18 (UTC+8)
- **测试人员**: 测试执行工程师
- **测试环境**:
  - OS: Windows 10
  - 浏览器：Chrome (Playwright)
  - 设备：Desktop
  - 网络：局域网

## 3. 测试步骤与结果

### 步骤 1: 打开页面
- **操作**: 打开页面 http://10.50.241.156:83/atr/mytraining
- **实际结果**: 页面成功打开，自动重定向到登录页
- **状态**: ✅ 通过
- **证据**: ![登录页](page-login.png)

### 步骤 2: 登录系统
- **操作**: 使用账号 admin/admin123 登录
- **实际结果**: 登录成功，进入我的培训页面
- **状态**: ✅ 通过
- **证据**: ![数据概览页](page-overview.png)

### 步骤 3: 找到课程完成率卡片
- **操作**: 在数据概览区域找到"课程完成率"卡片
- **实际结果**: 找到课程完成率卡片，显示完成率为 68%
- **状态**: ✅ 通过
- **证据**: ![数据概览页](page-overview.png)

### 步骤 4: 点击卡片
- **操作**: 点击课程完成率卡片
- **实际结果**: 点击后页面无明显变化，仍停留在 Summary 选项卡
- **状态**: ❌ 失败
- **证据**: ![点击卡片后](after-click-card.png)

### 步骤 5: 验证滚动效果
- **操作**: 验证页面是否自动滚动到课程进度表格位置
- **实际结果**: 页面未自动切换到"课程进度"Tab，也未滚动到表格位置
- **期望结果**: 点击课程完成率卡片后，页面应平滑滚动到课程进度表格区域，表格区域应被高亮或聚焦显示
- **状态**: ❌ 失败
- **证据**: ![点击后仍停留在 Summary](after-click-still-summary.png)

## 4. 测试结果
- **测试用例状态**: ❌ 失败
- **发现 Bug**: 是
- **Bug 严重程度**: P2 - 一般

## 5. Bug 详情

### BUG-001: 课程完成率卡片点击后未切换到课程进度 Tab

**问题描述**:
点击课程完成率卡片后，页面没有自动切换到"课程进度"Tab，也没有滚动到表格位置。

**复现步骤**:
1. 打开页面 http://10.50.241.156:83/atr/mytraining
2. 使用账号 admin/admin123 登录
3. 在数据概览区域找到"课程完成率"卡片
4. 点击该卡片
5. 观察页面状态

**实际结果**:
- 点击后页面仍停留在"Summary"选项卡
- 页面未滚动到课程进度表格位置
- 无任何视觉反馈

**期望结果**:
- 点击课程完成率卡片后，页面应自动切换到"课程进度"Tab
- 页面应平滑滚动到课程进度表格区域
- 表格区域应被高亮或聚焦显示

**代码分析**:
在 [`index.vue`](ruoyi-ui/src/views/atr/mytraining/index.vue:1232) 中，`scrollToTable()` 方法已定义：
```javascript
scrollToTable() {
  this.$refs.tableContainer.scrollIntoView({ behavior: 'smooth' })
}
```

但该方法仅执行滚动操作，没有切换到"课程进度"Tab。由于表格在"课程进度"Tab 下，当当前 Tab 为"Summary"时，`this.$refs.tableContainer` 可能不存在或不可见，导致滚动无效。

**建议修复**:
修改 `scrollToTable()` 方法，先切换到"课程进度"Tab，再执行滚动：
```javascript
scrollToTable() {
  // 先切换到课程进度 Tab
  this.activeTab = 'courseProgress'
  // 等待 DOM 更新后滚动到表格
  this.$nextTick(() => {
    this.$refs.tableContainer.scrollIntoView({ behavior: 'smooth' })
  })
}
```

**影响范围**:
- 所有使用课程完成率卡片快捷导航功能的用户
- 用户体验受损，快捷导航功能失效

**证据截图**:
- ![点击后仍停留在 Summary](after-click-still-summary.png)

## 6. 测试结论
- **测试用例执行结果**: ❌ 失败
- **原因**: 课程完成率卡片点击功能未按预期工作，页面未切换到课程进度 Tab 且未滚动到表格位置
- **建议**: 修复 Bug 后重新执行测试用例

## 7. 附件
- 证据目录：`/docs/test/test-report/mytraining/TC-OVERVIEW-002/`
- 截图文件:
  - `page-login.png` - 登录页截图
  - `page-overview.png` - 数据概览页截图
  - `after-click-card.png` - 点击卡片后截图
  - `after-click-still-summary.png` - 点击后仍停留在 Summary 选项卡
