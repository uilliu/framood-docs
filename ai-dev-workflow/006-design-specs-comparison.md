# Material Design 3 与 HarmonyOS 设计规范对比分析

> 文档版本: 1.0
> 创建日期: 2026-05-22
> 适用范围: 跨平台应用开发、UI/UX设计决策

---

## 目录

1. [概述](#1-概述)
2. [Material Design 3 核心规范](#2-material-design-3-核心规范)
3. [HarmonyOS 设计规范](#3-harmonyos-设计规范)
4. [两规范深度对比](#4-两规范深度对比)
5. [取舍策略](#5-取舍策略)
6. [AI按规范设计的方法](#6-ai按规范设计的方法)
7. [附录](#7-附录)

---

## 1. 概述

### 1.1 研究背景

在跨平台应用开发中，选择合适的设计规范直接影响用户体验一致性、开发效率和品牌形象。Material Design 3 (MD3) 和 HarmonyOS Design 是目前两大主流设计系统，各有特色。

### 1.2 设计系统定位

| 维度 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| **主导方** | Google | Huawei |
| **首次发布** | 2014年 (M1) → 2021年 (M3) | 2019年 |
| **核心平台** | Android, Web, Flutter | HarmonyOS 全生态 |
| **设计哲学** | 个性化、动态色彩 | 多设备协同、服务卡片 |
| **开源程度** | 完全开源，跨平台支持 | 部分开源，生态绑定 |

---

## 2. Material Design 3 核心规范

### 2.1 设计理念

Material Design 3 的核心理念可概括为 **"Personal, Adaptive, Expressive"**（个性化、自适应、表达性）：

```
核心原则:
├── Dynamic Color (动态色彩) - 从用户壁纸提取色彩
├── Personalized Design (个性化设计) - 用户驱动的视觉体验
├── Accessible by Default (默认无障碍) - WCAG 2.1 合规
└── Consistent Across Platforms (跨平台一致) - 统一的设计语言
```

#### M3 相对于 M2 的关键变化

| 变化领域 | Material Design 2 | Material Design 3 |
|----------|-------------------|-------------------|
| **色彩系统** | 固定调色板 | 动态色彩 + 色彩角色 |
| **圆角** | 4dp, 8dp | 8dp - 28dp (更圆润) |
| **阴影** | 传统投影 | 色调阴影 + 表面着色 |
| **组件数量** | 约80个 | 约60个 (精简) |
| **图标风格** | 填充/轮廓/双色/圆角/锐利 | 5种变体保留，更强调轮廓 |

### 2.2 色彩系统

#### 2.2.1 动态色彩 (Dynamic Color)

MD3 的核心创新是 **动态色彩系统**，从用户壁纸自动提取并生成配色方案：

```
色彩生成流程:
壁纸 → 算法提取主色 → 生成色调调色板(Tonal Palette) → 映射到色彩角色(Color Roles)
```

#### 2.2.2 色彩角色 (Color Roles)

| 角色名称 | 用途 | 浅色模式 | 深色模式 |
|----------|------|----------|----------|
| **Primary** | 主要操作、强调元素 | 主色调 | 亮色调变体 |
| **On Primary** | Primary 上的内容 | 白色/对比色 | 深色/对比色 |
| **Primary Container** | Primary 容器背景 | 浅色调 | 深色调 |
| **On Primary Container** | Container 上的内容 | 深色 | 浅色 |
| **Secondary** | 次要强调 | 辅助色 | 辅助亮色 |
| **Tertiary** | 第三强调色 | 对比/补充色 | 变体 |
| **Surface** | 背景表面 | 白色/浅灰 | 深灰/黑 |
| **Surface Variant** | 变体表面 | 浅色调 | 深色调 |
| **Outline** | 边框、分割 | 中性灰 | 中性亮 |

#### 2.2.3 色调调色板 (Tonal Palette)

每个颜色生成13个色调等级（0-100）：

```
示例：Primary 色调调色板
├── 0:   #000000 (最深)
├── 10:  #21005E
├── 20:  #381E72
├── ...
├── 50:  #6750A4
├── ...
├── 90:  #EADDFF
├── 95:  #F6EDFF
├── 99:  #FFFBFE
└── 100: #FFFFFF (最浅)
```

### 2.3 组件体系

#### 2.3.1 核心组件分类

```
Material Design 3 组件:
├── 基础组件 (Foundation)
│   ├── Button (Filled, Outlined, Tonal, Text, Elevated, FAB)
│   ├── Icon Button
│   ├── Segmented Button
│   └── Chip (Assist, Filter, Input, Suggestion)
│
├── 输入组件 (Input)
│   ├── Text Field (Filled, Outlined)
│   ├── Checkbox
│   ├── Radio Button
│   ├── Switch
│   ├── Slider
│   └── Date/Time Picker
│
├── 布局组件 (Layout)
│   ├── Card
│   ├── Dialog
│   ├── Navigation Bar (Bottom)
│   ├── Navigation Drawer
│   ├── Navigation Rail
│   ├── Side Sheet
│   └── Bottom Sheet
│
├── 信息展示 (Display)
│   ├── Badge
│   ├── Tooltip
│   ├── Snackbar
│   ├── Banner
│   ├── Progress Indicator (Linear, Circular)
│   └── Divider
│
└── 导航组件 (Navigation)
    ├── Top App Bar
    ├── Tabs
    ├── Search Bar
    └── Menu (Dropdown, Exposed)
```

#### 2.3.2 关键组件规范

**Button 组件规范：**

| 类型 | 高度 | 圆角 | 使用场景 |
|------|------|------|----------|
| Filled | 40dp | 20dp (full) | 主要操作 |
| Outlined | 40dp | 20dp | 次要操作 |
| Tonal | 40dp | 20dp | 中等强调 |
| Text | 40dp | 20dp | 低强调操作 |
| Elevated | 40dp | 20dp | 需要层次感 |
| FAB | 56dp | 16dp | 悬浮操作 |

**Card 组件规范：**

| 属性 | 标准值 |
|------|--------|
| 圆角 | 12dp |
| 内边距 | 16dp |
| 卡片间距 | 8dp |
| 阴影层级 | Level 0-1 (扁平为主) |

### 2.4 动效规范

#### 2.4.1 动效原则

```
MD3 动效四原则:
├── Clear (清晰) - 动效服务于理解
├── Responsive (响应) - 快速反馈用户操作
├── Natural (自然) - 遵循物理规律
└── Coherent (连贯) - 整体体验一致
```

#### 2.4.2 标准动效参数

| 动效类型 | 持续时间 | 缓动曲线 |
|----------|----------|----------|
| **简单动画** | 100-200ms | Standard easing |
| **复杂动画** | 200-400ms | Standard easing |
| **进入动画** | 150-300ms | Decelerate easing |
| **退出动画** | 100-200ms | Accelerate easing |
| **强调动画** | 300-500ms | Emphasized easing |

**缓动曲线定义：**

```css
/* Standard Easing (标准缓动) */
cubic-bezier(0.2, 0, 0, 1)

/* Emphasized Easing (强调缓动) */
cubic-bezier(0.2, 0, 0, 1) - 进入
cubic-bezier(0.05, 0.7, 0.1, 1) - 强调进入

/* Decelerate Easing (减速缓动) */
cubic-bezier(0, 0, 0, 1)

/* Accelerate Easing (加速缓动) */
cubic-bezier(0.3, 0, 1, 1)
```

#### 2.4.3 容器转换 (Container Transform)

MD3 强调容器转换动效，实现元素间平滑过渡：

```
容器转换应用场景:
├── 列表项 → 详情页
├── FAB → 全屏页面
├── 卡片 → 扩展卡片
├── 搜索栏 → 搜索结果页
└── 芯片 → 选择器
```

### 2.5 排版系统

#### 2.5.1 字体比例 (Type Scale)

| 样式名称 | 字号 | 行高 | 字重 | 用途 |
|----------|------|------|------|------|
| **Display Large** | 57sp | 64sp | Regular | 大标题 |
| **Display Medium** | 45sp | 52sp | Regular | 中标题 |
| **Display Small** | 36sp | 44sp | Regular | 小标题 |
| **Headline Large** | 32sp | 40sp | Regular | 强调标题 |
| **Headline Medium** | 28sp | 36sp | Regular | 区域标题 |
| **Headline Small** | 24sp | 32sp | Regular | 小区标题 |
| **Title Large** | 22sp | 28sp | Medium | 大标题 |
| **Title Medium** | 16sp | 24sp | Medium | 中标题 |
| **Title Small** | 14sp | 20sp | Medium | 小标题 |
| **Body Large** | 16sp | 24sp | Regular | 大正文 |
| **Body Medium** | 14sp | 20sp | Regular | 中正文 |
| **Body Small** | 12sp | 16sp | Regular | 小正文 |
| **Label Large** | 14sp | 20sp | Medium | 大标签 |
| **Label Medium** | 12sp | 16sp | Medium | 中标签 |
| **Label Small** | 11sp | 16sp | Medium | 小标签 |

#### 2.5.2 字体家族

```
推荐字体:
├── 英文: Google Sans, Roboto
├── 中文: Noto Sans SC, 思源黑体
└── 等宽: Roboto Mono, Google Sans Mono
```

---

## 3. HarmonyOS 设计规范

### 3.1 设计理念

HarmonyOS 设计系统的核心理念是 **"One as All, All as One"**（一生万物，万物归一），强调多设备协同与无缝体验：

```
核心原则:
├── Harmony (和谐) - 统一的多设备体验
├── Service-Centric (服务导向) - 服务卡片为核心交互
├── Multi-Device (多设备) - 从底层支持分布式架构
├── Intelligent (智能) - 主动服务、智能推荐
└── Fluid (流畅) - 物理动效、引力动画
```

### 3.2 色彩系统

#### 3.2.1 色彩架构

HarmonyOS 采用 **系统定义色彩** + **自定义品牌色** 的方式：

```
HarmonyOS 色彩层级:
├── 品牌色 (Brand Color)
│   └── 应用/服务的主色调
│
├── 功能色 (Functional Colors)
│   ├── 成功: #007DFF (蓝色系)
│   ├── 警告: #FA2D2D (红色系)
│   ├── 提示: #FF9900 (橙色系)
│   └── 成功: #4CAF50 (绿色系)
│
├── 中性色 (Neutral Colors)
│   └── 灰度色阶 (0-100)
│
└── 背景色 (Background Colors)
    ├── 浅色模式: 白色系
    └── 深色模式: 深灰/黑色系
```

#### 3.2.2 色彩规范对比

| 色彩角色 | HarmonyOS | Material Design 3 |
|----------|------------|-------------------|
| **主色** | 品牌自定义 | 动态提取 |
| **生成方式** | 设计师定义 | 算法生成 |
| **色调数量** | 10级灰度 | 13级色调 |
| **深色模式** | 手动切换 | 自动适配 |

### 3.3 组件体系 (ArkUI)

#### 3.3.1 组件分类

```
ArkUI 组件体系:
├── 基础组件 (Basic)
│   ├── Button (Normal, Capsule, Circle)
│   ├── Text
│   ├── Image
│   ├── TextInput / TextArea
│   ├── Toggle / Checkbox / Radio
│   ├── Slider
│   └── Progress
│
├── 容器组件 (Container)
│   ├── Column (垂直布局)
│   ├── Row (水平布局)
│   ├── Stack (层叠布局)
│   ├── List (列表)
│   ├── Grid (网格)
│   ├── Scroll (滚动容器)
│   └── Swiper (轮播)
│
├── 导航组件 (Navigation)
│   ├── NavRouter / NavDestination
│   ├── Tabs
│   ├── Navigator
│   └── NavBar
│
├── 服务卡片组件 (Service Widget)
│   ├── 2x2 卡片
│   ├── 2x4 卡片
│   ├── 4x4 卡片
│   └── 自定义尺寸卡片
│
└── 画布组件 (Canvas)
    ├── Canvas
    └── 自定义绘制
```

#### 3.3.2 关键组件规范

**Button 组件：**

| 类型 | 高度 | 圆角 | 使用场景 |
|------|------|------|----------|
| Normal | 40vp | 4vp | 普通按钮 |
| Capsule | 40vp | 20vp (全圆) | 强调按钮 |
| Circle | 40dp (直径) | 50% | 图标按钮 |

**服务卡片尺寸规范：**

| 尺寸 | 网格 | 实际像素 (标准屏) |
|------|------|-------------------|
| 小卡片 | 2x2 | 200x200px |
| 中卡片 | 2x4 | 200x400px |
| 大卡片 | 4x4 | 400x400px |

### 3.4 动效规范

#### 3.4.1 动效原则

```
HarmonyOS 动效三原则:
├── Natural (自然) - 遵循物理规律
├── Responsive (响应) - 即时反馈
└── Coherent (连贯) - 跨设备一致性
```

#### 3.4.2 特有动效：引力动画

HarmonyOS 独有的 **引力动画** 模拟真实物理引力效果：

```
引力动画特性:
├── 模拟重力加速度
├── 弹性碰撞效果
├── 惯性滚动
├── 元素间吸引/排斥
└── 手势跟随延迟
```

#### 3.4.3 标准动效参数

| 动效类型 | 持续时间 | 特性 |
|----------|----------|------|
| **页面切换** | 200-400ms | 共享元素转场 |
| **卡片展开** | 300-500ms | 弹性曲线 |
| **列表滚动** | 实时 | 惯性 + 阻尼 |
| **按钮反馈** | 50-100ms | 缩放 + 涟漪 |
| **服务卡片动画** | 200-400ms | 流畅过渡 |

### 3.5 排版系统

#### 3.5.1 字体比例

| 样式名称 | 字号 | 字重 | 用途 |
|----------|------|------|------|
| **H1** | 32fp | Medium | 页面大标题 |
| **H2** | 28fp | Medium | 区域标题 |
| **H3** | 24fp | Medium | 区块标题 |
| **H4** | 20fp | Medium | 小区块标题 |
| **Body** | 14-16fp | Regular | 正文内容 |
| **Caption** | 12fp | Regular | 辅助说明 |
| **Button** | 14fp | Medium | 按钮文字 |

#### 3.5.2 字体家族

```
HarmonyOS 字体:
├── HarmonyOS Sans (系统默认)
│   ├── Regular
│   ├── Medium
│   └── Bold
│
├── HarmonyOS Sans SC (简体中文)
└── HarmonyOS Sans Mono (等宽)
```

---

## 4. 两规范深度对比

### 4.1 设计理念差异

| 维度 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| **核心哲学** | 用户个性化、动态表达 | 多设备协同、服务聚合 |
| **色彩策略** | 算法动态生成 | 设计师定义品牌色 |
| **生态定位** | 移动优先，跨平台延伸 | 全设备原生，分布式架构 |
| **交互模型** | 触控优先 | 多模态输入 (触控/语音/手势/跨设备) |
| **信息架构** | 应用为中心 | 服务卡片为中心 |

### 4.2 组件命名与功能差异

#### 4.2.1 基础组件对比

| 功能 | Material Design 3 | HarmonyOS ArkUI |
|------|-------------------|-----------------|
| 按钮 | Button (6种变体) | Button (3种类型) |
| 浮动按钮 | FAB (多种尺寸) | 无直接对应，自定义实现 |
| 卡片 | Card | 无直接对应，使用Column/Row |
| 列表 | List + ListItem | List + ListItem |
| 网格 | Grid | Grid |
| 标签页 | Tabs | Tabs |
| 底部导航 | NavigationBar | Tabs (底部) |
| 侧边栏 | NavigationDrawer | NavRouter |
| 对话框 | Dialog | AlertDialog / CustomDialog |
| 底部面板 | BottomSheet | 无直接对应，自定义实现 |
| 芯片 | Chip (4种类型) | 无直接对应，自定义实现 |
| 开关 | Switch | Toggle |
| 滑块 | Slider | Slider |
| 进度条 | LinearProgress | Progress |
| 文本框 | TextField | TextInput / TextArea |

#### 4.2.2 特色组件

| Material Design 3 独有 | HarmonyOS 独有 |
|------------------------|-----------------|
| FAB (Floating Action Button) | 服务卡片 (Service Widget) |
| Chip (多种类型) | 分享面板 |
| SearchBar | 分布式数据组件 |
| SideSheet | 流转组件 |
| NavigationRail | 多设备协同组件 |

### 4.3 动效与交互差异

| 维度 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| **核心动效** | 容器转换、共享轴、淡入淡出 | 引力动画、共享元素转场 |
| **缓动曲线** | CSS标准曲线 | 自定义弹性曲线 |
| **物理模拟** | 简单物理 (弹性) | 复杂物理 (引力、碰撞) |
| **手势动效** | 涟漪效果 | 手势跟随延迟 |
| **跨设备动效** | 无 | 流转动效 |

### 4.4 色彩系统差异

| 维度 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| **色彩生成** | 动态提取 (算法) | 静态定义 (设计师) |
| **色彩角色** | 12+ 角色 | 基础分类 |
| **色调等级** | 13级 (0-100) | 10级灰度 |
| **深色模式** | 自动适配 | 手动定义 |
| **品牌定制** | 受限 (需保持语义) | 灵活 |

### 4.5 适用场景分析

| 场景 | 推荐 | 原因 |
|------|------|------|
| **Android 应用** | Material Design 3 | 官方推荐，用户熟悉 |
| **HarmonyOS 原生应用** | HarmonyOS Design | 系统一致性 |
| **跨平台应用 (Android+iOS)** | Material Design 3 | 更成熟的跨平台支持 |
| **跨平台应用 (含HarmonyOS)** | 混合策略 | 需适配各平台规范 |
| **IoT 设备界面** | HarmonyOS Design | 专为多设备设计 |
| **Web 应用** | Material Design 3 | MDC Web 支持完善 |
| **Flutter 应用** | Material Design 3 | Flutter 内置 MD 组件 |

---

## 5. 取舍策略

### 5.1 水印类产品设计建议

对于水印类产品，推荐采用 **Material Design 3** 作为基础，原因如下：

#### 5.1.1 优势分析

| 需求 | Material Design 3 优势 | HarmonyOS 优势 |
|------|------------------------|-----------------|
| 图像编辑界面 | 丰富的输入组件 | 无明显优势 |
| 跨平台一致性 | 成熟的跨平台方案 | 限于HarmonyOS |
| 自定义主题 | 动态色彩易实现品牌色 | 需手动定义 |
| 用户体验 | 用户熟悉的 Android 交互 | 用户群较小 |

#### 5.1.2 具体建议

```
水印类产品 UI 设计策略:
├── 基础框架: Material Design 3
│   ├── 使用 Card 展示预览
│   ├── 使用 Slider 控制透明度/位置
│   ├── 使用 BottomSheet 做工具面板
│   └── 使用 Chip 做快速选择
│
├── 定制化调整
│   ├── 品牌色: 覆盖 Primary 色彩角色
│   ├── 圆角: 可适度调整 (8-16dp)
│   └── 动效: 增加图片处理相关动效
│
└── 特殊处理
    ├── 图片预览区: 全屏沉浸式
    ├── 工具面板: 可折叠设计
    └── 快捷操作: FAB 或底部工具栏
```

### 5.2 跨平台复用策略

#### 5.2.1 分层架构

```
跨平台 UI 架构:
├── 业务逻辑层 (共享)
│   └── 状态管理、业务规则
│
├── 设计系统层 (抽象)
│   ├── 设计 Token (色彩、字体、间距)
│   ├── 组件接口定义
│   └── 动效规范
│
└── 平台适配层 (差异)
    ├── Android: Material Design 3 实现
    ├── iOS: 适配 Human Interface Guidelines
    ├── HarmonyOS: HarmonyOS Design 实现
    └── Web: Material Design 3 Web
```

#### 5.2.2 设计 Token 统一

定义统一的设计 Token，映射到各平台：

```json
{
  "color": {
    "primary": {
      "value": "#6750A4",
      "md3": "md.sys.color.primary",
      "harmonyos": "$r('app.color.primary')"
    },
    "surface": {
      "value": "#FFFBFE",
      "md3": "md.sys.color.surface",
      "harmonyos": "$r('app.color.surface')"
    }
  },
  "spacing": {
    "small": { "value": "8dp" },
    "medium": { "value": "16dp" },
    "large": { "value": "24dp" }
  },
  "radius": {
    "small": { "value": "8dp" },
    "medium": { "value": "12dp" },
    "large": { "value": "16dp" }
  }
}
```

### 5.3 混合使用可行性

#### 5.3.1 混合使用场景

| 场景 | 可行性 | 建议 |
|------|--------|------|
| 同一应用内混合组件 | 低 | 风格冲突，不推荐 |
| 不同平台不同规范 | 高 | 推荐做法 |
| 基于MD3定制类HarmonyOS风格 | 中 | 需谨慎平衡 |
| 设计Token统一，组件差异化 | 高 | 最佳实践 |

#### 5.3.2 混合使用风险

```
混合使用风险:
├── 视觉一致性差
│   └── 用户感知不连贯
│
├── 交互模式冲突
│   └── 手势、动效不一致
│
├── 维护成本高
│   └── 需要维护多套组件
│
└── 用户学习成本
    └── 不同页面交互不同
```

### 5.4 决策流程图

```
选择设计规范决策流程:
│
├── 目标平台是什么?
│   ├── 仅 HarmonyOS → HarmonyOS Design
│   ├── 仅 Android/Web → Material Design 3
│   └── 多平台 (含 HarmonyOS) → 继续
│
├── 是否有强品牌定制需求?
│   ├── 是 → 基于 MD3 深度定制
│   └── 否 → 使用标准 MD3
│
├── 是否需要服务卡片?
│   ├── 是 (HarmonyOS) → HarmonyOS Design 组件
│   └── 否 → Material Design 3
│
└── 最终策略
    ├── 纯 Android/Web: Material Design 3
    ├── 纯 HarmonyOS: HarmonyOS Design
    └── 跨平台: 设计 Token 统一 + 平台适配
```

---

## 6. AI按规范设计的方法

### 6.1 Prompt 模板

#### 6.1.1 通用设计规范 Prompt 模板

```markdown
# 角色
你是一位资深 UI/UX 设计师，精通 Material Design 3 和 HarmonyOS 设计规范。

# 任务
为 [应用名称/功能模块] 设计用户界面。

# 设计规范约束
## 采用规范: [Material Design 3 / HarmonyOS Design]

## 必须遵守的规范要点:
- 色彩: [具体约束，如: 使用 MD3 色彩角色，Primary 用于主操作按钮]
- 排版: [具体约束，如: 标题使用 Title Large，正文使用 Body Medium]
- 组件: [具体约束，如: 使用 Filled Button 作为主要操作]
- 间距: [具体约束，如: 卡片内边距 16dp，卡片间距 8dp]
- 圆角: [具体约束，如: 卡片圆角 12dp，按钮圆角 20dp]
- 动效: [具体约束，如: 页面切换使用共享元素转场]

## 禁止事项:
- [如: 不要使用过时的 Material Design 2 组件]
- [如: 不要混用不同设计规范的组件]

# 输出要求
1. 组件层级结构描述
2. 关键设计 Token 值
3. 可选: 对应代码实现 (React/Flutter/ArkTS)
```

#### 6.1.2 Material Design 3 专项 Prompt

```markdown
# Material Design 3 UI 设计 Prompt

## 角色设定
你是 Material Design 3 设计专家，熟悉所有 M3 组件、色彩系统和动效规范。

## 设计要求

### 色彩系统
使用 MD3 动态色彩系统:
- Primary: [指定或使用动态提取]
- Secondary: [指定或使用动态提取]
- Tertiary: [指定或使用动态提取]
- Surface: 自动适配深浅模式
- 错误色: 使用 MD3 Error 角色

### 组件规范
| 组件 | 类型 | 尺寸 | 使用场景 |
|------|------|------|----------|
| Button | Filled | 40dp 高 | 主要操作 |
| Button | Outlined | 40dp 高 | 次要操作 |
| Card | Elevated | 12dp 圆角 | 内容卡片 |
| Chip | Filter | 32dp 高 | 筛选选项 |
| FAB | Extended | 56dp 高 | 悬浮操作 |

### 动效规范
- 页面转场: 使用 Container Transform
- 列表滚动: 使用共享轴 (Shared Axis)
- 元素进入: 使用 Fade Through
- 组件状态: 使用 Ripple 效果

### 无障碍要求
- 所有可点击元素最小尺寸 48dp
- 文字对比度符合 WCAG 2.1 AA 标准
- 支持屏幕阅读器

## 输出格式
请按以下格式输出:
1. 设计描述
2. 设计 Token 配置 (JSON 格式)
3. 组件结构 (层级树)
4. 代码实现 (指定框架)
```

#### 6.1.3 HarmonyOS 设计专项 Prompt

```markdown
# HarmonyOS ArkUI 设计 Prompt

## 角色设定
你是 HarmonyOS 设计专家，熟悉 ArkUI 组件、服务卡片和多设备适配。

## 设计要求

### 色彩系统
使用 HarmonyOS 色彩规范:
- 品牌色: [指定]
- 功能色: 成功/警告/错误/提示
- 中性色: 10 级灰度
- 支持深色模式

### 组件规范
| 组件 | 类型 | 尺寸 | 使用场景 |
|------|------|------|----------|
| Button | Capsule | 40vp 高 | 强调按钮 |
| Button | Normal | 40vp 高 | 普通按钮 |
| List | - | - | 列表展示 |
| Grid | - | - | 网格布局 |
| Tabs | Bottom | - | 底部导航 |
| TextInput | - | 48vp 高 | 文本输入 |

### 动效规范
- 使用 ArkUI 动画 API: animateTo / transition
- 应用引力动画特性
- 页面转场: 共享元素转场
- 手势动效: 手势跟随延迟

### 多设备适配
- 断点设计: sm/md/lg
- 响应式布局: 使用 Row/Column + 媒体查询
- 服务卡片尺寸: 2x2 / 2x4 / 4x4

## 输出格式
请按以下格式输出:
1. 设计描述
2. ArkTS 设计 Token 配置
3. 组件结构 (@Component 装饰器)
4. 代码实现 (ArkTS)
```

#### 6.1.4 水印应用设计专项 Prompt

```markdown
# 水印应用 UI 设计 Prompt

## 角色设定
你是图像处理应用设计专家，熟悉水印产品的用户需求。

## 设计规范
基础规范: Material Design 3
品牌色: [指定]
目标平台: Android / iOS / Web

## 功能模块
1. 图片导入: 支持相册/相机/文件
2. 水印编辑:
   - 文字水印: 字体/颜色/大小/旋转/透明度
   - 图片水印: 上传/缩放/位置/透明度
3. 位置调整: 拖拽/预设位置/平铺
4. 预览: 实时预览/对比查看
5. 导出: 格式/质量/批量处理

## UI 组件建议
| 功能 | 推荐组件 | 说明 |
|------|----------|------|
| 图片预览 | Card + Image | 支持缩放手势 |
| 透明度控制 | Slider | 0-100% |
| 颜色选择 | Dialog + ColorPicker | 弹出式选择器 |
| 字体选择 | BottomSheet + List | 底部弹出列表 |
| 位置预设 | Chip Group | 九宫格预设 |
| 工具栏 | BottomAppBar | 固定底部操作 |
| 主操作 | FAB | 保存/导出 |

## 输出要求
1. 完整页面结构描述
2. 组件层级图
3. 交互流程说明
4. 关键代码实现
```

### 6.2 代码生成时的规范约束

#### 6.2.1 Material Design 3 代码约束

**React + MUI 示例约束：**

```typescript
// 代码生成约束 Prompt
生成 Material Design 3 规范的 React 组件代码:

## 约束条件:
1. 使用 @mui/material 组件库
2. 引入主题配置:
   import { createTheme, ThemeProvider } from '@mui/material/styles';

3. 使用 MD3 色彩角色:
   const theme = createTheme({
     palette: {
       primary: { main: '#6750A4' },
       secondary: { main: '#625B71' },
       // ...
     },
     shape: { borderRadius: 12 },
   });

4. 组件使用规范:
   - 主要按钮: <Button variant="contained">
   - 次要按钮: <Button variant="outlined">
   - 卡片: <Card elevation={1}>
   - 输入框: <TextField variant="outlined">

5. 间距使用 8dp 网格系统
```

**Flutter 示例约束：**

```dart
// 代码生成约束 Prompt
生成 Material Design 3 规范的 Flutter 代码:

## 约束条件:
1. 使用 Material 3 主题:
   ThemeData(
     useMaterial3: true,
     colorSchemeSeed: Colors.purple,
   );

2. 组件使用规范:
   - 主要按钮: FilledButton()
   - 次要按钮: OutlinedButton()
   - 卡片: Card(elevation: 1)
   - 输入框: TextField()

3. 间距使用 8.0 为基础单位
4. 圆角使用 BorderRadius.circular(12.0)
```

#### 6.2.2 HarmonyOS ArkUI 代码约束

**ArkTS 示例约束：**

```typescript
// 代码生成约束 Prompt
生成 HarmonyOS ArkUI 规范的 ArkTS 代码:

## 约束条件:
1. 使用 @Component 装饰器
2. 使用声明式 UI 语法:

@Component
struct MyComponent {
  build() {
    Column() {
      // 组件内容
    }
  }
}

3. 组件使用规范:
   - 按钮: Button('文本').type(ButtonType.Capsule)
   - 列表: List() { ListItem() { } }
   - 输入框: TextInput().placeholder('提示')

4. 状态管理:
   - @State: 组件内状态
   - @Prop: 父组件传入
   - @Link: 双向绑定

5. 动画使用:
   animateTo({ duration: 300 }, () => {
     // 状态变化
   })

6. 间距使用 vp 单位，基础单位 8vp
```

### 6.3 AI 输出符合规范的最佳实践

#### 6.3.1 分层引导策略

```
AI 设计生成流程:
│
├── 第一层: 需求理解
│   └── 明确功能需求、目标平台、用户场景
│
├── 第二层: 规范约束
│   ├── 选择设计规范 (MD3 / HarmonyOS)
│   ├── 定义设计 Token
│   └── 确定组件使用边界
│
├── 第三层: 结构设计
│   ├── 组件层级
│   ├── 交互流程
│   └── 响应式布局
│
├── 第四层: 细节填充
│   ├── 色彩值
│   ├── 字体大小
│   └── 间距圆角
│
└── 第五层: 代码生成
    ├── 框架选择
    ├── 组件实现
    └── 主题配置
```

#### 6.3.2 检查清单

```markdown
## Material Design 3 合规检查清单

### 色彩
- [ ] 是否使用 MD3 色彩角色?
- [ ] 深浅模式是否正确切换?
- [ ] 对比度是否符合 WCAG 标准?

### 组件
- [ ] 是否使用正确的组件变体?
- [ ] 组件尺寸是否符合规范?
- [ ] 状态(正常/悬停/聚焦/禁用)是否完整?

### 排版
- [ ] 字体比例是否正确?
- [ ] 行高是否符合规范?

### 间距
- [ ] 是否使用 8dp 网格?
- [ ] 内边距/外边距是否一致?

### 动效
- [ ] 持续时间是否在规范范围?
- [ ] 缓动曲线是否正确?

### 无障碍
- [ ] 最小点击区域是否 ≥ 48dp?
- [ ] 是否有语义化标签?
```

```markdown
## HarmonyOS Design 合规检查清单

### 色彩
- [ ] 是否定义品牌色?
- [ ] 深色模式是否支持?
- [ ] 功能色使用是否正确?

### 组件
- [ ] ArkUI 组件使用是否正确?
- [ ] 组件尺寸是否以 vp 为单位?

### 布局
- [ ] 是否使用响应式断点?
- [ ] 多设备适配是否考虑?

### 动效
- [ ] 是否使用 ArkUI 动画 API?
- [ ] 手势交互是否流畅?

### 服务卡片
- [ ] 卡片尺寸是否符合规范?
- [ ] 卡片刷新机制是否正确?
```

---

## 7. 附录

### 7.1 官方资源链接

| 资源 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| 官方文档 | https://m3.material.io/ | https://developer.huawei.com/consumer/cn/design/ |
| 组件库 | MDC (Android/Web/Flutter) | ArkUI |
| 设计工具 | Figma 插件 | DevEco Studio |
| 图标库 | Material Symbols | HarmonyOS Icons |
| 主题工具 | Theme Builder | - |

### 7.2 术语映射表

| 概念 | Material Design 3 | HarmonyOS Design |
|------|-------------------|-------------------|
| 主色 | Primary | 品牌色 |
| 背景色 | Surface | 背景色 |
| 圆角 | Corner Radius | 圆角 |
| 阴影 | Elevation | 阴影层级 |
| 按钮 | Button | Button |
| 卡片 | Card | 自定义容器 |
| 列表 | List | List |
| 对话框 | Dialog | AlertDialog |
| 底部面板 | Bottom Sheet | CustomPanel |
| 标签页 | Tabs | Tabs |
| 导航栏 | Navigation Bar | Navigation |

### 7.3 设计 Token 参考

```json
{
  "material-design-3": {
    "color": {
      "primary": "#6750A4",
      "on-primary": "#FFFFFF",
      "primary-container": "#EADDFF",
      "on-primary-container": "#21005E",
      "secondary": "#625B71",
      "on-secondary": "#FFFFFF",
      "secondary-container": "#E8DEF8",
      "on-secondary-container": "#1D192B",
      "tertiary": "#7D5260",
      "surface": "#FFFBFE",
      "surface-variant": "#E7E0EC",
      "outline": "#79747E",
      "error": "#B3261E"
    },
    "typography": {
      "display-large": { "size": 57, "line-height": 64, "weight": 400 },
      "display-medium": { "size": 45, "line-height": 52, "weight": 400 },
      "display-small": { "size": 36, "line-height": 44, "weight": 400 },
      "headline-large": { "size": 32, "line-height": 40, "weight": 400 },
      "headline-medium": { "size": 28, "line-height": 36, "weight": 400 },
      "headline-small": { "size": 24, "line-height": 32, "weight": 400 },
      "title-large": { "size": 22, "line-height": 28, "weight": 500 },
      "title-medium": { "size": 16, "line-height": 24, "weight": 500 },
      "title-small": { "size": 14, "line-height": 20, "weight": 500 },
      "body-large": { "size": 16, "line-height": 24, "weight": 400 },
      "body-medium": { "size": 14, "line-height": 20, "weight": 400 },
      "body-small": { "size": 12, "line-height": 16, "weight": 400 },
      "label-large": { "size": 14, "line-height": 20, "weight": 500 },
      "label-medium": { "size": 12, "line-height": 16, "weight": 500 },
      "label-small": { "size": 11, "line-height": 16, "weight": 500 }
    },
    "shape": {
      "corner-extra-small": 4,
      "corner-small": 8,
      "corner-medium": 12,
      "corner-large": 16,
      "corner-extra-large": 28
    },
    "spacing": {
      "none": 0,
      "extra-small": 4,
      "small": 8,
      "medium": 16,
      "large": 24,
      "extra-large": 32
    },
    "elevation": {
      "level-0": { "shadow": "none", "overlay": 0 },
      "level-1": { "shadow": "0 1px 2px rgba(0,0,0,0.3)", "overlay": 0.05 },
      "level-2": { "shadow": "0 1px 2px rgba(0,0,0,0.3)", "overlay": 0.08 },
      "level-3": { "shadow": "0 1px 3px rgba(0,0,0,0.3)", "overlay": 0.11 }
    }
  },
  "harmonyos-design": {
    "color": {
      "brand-primary": "#007DFF",
      "brand-secondary": "#4CAF50",
      "success": "#4CAF50",
      "warning": "#FF9900",
      "error": "#FA2D2D",
      "neutral-gray-10": "#000000",
      "neutral-gray-20": "#1A1A1A",
      "neutral-gray-90": "#E6E6E6",
      "neutral-gray-95": "#F2F2F2",
      "neutral-gray-100": "#FFFFFF"
    },
    "typography": {
      "h1": { "size": 32, "weight": 500 },
      "h2": { "size": 28, "weight": 500 },
      "h3": { "size": 24, "weight": 500 },
      "h4": { "size": 20, "weight": 500 },
      "body": { "size": 16, "weight": 400 },
      "caption": { "size": 12, "weight": 400 },
      "button": { "size": 14, "weight": 500 }
    },
    "shape": {
      "radius-small": 4,
      "radius-medium": 8,
      "radius-large": 12,
      "radius-capsule": 999
    },
    "spacing": {
      "xs": 4,
      "sm": 8,
      "md": 12,
      "lg": 16,
      "xl": 24
    }
  }
}
```

### 7.4 参考文献

1. Google. (2024). Material Design 3 Specification. https://m3.material.io/
2. Huawei. (2024). HarmonyOS Design Guidelines. https://developer.huawei.com/consumer/cn/design/
3. Google. (2024). Material Design Color System. https://m3.material.io/styles/color/
4. Huawei. (2024). ArkUI Component Reference. https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/
5. Google. (2024). Material Design Motion. https://m3.material.io/styles/motion/

---

## 变更记录

| 版本 | 日期 | 变更内容 | 作者 |
|------|------|----------|------|
| 1.0 | 2026-05-22 | 初始版本 | Claude |