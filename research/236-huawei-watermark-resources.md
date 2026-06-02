# 华为水印云推资源调研报告

> 调研日期：2026年5月21日
> 数据来源：华为开发者文档
> 可信度等级：★★★★★（高）

---

## 一、概述

华为水印云推资源采用分层架构，通过云端动态下发水印素材和配置，实现水印模板的灵活更新。云推资源主要包含三类配置文件：
- **watermark_index.json**：索引文件，用于筛选需要下载的Cover包
- **Cover包config.json**：封面预览配置，包含缩略图和基础信息
- **Full包config.json**：完整水印配置，包含全部素材和渲染参数

---

## 二、watermark_index.json解析

### 2.1 功能定位

watermark_index.json是云推资源的入口索引文件。每次请求Cover包时必定下载此文件，用于筛选需要下载的Cover包。

### 2.2 数据结构

```json
[
    {
        "fileId": "featureId10_typeId12_templateId31_resourceId101",
        "UIEffectiveTime": "2026-01-04 00:00:00",
        "UIExpirationTime": "2026-03-08 00:00:00",
        "devModel": ["ALL"],
        "romVersion": "6.1",
        "compatibleVersion": 1,
        "fullRomSize": 2224,
        "fullFileId": "featureId10_typeId12_templateId31_resourceId102",
        "resourceVersion": 2
    }
]
```

### 2.3 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| fileId | string | Cover包ID，尾号1代表Cover包。格式：featureId_typeId_templateId_resourceId，各数值需与存储路径、config.json保持一致 |
| UIEffectiveTime | string | 生效时间，格式：YYYY-MM-DD HH:MM:SS |
| UIExpirationTime | string | 失效时间，格式：YYYY-MM-DD HH:MM:SS |
| devModel | array | 支持机型列表。"ALL"表示所有机型生效；"ALN-AL10"表示特定机型生效；若设备型号为unknown，则支持所有为ALL的资源 |
| romVersion | string | ROM大版本号，最小为"6.0"。配置"6.1"表示对所有>=6.1的设备生效 |
| compatibleVersion | number | 兼容版本号，水印特性发生不兼容演进时版本号需+1 |
| fullRomSize | number | Full包大小，单位KB |
| fullFileId | string | Full包ID，尾号2代表Full包 |
| resourceVersion | number | 资源版本号，覆盖更新时需+1 |

### 2.4 机型适配规则

```
设备型号读取逻辑：
1. 获取设备devModel
2. 若读不到 → 所有资源不生效
3. 若等于unknown → 支持所有为ALL的资源
4. 若读到ALN-AL10 → 支持所有为ALL和ALN-AL10的资源
```

---

## 三、Cover包config.json解析

### 3.1 功能定位

Cover包包含水印的封面预览资源，用于相机和图库快速展示水印效果。

### 3.2 数据结构

```json
{
    "resourceId": 101,
    "fullResourceId": 102,
    "resourcePriority": 150,
    "templateId": 31,
    "typeId": 12,
    "typeName": "IDS_text_id",
    "typePriority": 150,
    "featureId": 10,
    "UIEffectiveTime": "2026-01-04 00:00:00",
    "UIExpirationTime": "2026-03-08 00:00:00",
    "fullRomSize": 2224,
    "resourceDownloadStatus": 102,
    "process": 10,
    "downloadedTime": "2026-01-04 00:00:00",
    "coverUriForCamera": [
        "/10/12/31/101/Red.jpg",
        "/10/12/31/101/Yellow.jpg"
    ],
    "coverUriForPhotoGallery": "/10/12/31/101/RedGallery.png"
}
```

