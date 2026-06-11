# Skill Trigger Discipline

日期：2026-06-11
来源：吸收 Superpowers `using-superpowers` 的技能触发纪律后整理。
用途：作为 Agent Hub 使用或设计 Skill 触发规则时的按需参考；用于防止无流程行动、过度加载、靠记忆执行旧技能或只读 description 跳过正文。

## Core Rule

Skill 是当前任务的方法入口，不是背景装饰。触发时读取当前 `SKILL.md`；没有触发时不要为了仪式感加载无关技能。

## Hub Adaptation

Hub 采用 Superpowers 的“先检查适用技能”机制，但不采用外部“1% 可能就强制加载所有相关技能”的高成本门禁。

Hub 规则：

- 按 `AGENTS.md` Loading Order 和当前任务语义判断技能。
- 用户明确点名某个 Skill 时必须读取它。
- 任务明显匹配某个 Skill 的 description 或 trigger 时读取它。
- 多个 Skill 同时适用时，先读决定工作方式的 process Skill，再读领域 Skill。
- 选择最小必要集合；不要为普通小动作加载整套目录。

## Trigger Checklist

开始任务、切换任务、收到用户纠偏或准备收尾时，快速问：

1. 用户是否点名了 Skill、插件、Hub、rule update、paper record 或项目内容整理？
2. 任务类型是否明显匹配某个 Hub skill：bug、review、plan、test、requirements、interface contract、research、execution lifecycle？
3. 是否涉及 AgentOS / Agent infra / Product UI / AI 技术研究，需要 agent team routing？
4. 是否要吸收外部方法论、插件、技能库或优秀实践，需要 methodology harvest？
5. 是否是高风险收尾，需要 completion evidence、diff review 或 test strategy？

任一答案为是时，读取对应当前文件；不要靠上次记忆。

## Minimal Skill Set

选择技能时用这个顺序：

| 场景 | 先读 | 再读 |
| --- | --- | --- |
| 模糊产品/功能想法 | `requirements-brief` | `implementation-plan` |
| 已有明确计划要执行 | `task-execution-lifecycle` | `implementation-plan` 或相关领域 Skill |
| bug / test failure | `bug-reproduction` | `test-strategy` |
| diff / PR / feedback | `code-review` | `task-execution-lifecycle` |
| API / tool surface | `interface-contract-audit` | `agent-tool-design` |
| 外部方法论吸收 | `methodology-harvest` | 目标领域 Skill |
| 规则/内容整理 | `project-content-curation` 或 `refactor-hub` | `design-principle-library` |

如果一个 Skill 已经覆盖本轮工作方式，不要再加载近义 Skill，除非出现新的任务阶段。

## Skill False Positive Exit Gate

有些 Skill 会因为关键词、相邻概念或用户表达宽泛而被谨慎读取；读取后发现它并不适用时，不要为了“已经读了”就强行套流程。

退出条件：

- Skill 的 Trigger 或正文前提与当前任务不匹配。
- 任务只是相邻概念，例如用户说“review”但实际要解释术语，不是审查 diff。
- 更具体的 Skill 已覆盖工作方式，当前 Skill 只会重复或扩大流程。
- 用户明确限定了输出形态，例如“只给一句命令”，而该 Skill 的完整流程会越界。

退出方式：

1. 记录一句短判断：`已检查 <skill>，当前不适用，因为 <原因>`。
2. 不继续读取该 Skill 的长 references。
3. 回到最小必要 Skill 集合或直接执行当前任务。
4. 如果后续任务阶段变化，再重新判断是否触发。

False positive 不是违规；读了 current `SKILL.md` 后仍把不适用流程强行套上，才是跑偏。

## Delegated Task Boundary

如果当前 agent 是被派发执行明确子任务，而不是主 Agent 接收用户原始请求：

- 不重新运行完整 Hub loading chain、外部方法论吸收或全仓规划。
- 只读取 brief scope 内必要的 Skill 和 reference。
- 缺关键上下文时返回 `NEEDS_CONTEXT`；scope 外问题记录为 concern，不直接扩写任务。
- 仍遵守被触发 Skill 的 compliance mode；委派身份不能豁免 stop rule、验证证据或用户硬边界。

主 Agent 负责全局技能路由、拆分、合并和最终验证。

## Skill Compliance Mode

读取 Skill 后，先判断它要求的是强门禁、弹性模式还是参考资料：

| Mode | Typical skill | How to follow |
| --- | --- | --- |
| Discipline / gate | bug reproduction、test strategy、interface contract、diff review、verification、paper record | 按 stop rule、证据要求和顺序执行；除非用户明确要求跳过，否则不能用“任务很小”“时间紧”弱化 |
| Guidance / pattern | requirements brief、implementation plan、methodology harvest、design principle | 按原则和检查点适配当前上下文；可以缩小流程，但要保留保护的不变量 |
| Reference / lookup | 长 checklist、模式表、portability map、历史报告 | 只读取相关小节；不要把整份 reference 变成强流程 |

用户指令定义目标、范围和优先级，不自动豁免适用 Skill 的执行方式。用户说“加 X”“修 Y”“快点”时，默认选择最快的合规路径；只有用户明确说“不要做 TDD/不要跑测试/只给答案/跳过某流程”等，才按用户指令调整，并在回复中说明被跳过的门禁和风险。

## Anti-Rationalization

常见跑偏：

- “我记得这个 Skill 内容”：Skill 会变，读当前文件。
- “description 已经够了”：description 只是触发条件，不能替代正文。
- “先看代码再说”：代码探索方式可能由 Skill 决定，先读方法 Skill。
- “这个太简单”：简单任务也可能触发轻量规则，但不需要重流程。
- “多读几个 Skill 更安全”：过度加载会稀释上下文，优先最小集合。
- “用户只说做 X，没说要按流程”：用户给的是 WHAT；除非明确豁免，HOW 仍按适用 Skill 执行。
- “我读过 Skill，可以灵活处理”：先判断 mode；强门禁不能被改写成普通建议。

## Output Etiquette

对用户说明技能使用时保持短：

- “我会按 `methodology-harvest` 做来源核对和 Hub 落位。”
- “这个是 bug，我先按 `bug-reproduction` 复现和追根因。”

不要把 skill loading 变成长篇仪式；关键是实际遵守流程。
