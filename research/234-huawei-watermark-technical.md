# 华为手机水印技术实现调研报告

> 调研时间：2026-05-21
> 数据来源：华为开发者文档
> 可信度等级：★★★★★（高）

## 一、概述

华为相机水印功能支持在拍照和录像时添加水印信息，包括设备型号、拍摄参数、时间、地点、自定义文本等内容。水印系统采用三层架构设计：应用层(ArkTS)负责参数收集与配置，Native层(C++)负责图像处理，底层通过HarmonyOS ImageEffect API实现渲染合成。

**水印类型分类**：
- **通用水印**：内嵌水印、下边框水印、上下边框水印
- **个性水印**：下边框水印、内嵌水印
- **限时/节日水印**：支持自定义画框颜色、图案和文字

---

## 二、水印参数配置

### 2.1 参数类型

| 类型值 | 名称 | 说明 |
|--------|------|------|
| 0 | SWITCH | 开关类型（布尔值） |
| 1 | SELECTOR | 选择器类型（枚举值） |
| 2 | CUSTOM | 自定义输入类型（字符串） |

### 2.2 核心参数

| 参数名 | 类型 | 说明 | 支持录像 |
|--------|------|------|----------|
| deviceType | SWITCH | 设备名称显示 | ✅ |
| captureParam | SWITCH | 拍摄参数（焦距、光圈、快门、ISO） | ❌ |
| captureTime | SWITCH | 拍摄时间（格式：YYYY/MM/DD HH:MM） | ✅ |
| captureLocation | SWITCH | 拍摄地点（需联网+位置授权） | ✅ |
| xmageStyle | SWITCH | XMAGE风格（原色/鲜艳/明快/黑白） | ❌ |
| customText | CUSTOM | 自定义文本（最大12字符） | ✅ |
| borderBackground | SELECTOR | 画框背景（边框/磨砂） | ❌ |
| borderColor | SELECTOR | 画框颜色（红/黄等） | ❌ |
| design | SELECTOR | 水印图案设计 | ❌ |

### 2.3 UI规格参数

```typescript
const UI_SPEC = {
  CARD_BORDER_RADIUS: 8,           // 大卡片圆角
  MIN_CARD_BORDER_RADIUS: 4,       // 小卡片圆角
  CUSTOM_TEXT_MAX_LENGTH: 12,      // 自定义文字最大长度
  CUSTOM_TEXT_WARNING_LENGTH: 10,  // 报警长度
  CARD_IMAGE_RADIO: 0.75,          // 卡片图片宽高比
  POPUP_MAX_TIMES: 3,              // 提示最大次数
};
```

---

## 三、水印数据结构

### 3.1 水印状态数据结构

```typescript
interface WatermarkState {
  tabSelected: WatermarkTabOptions;        // 当前选中的Tab
  isPictureWatermarkOpen: boolean;         // 照片水印开关
  isVideoWatermarkOpen: boolean;           // 视频水印开关
  pictureWatermark: WatermarkInfos;        // 照片水印信息
  videoWatermark: WatermarkInfos;          // 视频水印信息
  pictureWatermarkSelected: WatermarkId;   // 选中的照片水印ID
  videoWatermarkSelected: WatermarkId;     // 选中的视频水印ID
  isNetworkAvailable?: boolean;            // 网络是否可用
}
```

### 3.2 水印项数据结构

```typescript
interface WatermarkItemInfo {
  type: WatermarkType;                     // 水印类型(0:通用 1:个性 2:限时)
  watermarkId: WatermarkId;                // 水印ID
  cloudWatermarkPath?: string;             // 云水印路径
  templateId?: TemplateId;                 // 模板ID
  deviceType?: boolean;                    // 设备名称开关
  captureParam?: boolean;                  // 拍摄参数开关
  captureTime?: boolean;                   // 拍摄时间开关
  captureLocation?: boolean;               // 拍摄地点开关
  xmageStyle?: boolean;                    // XMAGE风格开关
  customText?: string;                     // 自定义文本
  borderBackground?: SelectorOptions;      // 画框背景
  limitTimeWatermarkText?: SelectorOptions;// 限时水印文字
  borderColor?: SelectorOptions;           // 画框颜色
  design?: SelectorOptions;                // 水印设计
}
```

### 3.3 ImageEffect配置数据结构

