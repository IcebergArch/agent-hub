---
name: docs-refresh
description: 当用户要求写 README、说明文档、运行手册、迁移指南、架构说明、用户指南、FAQ，或把代码/方案整理成可读文档时使用。
---

# Docs Refresh

写或更新面向真实读者任务的文档，不记录过程流水账。

## Trigger

- 写 README、说明、docs、运行手册、迁移指南、FAQ、架构说明、用户指南。
- “整理成文档”“给我一份可以贴到公共文档里的内容”。

## Workflow

1. **Audience**：确认读者是谁，要完成什么任务。
2. **Facts**：从代码、配置、测试、命令输出或官方来源收集事实；不把猜测写成确定说明。
3. **Shape**：README、Runbook、Architecture、User Guide、FAQ 各自服务不同任务。
4. **Write For Action**：命令给完整 `cd` 路径和必要环境变量；概念少而准；删除历史过程和无关背景。
5. **Validate**：检查路径、命令、参数、链接和文件名；能跑的命令尽量跑，不能跑则说明。
6. **Sync**：同步索引、目录图和过时入口。

## Output

默认输出可直接落文件的 Markdown。若只需总结，包含 What Changed、How To Use、Verification、Notes。

## Quality Bar

- 文档必须帮助读者行动。
- 命令、路径、参数可复制。
- 不确定或未验证内容要标注。