### 3.3 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| resourceId | number | Cover包的resourceId，缩略图实例编号（非枚举，自定义数值） |
| fullResourceId | number | Full包的resourceId |
| resourcePriority | number | 缩略图排列顺序权重 |
| templateId | number | UI模板ID，与编创框架样式一一对应（枚举定义，可映射） |
| typeId | number | 水印大类ID：基础水印10、个性水印11、限时水印12（枚举定义） |
| typeName | string | 水印大类名称（预留字段，typeId>12时需配置） |
| typePriority | number | 水印大类排列顺序权重（预留字段） |
| featureId | number | 特性ID，水印特性为10（枚举定义） |
| UIEffectiveTime | string | 生效时间 |
| UIExpirationTime | string | 失效时间 |
| fullRomSize | number | Full包大小，单位KB |
| resourceDownloadStatus | number | 包下载状态（相机框架维护，无需配置） |
| process | number | 下载进度百分比（相机框架维护，无需配置） |
| downloadedTime | string | 下载时间，用于老化处理（相机框架维护） |
| coverUriForCamera | array | 相机预览缩略图路径数组 |
| coverUriForPhotoGallery | string | 图库预览缩略图路径 |

### 3.4 路径规则

路径格式：`/featureId/typeId/templateId/resourceId/文件名`

示例：`/10/12/31/101/Red.jpg` 表示：
- featureId=10（水印特性）
- typeId=12（限时水印）
- templateId=31（UI模板）
- resourceId=101（Cover包实例）

---

## 四、Full包config.json解析

### 4.1 功能定位

Full包包含完整水印资源，包括品牌Logo、边框图案、文字配置、渲染参数等全部素材。

### 4.2 数据结构

```json
{
    "resourceId": 102,
    "fullResourceId": 102,
    "resourcePriority": 150,
    "templateId": 31,
    "typeId": 12,
    "typeName": "IDS_text_id",
    "typePriority": 150,
    "featureId": 10,
    "UIEffectiveTime": "2026-01-04 00:00:00",
    "UIExpirationTime": "2026-03-08 00:00:00",
    "fullRomSize": 2224,
    "resourceDownloadStatus": 102,
    "process": 10,
    "downloadedTime": "2026-01-04 00:00:00",
    "coverUriForCamera": [
        "/10/12/31/102/Red.jpg",
        "/10/12/31/102/Yellow.jpg"
    ],
    "coverUriForPhotoGallery": "/10/12/31/102/RedGallery.png",
    "paramList": [...]
}
```

### 4.3 paramList参数配置

paramList定义水印的可配置参数，每个参数包含：

| 字段 | 类型 | 说明 |
|------|------|------|
| paramId | string | 参数ID，如borderColor、design、limitTimeWatermarkText |
| defaultValue | number | 默认选项ID |
| titleText | string | 参数标题文本ID |
| type | number | 参数类型：0=开关型、1=选择器型 |
| affectedField | string | 关联字段，如design关联borderColor |
| selectors | array | 选项列表 |

### 4.4 selectors选项配置

每个selector包含：

| 字段 | 类型 | 说明 |
|------|------|------|
| id | number | 选项ID |
| type | number | 内容类型：0=图片、1=文字 |
| shapeType | number | 形状类型：0=矩形、1=文字、2=特殊形状 |
| source | array | 资源路径或文字内容数组 |
| previewSource | array | 预览图路径数组（可选） |
| textColor | array | 文字颜色数组（可选） |

### 4.5 参数配置示例

**边框颜色参数（borderColor）**：
```json
{
    "paramId": "borderColor",
    "defaultValue": 0,
    "titleText": "border_background",
    "type": 1,
    "selectors": [
        {
            "id": 0,
            "type": 0,
            "shapeType": 0,
            "source": ["/10/12/31/102/borderRed.png"],
            "textColor": ["#ffe0ae"]
        },
        {
            "id": 1,
            "type": 0,
            "shapeType": 0,
            "source": ["/10/12/31/102/borderYellow.png"],
            "textColor": ["#523500"]
        }
    ]
}
```

