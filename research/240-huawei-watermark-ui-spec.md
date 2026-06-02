# 鸿蒙相机水印UI规格文档

---

## 1. 水印类型概览

### 1.1 照片水印类型

| 类型 | ID | 名称 | 支持录像 |
|------|-----|------|----------|
| 通用水印 | 11 | COMMON_INNER (内圈) | ✅ |
| 通用水印 | 12 | COMMON_BORDER_BOTTOM (下边框) | ❌ |
| 通用水印 | 13 | COMMON_BORDER_TOP_BOTTOM (上下边框) | ❌ |
| 个性水印 | 21 | INDIVIDUALITY_BORDER_BOTTOM (下边框) | ❌ |
| 个性水印 | 22 | INDIVIDUALITY_INNER (内圈) | ❌ |
| 限时水印 | 31 | FESTIVAL_BORDER_BOTTOM (下边框) | ❌ |
| 限时水印 | 32 | FESTIVAL_BORDER_TOP_BOTTOM (上下边框) | ❌ |
| 限时水印 | 33 | FESTIVAL_BORDER_BOTTOM_ROUND (四周边框) | ❌ |

### 1.2 录像水印

- 仅支持内嵌通用水印
- 默认参数不可调
- 支持设备名称、拍摄时间、拍摄地点、自定义文本

---

## 2. UI尺寸规格

### 2.1 卡片与预览

| 参数名 | 值 | 说明 |
|--------|-----|------|
| CARD_BORDER_RADIUS | 8px | 大卡片圆角 |
| MIN_CARD_BORDER_RADIUS | 4px | 小卡片圆角 |
| CARD_IMAGE_RADIO | 0.75 | 卡片图片宽高比 (516/688) |
| MIN_IMAGE_DEFAULT_WIDTH | 129px | 总览页预览图默认宽度 |
| BIG_IMAGE_DEFAULT_WIDTH | 258px | 参数配置页预览图默认宽度 |

### 2.2 自定义文字输入

| 参数名 | 值 | 说明 |
|--------|-----|------|
| CUSTOM_TEXT_MAX_LENGTH | 12 | 自定义文字最大长度 |
| CUSTOM_TEXT_WARNING_LENGTH | 10 | 自定义文字报警长度（开始显示计数） |
| CUSTOM_BUTTON_INPUT_MARGIN | 16px | 自定义弹窗按钮边距 |
| CUSTOM_TEXT_INPUT_MARGIN | 24px | 自定义弹窗输入框边距 |
| CUSTOM_BUTTON_HEIGHT | 40px | 自定义弹窗按钮高度 |

### 2.3 其他UI参数

| 参数名 | 值 | 说明 |
|--------|-----|------|
| POPUP_MAX_TIMES | 3 | 水印参数配置页提示最大次数 |
| NET_ID_THRESHOLD | 100 | 联网判断阈值 |

---

## 3. 颜色规格

### 3.1 限时水印画框颜色

| 选项值 | 边框颜色 | 文字颜色 |
|--------|----------|----------|
| 0 | 红色 | #ffe0ae |
| 1 | 黄色 | #523500 |

### 3.2 XMAGE图标颜色

| 图标名称 | 说明 |
|----------|------|
| watermark_xmage_normal | 原色 |
| watermark_xmage_bright | 鲜艳 |
| watermark_xmage_soft | 明快 |
| watermark_xmage_black_white | 黑白 |

---

## 4. 字体规格

### 4.1 通用字体

> 默认字体名称和字号，待补充。

### 4.2 Nova特殊字体

| 参数 | 值 |
|------|-----|
| 字体名称 | 方正行楷 |
| 字体文件 | FZXingK-SC.ttf |
| 支持功能 | 个性化字体、镜头传播名 |

---

## 5. 录像水印规格

### 5.1 位置计算参数

| 参数名 | 默认值 | 说明 |
|--------|--------|------|
| PARAM_A | 274 | 水印水平距离参数 |
| PARAM_B | 170 | 水印垂直距离参数 |
| PARAM_C | 2800 | 水印宽度缩放参数 |
| PARAM_D | 6144 | 基准参数 |

### 5.2 位置计算公式

```
standBench = min(videoWidth, videoHeight)
widthC = standBench * PARAM_C / PARAM_D
scaleFactor = widthC / srcWaterMarkWidth

水平边距 = standBench * PARAM_A / PARAM_D
垂直边距 = standBench * PARAM_B / PARAM_D
```

### 5.3 前置摄像头水印

- 资源路径: `/watermark/front/dm.png`
- 配置文件: `/watermark/front/param.xml`

---

## 6. 水印参数配置

### 6.1 参数类型

| 类型值 | 名称 | 说明 |
|--------|------|------|
| 0 | SWITCH | 开关类型 |
| 1 | SELECTOR | 选择器类型 |
| 2 | CUSTOM | 自定义输入类型 |

### 6.2 各水印支持的参数

#### 内嵌通用水印 (COMMON_INNER)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| deviceType | SWITCH | true | 设备名称 |
| captureTime | SWITCH | false | 拍摄时间 |
| captureLocation | SWITCH | false | 拍摄地点 |
| customText | CUSTOM | '' | 自定义文本 |

