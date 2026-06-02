# Framood MVP 需求规格说明书 v4.0

> 版本：4.0
> 更新日期：2026年5月29日
> 变更说明：基于Spec审查报告修复11项问题，明确技术栈、资源来源、边界处理、智能推荐简化
> 状态：评审修订

---

## 一、文档体系架构

Framood 需求规格采用分层文档架构：

| 层级 | 文档类型 | 文档名称 | 内容定位 |
|------|---------|---------|---------|
| **L1** | 需求汇总 | 本文档 (spec.md) | 功能需求、验收标准、非功能需求总览 |
| **L1** | 技术架构 | architecture-harmonyos.md | HarmonyOS技术栈定义、原型映射规则 |
| **L2** | 数据格式规格 | 001-watermark-template-spec.md | 水印模板JSON结构、字体、颜色、位置等静态规格 |
| **L2** | 用户交互流程规格 | 003-user-interaction-spec.md | 用户操作流程、界面交互、业务逻辑、状态转换等动态规格 |
| **L2** | 用户旅程状态机 | 004-user-journey-spec.md | 用户交互细节、状态变化、页面状态展示 |
| **L2** | UI原型设计 | 005-ui-design-spec.md | UI页面原型、设计Token映射、组件规格 |
| **L2** | 样张规格 | 002-watermark-sample-spec.md | 水印模板样张生成规格和提示词 |
| **L2** | 资源清单 | asset-inventory.md | 应用预置资源清单（Logo、字体、图标等） |

**引用关系**：
```
spec.md (L1 需求汇总)
    │
    ├── architecture-harmonyos.md (L1 HarmonyOS技术架构)
    ├── 001-watermark-template-spec.md (L2 数据格式)
    ├── 003-user-interaction-spec.md (L2 交互流程)
    ├── 004-user-journey-spec.md (L2 用户旅程状态机)
    ├── 005-ui-design-spec.md (L2 UI原型设计)
    ├── 002-watermark-sample-spec.md (L2 样张规格)
    └── asset-inventory.md (L2 资源清单)
```

---

## 二、技术栈定义

### 2.1 应用技术栈

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **运行平台** | HarmonyOS NEXT 5.0+ | 原生 App |
| **开发语言** | ArkTS | 华为官方语言 |
| **UI框架** | ArkUI | 原生声明式组件库 |
| **图像处理** | @ohos.multimedia.image | PixelMap + Canvas |
| **文件操作** | @ohos.file.photoPicker / mediaLibrary | 相册读写 |

### 2.2 原型技术栈

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **原型格式** | HTML + Tailwind CSS + JS | 纯 Web |
| **目的** | 交互验证 | 验证用户旅程、UI布局、状态转换 |
| **与产品关系** | 不作为开发参考 | 原型验证交互，产品使用 ArkUI 实现 |

**原型与产品映射规则**：

| 原型元素 | 产品实现方式 |
|---------|-------------|
| HTML 页面结构 | ArkUI `@Entry` + `@Component` |
| Tailwind CSS 类 | ArkUI 样式属性（`.width()`, `.height()`, `.backgroundColor()` 等） |
| JavaScript 状态管理 | ArkUI `@State`, `@Prop`, `@Link` |
| CSS 变量（design_tokens） | ArkUI `Constants.ets` 常量定义 |
| JavaScript 事件处理 | ArkUI `.onClick()`, `.onChange()` |

### 2.3 混合开发可行性说明

> **结论**：MVP 不使用 Web 技术栈作为 App UI。

HarmonyOS 支持 WebView 混合开发，但存在以下问题：

| 问题 | 说明 |
|------|------|
| **性能开销** | WebView 渲染性能低于原生 ArkUI，图像处理场景不适用 |
| **API访问** | `@ohos.multimedia.image` 等 API 在 WebView 中调用复杂 |
| **用户体验** | 混合开发的手势传递、滚动体验不如原生 |

**决策**：App 界面使用原生 ArkUI，Web 原型仅用于交互验证。

---

## 三、水印资源来源定义

### 3.1 资源访问可行性分析

基于华为相关公开文档，华为水印资源分为两类：

