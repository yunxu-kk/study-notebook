# 测试报告：TC-WEEKLY-002 时间范围筛选 - Last 30 days

## 1. 测试概览
- **测试系统**: 测机管理系统
- **测试页面**: http://10.50.241.156:83/atr/mytraining
- **测试范围**: 阶段任务 - 时间范围筛选功能
- **测试时间**: 2026-05-19 14:47 - 15:00
- **测试人员**: 测试执行工程师
- **测试环境**:
  - OS: Windows 10
  - 浏览器：Chrome (Playwright)
  - 设备：Desktop
  - 网络：局域网

## 2. 测试用例信息
| 属性 | 值 |
|------|-----|
| 用例 ID | TC-WEEKLY-002 |
| 用例名称 | 时间范围筛选 - Last 30 days |
| 优先级 | P1 |
| 测试类型 | 功能测试 |

## 3. 测试步骤与结果

### 步骤 1: 登录系统并导航到阶段任务页面
- **操作**: 访问 http://10.50.241.156:83/atr/mytraining，点击"阶段任务"标签页
- **预期结果**: 页面加载成功，显示阶段任务内容
- **实际结果**: ✅ 通过 - 页面成功加载，显示 18 条周数据记录
- **证据**: 
  - ![步骤 1 截图](C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step1-snapshot-阶段任务页面初始状态.png)

### 步骤 2: 查看时间范围筛选下拉框选项
- **操作**: 点击时间范围筛选下拉框
- **预期结果**: 显示时间范围选项列表
- **实际结果**: ✅ 通过 - 下拉框显示 4 个选项：Last 30 days, Last 90 days, Last 6 months, Last 1 year
- **证据**: 
  - ![步骤 2 截图](C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step2-snapshot-时间范围下拉框选项.png)

### 步骤 3: 选择"Last 30 days"选项并验证数据
- **操作**: 选择"Last 30 days"选项
- **预期结果**: 图表刷新，只显示最近 30 天的周数据（约 4-5 条记录）
- **实际结果**: ❌ **失败** - 表格仍显示全部 18 条数据，未进行筛选
- **证据**: 
  - ![步骤 3 截图](C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step3-screenshot-选择-Last-30-days-后数据未变化.png)

## 4. 数据对比分析

### 筛选前数据状态
| 数据范围 | 记录数 | 日期范围 |
|---------|--------|----------|
| 全部数据 | 18 条 | W2601 (2026-01-04) 到 W2618 (2026-05-03) |

### 选择"Last 30 days"后预期数据
根据当前日期 2026-05-19，Last 30 days 应该显示：
| 预期数据范围 | 预期记录数 | 预期日期范围 |
|-------------|-----------|-------------|
| 最近 30 天 | 约 4-5 条 | 2026-04-19 之后 (W2616-W2618) |

### 实际数据状态
| 实际数据范围 | 实际记录数 | 实际日期范围 |
|-------------|-----------|-------------|
| 全部数据（未筛选） | 18 条 | W2601 (2026-01-04) 到 W2618 (2026-05-03) |

## 5. 发现的问题

### BUG-001: 时间范围筛选功能未生效
- **严重程度**: P1 (严重)
- **问题描述**: 选择"Last 30 days"选项后，表格数据未进行筛选，仍显示全部 18 条记录
- **复现步骤**:
  1. 登录系统
  2. 导航到"阶段任务"标签页
  3. 点击时间范围筛选下拉框
  4. 选择"Last 30 days"选项
  5. 观察表格数据变化
- **实际结果**: 表格仍显示全部 18 条数据
- **期望结果**: 表格应只显示最近 30 天的数据（约 4-5 条）
- **复现概率**: 必现 (5/5 次)
- **可能原因**: 
  1. 前端未实现筛选逻辑
  2. 筛选条件未传递到后端 API
  3. 后端 API 未处理时间范围参数

## 6. 控制台日志
```
[LOG] [HMR] Waiting for update signal from WDS...
[INFO] Download the Vue Devtools extension for a better development experience
[WARNING] [vue-router] Non-nested routes must include a leading slash character
[INFO] Slow network is detected
[WARNING] Can't get DOM width or height. Please check dom.clientWidth and dom.clientHeight
```

## 7. 网络请求
页面加载期间的主要请求：
- GET http://10.50.241.156:83/atr/mytraining => 200 OK
- GET http://10.50.241.156:83/dev-api/getInfo => 200 OK
- GET http://10.50.241.156:83/dev-api/getRouters => 200 OK

**注意**: 选择时间范围选项时，未观察到新的 API 请求，表明筛选逻辑可能仅在前端实现但未正确执行。

## 8. 测试结论

### 测试结果统计
| 测试步骤 | 结果 |
|---------|------|
| 步骤 1: 登录并导航 | ✅ 通过 |
| 步骤 2: 查看下拉框选项 | ✅ 通过 |
| 步骤 3: 验证筛选功能 | ❌ 失败 |

### 最终判定
- **测试用例状态**: ❌ **失败**
- **发现 Bug 数**: 1 个 (P1 级)

### 发版建议
- □ 可以直接发布
- □ 修复 P0 级 Bug 后可以发布
- ☑ 修复 P0 和 P1 级 Bug 后可以发布
- □ 不建议发布，需要全面修复

**理由**: 时间范围筛选是核心功能，筛选功能失效会影响用户查看特定时间段数据的需求，建议修复后再发布。

## 9. 附件
- 步骤 1 截图：`C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step1-snapshot-阶段任务页面初始状态.png`
- 步骤 2 截图：`C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step2-snapshot-时间范围下拉框选项.png`
- 步骤 3 截图：`C:\Users\amoszhu\AppData\Local\Temp\playwright-mcp-output\1779158450011\step3-screenshot-选择-Last-30-days-后数据未变化.png`

---
*测试报告生成时间：2026-05-19 15:00*
*测试执行工程师*
