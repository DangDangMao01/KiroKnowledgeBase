---
tags: [kiro, hook, 排查, 调试]
domain: tools
project: Kiro_Work
difficulty: basic
---

# Kiro Hook 排查指南

**日期：** 2025-12-23

## 1. 检查 Hook 文件格式

打开 `.kiro/hooks/auto-save-discussion.json`，确认格式正确：

```json
{
  "name": "Hook名称",
  "trigger": { "type": "onAgentComplete" },
  "action": {
    "type": "sendMessage",
    "message": "触发后发送的消息"
  }
}
```

## 2. 确认 Kiro 加载了 Hook

在 Kiro 侧边栏找 "Agent Hooks" 面板：
- 显示 Hook 名称 → 已加载
- 空的或显示引导文字 → 未加载

未加载时尝试：
- 点击面板刷新按钮
- 重启 Kiro

## 3. 确认 Agent Complete 状态

当回复完成，聊天框下方没有 "Thinking..." 或加载动画，就是 Agent Complete 状态。

## 常见问题

- Hook 文件 JSON 格式错误
- 文件路径不对（必须在 `.kiro/hooks/` 下）
- Kiro 未重新加载配置

## 解决方案

**脚本创建的 Hook 不被识别时，通过 UI 创建：**

1. 点击 Agent Hooks 面板的 **+** 号
2. 输入自然语言描述，如：
   ```
   When agent completes, check if conversation has valuable content and save to knowledge-base folder
   ```
3. 回车，Kiro 自动生成正确格式的 Hook

**注意：** UI 创建的 Hook 存储位置可能与 `.kiro/hooks/` 不同，但功能正常。
