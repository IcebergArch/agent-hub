# Superpowers Coverage Audit

日期：2026-06-25
来源：本地已安装 Superpowers 插件 `/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/<cache-id>`；本轮核对实例为 `7fd3161c`；外部官方来源为 `https://github.com/obra/superpowers`
版本：本地安装为 `5.1.3`；官方 `main` 的 `.codex-plugin/plugin.json` 为 `6.0.3`
用途：作为 `superpowers-pattern-map.md` 的覆盖审计账本，确认 14 个 Superpowers skills 与主要资源类别都已有 Hub-native 处理结论。

## Current Drift Note

2026-06-25 周检确认：

- 当前安装目录的 cache-id 已继续轮换为 `7fd3161c`。
- `.codex-plugin/plugin.json` 中的版本仍为 `5.1.3`。
- `skills/*/SKILL.md` 仍为 14 个，名称集合未变化。
- 官方仓库 `main` 的 `.codex-plugin/plugin.json` 已是 `6.0.3`，`RELEASE-NOTES.md` 新增 2026-06-18 的 v6.0.3 条目。
- 官方 `skills/` 目录仍为 14 个 skill，名称集合未变化；本轮没有新增 Hub skill owner。
- 官方新增的稳定机制是：subagent-driven-development 把 task brief、review diff、implementer report 和 progress ledger 从 `.git/` 挪到工作树内的自忽略 scratch 目录，避免 agent 写受保护路径。

因此本轮结论是“本地安装仍未升级，但官方上游已从 v6.0.2 漂移到 v6.0.3”。Hub 需要刷新来源证据，并把 scratch artifact placement 这条稳定机制映射到既有 reference；不新增 Skill owner，不复制外部 prompt、脚本、品牌资产或安装说明。

## Audit Rule

每次刷新 Superpowers 版本或重新吸收外部方法论时，先更新本文件，再决定是否修改 Hub Skill、Rule 或 report。审计状态只允许使用：

- `adopted`：机制已进入 Hub 规则、技能正文或长期 reference。
- `adapted`：只吸收结构、检查点、资源处理方式或验证语义，不复制原文或绑定运行时。
- `no-copy`：明确不进入 Hub，只在报告或映射表记录原因。
- `watch`：当前不落地，但未来若 Hub runtime、tooling 或多 agent 能力变化需复核。

## Skill Coverage

