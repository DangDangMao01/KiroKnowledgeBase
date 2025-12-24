---
tags: [kiro, git, 同步, 知识管理]
domain: tools
project: Kiro_Work
difficulty: intermediate
---

# 中央知识库同步方案 (B+C 方案)

**日期：** 2025-12-23

## 问题背景

多个工作区各自保存探讨记录，需要整合到一个中央备案库，且支持更换设备后恢复。

## 解决方案

采用 **本地保存 + 同步脚本 + Git 仓库** 结合方案：

1. 各项目本地保存到 `knowledge-base/`
2. 同步脚本汇总到中央库
3. Git 管理中央库，支持多设备同步

## 核心文件

### 同步脚本
`.kiro/scripts/sync-to-central.ps1`

```powershell
powershell -File .kiro/scripts/sync-to-central.ps1 -CentralPath "D:\KiroKnowledge"
```

功能：
- 自动复制本地记录到中央库
- 添加项目名前缀避免文件名冲突
- 跳过已存在的文件

### 设置指南
`knowledge-base/CENTRAL-REPO-SETUP.md`

## 工作流程

1. 创建 Git 仓库作为中央库
2. 克隆到本地固定位置
3. 各项目定期运行同步脚本
4. 中央库 git push 到远程
5. 换设备时 git clone 恢复

## 关键要点

- 本地记录按项目隔离，互不影响
- 同步时自动添加项目名前缀
- Git 提供版本管理和多设备同步
- 适合多工作区 + 换设备场景