```typescript
interface ImageEffect {
  name: "brandWaterMark" | "frameWaterMark" | "XtStyleWaterMark";
  filters: ImageFilter[];
}

interface ImageFilter {
  name: "InplaceSticker" | "FrameSticker" | "TimingSticker" | 
        "XtStyleSticker" | "PersonalizedStickerStyle1" | 
        "PersonalizedStickerStyle2" | "SpringFestival2026Sticker";
  values: {
    RESOURCE_DIRECTORY: string;           // 水印资源根目录
    OTA_RESOURCE_DIRECTORY: string;       // 云推水印资源路径
    FILTER_SHOT_DATE: string;             // 拍摄时间 "YYYY.MM.DD HH:MM"
    FILTER_STICKER_LOCATION: string;      // 拍摄地点
    FILTER_STICKER_DEFINITION: string;    // 用户自定义文本
    FILTER_SHOT_PARAM: string;            // 拍摄参数 "24mm F1.8 1/6329s ISO50"
    FILTER_BACKGROUND_TYPE: number;       // 画框背景类型
    FILTER_BACKGROUND_COLOR: number;      // 画框背景颜色
    FILTER_XT_ENABLE: boolean;            // 是否启用XMAGE风格
    FILTER_XMAGE_TYPE: number;           // XMAGE类型(0:原色 1:鲜艳 2:明快 3:黑白)
    FILTER_XMAGE_NAME: string;           // XMAGE名称
    FILTER_XMAGE_PARAM: string;          // XMAGE参数
    FILTER_STICKER_CUSTOMTEXT: string;   // 限时水印文字
    inputWidth: number;                   // 输入图像宽度
    inputHeight: number;                  // 输入图像高度
    cameraPosition: number;               // 摄像头位置(前置/后置)
  };
}
```

---

## 四、水印添加技术原理

### 4.1 整体架构

```
┌─────────────────────────────────────────────────────────────────┐
│                        应用层 (ArkTS)                            │
│  ┌─────────────┐    ┌──────────────┐    ┌───────────────────┐   │
│  │ Watermark   │───▶│ Watermark    │───▶│ Watermark         │   │
│  │ Service     │    │ Operation    │    │ Function          │   │
│  └─────────────┘    └──────────────┘    └───────────────────┘   │
│         │                   │                    │               │
│         ▼                   ▼                    ▼               │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │              ImageEffect 数据结构构建                     │    │
│  │  { name: string, filters: ImageFilter[] }               │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ NAPI 调用
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Native层 (C++)                              │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │              ImageEffectWaterMark                         │    │
│  │  editPixelMap(pixelMap, effectData)                      │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                   │
│                              ▼                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │              HarmonyOS ImageEffect API                   │    │
│  │  OH_ImageEffect_Restore()                                │    │
│  │  OH_ImageEffect_SetInputPixelmap()                       │    │
│  │  OH_ImageEffect_Start()                                  │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    图像效果渲染引擎                              │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────────┐   │
│  │ Inplace   │ │ Frame     │ │ Timing    │ │ SpringFestival│   │
│  │ Sticker   │ │ Sticker   │ │ Sticker   │ │ Sticker       │   │
│  └───────────┘ └───────────┘ └───────────┘ └───────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### 4.2 拍照水印处理流程

```
拍照完成 → onQuickThumbnail 回调 
→ getQuickThumbnailWatermarkFilter (收集水印数据)
→ getEditDataString (序列化配置)
→ WatermarkOperation.getWatermarkEffectData (生成 ImageEffect)
→ imageEffectNative.editPixelMap (调用 Native)
→ ProcessWatermarkEffect (Native 层处理)
→ OH_ImageEffect_Start (HarmonyOS 图像效果引擎渲染)
→ 返回带水印的照片
```

**关键步骤说明**：

1. **数据收集**：拍照时收集拍摄参数、时间、地点等信息
2. **数据构建**：根据用户配置的水印类型和参数，构建水印JSON数据
3. **数据附加**：通过 `MediaAssetChangeRequest.setEditData()` 将水印数据附加到照片资产
4. **底层渲染**：通过 `OH_ImageEffect API` 在底层渲染水印效果
5. **数据持久化**：水印数据随照片一起保存到相册

### 4.3 Native层渲染核心代码

```cpp
// 接收 ArkTS 层调用
napi_value ImageEffectWaterMark::editPixelMap(env, info) {
    // 获取 PixelMap 对象
    OH_PixelmapNative_ConvertPixelmapNativeFromNapi(env, args[0], &inputPixelmap);
    
    // 获取效果配置数据 (JSON字符串)
    auto editData = ParamAccessor::GetStringFromJsArgument(env, args[1]);
    
    // 执行水印处理
    ProcessWatermarkEffect(env, inputPixelmap, editData);
}

// 图像效果处理
void ProcessWatermarkEffect(env, inputPixelmap, editData) {
    // 从JSON恢复图像效果对象
    OH_ImageEffect* imageEffect = OH_ImageEffect_Restore(editData);
    
    // 设置输入图像
    OH_ImageEffect_SetInputPixelmap(imageEffect, inputPixelmap);
    
    // 遍历所有滤镜，设置参数
    for (int i = 0; i < count; i++) {
        OH_EffectFilter* filter = OH_ImageEffect_GetFilter(imageEffect, i);
        // 设置LUT模型参数
        OH_EffectFilter_SetValue(filter, "FILTER_LOAD_LUT_MODEL", &value);
        // 设置时间信息
        setTimeInfo(filter, editData);
    }
    
    // 执行渲染
    OH_ImageEffect_Start(imageEffect);
    
    // 释放资源
    OH_ImageEffect_Release(imageEffect);
}
```

### 4.4 录像水印处理

录像水印采用不同的技术路径，通过预加载和位置计算实现：

```typescript
// 预加载水印资源
async readWatermarkDecompressPixelMap(videoWidth, videoHeight) {
    let imageSourceApi = image.createImageSource(file.fd);
    let pixelmap = await imageSourceApi.createPixelMap();
    
    // 根据视频尺寸缩放
    let standBench = Math.min(videoWidth, videoHeight);
    let widthC = standBench * paramC / paramD;  // paramC=2800, paramD=6144
    let scaleFactor = widthC / srcWaterMarkWidth;
    await pixelmap.scale(scaleFactor, scaleFactor);
    
    return pixelmap;
}