| Superpowers skill | Hub landing | Evidence | Status |
| --- | --- | --- | --- |
| `using-superpowers` | `AGENTS.md` Loading Order、`skills/README.md`、`skill-trigger-discipline.md` | `skills/methodology-harvest/references/skill-trigger-discipline.md` | adapted |
| `brainstorming` | `requirements-brief`、`implementation-plan`、`task-execution-lifecycle` | `skills/requirements-brief/references/idea-shaping-patterns.md` | adopted |
| `writing-plans` | `implementation-plan` | `skills/implementation-plan/SKILL.md`、`skills/implementation-plan/references/plan-review-checklist.md` | adopted |
| `test-driven-development` | `test-strategy`、`bug-reproduction` | `skills/test-strategy/references/testing-anti-patterns.md` | adopted |
| `systematic-debugging` | `bug-reproduction` | `skills/bug-reproduction/references/debugging-patterns.md` | adopted |
| `verification-before-completion` | `task-execution-lifecycle`、完成证据 reference、全局验证语义 | `skills/task-execution-lifecycle/references/completion-evidence-patterns.md` | adopted |
| `requesting-code-review` | `code-review`、`task-execution-lifecycle` | `skills/code-review/references/review-checklists.md` | adapted |
| `receiving-code-review` | `code-review` | `skills/code-review/SKILL.md` | adopted |
| `dispatching-parallel-agents` | `task-execution-lifecycle` | `skills/task-execution-lifecycle/references/parallel-agent-coordination.md` | adapted |
| `subagent-driven-development` | `task-execution-lifecycle`、`code-review` | `skills/task-execution-lifecycle/references/parallel-agent-coordination.md` | adapted |
| `using-git-worktrees` | `task-execution-lifecycle`、Codex workspace rules | `skills/task-execution-lifecycle/references/execution-control-patterns.md` | adapted |
| `finishing-a-development-branch` | `task-execution-lifecycle`、Git rules | `skills/task-execution-lifecycle/references/execution-control-patterns.md` | adapted |
| `executing-plans` | `task-execution-lifecycle` | `skills/task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| `writing-skills` | `methodology-harvest`、`project-content-curation`、`design-principle-library` | `skills/methodology-harvest/references/skill-authoring-patterns.md` | adopted |

## Resource Coverage

| Resource class | Source examples | Hub treatment | Status |
| --- | --- | --- | --- |
| Skill entrypoints | `skills/*/SKILL.md` | Mechanisms mapped in `superpowers-pattern-map.md`; long behavior moved into domain references | adopted |
| Tool mapping references | `using-superpowers/references/codex-tools.md`、`copilot-tools.md`、`gemini-tools.md` | 抽成能力语义迁移规则，不复制具体平台配置 | adapted |
| Agent manifests | `skills/*/agents/openai.yaml` | 不复制 manifest；可发现性由 Hub frontmatter、`skills/README.md` 和工具适配文件承载 | no-copy |
| Review prompts and reception workflow | `requesting-code-review/code-reviewer.md`、`receiving-code-review/SKILL.md`、v6 `subagent-driven-development/task-reviewer-prompt.md` | Prompt原文不复制；抽成规格符合性、代码质量、`can't verify from diff`、Implementation Report Skepticism Gate、Feedback Source Priority Gate、证据要求、反馈核验、技术性 pushback、错误 pushback 修正、Plan issue 出口和 PR thread 处理 checklist | adapted |
| Plan/spec reviewer prompts | `brainstorming/spec-document-reviewer-prompt.md`、`writing-plans/plan-document-reviewer-prompt.md` | 抽成计划自检、no-placeholder、文件职责和验证命令要求 | adapted |
| Debugging references | `root-cause-tracing.md`、`condition-based-waiting*.md`、`defense-in-depth.md` | 抽成根因追踪、Condition-Based Waiting Gate、污染源定位、no-root-cause 证据门和多层防御模式表 | adopted |
| Debugging scripts | `systematic-debugging/find-polluter.sh` | 不复制脚本；保留污染源追踪方法，按项目语言重写 | adapted |
| TDD references | `test-driven-development/testing-anti-patterns.md` | 抽成测试反模式、TDD Exception Gate、testability feedback 和 red/green 证据要求 | adopted |
| Visual companion | `brainstorming/visual-companion.md`、`brainstorming/scripts/*` | 不复制运行时脚本；保留“看比读更清楚才用视觉辅助”的判断 | adapted |
| Skill authoring references | `testing-skills-with-subagents.md`、`persuasion-principles.md`、`anthropic-best-practices.md` | 抽成 Skill 触发描述、Trigger Discovery Coverage、渐进加载、reference 拓扑、压力测试证据链、脚本/工具假设、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate 和自由度控制 | adopted |
| Pressure tests and creation logs | `systematic-debugging/test-*.md`、`CREATION-LOG.md`、`writing-skills/examples/CLAUDE_MD_TESTING.md` | 抽成压力类型、反跑偏机制和 Skill authoring checklist；长场景原文不复制 | adapted |
| Scratch workspace files | `subagent-driven-development` 的 `task-brief`、`review-package`、implementer report、progress ledger | 采用“文件化交接可以保留，但 scratch 文件必须落在工作树内的自忽略目录，不写进 `.git/`”的稳定约束；不复制 helper 脚本和目录实现 | adapted |
| Graph / visualization tooling | `writing-skills/graphviz-conventions.dot`、`render-graphs.js` | 不复制 Graphviz 脚本；Hub 默认使用 Mermaid、Markdown 或当前工具视觉能力 | adapted |
| Plugin metadata | `.codex-plugin/plugin.json` | 只用于版本与来源证据，不成为 Hub 运行入口 | no-copy |
| Release notes | `RELEASE-NOTES.md` | 只抽取稳定机制变化和版本证据，不复制发布文案 | adapted |
| Brand assets | `assets/app-icon.png`、`assets/superpowers-small.svg` | 品牌资产不进入 Hub | no-copy |
| Install/community/license docs | README 安装段、community/license 信息 | 不复制；只记录 repository/version/source scope | no-copy |

## Mechanism Detail Coverage

这些细节来自后续逐项吸收，不一定能从资源类别表直接看出；刷新版本时要逐项复核是否仍成立。

