# MCP 连接失败修复

日期: 2025-12-27

## 问题

多个 Kiro Power MCP 服务器连接失败，报错 `MCP error -32000: Connection closed`

## 失败的服务器

- power-saas-builder-awslabs.aws-serverless-mcp
- power-aurora-dsql-aurora-dsql
- power-cloud-architect-context7
- power-stripe-stripe (Unauthenticated)
- power-datadog-datadog (Connecting 卡住)

## 原因

- 缺少必要的环境变量配置（AWS_PROFILE、REGION 等）
- 需要认证但未配置 API Key
- 服务不可用或网络问题

## 解决方案

禁用不使用的 Power MCP 服务器：

```powershell
$configPath = "$env:USERPROFILE\.kiro\settings\mcp.json"
$json = Get-Content $configPath -Raw | ConvertFrom-Json

# 添加 disabled: true
$json.powers.mcpServers.'power-xxx' | Add-Member -NotePropertyName "disabled" -NotePropertyValue $true -Force

$json | ConvertTo-Json -Depth 10 | Set-Content $configPath -Encoding UTF8
```

## 配置文件位置

- 用户级: `~/.kiro/settings/mcp.json`
- 工作区级: `.kiro/settings/mcp.json`

## 注意

MCP 配置是用户级别文件，不在 git 仓库内，无法版本控制。
