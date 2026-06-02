# Framood 项目架构规划 v2.0

> 版本：2.0
> 更新日期：2026年5月29日
> 变更说明：基于Spec审查修复问题，明确原型与产品映射关系
> 目的：定义项目目录结构、技术架构、原型映射规则

---

## 一、技术栈定义

### 1.1 应用技术栈

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **运行平台** | HarmonyOS NEXT 5.0+ | 原生 App |
| **开发语言** | ArkTS | 华为官方声明式语言 |
| **UI框架** | ArkUI | 原生组件库，无需 Web 技术 |
| **图像处理** | @ohos.multimedia.image | PixelMap + Canvas |
| **文件操作** | @ohos.file.photoPicker | 照片选择 |
| **媒体库** | @ohos.file.mediaLibrary | 相册保存 |

### 1.2 原型技术栈

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **原型格式** | HTML + Tailwind CSS + JavaScript | 纯 Web |
| **目的** | 交互验证 | 验证用户旅程、UI布局、状态转换 |
| **与产品关系** | 不作为开发代码参考 | 原型验证交互逻辑，产品使用 ArkUI 实现 |

---

## 二、原型与产品映射规则

### 2.1 映射对照表

| 原型元素（Web） | 产品实现方式（ArkUI） | 示例 |
|---------|-------------|------|
| HTML 页面 `<div class="page">` | `@Entry @Component struct PageName {}` | 每个页面对应一个 struct |
| Tailwind CSS 类 `.bg-brand` | `.backgroundColor('#0066cc')` | 样式属性替代 CSS 类 |
| CSS 变量 `var(--color-brand)` | `Constants.ets` 常量定义 | 统一常量文件 |
| JavaScript `state = {}` | `@State state: StateType = {}` | ArkUI 状态装饰器 |
| `onclick="func()"` | `.onClick(() => { this.func() })` | 事件处理 |
| `classList.toggle()` | 条件渲染 `.visibility()` 或三元表达式 | 状态切换 |
| CSS 动画 `transition` | `.animation()` 属性 | 动画效果 |

### 2.2 设计 Token 映射

**CSS 变量（原型）** → **ArkTS 常量（产品）**

```typescript
// Constants.ets
export class Colors {
  static readonly BRAND_PRIMARY: string = '#0066cc';
  static readonly TEXT_PRIMARY: string = '#1d1d1f';
  static readonly TEXT_MUTED: string = '#86868b';
  static readonly BG_PRIMARY: string = '#ffffff';
  static readonly BG_SECONDARY: string = '#f5f5f7';
}

export class Spacing {
  static readonly XXS: number = 4;
  static readonly XS: number = 8;
  static readonly SM: number = 12;
  static readonly MD: number = 16;
  static readonly LG: number = 24;
}

export class FontSize {
  static readonly DISPLAY_MD: number = 34;
  static readonly TITLE: number = 28;
  static readonly BODY: number = 17;
  static readonly CAPTION: number = 14;
}
```

### 2.3 页面映射

| 原型页面 HTML | ArkUI 页面文件 | 路径 |
|-------------|--------------|------|
| `page-home` | `Index.ets` | `pages/Index.ets` |
| `page-select` | `PhotoSelect.ets` | `pages/PhotoSelect.ets` |
| `page-edit` | `Editor.ets` | `pages/Editor.ets` |
| `page-quality` | `QualitySettings.ets` | `pages/QualitySettings.ets` |
| `page-success` | `Success.ets` | `pages/Success.ets` |

---

## 三、项目根目录结构

```
framood/
├── docs/                           # 文档库
│   ├── 0_meta/                     # 元级定义
│   ├── 1_product/                  # 产品与项目管理
│   ├── 2_design/                   # 设计产出
│   │   ├── ui-design.md            # UI设计文档
│   │   ├── user-journey-state-machine.md  # 交互流程
│   │   ├── design_tokens.json      # 设计Token定义
│   │   └── prototype/              # Web原型
│   │       └── index.html          # 原型文件
│   ├── 3_development/              # 开发规约
│   └── superpowers/                # 技能相关
│       └── specs/                  # 技术规格
│           ├── spec.md             # 需求规格
│           ├── 001-watermark-template-spec.md
│           ├── 003-user-interaction-spec.md
│           └── asset-inventory.md  # 资源清单
│
├── framood-harmonyos/              # 鸿蒙应用工程
│   └── entry/src/main/
│       ├── ets/                    # ArkTS源码
│       │   ├── pages/              # 页面
│       │   ├── components/         # 组件
│       │   ├── services/           # 服务层
│       │   ├── models/             # 数据模型
│       │   ├── utils/              # 工具函数
│       │   └── common/             # 公共定义
│       │       └── Constants.ets   # 设计Token常量
│       └── resources/rawfile/      # 资源文件
│           ├── templates/          # 模板JSON配置
│           ├── fonts/              # 字体文件
│           ├── logos/              # Logo文件
│           └── icons/              # 图标文件
│
├── openspec/                       # OpenSpec变更管理
│   └── changes/                    # 需求变更记录
│
├── CLAUDE.md                       # 项目开发指令
└── README.md                       # 项目说明
```

---

## 四、ArkTS工程详细结构

