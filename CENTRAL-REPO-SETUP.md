# 中央知识库 Git 仓库设置指南

## 1. 创建中央库仓库

在 GitHub/GitLab/Gitee 创建一个新仓库，例如 `kiro-knowledge-base`

## 2. 克隆到本地固定位置

```bash
# 选择一个固定位置，例如
git clone https://github.com/你的用户名/kiro-knowledge-base.git D:\KiroKnowledge
```

## 3. 初始化目录结构

```bash
cd D:\KiroKnowledge
mkdir discussions solutions notes
git add .
git commit -m "init: 初始化知识库结构"
git push
```

## 4. 在各项目中同步

```powershell
# 在任意项目中运行
powershell -File .kiro/scripts/sync-to-central.ps1 -CentralPath "D:\KiroKnowledge"
```

## 5. 换设备时

```bash
# 在新设备克隆中央库
git clone https://github.com/你的用户名/kiro-knowledge-base.git D:\KiroKnowledge
```

## 工作流程

1. 各项目本地保存探讨记录到 `knowledge-base/`
2. 定期运行同步脚本，汇总到中央库
3. 中央库 git push 到远程
4. 换设备时 git clone 即可恢复所有记录
