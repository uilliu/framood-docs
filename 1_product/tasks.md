# Framood MVP 任务清单

> **版本**：v2.0
> **更新日期**：2026年5月29日
> **基于规格**：spec.md v4.0
> **状态**：待开始

---

## 一、任务总览

### 1.1 功能编号映射

| 功能ID | 功能名称 | 实现模块 | 规格位置 |
|--------|---------|---------|---------|
| F01 | 照片选择 | PhotoService + Index页 | §003.三 |
| F02 | 水印模板选择 | TemplateService + TemplateCard | §003.四.4.2 |
| F03 | 参数配置 | HalfSheet + WatermarkConfig | §003.四.4.3 |
| F04 | 实时预览 | WatermarkRenderer | §003.四.4.4 |
| F05 | 画质设置 | QualitySettings页 + QualitySlider | §003.五 |
| F06 | 导出保存 | ExportService | §003.六 |
| F07 | EXIF处理 | EXIFService + ExportService | §003.六.6.5 |
| F08 | 机型识别 | DeviceMapper | §003.三.3.3 |
| F09 | 默认模板选择 | DeviceMapper + TemplateService | §003.三.3.3 |
| F10 | 错误处理 | ErrorCode枚举 + Services | §003.八 |
| F11 | 权限管理 | PermissionService | §003.十 |

### 1.2 开发周期

| 阶段 | 周期 | 目标 | 任务数 |
|------|------|------|--------|
| Phase 1 (基础设施) | 3天 | 项目结构 + 设计系统 + 模板配置 | 10 |
| Phase 2 (组件层) | 5天 | UI组件库 | 8 |
| Phase 3 (服务+页面) | 7天 | 业务逻辑 + 完整流程 | 12 |
| Phase 4 (测试验收) | 2天 | 集成测试 + 验收 | 2 |

---

## 二、Phase 1：基础设施

### T001 项目结构创建 [P0] [2h]

**描述**：创建 HarmonyOS NEXT 项目骨架

**任务项**：
- [ ] 创建 framood-harmonyos 项目目录结构
- [ ] 创建 ets 目录（common, components, services, pages, utils）
- [ ] 创建 resources/rawfile 目录（templates, fonts, icons）
- [ ] 创建 scripts 目录
- [ ] 创建 placeholder EntryAbility.ets
- [ ] 创建 placeholder Index.ets

**验收标准**：
- 目录结构符合架构规划
- placeholder文件可编译

**依赖**：无

**覆盖功能**：基础设施

---

### T002 Design Token转换脚本 [P0] [2h]

**描述**：创建 design_tokens.json → Constants.ets 转换脚本

**任务项**：
- [ ] 创建 scripts/generate-constants.js
- [ ] 实现 Colors 类生成
- [ ] 实现 FontSize/FontWeight 类生成
- [ ] 实现 Spacing 类生成
- [ ] 实现 Sizing 类生成
- [ ] 实现 Radius/BorderWidth/Opacity 类生成
- [ ] 运行脚本生成 Constants.ets

**验收标准**：
- Constants.ets 包含所有 design token 常量
- 常量值与 design_tokens.json 一致

**依赖**：T001

**覆盖功能**：基础设施

---

### T003 Enums定义 [P0] [1h]

**描述**：定义应用枚举类型

**任务项**：
- [ ] 定义 AppState 枚举（11状态）
- [ ] 定义 WatermarkType 枚举（4模板类型）
- [ ] 定义 ParamType 枚举（SWITCH/SELECTOR/CUSTOM）
- [ ] 定义 ErrorCode 枚举（E001-E006）
- [ ] 定义 SaveMethod 枚举
- [ ] 定义 ResolutionOption 枚举
- [ ] 定义 OutputFormat 枚举
- [ ] 定义 GPSHandling 枚举

**验收标准**：
- Enums.ets 无 TypeScript 语法错误
- 枚举值与 spec.md 一致

**依赖**：T001

