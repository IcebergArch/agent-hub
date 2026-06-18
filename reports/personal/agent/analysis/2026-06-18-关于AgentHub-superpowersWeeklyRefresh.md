日期：2026-06-18
类型：analysis
项目：agent-hub
来源：Agent Hub weekly upgrade review 自动化；复核本地已安装 Superpowers 来源、官方上游版本与 Hub 吸收漂移
版式：结论先行，按来源漂移与 Hub 落位说明

# Agent Hub Superpowers Weekly Refresh

## Summary

本周 review 发现了真实外部漂移：本地已安装 Superpowers 仍是 `015c0dff` / `5.1.3`，但官方 `https://github.com/obra/superpowers` 的 `main` 分支 `.codex-plugin/plugin.json` 已是 `6.0.2`，`RELEASE-NOTES.md` 记录 2026-06-16 的 v6.0.0-v6.0.2 更新。

关键判断：

- skill 名称集合仍是 14 个，没有新增 Hub skill owner。
- v6 的稳定变化主要是 review/plan/delegation/skill-authoring 机制增强，而不是目录结构新增。
- Hub 需要更新长期 evidence 和已有 owner 的 reference，避免继续声称“机制覆盖无需扩写”。
- 本轮不安装或升级本地插件，不复制外部 prompt、脚本、品牌资产或安装说明。

## Source Evidence

本轮读取了本地已安装来源：

```text
/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/015c0dff
```

本地核对结果：

- manifest：`.codex-plugin/plugin.json`
- version：`5.1.3`
- repository：`https://github.com/obra/superpowers`
- skills：14 个 `SKILL.md`

官方上游核对结果：

- `https://raw.githubusercontent.com/obra/superpowers/main/.codex-plugin/plugin.json`：version `6.0.2`
- `https://github.com/obra/superpowers/tree/main/skills`：仍列出 14 个 skill 目录
- `https://raw.githubusercontent.com/obra/superpowers/main/RELEASE-NOTES.md`：v6.0.0-v6.0.2 发布于 2026-06-16，包含 review flow、plan structure、harness-neutral wording、Codex packaging fix、visual companion security、skill authoring 等变化

## Drift Review

和 2026-06-11 的吸收基线相比，本轮确认到的变化：

- 安装缓存目录从 `c6ea566d`、同日早前观测到的 `43313cc9` 继续轮换到 `015c0dff`
- 本地安装版本仍停在 `5.1.3`
- 官方上游已经进入 `6.0.2`
- 官方 skill 名称集合仍是 14 个，未新增可直接映射成 Hub skill owner 的入口
- review 机制从两个 per-task reviewer prompt 收敛为单个 task reviewer，返回 spec compliance 与 quality 两类 verdict
- review 新增或强化 `can't verify from diff`，要求 controller 补 runtime/source evidence
- plan 机制强化 Global Constraints 与 per-task Interfaces，保证委派执行者和 reviewer 不依赖隐含上下文
- delegated task/review 更强调以文件或显式 context package 交接 diff、brief 和证据，并在可用时显式选择能力/model/tool level
- skill authoring 强化“指导形式匹配失败模式”和 cheap micro-test wording

本轮未采用的变化：

- 不复制外部 prompt 原文、browser companion 脚本、Graphviz 脚本、品牌资产或安装文档
- 不把官方 `main` 直接当成本地已安装状态；Hub 文档同时记录本地安装版本和官方上游版本
- 不为 Kimi/Pi/Antigravity/OpenCode 等 harness 新增 Hub 默认维护范围；当前 Hub 仍长期维护 Codex/Cursor

## Decision

本轮采用以下最小升级：

- 刷新 `superpowers-pattern-map.md` 与 `superpowers-coverage-audit.md` 的来源字段：区分本地安装 `5.1.3` 与官方上游 `6.0.2`。
- 在 `implementation-plan/references/plan-review-checklist.md` 增加 Global Constraints / per-task Interfaces 的委派契约检查。
- 在 `task-execution-lifecycle/references/parallel-agent-coordination.md` 增加 capability/tool level、文件化 context package、单 reviewer 双 verdict 和 `can't verify from diff` 处理。
- 在 `code-review/references/review-checklists.md` 增加 review package、只读 reviewer、不预设忽略项、`can't verify from diff` 证据缺口处理。
- 在 `skill-authoring-patterns.md` 增加 Form To Failure Match 和 Micro-Test Wording。

本轮刻意不做的事：

- 不新增 Hub skill owner，因为官方 skill 名称集合未变。
- 不重写 2026-06-11 的历史报告；它保留当时的来源事实，本轮以新 dated analysis 追加复核结论。
- 不触碰 `reports/company/projects/`。

## Changed Files

- `skills/methodology-harvest/SKILL.md`
- `skills/methodology-harvest/references/superpowers-pattern-map.md`
- `skills/methodology-harvest/references/superpowers-coverage-audit.md`
- `skills/methodology-harvest/references/skill-authoring-patterns.md`
- `skills/implementation-plan/references/plan-review-checklist.md`
- `skills/task-execution-lifecycle/references/parallel-agent-coordination.md`
- `skills/code-review/references/review-checklists.md`
- `reports/personal/agent/analysis/2026-06-18-关于AgentHub-superpowersWeeklyRefresh.md`
- `reports/index.md`

## Validation

本轮应执行并用于收尾的检查：

```sh
rg -n '6.0.2|5.1.3|015c0dff|Global Constraints|per-task Interfaces|can.t verify from diff|task-reviewer|Micro-Test Wording|Form To Failure' skills reports
find /Users/shatang/.codex/plugins/cache/openai-curated/superpowers -path '*/skills/*/SKILL.md' -print | sort
git diff --check
rg -n '[ \t]+$' AGENTS.md README.md agents/codex.md skills reports/personal/agent/analysis/2026-06-18-关于AgentHub-superpowersWeeklyRefresh.md
```

## Follow-ups

- 若本地 Codex 插件后续安装源升级到 `6.0.2` 或更高版本，再复核 packaged plugin 是否与官方 `main` 一致。
- 若官方 skill 清单新增、删除或改名，再按 `superpowers-coverage-audit.md` 的 refresh checklist 扩展真实机制落位。
- 若只是 cache-id 再次轮换，Hub 无需新增机制，只需刷新来源证据。
