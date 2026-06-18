日期：2026-06-18
类型：analysis
项目：agent-hub
来源：Agent Hub weekly upgrade review 自动化；复核本地已安装 Superpowers 来源并检查 Hub 吸收漂移
版式：结论先行，按来源漂移与 Hub 落位说明

# Agent Hub Superpowers Weekly Refresh

## Summary

本周 review 没有发现新的 Superpowers 机制需要吸收到 Hub。当前高价值变化不是 skill 语义，而是来源证据本身已经漂移：历史吸收资产里写死的 cache-id `c6ea566d` 已失效，当前安装来源变为 `43313cc9`，但版本仍为 `5.1.3`，14 个 skill 清单也未变化。

因此本轮升级只做两件事：

- 刷新 `methodology-harvest` 链路中的来源证据写法，避免把单次安装 cache-id 继续写成长期 reference。
- 记录 2026-06-18 周检结论：机制覆盖不变，无需新增 Skill、Rule 或 Reference owner。

## Source Evidence

本轮只读取本地已安装来源，未联网：

```text
/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/43313cc9
```

核对结果：

- manifest：`.codex-plugin/plugin.json`
- version：`5.1.3`
- repository：`https://github.com/obra/superpowers`
- skills：14 个 `SKILL.md`
- 资源类别：README、assets、agent manifests、prompt/examples、scripts/references 的大类入口仍与 2026-06-11 吸收基线一致

## Drift Review

和 2026-06-11 的吸收基线相比，本轮确认到的变化只有：

- 安装缓存目录从 `c6ea566d` 轮换到 `43313cc9`

本轮未确认到的变化：

- 插件版本未变化，仍为 `5.1.3`
- 14 个 skill 的名称集合未变化
- 不存在新的高价值 skill owner 需要在 Hub 新增 landing

这说明当前需要修复的是 Hub 的来源证据表达方式，而不是机制覆盖面。

## Decision

本轮采用以下最小升级：

- 在 `skills/methodology-harvest/SKILL.md` 明确：安装来源应优先记录稳定父目录或路径模式，再补本轮观测到的 cache-id/版本。
- 刷新 `superpowers-pattern-map.md` 与 `superpowers-coverage-audit.md` 的来源字段和周检结论。
- 把覆盖审计里的验证命令从写死 `c6ea566d` 改为基于父目录的路径模式搜索，避免下次 cache 轮换后 reference 立即失效。

本轮刻意不做的事：

- 不改动既有 Hub skill 的机制落位，因为当前没有发现新的稳定模式。
- 不重写 2026-06-11 的历史报告；它保留当时的来源事实，本轮以新 dated analysis 追加复核结论。
- 不触碰 `reports/company/projects/`。

## Changed Files

- `skills/methodology-harvest/SKILL.md`
- `skills/methodology-harvest/references/superpowers-pattern-map.md`
- `skills/methodology-harvest/references/superpowers-coverage-audit.md`
- `reports/personal/agent/analysis/2026-06-18-关于AgentHub-superpowersWeeklyRefresh.md`
- `reports/index.md`

## Validation

本轮应执行并已用于收尾的检查：

```sh
rg -n 'c6ea566d|43313cc9|<cache-id>|weekly refresh|Current Drift Note' skills/methodology-harvest reports/index.md reports/personal/agent/analysis
find /Users/shatang/.codex/plugins/cache/openai-curated/superpowers -path '*/skills/*/SKILL.md' -print | sort
git diff --check
rg -n '[ \t]+$' skills/methodology-harvest reports/index.md reports/personal/agent/analysis
```

## Follow-ups

- 若未来 Superpowers 版本或 skill 清单变化，再按 `superpowers-coverage-audit.md` 的 refresh checklist 扩展真实机制落位。
- 若只是 cache-id 再次轮换，Hub 无需新增机制，只需重复本轮这种来源证据复核。