**覆盖功能**：F10 (错误处理)

---

### T004 Types定义 [P0] [2h]

**描述**：定义应用数据类型接口

**任务项**：
- [ ] 定义 PhotoInfo 接口（含 isValid 字段）
- [ ] 定义 EXIFData 接口
- [ ] 定义 TemplateElement/ElementPosition/ElementStyle 接口
- [ ] 定义 LayoutConfig/ParamConfig 接口
- [ ] 定义 WatermarkTemplate 接口
- [ ] 定义 WatermarkConfig 接口
- [ ] 定义 QualitySettings 接口
- [ ] 定义 ExportResult 接口
- [ ] 定义 EditorState/AppStateData 接口

**验收标准**：
- Types.ets 无 TypeScript 语法错误
- 接口结构与模板 JSON 一致

**依赖**：T003

**覆盖功能**：数据层基础

---

### T005 Logger工具 [P0] [1h]

**描述**：创建日志工具封装 hilog

**任务项**：
- [ ] 创建 Logger 类
- [ ] 封装 debug/info/warn/error 方法
- [ ] 实现 format 辅助方法
- [ ] 实现 createLogger 工厂函数

**验收标准**：
- Logger 可正常调用 hilog

**依赖**：T001

**覆盖功能**：基础设施

---

### T006 模板A JSON配置 [P0] [1h]

**描述**：创建下边框水印模板配置

**任务项**：
- [ ] 创建 template-a.json
- [ ] 定义 elements（deviceName, xmage, captureParam, time, location, customText）
- [ ] 定义 layout（borderHeightRatio 15%）
- [ ] 定义 defaultParams
- [ ] 定义 formatConfig

**验收标准**：
- JSON 结构符合 WatermarkTemplate 接口
- 元素位置规格正确

**依赖**：T001

**覆盖功能**：F02

---

### T007 模板B JSON配置 [P0] [1h]

**描述**：创建四边边框水印模板配置

**任务项**：
- [ ] 创建 template-b.json
- [ ] 定义 elements（deviceName, xmage, colorPalette, captureParam）
- [ ] 定义 layout（top 8%, bottom 10%, left/right 3%）
- [ ] 定义 defaultParams

**验收标准**：
- JSON 结构符合 WatermarkTemplate 接口

**依赖**：T001

**覆盖功能**：F02

---

### T008 模板C JSON配置 [P0] [1h]

**描述**：创建内嵌水印模板配置

**任务项**：
- [ ] 创建 template-c.json
- [ ] 定义 elements（themeText, deviceName, xmage）
- [ ] 定义 layout（无边框，透明背景）
- [ ] 定义 themeOptions（毕业快乐/青春不散场/未来可期）
- [ ] 定义元素样式（白色 + 阴影）

**验收标准**：
- JSON 结构正确
- 主题文字样式符合规格

**依赖**：T001

**覆盖功能**：F02

---

### T009 模板D JSON配置 [P0] [1h]

**描述**：创建渐变边框水印模板配置

**任务项**：
- [ ] 创建 template-d.json
- [ ] 定义 elements（deviceName, xmage）
- [ ] 定义 layout（渐变边框 12px）
- [ ] 定义 gradient 配置（橘蓝渐变）

**验收标准**：
- JSON 结构正确
- 渐变配色符合规格

**依赖**：T001

**覆盖功能**：F02

---

### T010 设备映射配置 [P0] [0.5h]

**描述**：创建设备品牌→模板映射配置

**任务项**：
- [ ] 创建 deviceMapping.json
- [ ] 定义 HUAWEI → T-A 映射
- [ ] 定义其他品牌兜底映射
- [ ] 定义 displayName 字段

**验收标准**：
- 映射结构正确
- F09 默认模板规则符合规格

**依赖**：T001

**覆盖功能**：F08, F09

---

## 三、Phase 2：组件层

### T011 Button组件 [P0] [2h]

**描述**：创建按钮组件

