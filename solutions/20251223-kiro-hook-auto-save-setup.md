---
tags: [kiro, hook, 自动化, 知识管理]
domain: tools
project: Kiro_Work
difficulty: intermediate
---

# Kiro Hook 自动保存探讨记录配置方案

**日期：** 2025-12-23

## 问题背景

希望在与 Kiro 探讨问题后，自动将有价值的内容保存到知识库，而不需要每次手动提出保存请求。

## 解决方案

### 1. 知识库目录结构

```
knowledge-base/
├── discussions/   # 探讨记录
├── solutions/     # 解决方案
├── notes/         # 学习笔记
└── README.md
```

### 2. Hook 配置

在 `.kiro/hooks/auto-save-discussion.json` 创建 Hook：

```json
{
  "name": "自动保存探讨记录",
  "description": "当对话结束时，自动将探讨内容和解决方案保存到 knowledge-base 文件夹",
  "trigger": {
    "type": "onAgentComplete"
  },
  "action": {
    "type": "sendMessage",
    "message": "检查这次对话是否有价值，如有请保存到 knowledge-base 文件夹"
  }
}
```

### 3. 可用的触发条件

- `onAgentComplete` - agent 执行完成时
- `onSessionCreate` - 新会话创建时
- `onFileSave` - 文件保存时
- `onMessage` - 发送消息时
- `manual` - 手动点击触发

## 关键要点

1. Hook 是**工作区级别**的，只对当前项目有效
2. 关闭 Kiro 再打开同一工作区，Hook 仍然有效
3. 打开新的工程文件夹需要重新配置 Hook
4. 可以通过 UI 界面（Agent Hooks 面板加号）或自然语言描述创建 Hook
5. 目前不支持"空闲超时"触发，使用 `onAgentComplete` 作为替代方案

## 跨工作区复用配置

由于 Hook 是工作区级别的，新项目需要重新配置。采用 Steering + 初始化脚本结合方案：

### 初始化脚本

文件位置：`.kiro/scripts/init-knowledge-base.ps1`

运行命令：
```powershell
powershell -File .kiro/scripts/init-knowledge-base.ps1
```

脚本功能：
- 创建 `knowledge-base/` 目录结构
- 创建 `.kiro/hooks/auto-save-discussion.json`
- 创建 README 文件

### Steering 规则

文件位置：`.kiro/steering/check-hook-setup.md`

功能：每次新会话开始时，自动检查是否已配置 Hook，未配置则提醒运行初始化脚本。

### 使用流程

1. 打开新项目
2. Kiro 自动检测到未配置 Hook，提醒运行脚本
3. 运行 `powershell -File .kiro/scripts/init-knowledge-base.ps1`
4. 配置完成，自动保存功能生效


## 智能标签系统 (1+4 方案)

### 文档标签格式

每个文档头部添加 YAML front-matter：

```yaml
---
tags: [关键技术标签]
domain: 领域分类(frontend/backend/database/devops/tools/other)
project: 项目名称
difficulty: 难度(basic/intermediate/advanced)
---
```

### 索引生成

运行脚本自动生成分类索引：

```powershell
powershell -File .kiro/scripts/generate-index.ps1
```

索引按领域和类型两个维度分类展示所有文档。

### 工作流程

1. 保存时 Kiro 自动分析内容并添加标签
2. 同步到中央库后可运行索引脚本
3. 通过 INDEX.md 快速查找和导航


## 全局提醒方案 (1+3 方案)

### 用户级 Steering 规则

位置：`~/.kiro/steering/check-knowledge-base.md`

功能：无论打开哪个项目，都会检查是否配置了知识库，没有则提醒初始化。

### 初始化方式

方式一：从 GitHub 直接下载运行
```powershell
powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/DangDangMao01/Kiro_work/main/.kiro/scripts/init-knowledge-base.ps1' -OutFile 'init-kb.ps1'; .\init-kb.ps1; Remove-Item 'init-kb.ps1'"
```

方式二：从本地中央库运行
```powershell
powershell -File D:\G_GitHub\Kiro_Work\.kiro\scripts\init-knowledge-base.ps1
```