```
framood-harmonyos/entry/src/main/ets/
├── entryability/
│   └── EntryAbility.ets            # 应用入口
│
├── pages/
│   ├── Index.ets                   # 首页（空状态）
│   ├── PhotoSelect.ets             # 照片选择页
│   ├── Editor.ets                  # 水印编辑页
│   ├── QualitySettings.ets         # 画质设置页
│   └── Success.ets                 # 导出成功页
│
├── components/
│   ├── PhotoPreview.ets            # 照片预览组件
│   ├── TemplateCard.ets            # 模板卡片组件
│   ├── WatermarkRenderer.ets       # 水印渲染组件（Canvas）
│   ├── HalfSheet.ets               # 半屏参数配置卡片
│   ├── ToggleSwitch.ets            # 开关组件
│   ├── QualitySlider.ets           # 质量滑块组件
│   └── ModalDialog.ets             # 弹窗组件
│
├── services/
│   ├── PhotoService.ets            # 照片选择服务
│   ├── EXIFService.ets             # EXIF提取服务
│   ├── WatermarkService.ets        # 水印合成服务
│   ├── ExportService.ets           # 导出服务
│   ├── TemplateService.ets         # 模板管理服务
│   └── PermissionService.ets       # 权限服务
│
├── models/
│   ├── PhotoInfo.ets               # 照片信息模型
│   ├── EXIFData.ets                # EXIF数据模型
│   ├── WatermarkTemplate.ets       # 水印模板模型
│   ├── WatermarkConfig.ets         # 水印配置模型
│   └── QualitySettings.ets         # 画质设置模型
│
├── utils/
│   ├── EXIFParser.ets              # EXIF解析工具
│   ├── CanvasHelper.ets            # Canvas绘制工具
│   ├── FormatHelper.ets            # 格式化工具
│   └── DeviceMapper.ets            # 机型映射工具
│
└── common/
    ├── Constants.ets               # 常量（颜色、尺寸、字体）
    ├── Types.ets                   # 类型定义
    ├── Enums.ets                   # 枚举定义
    └── Logger.ets                  # 日志工具
```

---

## 五、数据流设计

### 5.1 主流程数据流

```
┌──────────────────────────────────────────────────────────────────────┐
│                        主流程数据流                                    │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  用户点击「选择照片」                                                 │
│      │                                                               │
│      ▼                                                               │
│  PhotoPicker API → PhotoInfo (URI、尺寸)                             │
│      │                                                               │
│      ▼                                                               │
│  EXIFService.extract() → EXIFData                                    │
│      │                                                               │
│      ├──────────────────┐                                            │
│      │                  │                                            │
│      ▼                  ▼                                            │
│  DeviceMapper        TemplateService                                 │
│  .getBrand()         .loadTemplates()                                │
│      │                  │                                            │
│      ▼                  ▼                                            │
│  默认模板ID          模板列表（内置JSON）                             │
│      │                  │                                            │
│      └────────────────────┘                                          │
│                │                                                      │
│                ▼                                                      │
│        WatermarkConfig（用户选择）                                    │
│                │                                                      │
│                ▼                                                      │
│        WatermarkService.render() → PixelMap                          │
│                │                                                      │
│                ▼                                                      │
│        QualitySettings（分辨率、质量）                                │
│                │                                                      │
│                ▼                                                      │
│        ExportService.save() → 相册                                   │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### 5.2 模板数据来源

| 数据来源 | MVP | Phase 2 | 存储位置 |
|---------|-----|---------|---------|
| **内置模板** | ✅ JSON配置 | ✅ | `resources/rawfile/templates/*.json` |
| **服务端推送** | ❌ | ✅ | 预留接口 |
| **华为系统模板** | ❌ | ❌ | 技术不可行 |

---

## 六、模块职责划分

### 6.1 页面职责

| 页面 | 职责 | 状态管理 |
|------|------|---------|
| Index | 首页空状态，引导选择照片 | `@State currentPage` |
| PhotoSelect | 照片选择网格 | `@State selectedPhoto` |
| Editor | 水印编辑，模板选择，参数配置 | `@State template`, `@State params` |
| QualitySettings | 分辨率、质量、格式设置 | `@State qualitySettings` |
| Success | 导出成功提示 | 无状态 |

### 6.2 服务层职责

| 服务 | 职责 | 依赖API |
|------|------|---------|
| **PhotoService** | 照片选择 | `@ohos.file.photoPicker` |
| **EXIFService** | EXIF提取 | `@ohos.multimedia.image` |
| **WatermarkService** | Canvas渲染、水印合成 | `image.createPixelMap()` |
| **ExportService** | 图片导出、相册保存 | `@ohos.file.mediaLibrary` |
| **TemplateService** | 模板加载、配置管理 | JSON解析 |
| **PermissionService** | 权限申请 | `@ohos.abilityAccessCtrl` |

---

## 七、下一步行动

| 优先级 | 行动项 | 对应文档 |
|-------|-------|---------|
| P0 | 创建 `framood-harmonyos` 工程 | — |
| P0 | 实现 `Constants.ets` 设计Token | 参照 `design_tokens.json` |
| P0 | 实现 4 个内置模板 JSON | 参照 `001-watermark-template-spec.md` |
| P1 | 补充资源文件（字体、图标） | 参照 `asset-inventory.md` |

---

*项目架构规划 v2.0*
*2026-05-29*