**任务项**：
- [ ] 创建 PrimaryButton 组件
- [ ] 创建 SecondaryButton 组件
- [ ] 应用 Constants 设计 token
- [ ] 实现 disabled 状态
- [ ] 实现 onClick 事件

**验收标准**：
- 按钮样式符合 design_tokens
- 高度 44px

**依赖**：T002

**覆盖功能**：UI基础设施

---

### T012 ToggleSwitch组件 [P0] [2h]

**描述**：创建开关切换组件

**任务项**：
- [ ] 创建 ToggleSwitch 组件
- [ ] 实现 on/off 状态
- [ ] 实现动画过渡
- [ ] 实现 onChange 回调
- [ ] 应用设计 token

**验收标准**：
- 宽度 48px，高度 28px
- 状态切换流畅

**依赖**：T002

**覆盖功能**：F03

---

### T013 QualitySlider组件 [P0] [2h]

**描述**：创建质量滑块组件

**任务项**：
- [ ] 创建 QualitySlider 组件
- [ ] 实现范围 50%-100%
- [ ] 实现步进 10%
- [ ] 实现数值显示
- [ ] 实现 onChange 回调

**验收标准**：
- 滑块范围正确
- 默认值 80%

**依赖**：T002

**覆盖功能**：F05

---

### T014 ModalDialog组件 [P0] [2h]

**描述**：创建模态对话框组件

**任务项**：
- [ ] 创建 ModalDialog 组件
- [ ] 实现 确认/取消 按钮
- [ ] 实现 标题/内容 自定义
- [ ] 实现 关闭动画

**验收标准**：
- 对话框显示正确
- 按钮回调正确

**依赖**：T011

**覆盖功能**：F10 (错误处理UI)

---

### T015 TemplateCard组件 [P0] [3h]

**描述**：创建模板选择卡片组件

**任务项**：
- [ ] 创建 TemplateCard 组件
- [ ] 实现 模板缩略图渲染
- [ ] 实现 选中状态标识
- [ ] 实现 编辑图标显示
- [ ] 实现 onClick 回调
- [ ] 应用设计 token（宽度129px，宽高比0.75）

**验收标准**：
- 卡片尺寸正确
- 选中状态明显

**依赖**：T002, T006-T009

**覆盖功能**：F02

---

### T016 PhotoPreview组件 [P0] [3h]

**描述**：创建照片预览组件

**任务项**：
- [ ] 创建 PhotoPreview 组件
- [ ] 实现 skeleton 加载态
- [ ] 实现 error 状态
- [ ] 实现 图片显示
- [ ] 实现 宽度自适应（竖屏280px/横屏380px）

**验收标准**：
- 加载态显示正确
- 错误态显示正确

**依赖**：T002

**覆盖功能**：F04

---

### T017 HalfSheet组件 [P0] [4h]

**描述**：创建半屏参数配置面板组件

**任务项**：
- [ ] 创建 HalfSheet 组件
- [ ] 实现 开关面板动画
- [ ] 实现 参数控件列表
  - [ ] deviceType ToggleSwitch
  - [ ] xmageStyle ToggleSwitch
  - [ ] captureParam ToggleSwitch
  - [ ] captureTime ToggleSwitch
  - [ ] captureLocation ToggleSwitch
  - [ ] customText TextInput（12字符上限）
- [ ] 实现 参数变更回调
- [ ] 实现 截断提示（≥10字符显示剩余，≥12显示上限）

**验收标准**：
- 参数控件正常工作
- 自定义文本截断符合 §5.1 规格

**依赖**：T012

**覆盖功能**：F03

---

### T018 WatermarkRenderer组件 [P0] [6h]

**描述**：创建 Canvas 水印渲染组件