| 资源类型 | 存储位置 | 第三方App可访问性 | 说明 |
|---------|---------|------------------|------|
| **系统内置水印** | `/system/etc/watermark/` | ❌ 不可访问 | 需系统权限，第三方App无法读取 |
| **云推水印资源** | `/data/camera_service/network_resource/` | ❌ 不可访问 | 相机服务私有目录 |
| **水印裁剪图(FRAGMENT_MAP)** | 照片私有box | ✅ 可解析 | 存储在JPEG/HEIF文件中，可读取但无法反向生成水印 |
| **XMAGE Logo** | 系统资源 | ❌ 不可访问 | 华为品牌资源，第三方无法使用 |
| **方正行楷字体** | Nova系统内置 | ❌ 不可访问 | 系统私有字体 |

**结论**：华为水印资源、XMAGE Logo、方正行楷字体等均**不可**被第三方App直接访问。

### 3.2 Framood 资源策略

| 资源类型 | MVP策略 | Phase 2 | 来源 |
|---------|---------|---------|------|
| **模板配置** | 内置 JSON | + 服务端推送 | 自定义 |
| **XMAGE标识** | 文字渲染（不用Logo） | 同 MVP | 自绘 |
| **品牌字体** | 系统默认字体 | + 开源字体 | HarmonyOS Sans |
| **机型名称** | EXIF.Model 提取 | 同 MVP | 照片元数据 |
| **边框图案** | Canvas 绘制 | + 图片素材 | 自绘 |
| **毛笔字体（内嵌水印）** | 待定 | 开源书法字体 | asset-inventory.md 定义 |

### 3.3 内置模板定义

MVP 内置 4 种模板，不依赖华为系统资源：

| 模板ID | 名称 | 水印类型 | 元素组合 | 资源来源 |
|--------|------|---------|---------|---------|
| **T-A** | 下边框水印 | BORDER_BOTTOM | 机型+XMAGE(文字)+参数+时间+自定义 | 全自绘 |
| **T-B** | 四边边框水印 | BORDER_TOP_BOTTOM | 机型+XMAGE(文字)+色卡+参数 | 全自绘 |
| **T-C** | 内嵌水印 | INNER | 主题文字+机型+XMAGE(文字) | 字体待定 |
| **T-D** | 渐变边框 | BORDER_ROUND（参考橘子海） | 机型+XMAGE(文字) | 全自绘 |

> 模板详细规格见：`001-watermark-template-spec.md`

### 3.4 限时水印机制处理

| 功能 | MVP | Phase 2 | 说明 |
|------|-----|---------|------|
| **限时水印机制**（云端控制周期） | ❌ 移除 | ✅ | 需服务端支持 |
| **限时水印样式**（作为普通模板） | ✅ T-D | ✅ | 橘子海样式作为内置模板，不限时 |

---

## 四、功能需求总览

### 4.1 MVP功能列表（11项全保留）

| 功能ID | 功能名称 | 详细规格位置 | 优先级 | MVP实现方式 |
|--------|---------|-------------|--------|------------|
| F01 | 照片选择（按相册顺序） | §003.三 | P0 | PhotoPicker API |
| F02 | 水印模板选择 | §003.四.4.2 | P0 | 内置4模板，Canvas渲染 |
| F03 | 参数配置 | §003.四.4.3 | P0 | 开关+自定义文本 |
| F04 | 实时预览 | §003.四.4.4 | P0 | Canvas实时渲染 |
| F05 | 画质设置 | §003.五 | P0 | 分辨率+压缩质量 |
| F06 | 导出保存 | §003.六 | P0 | MediaLibrary API |
| F07 | EXIF处理 | §003.六.6.5 | P0 | 保留+GPS移除 |
| F08 | 机型识别 | §003.三.3.3 | P0 | EXIF.Model提取 |
| F09 | 默认模板选择 | §003.三.3.3 | P1 | 机型品牌匹配，非智能推荐 |
| F10 | 错误处理 | §003.八 | P0 | 权限/加载/导出错误 |
| F11 | 权限管理 | §003.十 | P0 | READ/WRITE权限 |