**设计图案参数（design）**：
```json
{
    "paramId": "design",
    "defaultValue": 1,
    "titleText": "watermark_design",
    "type": 1,
    "affectedField": "borderColor",
    "selectors": [
        {
            "id": 0,
            "type": 0,
            "shapeType": 0,
            "source": ["/10/12/31/102/no_sign.png"]
        },
        {
            "id": 1,
            "type": 0,
            "shapeType": 0,
            "source": [
                "/10/12/31/102/Settings2026Red.png",
                "/10/12/31/102/Settings2026Yellow.png"
            ],
            "previewSource": [
                "/10/12/31/102/iconRed.png",
                "/10/12/31/102/iconYellow.png"
            ]
        }
    ]
}
```

**限时水印文字参数（limitTimeWatermarkText）**：
```json
{
    "paramId": "limitTimeWatermarkText",
    "defaultValue": 1,
    "titleText": "watermark_text",
    "type": 1,
    "selectors": [
        {
            "id": 0,
            "type": 0,
            "shapeType": 1,
            "source": ["/10/12/31/102/no_sign.png"]
        },
        {
            "id": 1,
            "type": 1,
            "shapeType": 1,
            "source": ["马年大吉  万事如意"]
        },
        {
            "id": 2,
            "type": 1,
            "shapeType": 1,
            "source": ["金马献瑞  万事亨通"]
        },
        {
            "id": 3,
            "type": 1,
            "shapeType": 2,
            "source": ["马跃新程  福满人间"]
        }
    ]
}
```

---

## 五、更新机制

### 5.1 分层下载策略

```
┌─────────────────────────────────────────────────────┐
│ 请求流程                                            │
├─────────────────────────────────────────────────────┤
│ 1. 客户端请求水印列表                               │
│    ↓                                               │
│ 2. 下载 watermark_index.json                       │
│    ↓                                               │
│ 3. 根据devModel、romVersion筛选需要的Cover包        │
│    ↓                                               │
│ 4. 下载对应Cover包（轻量预览）                      │
│    ↓                                               │
│ 5. 用户选择后下载Full包（完整资源）                 │
└─────────────────────────────────────────────────────┘
```

### 5.2 版本管理机制

| 机制 | 说明 |
|------|------|
| resourceVersion | 覆盖更新时版本号+1 |
| compatibleVersion | 不兼容演进时版本号+1 |
| UIEffectiveTime | 定时生效，精确到秒 |
| UIExpirationTime | 定时失效，自动清理 |

### 5.3 下载状态管理

| 字段 | 说明 | 维护方 |
|------|------|--------|
| resourceDownloadStatus | 下载状态码 | 相机框架 |
| process | 下载进度百分比 | 相机框架 |
| downloadedTime | 下载完成时间 | 相机框架 |

### 5.4 老化处理机制

- downloadedTime记录下载时间
- 总ROM达5G触发老化删除
- 失效时间过期自动清理

---

## 六、ID枚举定义

### 6.1 featureId（特性ID）

| ID | 特性名称 |
|----|---------|
| 10 | 水印特性 |

### 6.2 typeId（水印大类ID）

| ID | 大类名称 |
|----|---------|
| 10 | 基础水印 |
| 11 | 个性水印 |
| 12 | 限时水印 |

### 6.3 resourceId规则

| 尾号 | 包类型 |
|------|--------|
| 1 | Cover包 |
| 2 | Full包 |

---

## 七、小结

华为水印云推资源采用分层架构设计，核心特点：

1. **三层索引结构**：watermark_index.json → Cover包 → Full包，渐进式加载
2. **机型精准适配**：devModel数组支持ALL和特定机型，unknown设备自动降级
3. **版本号管理**：resourceVersion覆盖更新、compatibleVersion兼容演进
4. **参数化配置**：paramList定义可配置参数，支持图片/文字混合、颜色关联
5. **路径规范化**：/featureId/typeId/templateId/resourceId/文件名，层级清晰
6. **状态框架维护**：下载状态、进度、时间由相机框架自动管理

**技术亮点**：
- 配置驱动：所有水印元素通过JSON配置定义
- 动态下发：无需版本更新即可上线新水印
- 精准控制：机型、ROM版本、生效时间精确筛选
- 自动管理：下载状态、老化处理框架自动维护