**任务项**：
- [ ] 创建 WatermarkRenderer 组件
- [ ] 实现 Canvas 绑定 PixelMap
- [ ] 实现 边框绘制逻辑
- [ ] 实现 文字绘制逻辑
- [ ] 实现 XMAGE 渐变文字
- [ ] 实现 色卡绘制（模板B）
- [ ] 实现 异步渲染策略（< 200ms）
- [ ] 实现 缩略图策略

**验收标准**：
- 渲染结果正确
- 渲染时间 < 200ms
- 不阻塞 UI

**依赖**：T004, T006-T009

**覆盖功能**：F04

---

## 四、Phase 3：服务层+页面层

### T019 PermissionService [P0] [3h]

**描述**：创建权限管理服务

**任务项**：
- [ ] 创建 PermissionService
- [ ] 实现 checkReadPermission()
- [ ] 实现 requestReadPermission()
- [ ] 实现 checkWritePermission()
- [ ] 实现 requestWritePermission()
- [ ] 实现 jumpToSettings()（调用 @ohos.settings API）
- [ ] 处理 E001 权限拒绝错误

**验收标准**：
- 权限检查正确
- 设置跳转功能正常
- E001 错误处理正确

**依赖**：T003, T005

**覆盖功能**：F11

---

### T020 PhotoService [P0] [4h]

**描述**：创建照片选择服务

**任务项**：
- [ ] 创建 PhotoService
- [ ] 集成 @ohos.file.photoPicker API
- [ ] 实现 selectPhoto()（单选模式）
- [ ] 实现 loadPhoto(uri)（< 1s）
- [ ] 实现 validateFormat(path)（HEIC验证）
- [ ] 处理 E002 照片加载失败错误
- [ ] 返回 PhotoInfo（含 isValid 字段）

**验收标准**：
- PhotoPicker 正确调用
- 加载时间 < 1s
- HEIC 格式验证正确
- E002 错误处理正确

**依赖**：T004, T005, T019

**覆盖功能**：F01

---

### T021 EXIFService [P0] [4h]

**描述**：创建 EXIF 提取服务

**任务项**：
- [ ] 创建 EXIFService
- [ ] 集成 ImageSource.getImageProperty() API
- [ ] 实现 extractEXIF(uri)（< 100ms）
- [ ] 提取字段：FocalLength, FNumber, ExposureTime, ISOSpeedRatings, DateTimeOriginal, GPSLatitude/GPSLongitude, Model
- [ ] 实现 formatParam() 参数格式化
- [ ] 实现 formatTime() 时间格式化
- [ ] 实现缺失字段默认值兜底
- [ ] 处理 E003 EXIF 提取失败（不阻断流程）

**验收标准**：
- EXIF 字段正确提取
- 提取时间 < 100ms
- 缺失字段使用默认值

**依赖**：T004, T005

**覆盖功能**：F07

---

### T022 TemplateService [P0] [3h]

**描述**：创建模板加载服务

**任务项**：
- [ ] 创建 TemplateService
- [ ] 实现 loadTemplates()（加载4个内置模板）
- [ ] 实现 getTemplate(id)
- [ ] 实现 resolveTemplateVariables()（变量替换）
- [ ] 实现模板缓存

**验收标准**：
- 4个模板正确加载
- 变量替换正确

**依赖**：T004, T006-T009

**覆盖功能**：F02

---

### T023 WatermarkService [P0] [6h]

**描述**：创建水印合成服务

**任务项**：
- [ ] 创建 WatermarkService
- [ ] 实现 renderPreview()（< 200ms，缩略图策略）
- [ ] 实现 composeWatermark()（< 700ms，全分辨率）
- [ ] 实现 PixelMap 创建和合成
- [ ] 实现边框 + 水印元素 + 原图合成
- [ ] 处理 E004 内存溢出错误
- [ ] 处理 E006 合成失败错误

**验收标准**：
- 预览时间 < 200ms
- 合成时间 < 700ms
- 合成结果正确

**依赖**：T018, T022

**覆盖功能**：F04

---

### T024 ExportService [P0] [5h]

**描述**：创建导出保存服务

**任务项**：
- [ ] 创建 ExportService
- [ ] 集成 @ohos.file.mediaLibrary API
- [ ] 实现 saveToAlbum()（< 2s）
- [ ] 实现 Framood 相册自动创建
- [ ] 实现文件命名逻辑
- [ ] 实现 handleEXIF()（GPS移除逻辑）
  - [ ] GPSHandling.REMOVE 时删除 GPSLatitude/GPSLongitude
- [ ] 处理 E005 导出失败错误

**验收标准**：
- 导出时间 < 2s
- GPS 移除正确
- E005 错误处理正确

**依赖**：T004, T005, T019, T023

**覆盖功能**：F06, F07

---

### T025 DeviceMapper [P0] [2h]

**描述**：创建设备映射工具

**任务项**：
- [ ] 创建 DeviceMapper
- [ ] 实现 getBrandFromModel(model)
- [ ] 实现 getDefaultTemplateId(brand)
- [ ] 加载 deviceMapping.json
- [ ] 实现华为品牌 → T-A 映射
- [ ] 实现其他品牌兜底

**验收标准**：
- 品牌提取正确
- 默认模板选择符合 F09 规格

**依赖**：T010

**覆盖功能**：F08, F09

---

### T026 FormatHelper [P0] [2h]

**描述**：创建格式化工具

**任务项**：
- [ ] 创建 FormatHelper
- [ ] 实现 truncateText(text, maxLen)（按字符计数，Emoji计1字符）
- [ ] 实现 formatParamString(exif)
- [ ] 实现 formatTimeString(dateTime)（YYYY/MM/DD HH:MM）

**验收标准**：
- truncateText 正确处理 Emoji
- 截断符合 §5.1 规格

**依赖**：无

**覆盖功能**：F03, 边界处理

---

### T027 CanvasHelper [P0] [3h]

**描述**：创建 Canvas 绘制工具

**任务项**：
- [ ] 创建 CanvasHelper
- [ ] 实现 drawGradientText()
- [ ] 实现 drawBorder()
- [ ] 实现 calculateElementPosition()

**验收标准**：
- 渐变文字绘制正确
- 位置计算正确

**依赖**：T004

**覆盖功能**：F04

---

### T028 Index页面 [P0] [3h]

**描述**：创建首页（空状态）

**任务项**：
- [ ] 创建 Index 页面
- [ ] 实现 空状态 UI（图标 + 文案）
- [ ] 实现 选择照片 按钮
- [ ] 集成 PhotoService
- [ ] 集成 PermissionService
- [ ] 处理权限引导状态

**验收标准**：
- 空状态显示正确
- 选择照片流程正确
- 权限处理正确

**依赖**：T011, T016, T019, T020

**覆盖功能**：F01, F11

---

### T029 Editor页面 [P0] [6h]

**描述**：创建编辑页面

**任务项**：
- [ ] 创建 Editor 页面
- [ ] 实现 照片预览区域
- [ ] 实现 模板选择区域（TemplateCard 网格）
- [ ] 集成 WatermarkRenderer
- [ ] 集成 HalfSheet
- [ ] 集成 TemplateService
- [ ] 集成 DeviceMapper（F09 默认模板）
- [ ] 实现 实时预览更新
- [ ] 实现 下一步 按钮（跳转 QualitySettings）

**验收标准**：
- 模板选择正确
- 参数配置正确
- 实时预览更新 < 200ms

**依赖**：T015, T017, T018, T022, T025, T028

**覆盖功能**：F02, F03, F04, F08, F09

---

### T030 QualitySettings页面 [P0] [4h]

**描述**：创建画质设置页面

**任务项**：
- [ ] 创建 QualitySettings 页面
- [ ] 实现 分辨率选择组件
  - [ ] ORIGINAL（原分辨率）
  - [ ] FHD_1080P
  - [ ] HD_720P