> **F09 简化说明**：移除"智能推荐"，改为"机型品牌匹配"
> - 华为机型 → 默认 T-A 下边框模板
> - 其他机型 → 默认 T-A
> - 仅为默认选择，不强制用户

### 4.2 水印模板布局差异说明

| 模板类型 | 元素组合 | 布局特点 |
|---------|---------|---------|
| **T-A 下边框** | 机型+XMAGE(文字)+参数+时间+自定义 | 机型居左，其他居右纵向排列 |
| **T-B 四边框** | 机型+XMAGE(文字)(顶)+色卡+参数(底) | 四边白边框，顶部/底部布局 |
| **T-C 内嵌** | 主题文字+机型+XMAGE(文字) | 居中，无底纹，全白色 |
| **T-D 渐变边框** | 机型+XMAGE(文字) | 四边橘蓝渐变边框 |

---

## 五、边界与异常处理规格

### 5.1 自定义文本限制

| 规则 | 定义 |
|------|------|
| **长度计算** | 按字符计数（不按字节），Emoji 计为 1 字符 |
| **最大长度** | 12 字符 |
| **警告长度** | 10 字符（开始显示剩余计数） |
| **超长截断** | 输入时自动截断，粘贴时截断前 12 字符 |
| **警告提示** | ≥10 字符：显示"剩余 X 字符"；≥12 字符：显示"已达上限" |
| **UI反馈** | 超限时输入框边框变红（#cc0000），不弹 Toast |

### 5.2 图片格式支持

| 格式 | MVP | 处理方式 |
|------|-----|---------|
| JPEG | ✅ | 主格式 |
| PNG | ✅ | 支持格式 |
| HEIC | ⚠️ 待验证 | 需测试 HarmonyOS API 兼容性，失败时提示"格式不支持" |

### 5.3 网络依赖说明

| 功能 | 是否需要网络 | 说明 |
|------|-------------|------|
| 照片选择 | ❌ | 本地相册 |
| EXIF提取 | ❌ | 本地解析 |
| 水印合成 | ❌ | Canvas 本地渲染 |
| 导出保存 | ❌ | 本地存储 |
| 限时水印下载 | ❌ MVP | Phase 2 功能 |

**结论**：MVP 完全离线可用，无需网络状态判断逻辑。

### 5.4 GPS 移除规格

| 场景 | UI表现 | EXIF处理 |
|------|---------|---------|
| **默认状态** | GPS 开关关闭，水印不显示地点 | — |
| **开关开启** | 水印显示地点信息 | 保留 GPS 字段 |
| **开关关闭** | 水印隐藏地点行，无占位文字 | 删除 GPSLatitude/GPSLongitude |

### 5.5 权限处理流程

| 权限 | 申请时机 | 拒绝处理 |
|------|---------|---------|
| **READ_IMAGEVIDEO** | 首页"选择照片"点击时 | 弹窗提示 + "前往设置"按钮，首页显示权限引导 |
| **WRITE_IMAGEVIDEO** | 导出页"保存导出"点击时 | 弹窗提示 + "前往设置"按钮，导出流程中断 |

**权限设置跳转**：调用 `@ohos.settings` API 打开系统权限设置页。

### 5.6 错误处理规格

| 错误类型 | 错误代码 | UI处理 | 系统处理 |
|---------|---------|---------|---------|
| **权限拒绝** | E001 | 弹窗 + "前往设置"按钮 | 调用 `@ohos.settings` |
| **照片加载失败** | E002 | Error 页面 + "重新选择"按钮 | 返回首页 |
| **EXIF提取失败** | E003 | 使用默认值兜底 | 不阻断流程 |
| **内存溢出** | E004 | 提示"图片过大，请降低分辨率" | 返回画质设置页 |
| **导出失败** | E005 | Error 页面 + "重试"按钮 | 保持编辑状态 |
| **合成失败** | E006 | Error 页面 + "返回编辑"按钮 | 返回编辑页 |

---

## 六、用户操作流程总览

### 6.1 主流程

```
启动App → 选择照片 → 编辑水印 → 设置画质 → 导出保存
```