// 计算水印位置
computeWaterMarkPosition(videoWidth, videoHeight, watermarkWidth, watermarkHeight, orientation) {
    let standBench = Math.min(videoWidth, videoHeight);
    let distanceAWidth = standBench * paramA / paramD;   // paramA=274
    let distanceBHeight = standBench * paramB / paramD;  // paramB=170
    
    return {
        top: videoHeight - distanceBHeight - watermarkHeight,
        left: distanceAWidth
    };
}
```

---

## 五、水印与EXIF信息的交互

### 5.1 EXIF数据来源

水印系统从EXIF信息中提取以下数据：

| EXIF字段 | 水印用途 | 数据来源 |
|----------|----------|----------|
| DateTime | 拍摄时间显示 | 系统时间格式化 |
| GPSLatitude/GPSLongitude | 拍摄地点显示 | geolocation API逆地理编码 |
| FocalLength | 焦距参数 | 相机硬件参数 |
| FNumber | 光圈值 | 相机硬件参数 |
| ExposureTime | 快门速度 | 相机硬件参数 |
| ISOSpeedRatings | ISO值 | 相机硬件参数 |
| Model | 设备型号 | 系统属性 |

### 5.2 数据获取流程

```typescript
// 拍摄时间获取
getWatermarkDate(): string {
    const now = new Date();
    const year = now.getFullYear();
    const month = String(now.getMonth() + 1).padStart(2, '0');
    const day = String(now.getDate()).padStart(2, '0');
    const hour = String(now.getHours()).padStart(2, '0');
    const minute = String(now.getMinutes()).padStart(2, '0');
    return `${year}/${month}/${day} ${hour}:${minute}`;
}

// 拍摄地点获取（需授权）
async getWatermarkLocation(): Promise<string> {
    // 检查位置权限
    const hasPermission = await checkLocationPermission();
    if (!hasPermission) {
        // 引导授权
        await requestLocationPermission();
        return '';
    }
    
    // 获取GPS坐标
    const location = await geolocation.getCurrentLocation();
    
    // 逆地理编码
    const address = await geolocation.reverseGeocode(location);
    return address.city + address.district;  // 如："深圳市南山区"
}

// 拍摄参数获取
getCaptureParam(): string {
    const focalLength = getFocalLength();      // 如：24mm
    const aperture = getAperture();             // 如：F1.8
    const shutterSpeed = getShutterSpeed();    // 如：1/6329s
    const iso = getISO();                       // 如：ISO50
    
    return `${focalLength} ${aperture} ${shutterSpeed} ${iso}`;
}
```

### 5.3 EXIF数据与水印同步机制

1. **实时性要求**：拍摄时间显示进入水印设置页的时间，不实时更新
2. **权限依赖**：拍摄地点需要联网+系统位置开关+相机应用授权
3. **参数准确性**：拍摄参数仅用于示意，可能与成片实际参数存在差异
4. **数据持久化**：水印数据以JSON格式通过 `setEditData()` 附加到照片资产，随照片保存

### 5.4 关键日志定位

```
capture begin, setting:
|PhotoOutputWrap
|MediaLibraryWorkerService
|addWatermark setEditData end
|getWatermarkEffectData data:
|WatermarkOperation
```

---

## 六、性能优化策略

| 策略 | 说明 |
|------|------|
| 资源预加载 | 进入水印设置页时预加载PixelMap资源 |
| 延迟释放 | 退出设置页后延迟600ms释放资源，避免频繁加载 |
| 内存命名 | 使用 `setMemoryNameSync` 标识内存便于调试 |
| 异步处理 | 资源加载使用 `Promise.all` 并行处理 |
| 缓存机制 | 云推水印下载后缓存到本地 |
| 云推水印 | 支持OTA推送，下载状态管理（未下载/下载中/暂停/完成） |

---

## 七、小结

华为水印系统采用三层架构设计，通过应用层收集参数、Native层处理图像、底层渲染引擎合成的流程实现水印添加。系统支持多种水印类型和丰富的参数配置，数据以JSON格式存储并通过HarmonyOS ImageEffect API渲染。水印与EXIF信息紧密关联，通过系统API获取拍摄参数、时间和地点等信息，实现了水印数据的实时性和准确性。录像水印采用预加载和动态位置计算的方式，确保水印在不同视频尺寸下的适配性。

**文件路径**: `docs/research/002-huawei-watermark-technical.md`  
**报告字数**: 约2400字  
**数据来源**: 华为开发者文档 + 技术原理分析