### 完整工作流程

1. 打开任意新项目 → Kiro 自动提醒初始化
2. 运行初始化脚本 → 创建 Hook + 同步脚本 + 目录
3. 正常对话 → 自动保存有价值内容并打标签
4. 同步到中央库 → 运行同步脚本汇总


## 历史知识扫描功能

### 检查流程

打开新项目时，检查以下任一条件：
1. `.kiro/hooks/auto-save-discussion.json` 文件存在
2. Agent Hooks 面板有相关 Hook
3. `knowledge-base/` 目录存在

满足任一条件则不提醒，否则提示初始化。

### 用户级 Steering 规则

位置：`~/.kiro/steering/check-knowledge-base.md`

此规则全局生效，所有项目都会执行检查流程。

## 重要发现

**脚本创建的 Hook 可能不被 Kiro 识别**，推荐通过 UI 创建：
1. 点击 Agent Hooks 面板的 **+** 号
2. 输入：`When agent completes, check if conversation has valuable content and save to knowledge-base folder`
3. 回车自动生成


## 功能测试总结

| 功能 | 脚本/配置 | 状态 |
|------|----------|------|
| 初始化知识库 | `init-knowledge-base.ps1` | ✅ 通过 |
| 自动保存 Hook | UI 创建 | ✅ 通过 |
| 同步到中央库 | `sync-to-central.ps1` | ✅ 通过 |
| 生成索引 | `generate-index.ps1` | ✅ 通过 |
| 全局提醒规则 | `~/.kiro/steering/` | ✅ 配置 |

## 完整工作流程

1. 新项目运行初始化脚本
2. 通过 UI 创建自动保存 Hook
3. 对话后自动保存带标签的文档
4. 运行同步脚本汇总到中央库
5. 运行索引脚本生成分类导航


## 历史知识自动发现功能

### 扫描范围

打开新项目时，自动扫描以下位置：
- `.kiro/*.md` - 之前的 Kiro 交互记录
- `README.md`、`CHANGELOG.md` - 项目文档
- `docs/` 目录 - 文档文件
- 根目录 `*.md` - 任何 markdown 文件

### 工作流程

1. 检测到未配置知识库
2. 扫描项目中的潜在知识内容
3. 发现内容后提示用户确认
4. 用户确认后智能分析并添加标签
5. 保存到本地 `knowledge-base/`
6. 提醒运行同步脚本


## 优化后的全自动流程

### 问题

- 手动操作太多（运行脚本、同步）
- 新项目 Kiro 提醒不明显
- 智能标签没有自动添加

### 解决方案

更新全局 Steering 规则，每次对话结束后自动执行：

1. 判断内容是否有价值
2. 有价值则自动保存并添加标签
3. 自动创建目录（如不存在）
4. 自动运行同步脚本
5. 简短确认消息

### 新项目设置

只需通过 UI 创建一次 Hook：
```
When agent completes, save valuable content to knowledge-base with tags and sync to central repository
```

之后全自动运行。


## 同步冲突处理机制

### 不会产生冲突的原因

1. **跳过已存在文件** - 同步脚本检查目标文件是否存在，已存在则跳过
2. **项目名前缀** - 同步时自动添加项目名前缀，不同项目文件名不重复
   - 原文件：`2025-12-23-shader-bug.md`
   - 同步后：`Assets-2025-12-23-shader-bug.md`

### 强制更新

使用 `-Force` 参数覆盖已存在的文件：

```powershell
powershell -File "E:\K_Kiro_Work\.kiro\scripts\sync-to-central.ps1" -CentralPath "E:\K_Kiro_Work\knowledge-base" -Force
```


## 新设备初始化

### 问题

换设备后：
- 全局 Steering 规则不存在
- 中央库路径可能不同
- 需要重新配置

### 解决方案

创建 `setup-new-device.ps1` 脚本，克隆仓库后运行一次：

```powershell
cd Kiro_work
powershell -ExecutionPolicy Bypass -File ".kiro\scripts\setup-new-device.ps1"
```

脚本功能：
- 自动创建全局 Steering 规则
- 使用当前设备的路径
- 显示适合当前设备的同步命令