| Superpowers mechanism detail | Hub evidence | Status |
| --- | --- | --- |
| Skill loading uses current instructions, not memory or description-only execution | `skill-trigger-discipline.md`、`skills/README.md` | adopted |
| Skill false positives can exit after reading current instructions, instead of forcing an inapplicable workflow | `skill-trigger-discipline.md`、`skills/README.md`、`design-principle-library/SKILL.md` | adopted |
| Delegated subagents skip global skill discovery and execute only their brief scope, while still honoring task-specific gates | `skill-trigger-discipline.md`、`parallel-agent-coordination.md`、`design-principle-library/SKILL.md` | adopted |
| Triggered skills distinguish discipline/gate, guidance/pattern and reference/lookup modes; user WHAT does not automatically waive skill HOW | `skill-trigger-discipline.md`、`skills/README.md`、`design-principle-library/SKILL.md` | adopted |
| 外部 tool names / agent manifests 按能力迁移，不伪造 Hub 入口 | `external-skill-portability.md`、`agents/codex.md`、`agents/cursor.md` | adopted |
| Completion claims require fresh evidence and calibrated status words | `AGENTS.md` Verification Semantics、`completion-evidence-patterns.md` | adopted |
| Verification output must match the target: command scope, counts, skipped/no-op/cache state and business semantics are inspected before claiming pass | `completion-evidence-patterns.md`、`task-execution-lifecycle/SKILL.md`、`design-principle-library/SKILL.md` | adopted |
| Requirements-met claims require a requirement-by-requirement checklist with direct evidence, not just tests passing or implementation summaries | `completion-evidence-patterns.md`、`task-execution-lifecycle/SKILL.md`、`design-principle-library/SKILL.md` | adopted |
| Positive or satisfied language implies status and must pass the same evidence gate before use | `task-execution-lifecycle/SKILL.md`、`completion-evidence-patterns.md` | adopted |
| Worktree detection includes submodule guard | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Default/protected branch work requires explicit in-place intent or isolated branch/workspace for non-trivial implementation | `task-execution-lifecycle/SKILL.md`、`task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Isolated workspace setup requires clean baseline or explicit baseline failure record | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Detached/external workspace does not default to local merge option | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Branch finish confirms base branch before integration options | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Failed or missing verification stops branch finish before commit/PR/merge/discard options | `task-execution-lifecycle/SKILL.md`、`task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Finish handoff presents finite environment-specific options after verification and diff boundary checks, not open-ended next-step questions | `task-execution-lifecycle/SKILL.md`、`task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Worktree cleanup requires provenance and happens only after merge/discard confirmation | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Review requests need self-contained review brief, not session history | `code-review/references/review-checklists.md` | adopted |
| Stuck work should trigger a fresh perspective review with current stuck point, falsified hypotheses, diff and evidence gaps before more patching | `code-review/SKILL.md`、`code-review/references/review-checklists.md`、`task-execution-lifecycle/SKILL.md`、`design-principle-library/SKILL.md` | adopted |
| Implementation reports, PR descriptions and subagent statuses are claims that require independent code/diff/test evidence | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| Review can flag plan/spec defects as `Plan issue` instead of misclassifying them as implementation quality issues | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| Review severity controls next action: Critical/Important/Minor gates | `code-review/references/review-checklists.md` | adopted |
| Review output requires an evidence-matched verdict, not vague readiness language | `code-review/SKILL.md`、`code-review/references/review-checklists.md`、`design-principle-library/SKILL.md` | adopted |
| Multi-item feedback requires intake and clarification before implementing dependent or ambiguous subsets | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| External reviewer, subagent and tool feedback cannot silently override user-confirmed scope, architecture or compatibility decisions | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| Review pushback must use technical evidence, and wrong pushback should be corrected factually before fixing | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| Inline PR feedback should be resolved in its original comment/thread with evidence | `code-review/SKILL.md`、`code-review/references/review-checklists.md` | adopted |
| Refactor without behavior change requires baseline/characterization proof | `test-strategy/SKILL.md`、`test-strategy/references/testing-anti-patterns.md` | adopted |
| Code written before tests is exploration, not verified implementation, until isolated and red/green or equivalent proof is shown | `test-strategy/SKILL.md`、`test-strategy/references/testing-anti-patterns.md` | adopted |
| RED failure must prove the target behavior gap, not syntax/import/fixture/mock/command-scope failure or 0-test no-op | `test-strategy/SKILL.md`、`test-strategy/references/testing-anti-patterns.md`、`design-principle-library/SKILL.md` | adopted |
| TDD exceptions must be classified and backed by equivalent evidence; urgency, simplicity, manual testing or tests-later are not valid exceptions | `test-strategy/SKILL.md`、`test-strategy/references/testing-anti-patterns.md`、`design-principle-library/SKILL.md` | adopted |
| Hard-to-write tests are design feedback; simplify API, split behavior or adjust dependency boundaries before adding mock complexity | `test-strategy/SKILL.md`、`test-strategy/references/testing-anti-patterns.md` | adopted |
| Flaky/timeout/hang fixes wait for real observable conditions with fresh reads, timeouts and polling, not arbitrary sleeps | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md`、`test-strategy/references/testing-anti-patterns.md` | adopted |
| Similar working implementations must be read completely and compared difference-by-difference before adapting a fix | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md`、`design-principle-library/SKILL.md` | adopted |
| Debugging hypotheses are tracked as observation, hypothesis, test, output, result and next action; falsified hypotheses cannot support later fixes | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md`、`design-principle-library/SKILL.md` | adopted |
| Incident mitigation is separate from root cause fix | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md` | adopted |
| No-root-cause outcomes require investigated boundaries, falsified hypotheses, unresolved status and next observation triggers | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md` | adopted |
| Three failed local fixes require architecture/premise review before another patch | `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md` | adopted |
| Visual companion is per-question and stale visuals must not imply current truth | `requirements-brief/references/idea-shaping-patterns.md` | adapted |
| Large ideas with independent subsystems require decomposition before detailed questions | `requirements-brief/SKILL.md`、`requirements-brief/references/idea-shaping-patterns.md` | adopted |
| Complex designs require sectioned approval across architecture, objects/components, data/state, errors/permissions and validation | `requirements-brief/SKILL.md`、`requirements-brief/references/idea-shaping-patterns.md` | adopted |
| Brief/spec readiness review checks completeness, consistency, clarity, scope and YAGNI before implementation planning, while separating blockers from advisory polish | `requirements-brief/SKILL.md`、`requirements-brief/references/idea-shaping-patterns.md`、`design-principle-library/SKILL.md` | adopted |
| New or reshaped units need clear purpose, interface, dependencies and testability before planning | `requirements-brief/SKILL.md`、`requirements-brief/references/idea-shaping-patterns.md`、`implementation-plan/references/plan-review-checklist.md` | adopted |
| Plans must be executable by a fresh worker without hidden conversation context | `implementation-plan/SKILL.md`、`execution-control-patterns.md` | adopted |
| Plans for delegated work need explicit global constraints and per-task interfaces, so downstream workers and reviewers receive stable boundaries | `implementation-plan/references/plan-review-checklist.md`、`execution-control-patterns.md` | adopted |
| Plan review checks completeness, spec alignment, task decomposition and buildability, while separating blockers from advisory recommendations | `implementation-plan/references/plan-review-checklist.md`、`execution-control-patterns.md` | adopted |
| Plan execution keeps a progress ledger and resumes only after reconciling current diff, evidence and task state | `task-execution-lifecycle/references/execution-control-patterns.md` | adopted |
| Plan execution rejects hidden context and unconfirmed git/destructive actions | `execution-control-patterns.md` | adopted |
| Parallel dispatch requires independent domains and self-contained task briefs | `parallel-agent-coordination.md` | adopted |
| Agent/model/tool capability should match task complexity; blocked retries must change capability, context or scope | `task-execution-lifecycle/SKILL.md`、`parallel-agent-coordination.md` | adopted |
| Subagent questions are treated as context/scope/plan feedback and require brief updates, task split or minimal user clarification before continuing | `task-execution-lifecycle/SKILL.md`、`parallel-agent-coordination.md`、`design-principle-library/SKILL.md` | adopted |
| Subagent statuses require distinct handling: `DONE_WITH_CONCERNS` / `NEEDS_CONTEXT` / `BLOCKED` | `parallel-agent-coordination.md` | adopted |
| Spec compliance review must pass before code quality review | `parallel-agent-coordination.md`、`code-review/references/review-checklists.md` | adopted |
| v6 task review can be one reviewer returning both spec-compliance and quality verdicts; spec verdict still gates quality action | `parallel-agent-coordination.md`、`code-review/references/review-checklists.md` | adopted |
| Review may return `can't verify from diff`; controller must gather runtime/source evidence instead of treating it as pass or fail | `code-review/references/review-checklists.md`、`completion-evidence-patterns.md` | adopted |
| Task text and review diff should be packaged as files or explicit context instead of relying on pasted session history | `parallel-agent-coordination.md`、`code-review/references/review-checklists.md` | adapted |
| File-based task/review scratch artifacts live in a self-ignoring worktree-local directory, not under protected `.git/`; if cleanup can delete them, the recovery path must be explicit | `external-skill-portability.md` | adapted |
| Every delegated task/review should state intended capability/model/tool level when the platform exposes that choice; otherwise record equivalent capability choice | `parallel-agent-coordination.md` | adapted |
| Multi-task/subagent work requires a final integration review after per-task reviews | `task-execution-lifecycle/SKILL.md`、`parallel-agent-coordination.md`、`code-review/references/review-checklists.md` | adopted |
| Skill authoring uses trigger description, progressive loading and pressure tests | `skill-authoring-patterns.md`、`skill-pressure-test-template.md` | adopted |
| Skill guidance form should match the failure mode; cheap micro-tests can check wording before changing durable rules | `skill-authoring-patterns.md`、`skill-pressure-test-template.md` | adapted |
| Skill discovery covers real user phrases, symptoms, error/status terms, object types, tools/files and synonyms without over-triggering | `skill-authoring-patterns.md`、`skills/README.md` | adopted |
| Skill pressure tests distinguish RED baseline, GREEN re-test, REFACTOR loophole closing and META clarity checks | `skill-pressure-test-template.md`、`skill-authoring-patterns.md` | adopted |
| Skill references stay directly reachable from `SKILL.md`; long references need a top-level map and stale/time-sensitive content needs refresh conditions | `skill-authoring-patterns.md` | adopted |
| Skill scripts and tool references declare runtime, permission, input/output and verification assumptions instead of assuming external tools exist | `skill-authoring-patterns.md`、`external-skill-portability.md` | adopted |
| Frequently loaded surfaces keep only trigger, hard constraints, stop rules, output shape and pointers; long rationale/catalogs/examples move to references/reports/script help | `skill-authoring-patterns.md`、`skills/README.md`、`design-principle-library/SKILL.md` | adopted |
| Public Hub skills and rules must hold for Codex/Cursor; tool-specific UI, MCP, browser, subagent or host-thread semantics land in adapters, fallback or watch | `skill-authoring-patterns.md`、`external-skill-portability.md`、`skills/README.md`、`design-principle-library/SKILL.md` | adopted |
| Skill changes require per-skill deployment gate before moving to another skill | `skill-authoring-patterns.md`、`skills/README.md` | adopted |

