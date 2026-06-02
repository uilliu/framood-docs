# 华为水印内存优化调研报告

> 调研日期：2026-05-21  
> 数据来源：华为开发者文档
> 可信度等级：★★★★★（高）

## 一、概述

2亿像素拍照场景下，添加四周水印时需要同时持有原图(A, 约288MB)和水印图(B, 约400MB)两个DMA buffer，导致进程峰值内存需求高达约688MB，大内存申请带来显著时延。本调研分析内存优化方案，目标将峰值内存从A+B降低到B。

## 二、内存优化方案

### 2.1 核心思路

基于当前实现（原图和水印图均采用SurfaceBuffer申请），在相同分配器下具备内存扩展条件。SurfaceBuffer提供类似realloc接口，在保持已有内存内容的情况下扩展内存大小，并支持在扩展内存上进行内容搬移。

### 2.2 边框水印优化

边框水印指在照片底部增加水印信息。优化后内存峰值从(原图A+水印图B)降至水印图B，消除原图内存占用。

### 2.3 四周水印优化

四周水印在原图四周都加入边框，优化策略相同：通过内存扩展替代重新申请，峰值内存从A+B降至B。

## 三、SurfaceBuffer机制

### 3.1 当前接口

SurfaceBuffer当前仅暴露Alloc/Map/Unmap接口，未提供类似Realloc的内存扩展接口。

### 3.2 实现层次

```
编创框架 -> SurfaceBuffer -> Display HDI -> DMA内存管理
```

- **SurfaceBufferImpl**：包含reuse能力，在配置变化允许情况下可复用已有buffer
- **BufferHandle**：底层内存句柄管理
- **IDisplayBuffer**：HDI接口层（已演进至v1.4）

### 3.3 扩展能力需求

确认结果（丁盼云 00822555, 2025/12/30）：
1. 当前无对外扩展内存接口，需新开放
2. 内存扩展最终在HDI底层实现（海思）
3. 扩展后数据搬移需与海思进一步沟通

## 四、内存申请与管理

### 4.1 内存申请机制

编创框架管理三种内存类型：
- **DMA Buffer**：对接SurfaceBuffer
- **Shared Memory**：共享内存
- **HEAP**：堆内存

水印处理使用DMA Buffer，通过`effect_memory_manager.cpp`管理。

### 4.2 日志示例

```
[Render] width:3072, rowStride:3072, height:4096, length:18878464, 
formatType:YUVNV21, pixelMapType:Primary, bufferType:DMA_BUFFER

alloc new buffer: width:3316, rowStride:3328, height:5170, 
length:25808896, formatType:YUVNV21
```

### 4.3 方案分解

| 领域 | 团队 | 工作内容 |
|------|------|----------|
| 媒体库 | HO | 接口不变，不感知修改 |
| 编创 | HO | 修改新内存申请为内存扩展，按扩展后大小进行图像搬移 |
| 图形-SurfaceBuffer | OH-图形 | 新增接口，支持内存扩展，对接HDI |
| Display HDI驱动 | 海思 | 新增接口供SurfaceBuffer调用 |
| VDI驱动 | 海思-DMA | 新增接口供MapperService调用 |

## 五、关键问题与解决

### 5.1 虚拟内存空间冲突

**问题**：原内存尾部扩展时，扩展区域虚拟地址可能已被使用。

**解决方案**：上下层配合，下层返回新地址buffer；上层保证地址变化后正常使用，原buffer地址不再使用。

### 5.2 format/usage约束

format/usage是否可保持不变是方案可行性关键，若变化则可能影响方案实施。

## 六、小结

1. **优化效果**：峰值内存从A+B(约688MB)降至B(约400MB)，降幅约42%
2. **技术路径**：SurfaceBuffer新增ReallocMemory接口 -> Display HDI扩展支持 -> DMA内存管理扩展
3. **待解决**：虚拟地址冲突处理、format/usage约束确认、数据搬移规则细化
4. **涉及团队**：媒体库、编创、图形SurfaceBuffer、海思驱动等多团队协作
