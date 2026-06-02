# AI操作设计工具调研报告
如何让Claude Code/OpenCode通过API操作Figma/Motiff完成原型设计

> 调研日期：2026年5月26日
> 目标：分析Figma/Motiff的API能力，以及如何让AI编程助手操作这些设计工具

---

## 一、现状分析
### 1.1 用户需求
用户不直接使用Figma/Motiff，希望通过Claude Code/OpenCode等AI编程助手来操作这些设计工具，自动完成原型设计。

### 1.2 可行性评估
| 方案 | 可行性 | 说明 |
|------|---------|---------|
| Claude Code直接操作Figma | ⚠️ 有限 | Claude Code无内置Figma API，需通过MCP Server扩展 |
| OpenCode直接操作Figma | ⚠️ 有限 | 同上，需配置外部API |
| 通过Figma API间接操作 | ✅ 可行 | 需编写MCP Server调用Figma API |
| 通过Figma Plugin | ✅ 可行 | 需开发插件让AI调用 |
| 替代方案：纯代码生成 | ✅ 推荐 | 用AI生成设计代码，跳过设计工具 |

---

## 二、Figma API能力分析
### 2.1 Figma API概述
Figma提供REST API，支持以下操作：
| API | 功能 | 用途 |
|------|------|------|
| GET /files | 获取文件列表 | 查看项目结构 |
| GET /files/:key | 获取单个文件 | 读取设计文件 |
| POST /files | 创建文件 | 创建新设计 |
| PUT /files/:key | 更新文件 | 修改设计 |
| DELETE /files/:key | 删除文件 | 删除设计 |
| POST /comments | 添加评论 | AI注释设计问题 |
| GET /styles | 获取样式 | 读取设计规范 |

### 2.2 Figma API限制
| 限制 | 说明 | 影响 |
|------|---------|---------|
| 需要OAuth认证 | 需Figma账号Token | 需配置Token |
| 文件格式复杂 | Figma文件为二进制格式 | 难以直接编辑 |
| 操作粒度有限 | API不支持细粒度编辑 | 需通过插件或本地操作 |
| 无图像生成API | API不生成图像素材 | 需外部AI生成 |

### 2.3 Claude Code操作Figma的技术路径
```
Claude Code ─── MCP Server ─── Figma API ─── Figma文件
    │                │                    │
    │                ▼                    │
    │         需配置MCP Server         │
    │         需编写Python/TS代码      │
    │         需配置Figma Token        │
    └─────────────────────────────────────┘
```
---

## 三、MCP Server扩展方案
### 3.1 MCP Server架构
MCP (Model Context Protocol) 是Claude Code的扩展机制，允许添加自定义工具。
```
┌──────────────────┐   ┌─────────────────────┐   ┌───────────────┐
│ Claude Code CLI  │──▶│ MCP Server    │──▶│ Figma API    │
│                  │   │ (Python/TS)   │   │ (REST)      │
│  调用MCP工具    │◀──│ 暴露工具    │◀──│ 调用API    │
│                  │   │              │   │              │
└──────────────────└─────────────────────┘───────────────┘
```

### 3.2 Figma MCP Server实现示例
**Python MCP Server代码框架**：
```python
# figma_mcp_server.py
from mcp.server import Server
from mcp.types import Tool, TextContent
import httpx
import os

class FigmaMCPServer(Server):
    def __init__(self):
        self.figma_token = os.environ.get("FIGMA_TOKEN")
        self.base_url = "https://api.figma.com/v1"
    
    @Server.list_tools()
    async def list_files(self, project_key: str) -> list[Tool]:
        """列出Figma项目中的文件"""
        url = f"{self.base_url}/projects/{project_key}/files"
        headers = {"Authorization": f"Bearer {self.figma_token}"}
        response = httpx.get(url, headers=headers)
        return [TextContent(type="text", text=response.text)]
    
    @Server.list_tools()
    async def create_design(self, project_key: str, name: str) -> list[Tool]:
        """创建新的Figma设计文件"""
        url = f"{self.base_url}/projects/{project_key}/files"
        headers = {"Authorization": f"Bearer {self.figma_token}"}
        data = {"name": name, "type": "CANVAS"}
        response = httpx.post(url, headers=headers, json=data)
        return [TextContent(type="text", text=f"Created file: {response.json()['key']}")]

# Claude Code配置
# .claude/settings.json
{
  "mcpServers": {
    "figma": {
      "command": "python figma_mcp_server.py",
      "env": {
        "FIGMA_TOKEN": "your-figma-personal-access-token"
      }
    }
  }
}
```

### 3.3 MCP Server能力与局限
| 能实现 | 不能实现 |
|--------|---------|
| 读取Figma文件结构 | 直接编辑设计元素 |
| 创建新设计文件 | 生成复杂UI组件 |
| 添加文本评论 | 创建图像素材 |
| 获取样式规范 | 布局精细调整 |
| 删除文件 | 自动对齐、约束 |

---

## 四、替代方案：纯代码生成（推荐）
### 4.1 为什么推荐替代方案
1. **Figma API操作粒度有限**：无法精确控制设计元素
2. **设计文件格式复杂**：难以程序化编辑
3. **Claude Code擅长代码生成**：直接生成设计代码更高效
4. **开发效率更高**：跳过设计工具，直接进入开发

### 4.2 替代方案流程
```
┌─────────────────┐
│ 设计规范文档   │ ← CLAUDE.md + 设计Token定义
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Claude Code生成 │
│ 设计代码       │ ← ArkTS/Flutter/React代码
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 预览/调整      │ ← 本地运行预览
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 设计确认       │ ← 人工确认后继续开发
└─────────────────┘
```

