# 知识库 - 探讨与问题解决存储

用于记录与 Kiro 探讨问题、解决方案和学习笔记的工程目录。

## 新设备初始化

克隆仓库后，在仓库目录运行：
```powershell
powershell -ExecutionPolicy Bypass -File ".kiro\scripts\setup-new-device.ps1"
```

这会自动配置全局 Steering 规则，并显示适合当前设备的同步命令。

## 快速设置（新项目）

### 1. 创建 Hook（在 Agent Hooks 面板点 + 输入）
```
auto save kb
```

### 2. 手动同步命令
```powershell
powershell -File "E:\K_Kiro_Work\.kiro\scripts\sync-to-central.ps1" -CentralPath "E:\K_Kiro_Work\knowledge-base"
```

### 3. 生成索引（在中央库运行）
```powershell
powershell -File ".kiro\scripts\generate-index.ps1"
```

## 目录结构

- `discussions/` - 探讨记录
- `solutions/` - 解决方案
- `notes/` - 学习笔记
- `INDEX.md` - 自动生成的索引
