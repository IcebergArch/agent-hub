# Methodology Harvest Evidence Template

用途：吸收外部 Agent 方法论、插件、技能库或工程 workflow 时，把本模板复制到对应 `reports/personal/agent/analysis/` 报告中填写。模板是审计骨架，不是完成证据；空项必须保留为缺口或继续补齐。

## Source Scope

| Item | Evidence |
| --- | --- |
| 来源名称 |  |
| 来源版本/日期 |  |
| 来源路径或 URL |  |
| 本次读取入口 |  |
| 代表性技能/模块 |  |
| references / docs |  |
| prompt templates |  |
| scripts / tools |  |
| assets / manifests |  |
| 未读取但可能相关 |  |

## Mechanism Extraction

| Mechanism | Invariant | Decision | Hub Destination | Notes |
| --- | --- | --- | --- | --- |
|  |  | adopted / adapted / not adopted |  |  |

## Resource Decision

| Resource | Decision | Destination | Reason |
| --- | --- | --- | --- |
| prompt template | copy / adapt / no-copy |  |  |
| script/tool | copy / adapt / no-copy |  |  |
| reference/doc | copy / adapt / no-copy |  |  |
| asset/brand | copy / adapt / no-copy |  |  |
| manifest/install/community | copy / adapt / no-copy |  |  |

## Pressure Scenarios

| Scenario | Target Skill/Rule | Result | Evidence |
| --- | --- | --- | --- |
|  |  | pass / partial / fail / not needed |  |

## Hub Landing

| Destination | File | Change |
| --- | --- | --- |
| Rule | `AGENTS.md` / `agents/<tool>.md` |  |
| Living context | `README.md` |  |
| Skill index | `skills/README.md` |  |
| Skill | `skills/<skill-slug>/SKILL.md` |  |
| Reference | `skills/<skill-slug>/references/<file>.md` |  |
| Report | `reports/personal/agent/analysis/<date>-关于<领域>-<topic>.md` |  |
| Report index | `reports/index.md` |  |

## Completion Evidence

| Requirement | Evidence | Status |
| --- | --- | --- |
| 外部来源已盘点 |  | complete / partial / missing |
| 机制已抽取 |  | complete / partial / missing |
| 资源已处理 |  | complete / partial / missing |
| Hub 落位已完成 |  | complete / partial / missing |
| 入口可发现 |  | complete / partial / missing |
| 边界无污染 |  | complete / partial / missing |
| 验证已执行 |  | complete / partial / missing |

## Verification Commands

```bash
rg -n "<trigger>|<skill-name>|<report-title>" AGENTS.md README.md agents skills reports/index.md
git diff --check
rg -n "[ \t]+$" AGENTS.md README.md agents skills reports
git status --short --untracked-files=all
```

## Final Boundary Notes

- Copied:
- Adapted:
- No-copy:
- Existing user changes preserved:
- Known gaps:
