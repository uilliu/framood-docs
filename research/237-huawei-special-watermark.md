# 华为特殊水印调研报告

> 调研时间：2026-05-21
> 数据来源：华为开发者文档
> 可信度等级：★★★★★（高）

## 一、节假日水印规格

### 1.1 限时水印机制

华为节假日水印采用**限时水印机制**,通过资源云推方式实现,无需应用版本更新即可支持新的节假日水印。该机制的核心设计原则是:

- **规格化设计**:所有节假日水印必须遵循统一规格,确保通过资源配置即可实现新水印上线
- **双类型支持**:提供画框水印(FestivalFrameStickerEFilter)和内嵌水印(FestivalInplaceStickerEFilter)两种类型
- **配置驱动**:通过水印资源配置文件控制水印样式,应用层通过下发key值实现渲染

### 1.2 水印素材管理

#### 画框水印配置项

| 配置项 | 规格说明 | 实现方案 |
|--------|---------|---------|
| **背景颜色** | 支持纯色,数量和颜色可配置。配置"borderColor"选项(0、1、2...),每个选项包含backgroundColor、textColor、xmageSource | 应用下发BORDER_COLOR key值,从配置文件读取对应颜色值 |
| **背景图案** | 支持两种绘制方式:0-从左到右平铺拼接,1-两张图分别居左居右。配置patternRenderType和图片资源 | 根据patternRenderType值选择绘制方式 |
| **前景图案** | 数量不定且可配置不选。配置"design"选项,每个选项设置affectedField为borderColor,previewSource按背景颜色id对应图案资源 | 应用下发DESIGN_IMAGE key值,结合BORDER_COLOR读取对应资源 |
| **文案文字** | 颜色与背景色绑定,从borderColor读取textColor | 应用下发WATERMARK_TEXT key值(文字内容) |
| **传播名** | 位置居左,颜色从borderColor读取textColor | 复用现有节假日水印逻辑 |
| **XMAGE图标** | 在传播名下面左对齐,Nova系列不显示。颜色从borderColor读取xmageSource获取图标资源 | 根据BORDER_COLOR读取xmageSource绘制 |
| **其它信息** | 拍照参数、时间、地点、自定义 | 复用现有逻辑 |

#### 内嵌水印配置项

| 配置项 | 规格说明 | 实现方案 |
|--------|---------|---------|
| **图案** | 可选图案数量不限且必选,每种图案可选颜色数量不限。配置design选项和source资源列表 | 应用下发DESIGN_IMAGE和DESIGN_COLOR两个key值 |
| **传播名** | 固定为白色,除UD系列外包含XMAGE字样(Nova不带) | 复用现有内嵌个性水印逻辑 |
| **XMAGE图标** | 除UD系列外跟随传播名资源,UD系列单独一行显示白色 | 复用现有内嵌个性水印逻辑 |
| **其它信息** | 拍照参数、时间、地点、自定义 | 复用现有内嵌水印逻辑 |

### 1.3 更新策略

节假日水印采用**资源云推更新策略**:

1. **资源配置化**:所有水印元素(图案、颜色、文字)均通过配置文件定义
2. **动态下发**:应用层通过key值机制下发配置,无需修改代码
3. **版本独立**:新增节假日水印只需更新资源包,不依赖应用版本发布
4. **规格约束**:UX设计必须在规格范围内,超出规格需额外开发

---

## 二、个性化水印

### 2.1 橘子海水印(个性水印画框背景)

橘子海水印是华为MS产品(对应代码中isNovaProduct)专属的个性化水印功能,提供三种画框背景样式:

| 背景类型 | 常量名 | 滤镜名称 | 说明 |
|---------|--------|---------|------|
| 橘子海 | INDIVIDUALITY_ORANGE_SEA(2) | OrangeSeaSticker | 新增,默认选项 |
| 暮光森林 | INDIVIDUALITY_TWILIGHT_FOREST(3) | TwilightForestSticker | 新增 |
| 极光蓝 | INDIVIDUALITY_AURORA_BLUE(4) | AuroraBlueSticker | 新增 |

**产品差异化设计**:
- **MS产品**:显示5个画框背景选项(无背景、磨砂、橘子海、暮光森林、极光蓝),默认选中橘子海
- **非MS产品**:仅显示2个选项(无背景、磨砂),默认无背景

### 2.2 自定义水印功能

个性化水印支持以下自定义能力:

1. **画框背景选择**:用户可在设置页选择不同画框背景样式
2. **参数持久化**:用户选择通过PreferencesService持久化存储,key为FunctionId.WATERMARK
3. **实时预览**:切换背景时自动更新预览图,预览图通过TaskPool线程渲染
4. **拍照渲染**:拍照时通过编创框架(ImageEffect)将画框背景叠加到照片底部

---

## 三、特殊水印的技术实现

### 3.1 架构设计

特殊水印系统采用**分层架构+数据驱动**设计:

```
UI层(features/extend)
  └─ WatermarkSettingView → SelectorParam → WatermarkOverView
      ↓
Redux状态管理层
  └─ WatermarkReducer → WatermarkAction → WatermarkDispatcher
      ↓
水印服务层(common/service/watermark)
  └─ WatermarkService → WatermarkHelper → WatermarkOperation
      ↓
拍照保存层(common/service/medialibrary)
  └─ MediaLibraryWorkerService(Worker子线程)
      ↓
编创框架(系统Native层)
  └─ ImageEffect渲染引擎
```

**核心设计原则**:
- **配置驱动UI**:ParamInfo配置化定义参数,UI组件自动渲染
- **编创框架解耦**:水印参数构建在ArkTS层,实际渲染在Native层
- **产品能力隔离**:通过CameraAppCapability.getIsNovaProduct()实现产品差异化

### 3.2 关键技术实现

#### 3.2.1 数据流

**选择流程**:
```
用户选择画框背景 → SelectorParam组件
  → dispatch(UPDATE_WATERMARK_PARAM)
  → WatermarkReducer更新state.pictureWatermark[21].borderBackground
  → WatermarkService持久化
  → UI刷新预览图
```

**拍照渲染流程**:
```
拍照完成 → MediaLibraryWorkerService接收watermarkMessage
  → WatermarkOperation.getWatermarkEffectData构建ImageEffect数据
    → 设置FILTER_BACKGROUND_TYPE参数
    → 根据isNovaProduct和borderBackground映射滤镜名称
      (2→OrangeSeaSticker, 3→TwilightForestSticker, 4→AuroraBlueSticker)
  → 调用Native层ImageEffect.editPixelMap渲染
  → 保存照片到相册
```

#### 3.2.2 核心代码实现

**ParamBackBorder配置扩展**:
```typescript
export const ParamBackBorder: ParamInfo = {
  paramId: 'borderBackground',
  defaultValue: (watermarkId: WatermarkId) => {
    return CameraAppCapability.getInstance().getIsNovaProduct() 
      ? SelectorOptions.SELECTOR_OPTION_THIRD  // 橘子海
      : SelectorOptions.SELECTOR_OPTION_FIRST;  // 无背景
  },
  selectors: [
    { id: 0, source: [''] },  // 无背景
    { id: 1, source: ['watermark_blur_background'] },  // 磨砂
    { id: 2, source: ['watermark_orange_sea_background'] },  // 橘子海
    { id: 3, source: ['watermark_twilight_forest_background'] },  // 暮光森林
    { id: 4, source: ['watermark_aurora_blue_background'] }  // 极光蓝
  ]
};
```

**滤镜名称映射**(WatermarkOperation.processNovaField):
```typescript
if (watermarkData.isNovaProduct && watermarkId == 21) {
  switch (borderBackground) {
    case 0: item.name = 'BorderSticker'; break;
    case 1: item.name = 'FrostedSticker'; break;
    case 2: item.name = 'OrangeSeaSticker'; break;  // 新增
    case 3: item.name = 'TwilightForestSticker'; break;  // 新增
    case 4: item.name = 'AuroraBlueSticker'; break;  // 新增
  }
}
```

#### 3.2.3 线程模型

| 线程 | 职责 | 关键操作 |
|------|------|---------|
| UI主线程 | 水印设置页渲染、用户交互 | SelectorParam组件、Redux状态更新 |
| Worker子线程 | 相机拍照、照片保存、水印叠加 | MediaLibraryWorkerService、WatermarkOperation |
| TaskPool线程 | 水印预览图渲染 | WatermarkRenderService |

#### 3.2.4 数据持久化

**存储结构**:
```typescript
interface WatermarkItemInfo {
  borderBackground: SelectorOptions;  // 0-4
  // ...其他参数
}
```

**兼容性设计**:
- 旧版本borderBackground值(0/1)可正常读取
- 新增值(2/3/4)仅在MS产品出现
- mergeConfigAndPreferencesData()处理数据合并

### 3.3 资源管理

**预置资源路径**: `/sys_prod/resource/camera/watermark/`

**资源文件**:
- watermark_orange_sea_background.png(橘子海预览图)
- watermark_twilight_forest_background.png(暮光森林预览图)
- watermark_aurora_blue_background.png(极光蓝预览图)

**缓存机制**:
- 预览图缓存:WatermarkRenderService.previewImageCache(以WatermarkIdType为key)
- 预设资源缓存:WatermarkService.watermarkPresetResource(以资源文件名为key)

---

## 四、小结

华为特殊水印系统通过**规格化设计**和**配置驱动**实现了灵活的水印扩展能力:

### 核心特点

1. **节假日水印**:采用资源云推机制,通过统一的规格配置实现快速上线,支持画框和内嵌两种类型,配置项涵盖背景、图案、文字、图标等全要素

2. **个性化水印**:针对MS产品提供差异化画框背景,通过产品能力判断实现功能隔离,采用数据驱动UI和编创框架解耦的架构设计

3. **技术实现**:采用分层架构(UI层→状态层→服务层→Native层),通过Redux实现单向数据流,Worker子线程处理拍照渲染,TaskPool线程处理预览图渲染,确保UI流畅性

### 技术亮点

- **配置化参数**:ParamInfo配置驱动UI渲染,新增功能仅需修改配置
- **产品差异化**:通过CameraAppCapability实现产品能力隔离
- **编创框架解耦**:ArkTS层构建参数,Native层执行渲染
- **资源云推**:节假日水印无需版本更新即可上线

### 扩展建议

1. **性能优化**:新增画框背景选项后,可考虑预加载预览图资源
2. **错误处理**:增强编创框架滤镜不支持时的降级策略
3. **测试覆盖**:重点关注MS/非MS产品切换、旧版本数据升级等兼容性场景
