日期：2026-06-25
类型：analysis
项目：agent-hub
来源：Agent Hub weekly upgrade review 自动化；复核本地已安装 Superpowers 缓存、官方上游 release notes 与 Hub 现有吸收映射
版式：结论先行，按来源漂移与 Hub 落位说明

# Agent Hub Superpowers Weekly Refresh

## Summary

本周 review 的结论是：Superpowers 继续发生真实上游漂移，但 Hub 只需要做一条很窄的升级。

- 本地已安装来源已轮换到 `7fd3161c`，`.codex-plugin/plugin.json` 仍是 `5.1.3`。
- 官方 `main` 的 `.codex-plugin/plugin.json` 已到 `6.0.3`，`RELEASE-NOTES.md` 新增了 2026-06-18 的 `v6.0.3`。
- 官方 `skills/` 目录仍是 14 个 skill，名称集合未变，因此本轮不新增 Hub skill owner。
- 新增且值得吸收的稳定机制只有一条：当 workflow 采用文件化 task/review handoff 时，scratch/report/progress 文件不要落到受保护的 `.git/`，而要放在工作树内的自忽略目录。

## Source Evidence

本轮读取的本地安装来源：

```text
/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/7fd3161c
```

本地核对结果：

- manifest：`.codex-plugin/plugin.json`
- version：`5.1.3`
- skills：14 个 `SKILL.md`

官方上游核对结果：

- `.codex-plugin/plugin.json`：`6.0.3`
- `RELEASE-NOTES.md`：包含 `v6.0.3 (2026-06-18)`、`v6.0.2 (2026-06-16)`、`v6.0.1 (2026-06-16)`、`v6.0.0 (2026-06-16)`
- `skills/` 目录：14 个 skill，名称集合与上周一致

## Drift Review

相对 2026-06-18 的周报，本周真实变化是：

- 本地 cache-id 从 `015c0dff` 继续轮换到 `7fd3161c`
- 官方版本从 `6.0.2` 前进到 `6.0.3`
- 官方新增一条与 Hub 相关的稳定机制：`subagent-driven-development` 不再把 implementer report、review diff 和 progress ledger 写到 `.git/`，而是改放到工作树内的自忽略 scratch 目录

本轮未发现以下变化：

- 没有新增或删减 skill owner
- 没有新增必须复制进 Hub 的 prompt template、script、brand asset 或安装文档
- 没有需要触碰 `reports/company/projects/` 的内容

## Decision

本轮采用的最小升级：

- 刷新 `superpowers-pattern-map.md` 和 `superpowers-coverage-audit.md` 的来源与版本证据，改为 `7fd3161c / 5.1.3 -> 6.0.3`
- 在 `external-skill-portability.md` 新增 scratch artifact placement 规则
- 把这条规则登记回 Superpowers 映射和覆盖审计

本轮刻意不做的事：

- 不修改 `AGENTS.md`、`README.md`、`task-execution-lifecycle/SKILL.md` 等当前已有用户改动的入口或主 Skill
- 不把本周周报加入根 `reports/index.md`；根索引是高频发现入口，而这份周报属于阶段性证据刷新
- 不升级本地插件安装状态，只记录来源事实

## Changed Files

- `skills/methodology-harvest/references/external-skill-portability.md`
- `skills/methodology-harvest/references/superpowers-pattern-map.md`
- `skills/methodology-harvest/references/superpowers-coverage-audit.md`
- `reports/personal/agent/analysis/2026-06-25-关于AgentHub-superpowersWeeklyRefresh.md`

## Validation

本轮收尾验证应覆盖：

```sh
rg -n "6\\.0\\.3|5\\.1\\.3|7fd3161c|scratch artifact|self-ignoring|\\.git/" skills/methodology-harvest reports/personal/agent/analysis/2026-06-25-关于AgentHub-superpowersWeeklyRefresh.md
git diff --check -- skills/methodology-harvest/references/external-skill-portability.md skills/methodology-harvest/references/superpowers-pattern-map.md skills/methodology-harvest/references/superpowers-coverage-audit.md reports/personal/agent/analysis/2026-06-25-关于AgentHub-superpowersWeeklyRefresh.md
rg -n '[ \t]+$' skills/methodology-harvest/references/external-skill-portability.md skills/methodology-harvest/references/superpowers-pattern-map.md skills/methodology-harvest/references/superpowers-coverage-audit.md reports/personal/agent/analysis/2026-06-25-关于AgentHub-superpowersWeeklyRefresh.md
```

## Follow-ups

- 如果本地安装源后续升级到 `6.0.3` 或更高版本，再复核 packaged plugin 是否与官方 `main` 一致。
- 如果后续只是 cache-id 再次轮换且 release notes 没有新机制，周检可以只刷新来源证据，不再扩写 Hub owner。
