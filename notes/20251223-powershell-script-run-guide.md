---
tags: [powershell, windows, 脚本]
domain: tools
project: Kiro_Work
difficulty: basic
---

# PowerShell 脚本运行指南

**日期：** 2025-12-23

## 问题背景

Windows 中 PowerShell 脚本（.ps1 文件）不能直接双击运行，需要通过命令行执行。

## 运行方法

### 方法一：在 Kiro/VS Code 终端中运行（推荐）

1. 打开项目
2. 按 `Ctrl + `` 打开终端面板
3. 运行命令：
```powershell
powershell -ExecutionPolicy Bypass -File "脚本路径"
```

### 方法二：在 Windows PowerShell 中运行

1. 按 `Win + X`，选择 "Windows PowerShell"
2. 切换到目标目录：`cd "项目路径"`
3. 运行脚本

### 方法三：右键运行

右键点击 .ps1 文件 → "使用 PowerShell 运行"

注意：此方式会在脚本所在目录执行

## 关键要点

- `-ExecutionPolicy Bypass` 绕过执行策略限制
- 必须先 `cd` 到目标目录，脚本才会在正确位置创建文件
- 双击 .ps1 文件默认会用记事本打开，不会执行
