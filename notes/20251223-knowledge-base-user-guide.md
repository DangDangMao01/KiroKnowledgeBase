---
tags: [kiro, 知识库, 使用指南]
domain: tools
project: Kiro_Work
difficulty: basic
---

# 知识库系统使用指南

**日期：** 2025-12-23

## 新项目快速设置

### 步骤 1：创建自动保存 Hook

1. 打开新项目的 Kiro
2. 找到侧边栏的 **AGENT HOOKS** 面板
3. 点击右上角的 **+** 号
4. 输入：`auto save kb`
5. 按回车确认

完成！之后每次对话结束都会自动保存有价值内容。

### 换设备时

从 GitHub README 复制命令：https://github.com/DangDangMao01/Kiro_work

## 手动操作命令

### 初始化知识库（可选）

```powershell
powershell -ExecutionPolicy Bypass -File "E:\K_Kiro_Work\.kiro\scripts\init-knowledge-base.ps1"
```

### 同步到中央库

```powershell
powershell -File "E:\K_Kiro_Work\.kiro\scripts\sync-to-central.ps1" -CentralPath "E:\K_Kiro_Work\knowledge-base"
```

### 生成索引（在中央库运行）

```powershell
powershell -File ".kiro\scripts\generate-index.ps1"
```

## 目录结构

```
knowledge-base/
├── discussions/   # 探讨记录
├── solutions/     # 解决方案
├── notes/         # 学习笔记
└── INDEX.md       # 自动生成的索引
```

## 文档格式

每个文档头部包含标签：

```yaml
---
tags: [技术标签]
domain: frontend|backend|database|devops|tools|unity|other
project: 项目名称
difficulty: basic|intermediate|advanced
---
```

## 工作流程

1. 在任意项目与 Kiro 对话
2. 对话结束后自动判断是否有价值
3. 有价值内容自动保存并添加标签
4. 自动同步到中央知识库
5. 在中央库可运行索引脚本生成导航
