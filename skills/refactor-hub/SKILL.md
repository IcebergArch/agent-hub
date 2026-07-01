---
name: refactor-hub
description: 当用户说 project refactor、receive、refactor hub、优化/整理 hub，或要求从当前对话抽取可复用规则、技能、注意点时使用。
---

# Refactor Hub

把当前协作暴露出的稳定经验沉淀回 Agent Hub。它处理“从对话到 Hub”的回流，不负责一般内容清理。

## Trigger

- `receive`、`project refactor`、`refactor hub`、优化/整理 hub、更新 hub 指令。
- 用户要求回看/复盘当前对话，并抽取可复用规则、技能、注意点或工作方法。
- 用户要求纠正某类长期流程，例如 `git update`、`git diff`、验证、提交、发布、调试。

只修改当前项目 README、代码或测试时不触发，除非用户明确要求沉淀到 Hub。

## Workflow

1. **Review Conversation**：提取用户明确要求、纠偏点、反复偏好和流程缺口；上下文缺失时不编造。
2. **Inventory Hub**：读入口和相关 Skill，先查现有 owner，避免新增近义规则。
3. **Analyze Gap**：判断缺的是触发、动作点、owner、验证、规则位置、reference，还是已有规则未生效。
4. **Classify**：按 `project-content-curation` 分类为 Skill / Rule / Reference / Project Helper / Report / Keep / Delete。
5. **Patch Existing Owner**：优先合并、压缩、下沉或删除；只有缺 owner 时才新增 Skill。
6. **Sync And Validate**：更新入口、索引、旧触发词引用；跑引用搜索和 diff check。

## Output

说明本轮抽取了什么、调整了哪些文件、哪些内容没有沉淀及原因、执行了哪些验证。

## Quality Bar

- 不把对话过程原样归档。
- 不把项目名、业务方案、临时命令或未验证猜测写进通用规则。
- 规则必须有触发、保护的不变量、检查工件、例外和验证方式。