### 6.2 关键操作规格

| 操作 | 交互方式 | 技术实现 | 耗时要求 |
|------|---------|---------|---------|
| 选择照片 | PhotoPicker API | @ohos.file.photoPicker | < 500ms |
| 加载照片 | ImageSource API | image.createImageSource() | < 1s |
| 提取EXIF | ImageProperty API | ImageSource.getImageProperty() | < 100ms |
| 模板切换 | 点击卡片 | Canvas 实时渲染 | < 200ms |
| 参数配置 | 开关/输入框 | Canvas 实时渲染 | < 200ms |
| 水印合成 | Canvas渲染 | PixelMap合成 | < 700ms |
| 导出保存 | MediaLibrary API | 保存到相册 | < 2s |

---

## 七、数据架构规格

### 7.1 模板数据来源

| 数据来源 | MVP | Phase 2 | 存储位置 |
|---------|-----|---------|---------|
| **内置模板** | ✅ JSON 配置 | ✅ | `resources/rawfile/templates/*.json` |
| **服务端推送** | ❌ | ✅ | 预留接口，暂不实现 |
| **华为系统模板** | ❌ | ❌ | 技术不可行 |

### 7.2 模板 JSON 结构

```typescript
interface WatermarkTemplate {
  id: string;                 // T-A, T-B, T-C, T-D
  name: string;               // 显示名称
  type: 'BORDER_BOTTOM' | 'BORDER_TOP_BOTTOM' | 'INNER' | 'BORDER_ROUND';
  elements: TemplateElement[];
  layout: LayoutConfig;
  defaultParams: ParamConfig;
}
```

> 详细结构见：`001-watermark-template-spec.md`

---

## 八、UI规格摘要

| 参数 | 值 | 说明 |
|------|-----|------|
| CARD_BORDER_RADIUS | 8px | 卡片圆角 |
| CARD_IMAGE_RADIO | 0.75 | 卡片宽高比 |
| PREVIEW_WIDTH | 280px（竖屏）/ 380px（横屏） | 预览宽度 |
| CUSTOM_TEXT_MAX_LENGTH | 12 | 最大字符 |
| BUTTON_HEIGHT | 44px | 按钮高度 |

---

## 九、性能规格

| 指标 | 要求 |
|------|------|
| 应用启动 | < 2s |
| 照片加载 | < 1s |
| EXIF提取 | < 100ms |
| 预览刷新 | < 200ms |
| 水印合成 | < 700ms |
| 导出保存 | < 2s |

---

## 十、验收标准

| 功能ID | 验收项 | 测试用例位置 |
|--------|--------|-------------|
| F01 | 照片正确加载 | §003.十二 TC001 |
| F02 | 模板切换正常 | §003.十二 TC002 |
| F03 | 参数配置生效 | §003.十二 TC003-005 |
| F05 | 画质设置生效 | §003.十二 TC006-007 |
| F06 | 导出保存成功 | §003.十二 TC008 |
| F07 | EXIF正确处理 | §003.十二 TC009-010 |

---

## 十一、参考资料索引

| 文档编号 | 文档名称 | 路径 |
|---------|---------|------|
| 001 | 水印模板规格文档 | docs/superpowers/specs/001-watermark-template-spec.md |
| 002 | 水印样张规格文档 | docs/superpowers/specs/002-watermark-sample-spec.md |
| 003 | 用户交互流程规格文档 | docs/superpowers/specs/003-user-interaction-spec.md |
| 004 | 用户旅程状态机文档 | docs/superpowers/specs/004-user-journey-spec.md |
| 005 | UI原型设计文档 | docs/superpowers/specs/005-ui-design-spec.md |
| 006 | 资源清单文档 | docs/superpowers/specs/asset-inventory.md |
| 240 | 华为水印UI规格文档 | docs/research/240-huawei-watermark-ui-spec.md |
| 236 | 华为水印云推资源文档 | docs/research/236-huawei-watermark-resources.md |

---

**文档状态**：评审修订
**修订内容**：修复11项审查问题
**下一步**：创建 asset-inventory.md