### 4.3 具体实施步骤
**Step 1：准备设计规范文档**
```markdown
# CLAUDE.md

## 设计规范

### 调色板
- 主色：#1A73E8（XMAGE紫）
- 辅色：#FFFFFF（白色文字）
- 背景：#000000（黑色边框）

### 字体
- 主标题：14sp Medium（Roboto/鸿蒙字体）
- 参数文字：12sp Regular
- 信息文字：10sp Light

### 组件
- 边框水印：底部参数条，高度占图片宽度12%
- Logo位置：左侧，宽度80px
- 参数排列：机型 | 时间 | GPS | 光圈 快门 ISO

### 尺寸
- 边框高度：图片宽度的12%
- Logo宽度：80px
- 文字间距：16px
- 边距：16px

### 动效
- 选中状态：scale(1.02)
- 过渡时长：200ms
```

**Step 2：让Claude Code生成设计代码**
```
请根据以下设计规范，生成HarmonyOS ArkTS的边框水印组件：

设计规范：
- 边框高度：图片宽度的12%
- Logo位置：左侧
- 参数文字：机型 | 时间 | GPS | 光圈 快门 ISO
- 主色：#1A73E8（XMAGE紫）

要求：
1. 生成可复用的WatermarkFrame组件
2. 支持动态参数配置
3. 遵循HarmonyOS ArkUI规范
4. 包含完整的样式代码
```

**Step 3：预览与调整**
- 本地运行DevEco Studio预览
- 人工调整设计细节
- 确认后继续开发

### 4.4 Claude Code生成设计代码示例
**Prompt模板**：
```
根据以下设计规范，生成[平台]的[组件名]组件代码。

设计规范：
[调色板]
[字体规范]
[布局规范]
[尺寸规范]

要求：
1. 可复用组件设计
2. 支持动态配置
3. 遵循[平台]规范
4. 包含完整样式
5. 输出文件路径建议
```

---

## 五、完整工作流建议
### 5.1 方案对比与推荐
| 方案 | 复杂度 | 效率 | 推荐场景 |
|------|---------|---------|---------|
| MCP Server操作Figma | 高 | 低 | 需要对接现有Figma项目 |
| Figma Plugin | 高 | 低 | 团队使用Figma协作 |
| **纯代码生成（推荐）** | 低 | 高 | 个人开发、快速迭代 |
| 设计Token + 代码生成 | 中 | 高 | 需要设计规范沉淀 |

### 5.2 推荐工作流（设计Token + 代码生成）
```
Phase 1: 建立设计规范
├── 定义调色板、字体、组件规范
├── 存入CLAUDE.md或design.md
├── 建立设计Token JSON文件

Phase 2: AI生成设计代码
├── 提供设计规范 + 功能需求
├── Claude Code生成组件代码
├── 本地预览验证

Phase 3: 设计迭代
├── 人工调整设计细节
├── 更新设计Token
├── AI重新生成代码

Phase 4: 继续开发
├── 设计确认后进入功能开发
└───────────────────────────────────────┘
```

### 5.3 设计Token文件示例
**design_tokens.json**：
```json
{
  "color": {
    "primary": "#1A73E8",
    "secondary": "#FFFFFF",
    "background": "#000000",
    "accent": "#FF6B35"
  },
  "typography": {
    "heading": {
      "fontFamily": "Roboto-Medium",
      "fontSize": 14,
      "lineHeight": 20
    },
    "body": {
      "fontFamily": "Roboto-Regular",
      "fontSize": 12,
      "lineHeight": 16
    },
    "caption": {
      "fontFamily": "Roboto-Light",
      "fontSize": 10,
      "lineHeight": 14
    }
  },
  "spacing": {
    "padding": 16,
    "margin": 8,
    "gap": 16
  },
  "components": {
    "watermarkFrame": {
      "heightRatio": 0.12,
      "logoWidth": 80,
      "textSpacing": 16
    }
  }
}
```

---

## 六、MCP Server开发指南（可选）
### 6.1 如果必须操作Figma
**前提条件**：
- Figma Personal Access Token
- Python环境（mcp库）
- Claude Code配置权限

**开发步骤**：
1. 创建MCP Server Python文件
2. 配置Figma API调用
3. 在Claude Code中注册MCP Server
4. 测试工具调用

**注意事项**：
- Figma Token需保密，不要提交到Git
- API调用有频率限制
- 复杂设计操作可能失败

### 6.2 MCP Server调试
```bash
# 测试MCP Server
python figma_mcp_server.py --test

# Claude Code调用MCP工具
> use figma tool: list_files with project_key: "YOUR_PROJECT_KEY"
```

---

## 七、总结与建议
### 7.1 核心结论
| 问题 | 答案 |
|------|------|
| 能否让AI操作Figma？ | 可以，但需MCP Server扩展，复杂度高 |
| 是否推荐操作Figma？ | 不推荐，效率低，操作粒度有限 |
| 推荐方案是什么？ | 设计Token + AI直接生成设计代码 |
| 如何实施？ | 1. 建立设计规范 → 2. AI生成代码 → 3. 预览调整 |

### 7.2 实施路线
```
立即可做：
├── 建立设计Token文件（design_tokens.json）
├── 更新CLAUDE.md添加设计规范章节
├── 使用Claude Code生成水印组件代码

后续可选：
├── 如果团队需要Figma协作，开发MCP Server
├── 配置Figma API Token
└───────────────────────────────────────┘
```

---

*报告完成于 2026年5月26日*