## Refresh Checklist

刷新 Superpowers 或同类外部方法论时，至少重新执行：

1. 读取 manifest/package/release 信息，记录版本、来源路径或 URL。
2. 列出 `skills/*/SKILL.md`，与本文件 `Skill Coverage` 对比新增、删除、改名。
3. 搜索 prompt templates、scripts、assets、references，更新 `Resource Coverage`。
4. 对照 `Mechanism Detail Coverage`，逐项确认细节机制是否仍被 Hub 规则、Skill 或 reference 承载。
5. 对平台工具映射、agent manifest、视觉脚本和压力测试，按 `external-skill-portability.md` 判断 `adapted`、`no-copy` 或 `watch`。
6. 若 skill 或资源语义变化，先更新 `superpowers-pattern-map.md`，再更新落位 Skill/reference/report。
7. 运行引用搜索，确认入口能追到 mapping、coverage audit、portability reference、report 和具体 Hub skill。
8. 运行 diff 与格式检查，确认没有误复制外部品牌资产、安装说明、原 prompt 或工具绑定脚本。

## Current Verification

本轮已执行或应在收尾重复执行的验证：

```sh
find /Users/shatang/.codex/plugins/cache/openai-curated/superpowers -path '*/skills/*/SKILL.md' -print | sort
rg -n "6\\.0\\.3|5\\.1\\.3|7fd3161c|scratch artifact|self-ignoring|Global Constraints|per-task Interfaces|can't verify from diff|task-reviewer|review package|Capability / tool level|Form To Failure|Micro-Test Wording|superpowers-coverage-audit|superpowers-pattern-map" skills reports
git diff --check
rg -n '[ \t]+$' AGENTS.md README.md agents/codex.md skills/README.md skills/code-review/references/review-checklists.md skills/implementation-plan/references/plan-review-checklist.md skills/methodology-harvest/references/external-skill-portability.md skills/methodology-harvest/references/skill-authoring-patterns.md skills/methodology-harvest/references/skill-trigger-discipline.md skills/methodology-harvest/references/superpowers-coverage-audit.md skills/methodology-harvest/references/superpowers-pattern-map.md reports/personal/agent/analysis/2026-06-25-关于AgentHub-superpowersWeeklyRefresh.md skills/self-evolution-engine
git diff --cached --name-status
git status --short --untracked-files=all reports/company/projects
```