- [ ] 实现 QualitySlider（50%-100%）
- [ ] 实现 输出格式选择（JPEG/PNG）
- [ ] 实现 GPS 处理选项（保留/移除）
- [ ] 实现 保存导出 按钮
- [ ] 集成 ExportService
- [ ] 处理权限引导（WRITE权限）

**验收标准**：
- 画质设置正确生效
- GPS 移除选项正确
- 导出流程正确

**依赖**：T013, T014, T024

**覆盖功能**：F05, F06, F07

---

### T031 Success页面 [P0] [2h]

**描述**：创建导出成功页面

**任务项**：
- [ ] 创建 Success 页面
- [ ] 实现 成功图标 + 文案
- [ ] 实现 查看照片 按钮
- [ ] 实现 继续编辑 按钮（返回首页）

**验收标准**：
- 成功状态显示正确
- 按钮功能正确

**依赖**：T011

**覆盖功能**：F06

---

## 五、Phase 4：测试验收

### T032 集成测试 [P0] [8h]

**描述**：完成 MVP 功能集成测试

**任务项**：
- [ ] 编写主流程测试
  - [ ] 选择照片 → 识别机型 → 默认模板 → 编辑 → 导出保存
- [ ] 编写分支流程测试
  - [ ] 手动切换模板
  - [ ] 参数配置变更
  - [ ] 自定义文本输入（含截断测试）
  - [ ] 画质设置调整
- [ ] 编写异常流程测试
  - [ ] 权限拒绝场景
  - [ ] HEIC 格式不支持
  - [ ] EXIF 缺失场景
  - [ ] 大图处理场景
- [ ] 执行性能测试
  - [ ] 启动 < 2s
  - [ ] 加载 < 1s
  - [ ] EXIF < 100ms
  - [ ] 预览 < 200ms
  - [ ] 合成 < 700ms
  - [ ] 导出 < 2s

**验收标准**：
- 所有测试用例通过
- 性能指标达标

**依赖**：T031

**覆盖功能**：全部 (F01-F11)

---

### T033 验收测试 [P0] [4h]

**描述**：完成 MVP 功能验收测试

**任务项**：
- [ ] 按 spec.md 验收标准逐项验收
- [ ] 用户体验测试
- [ ] 兼容性测试（不同 HarmonyOS 设备）
- [ ] 边界场景测试
  - [ ] 自定义文本12字符上限
  - [ ] Emoji 字符计数
  - [ ] GPS 移除验证
  - [ ] 权限设置跳转验证

**验收标准**：
- 功能验收表全部通过
- 用户可完成完整流程
- 边界处理符合规格

**依赖**：T032

**覆盖功能**：全部 (F01-F11)

---

## 六、任务依赖关系图

```
Phase 1 (基础设施)
T001 项目结构
  ├─ T002 Design Token脚本
  ├─ T003 Enums定义
  │   └─ T004 Types定义
  ├─ T005 Logger
  ├─ T006 模板A ─┐
  ├─ T007 模板B ─┼─ T010 设备映射
  ├─ T008 模板C ─┤
  └─ T009 模板D ─┘

Phase 2 (组件层)
T002 ─┬─ T011 Button
      │   └─ T014 ModalDialog
      ├─ T012 ToggleSwitch
      │   └─ T017 HalfSheet
      ├─ T013 QualitySlider
      ├─ T015 TemplateCard ← T006-T009
      ├─ T016 PhotoPreview
      └─ T018 WatermarkRenderer ← T004, T006-T009

Phase 3 (服务层+页面层)
T003,T005 ─┬─ T019 PermissionService
           │
T004,T005 ─┼─ T020 PhotoService ← T019
           ├─ T021 EXIFService
           │
T006-T009 ─┼─ T022 TemplateService
           │
T018,T022 ─┼─ T023 WatermarkService
           │
T019,T023 ─┼─ T024 ExportService
           │
T010 ──────┼─ T025 DeviceMapper
           │
           ├─ T026 FormatHelper
           ├─ T027 CanvasHelper ← T004
           │
T011,T016 ─┼─ T028 Index ← T019,T020
T019,T020 ─┤
           │
T015,T017 ─┼─ T029 Editor ← T018,T022,T025
T018,T022 ─┤
T025 ──────┤
           │
T013,T014 ─┼─ T030 QualitySettings ← T024
T024 ──────┤
           │
T011 ──────┼─ T031 Success

Phase 4 (测试验收)
T031 ──── T032 集成测试
         └─ T033 验收测试
```