#### 下边框通用水印 (COMMON_BORDER_BOTTOM)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| xmageStyle | SWITCH | false | XMAGE风格 |
| captureParam | SWITCH | true | 拍摄参数 |
| captureTime | SWITCH | true | 拍摄时间 |
| captureLocation | SWITCH | false | 拍摄地点 |
| customText | CUSTOM | '' | 自定义文本 |

#### 上下边框通用水印 (COMMON_BORDER_TOP_BOTTOM)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| xmageStyle | SWITCH | 条件默认 | XMAGE风格（支持自定义颜色时默认开启） |
| captureParam | SWITCH | true | 拍摄参数 |
| captureTime | SWITCH | false | 拍摄时间 |
| captureLocation | SWITCH | false | 拍摄地点 |

#### 下边框个性水印 (INDIVIDUALITY_BORDER_BOTTOM)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| borderBackground | SELECTOR | 0 | 画框背景（边框/磨砂） |
| captureParam | SWITCH | true | 拍摄参数 |
| captureTime | SWITCH | false | 拍摄时间 |
| captureLocation | SWITCH | false | 拍摄地点 |
| customText | CUSTOM | '' | 自定义文本 |

#### 内嵌个性水印 (INDIVIDUALITY_INNER)

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| captureParam | SWITCH | true | 拍摄参数 |
| captureTime | SWITCH | false | 拍摄时间 |
| captureLocation | SWITCH | false | 拍摄地点 |
| customText | CUSTOM | '' | 自定义文本 |

### 6.3 限时水印参数

#### 画框颜色 (borderColor)

| 选项值 | 边框颜色 | 文字颜色 |
|--------|----------|----------|
| 0 | 红色 | #ffe0ae |
| 1 | 黄色 | #523500 |

#### 水印设计 (design)

| 选项值 | 说明 |
|--------|------|
| 0 | 无签名 |
| 1 | 兔子图标（红/黄） |

#### 水印文字 (limitTimeWatermarkText)

| 选项值 | 文字内容 |
|--------|----------|
| 0 | 无文字 |
| 1 | 马年大吉  万事如意 |
| 2 | 金马献瑞  万事亨通 |
| 3 | 马跃新程  福满人间 |

---

## 7. 拍摄参数显示格式

### 7.1 时间格式

```
拍摄时间: YYYY/MM/DD HH:MM
示例: 2026/03/23 10:30
```

### 7.2 拍摄参数格式

```
格式: 焦距 光圈 快门 ISO
示例: 24mm F1.8 1/6329s ISO50
```

> 注意：拍摄参数仅用于示意，与成片拍摄参数可能不一致。

---

## 8. 设置页布局

### 8.1 折叠屏布局

- 左右分栏布局
- 参数配置在右侧面板

### 8.2 参数配置页组件

```
┌─────────────────────────────────────┐
│  水印预览区 (258px宽)                │
├─────────────────────────────────────┤
│  □ 拍摄机型                          │
│  □ 拍摄参数                          │
│  □ 拍摄时间                          │
│  □ 拍摄地点                          │
│  □ 自定义文本 (最多12字符)           │
│  □ XMAGE 风格                        │
└─────────────────────────────────────┘
```

---

## 9. 不支持水印的场景

1. 非手机/TV设备
2. Picker模式
3. PRO模式 + RAW格式
4. 前置摄像头（不支持前置水印时）
5. 全景模式 (PANORAMA)
6. VLOG模式
7. 慢动作模式（不支持录像水印时）

---

## 10. 云推水印状态

### 10.1 下载状态

| 状态值 | 名称 | 说明 |
|--------|------|------|
| 0 | UNLOAD | 未下载 |
| 1 | LOADING | 下载中 |
| 2 | LOAD_PAUSE | 暂停下载 |
| 3 | LOADED | 下载完成 |

### 10.2 网络状态

| 状态值 | 名称 | 说明 |
|--------|------|------|
| 0 | NO_INTERNET | 无网络 |
| 1 | CELLULAR_NETWORK | 蜂窝网络 |
| 2 | WIFI | WiFi网络 |

---

## 11. 水印编辑数据结构

### 11.1 FRAGMENT_MAP（水印裁剪图）

| 参数 | 值 |
|------|-----|
| 存储位置 | JPEG私有box / HEIF FRAGMENT_MAP box |
| 数据格式 | NV12 |
| 是否编码 | 是 |
| 功能 | 保存被水印覆盖的原始区域，支持编辑可回退 |

### 11.2 FRAGMENT_METADATA（水印裁剪图元数据）

| 参数 | 说明 |
|------|------|
| 存储位置 | JPEG/HEIF元数据区 |
| 功能 | 描述水印裁剪图的位置、尺寸、关联关系 |
| 应用场景 | 图库水印编辑、水印接续编辑、水印移除/替换 |

---

## 12. 待补充信息

| 项目 | 状态 | 说明 |
|------|------|------|
| 默认字体名称 | ❌ 待补充 | 非Nova设备的默认字体 |
| 默认字体字号 | ❌ 待补充 | 各水印类型的字体大小 |
| 边框背景色 | ❌ 待补充 | 非限时水印的边框背景颜色 |
| 分隔线条颜色 | ❌ 待补充 | 信息项之间的分隔线颜色 |
| 分隔线条粗细 | ❌ 待补充 | 分隔线的像素宽度 |
| 边框高度 | ❌ 待补充 | 边框水印的高度比例或像素值 |

> **补充说明**：水印裁剪图相关数据结构详见 `238-huawei-picture-metadata.md`