---

## 七、工时估算汇总

| Phase | 任务数 | 工时 |
|-------|--------|------|
| Phase 1 | 10 | 12h |
| Phase 2 | 8 | 22h |
| Phase 3 | 12 | 40h |
| Phase 4 | 2 | 12h |
| **总计** | **32** | **86h** |

---

## 八、边界处理检查表

### 8.1 自定义文本边界

| 检查项 | 规格位置 | 实现模块 | 状态 |
|--------|---------|---------|------|
| 12字符上限 | §5.1 | T017 HalfSheet + T026 FormatHelper | [ ] |
| Emoji计1字符 | §5.1 | T026 FormatHelper.truncateText() | [ ] |
| ≥10字符显示剩余 | §5.1 | T017 HalfSheet UI反馈 | [ ] |
| ≥12字符显示上限 | §5.1 | T017 HalfSheet UI反馈 | [ ] |

### 8.2 图片格式边界

| 检查项 | 规格位置 | 实现模块 | 状态 |
|--------|---------|---------|------|
| HEIC格式验证 | §5.2 | T020 PhotoService.validateFormat() | [ ] |
| HEIC失败提示"格式不支持" | §5.2 | T020 PhotoService E002处理 | [ ] |

### 8.3 GPS处理边界

| 检查项 | 规格位置 | 实现模块 | 状态 |
|--------|---------|---------|------|
| GPS移除逻辑 | §5.4 | T024 ExportService.handleEXIF() | [ ] |
| 删除GPSLatitude/GPSLongitude | §5.4 | T024 ExportService | [ ] |

### 8.4 权限处理边界

| 检查项 | 规格位置 | 实现模块 | 状态 |
|--------|---------|---------|------|
| 设置跳转API | §5.5 | T019 PermissionService.jumpToSettings() | [ ] |
| E001权限拒绝处理 | §5.6 | T019 PermissionService + T014 ModalDialog | [ ] |

---

## 九、性能验收指标

| 指标 | 要求 | 测试方法 | 实现模块 |
|------|------|---------|---------|
| 应用启动 | < 2s | DevEco Studio性能分析 | T001 EntryAbility |
| 照片加载 | < 1s | 计时测试 | T020 PhotoService |
| EXIF提取 | < 100ms | 计时测试 | T021 EXIFService |
| 预览刷新 | < 200ms | 参数变更计时 | T018 WatermarkRenderer |
| 水印合成 | < 700ms | 合成计时 | T023 WatermarkService |
| 导出保存 | < 2s | 导出计时 | T024 ExportService |

---

## 十、错误码验收检查表

| 错误码 | 名称 | 实现模块 | UI处理 | 状态 |
|--------|------|---------|---------|------|
| E001 | 权限拒绝 | T019 PermissionService | 弹窗 + "前往设置" | [ ] |
| E002 | 照片加载失败 | T020 PhotoService | Error页 + "重新选择" | [ ] |
| E003 | EXIF提取失败 | T021 EXIFService | 默认值兜底 | [ ] |
| E004 | 内存溢出 | T023 WatermarkService | 提示降低分辨率 | [ ] |
| E005 | 导出失败 | T024 ExportService | Error页 + "重试" | [ ] |
| E006 | 合成失败 | T023 WatermarkService | Error页 + "返回编辑" | [ ] |

---

**文档状态**：待开始
**下一步**：执行 T001 项目结构创建