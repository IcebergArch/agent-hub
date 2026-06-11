# Skill Authoring Patterns

日期：2026-06-11
来源：吸收 Superpowers `writing-skills`、`testing-skills-with-subagents.md`、`persuasion-principles.md` 和 `anthropic-best-practices.md` 的 Skill 写作机制后整理。
用途：作为 Agent Hub 新增或显著修改 Skill 前的按需参考；用于检查触发描述、渐进加载、压力测试和抗跑偏规则。

## Core Rule

Skill 是可重复触发的工作方法，不是一次性项目复盘。写 Skill 时先定义它保护的行为不变量，再决定触发、正文、references、脚本和测试方式。

## Creation Gate

适合写成 Skill：

- 后续会被重复触发。
- 有明确触发条件、步骤、输出形态或检查点。
- 靠判断和流程约束才能做好，不能完全用脚本或静态检查替代。
- 适用于跨项目或跨任务，不包含具体业务方案、私密信息或一次性路径。

不适合写成 Skill：

- 一次性结论、过程日志、临时计划。
- 单个项目的业务决策或公司私有上下文。
- 标准常识、无需额外指导的机械操作。
- 能更好放在 report、rule、tool adapter、script 或测试里的内容。

## Description Discipline

`description` 是触发器，不是摘要。它应该回答“什么时候读这个 Skill”，不要把工作流写进去。

检查：

- 是否只写触发条件、症状、场景和关键词。
- 是否避免了“会先 A 再 B 再 C”的流程摘要。
- 是否包含用户可能说出的近义词、错误信号、对象类型或文件类型。
- 是否足够短，避免 Agent 只读描述就按描述执行。

坏信号：

- description 总结完整步骤。
- description 太泛，例如“帮助处理文档”“用于开发”。
- description 写成第一人称或面向用户的营销句。

## Trigger Discovery Coverage

Skill 能不能生效，第一步取决于 Agent 能不能在正确任务里发现它。写 description 和 Trigger 时，要覆盖用户可能说出的真实信号，而不是只写作者心里的抽象分类。

覆盖维度：

| 维度 | 示例 |
| --- | --- |
| 用户表达 | “报错了”“review 一下”“先给方案”“paper record” |
| 症状词 | flaky、hang、timeout、权限不对、mock 误报、范围膨胀 |
| 错误/状态词 | `ENOTEMPTY`、`unauthorized`、`DONE_WITH_CONCERNS`、`root cause unresolved` |
| 对象类型 | API、route、tool surface、PR、spreadsheet、PPT、UI workflow |
| 文件/命令/工具 | `.docx`、`go test`、`gh pr`、`openapi.yaml`、`SKILL.md` |
| 近义词 | debug/定位/root cause，计划/方案/checklist，验证/smoke/回归 |

检查：

- description 是否太抽象，导致 Agent 只在“完美命名”的请求里才会触发。
- 是否把具体工具词写进了非工具专属 Skill，导致触发范围过窄。
- 是否遗漏用户常用语言、中文/英文混用、缩写或反向信号，例如“不要实现，先看方案”。
- 是否有误触发风险：关键词太宽时，在 Trigger 或 “不用在这些场景” 中加排除条件。

不要把关键词堆成 catalog。关键词必须服务触发判断；长列表、案例和压力场景放到 reference 或 report。

## Body Structure

`SKILL.md` 保持短、索引化、可扫描：

1. Trigger：什么时候用，什么时候不用。
2. Workflow：短步骤，强调决策点和 stop rule。
3. References：长表格、模板、反模式、脚本说明放入 `references/`。
4. Output：默认交付形态。
5. Quality Bar：不能违反的最低标准。

正文不要复述其它 Skill 的完整流程；用相对路径或技能名指向即可。

## Progressive Loading

内容放置规则：

| 内容 | 放置位置 |
| --- | --- |
| 触发条件、核心流程、输出格式 | `SKILL.md` |
| 长模式表、反模式、模板、检查清单 | `references/*.md` |
| 可运行工具、生成器、验证脚本 | `scripts/` |
| 长期结论、来源盘点、取舍理由 | `reports/` |
| 跨工具硬规则 | `AGENTS.md` 或 `agents/<tool>.md` |

频繁触发的 Skill 更要短；不常触发但复杂的 Skill 可以用 references 承载细节。

## Loaded Surface Token Budget Gate

高频加载面包括 `AGENTS.md`、`README.md`、`agents/<tool>.md`、Skill frontmatter、频繁触发的 `SKILL.md` 和常用索引。修改这些位置前先问：

| Question | Decision |
| --- | --- |
| Agent 是否在触发当下必须知道这条规则？ | 是：保留短规则；否：移到 reference/report |
| 这段内容是触发、stop rule、输出形态还是长解释？ | 前三者可留入口；长解释下沉 |
| 是否列了命令全量 flags、长样例、历史论证或完整 catalog？ | 改成指向 `--help`、reference 或 report |
| 是否会被几乎每个任务读取？ | 只保留索引和硬约束 |
| 是否重复了另一个 Skill 的流程？ | 删除重复，保留交叉引用 |

默认预算：

- frontmatter description：只写触发，避免流程摘要。
- frequent `SKILL.md`：优先短正文，复杂表格放 reference。
- `AGENTS.md` / adapter：只写跨工具硬规则和触发路由。
- reports：承载来源、取舍、历史证据和完整论证。
- scripts：把可自解释的 flags 放在 `--help` 或脚本注释，Skill 只说明何时用和如何验证。

新增长内容前，如果不能说明“触发时必须读”，就不要放在 loaded surface。

## Cross-Tool Compatibility Gate

Hub 的公共 Skill/Rule 默认服务 Codex 和 Cursor。新增或显著修改公共资产前，先检查它是否只对当前工具成立。

| Question | Decision |
| --- | --- |
| 规则是否依赖某个宿主工具名、MCP、浏览器、subagent、线程或 UI 指令？ | 放到 `agents/<tool>.md`，或在公共 Skill 写成能力语义和 fallback |
| Codex 和 Cursor 是否都能读取同一路径、触发词和 reference？ | 是：保留公共规则；否：补适配入口或降级为工具专属 |
| 规则是否要求某平台的 manifest、prompt、tool call 或自动调度语义？ | 抽象成 checklist/brief；真实执行入口交给 adapter |
| 是否只有某模型/agent 能稳定遵守高自由度规则？ | 降低自由度、增加 stop rule，或写明需要更强 review/验证 |
| 是否会让另一个工具误以为自己有不存在的能力？ | 明确 `unsupported`、`watch` 或替代验证方式 |

公共 Skill 只写跨工具不变量、输入输出和判断标准；工具差异写入 `agents/codex.md`、`agents/cursor.md` 或相应工具规则。无法跨工具验证的能力，不要写成 Hub 已具备的默认 workflow。

## Reference Topology

Skill 的按需资料要让 Agent 容易一次读对，避免藏在多层跳转里。

- `SKILL.md` 应直接链接所有重要 `references/`、`scripts/` 和报告入口；不要让 reference 再指向另一个必须阅读的 reference。
- 超过约 100 行的 reference 应在顶部写短目录或适用场景，方便 Agent 先看全局再跳到具体小节。
- 同一概念用同一术语；不要在一个 Skill 内混用 route/path/API endpoint、resource/tool/capability 等近义词。
- 避免把会过期的时间条件写成长期规则；需要保留历史时，放入 `Old patterns`、deprecated 或 dated report，并标明复核触发。
- 外部链接、缓存路径和第三方工具版本只作为来源证据；运行入口必须指向 Hub 当前可用的本地文件、脚本或工具适配。

坏信号：

- `SKILL.md` 只说“详见 advanced.md”，而 `advanced.md` 又继续要求读 `details.md` 才能执行。
- reference 很长但没有目录，Agent 只能靠部分读取猜测剩余内容。
- 规则使用“当前”“最近”“新版”但没有日期、来源或刷新条件。

## Freedom Level

按风险设置指令自由度：

| 场景 | 自由度 | 写法 |
| --- | --- | --- |
| 多种方案都可行 | 高 | 原则、启发式、取舍表 |
| 有推荐路径但需适配 | 中 | 模板、伪代码、可替换步骤 |
| 脆弱或高风险流程 | 低 | 明确 gate、禁止项、精确命令或确认要求 |

不要对低风险创意工作使用重门禁；也不要对删除、验证、权限、安全、git 清理这类高风险动作只写温和建议。

## Skill TDD

高约束 Skill/Rule 需要压力测试，而不是只检查文案是否顺眼。

TDD 映射：

| Code TDD | Skill TDD |
| --- | --- |
| failing test | 没有 Skill 时 Agent 会跑偏的压力场景 |
| implementation | `SKILL.md` 和 references |
| passing test | 有 Skill 时 Agent 仍按规则行动 |
| refactor | 补 explicit counter、red flag、stop rule、output gate |

使用 `skill-pressure-test-template.md` 设计场景。完整 Skill TDD 要能拿出 RED/GREEN/REFACTOR 证据；只有场景草稿、没有 baseline failure 和 re-test，不能宣称“已验证会改变 Agent 行为”。优先测试这些规则：

- 有执行成本，例如 TDD、验证、设计门、review。
- Agent 容易用“太简单”“太急”“先做后补”“只是这次”绕过。
- 用户或外部 reviewer 的压力可能诱导跑偏。

## Anti-Rationalization

当压力测试发现跑偏，把真实借口写进规则：

| 观察到的借口 | 应补内容 |
| --- | --- |
| “这次很简单” | 明确简单场景的最低门槛 |
| “我已经手动验证了” | 区分手动观察和可复现证据 |
| “先提交，之后补” | 写 stop rule 或收尾 gate |
| “reviewer 说了就改” | 写技术核验和用户约束优先级 |
| “照精神不照字面” | 写保护的不变量和不可接受替代 |

反跑偏规则要具体；“不要偷懒”不如“不能把错误暴露写成已打通”。

## Skill Change Deployment Gate

新增或显著修改 Skill 后，先完成当前 Skill 的发布验证，再继续改下一个 Skill。不要为了效率批量写多个 Skill 后才回头检查；这会让触发词、索引、引用和压力场景的错误互相遮蔽。

发布验证按顺序检查：

1. **Identity**：目录名、frontmatter `name`、`description` 和 `skills/README.md` 登记一致；description 仍只写触发条件。
2. **Discovery**：入口规则、工具适配文件或相关 Skill 是否需要新增触发；检查用户表达、症状词、错误/状态词、对象类型、文件/工具和近义词覆盖；用 `rg` 搜索新旧触发词，确认能从入口追到 `SKILL.md`。
3. **References**：正文中的 `references/`、`scripts/`、报告和相邻 Skill 链接都存在，且使用相对路径或 Hub 约定路径。
4. **Pressure / Fit**：高约束 Skill 需要压力场景或说明为什么暂不需要；如果未做压力测试，不能宣称“已验证改变 Agent 行为”。
5. **Content Placement**：长表格、模板、案例和来源放到 references/report，不塞进频繁触发的 `SKILL.md`。
6. **Loaded Surface Budget**：新增内容是否放在最小必要加载面；`AGENTS.md`、`README.md`、adapter 和 `SKILL.md` 是否只保留触发、硬约束、stop rule、输出形态和指针。
7. **Cross-Tool Fit**：公共 Skill/Rule 是否对 Codex 和 Cursor 都成立；工具专属能力是否已放入 adapter 或写成 fallback/watch。
8. **Reference Topology**：重要 reference 由 `SKILL.md` 直接链接；长 reference 有目录；没有二跳必读依赖或 stale source。
9. **Script / Tool Fit**：新增脚本要解决重复脆弱动作，声明运行时和权限假设，并产生可检查的中间输出；不能假设外部工具一定安装。
10. **Diff Boundary**：检查是否误改无关 Skill、入口或公司报告；`git diff --check` 和引用/空白搜索应通过。
11. **Handoff Evidence**：最终说明本次 Skill 变更、索引/触发更新、验证命令和未验证风险。

如果发布验证发现触发冲突、断链、索引遗漏或压力测试失败，先修当前 Skill；不要继续写新的 Skill 来掩盖问题。

## Scripts And Tool Assumptions

Skill 内部脚本只在它能降低重复错误或提供真实验证时引入。

- 脚本应封装脆弱操作、格式转换、渲染、校验或诊断循环，不用来替代应该由 Agent 判断的设计决策。
- `SKILL.md` 或脚本说明要写明依赖、输入、输出和失败时如何处理；不要假设 `node`、`python`、浏览器、Graphviz、MCP tool 或网络一定可用。
- 长流程脚本应产生可检查的中间文件、日志或报告，让 Agent 能验证每一步，而不是只给最终“成功”文字。
- 从外部 skill 导入脚本前，先按 `external-skill-portability.md` 判断它是方法论、平台 runtime，还是可复用工具；默认不复制平台绑定脚本。

## Persuasion Boundaries

强语气只用于保护用户利益和高风险约束。

- Discipline skills：可用 `must`、`never`、明确 stop rule 和 required evidence。
- Guidance skills：用推荐、取舍和判断标准，避免过度命令。
- Reference skills：保持清晰即可，不需要压力语言。

避免用讨好、内疚、虚假紧急或情绪操控来换取遵守。规则强度要和风险匹配。

## Authoring Checklist

发布或交付前检查：

- 触发条件明确，description 没有流程摘要。
- description / Trigger 覆盖真实用户表达、症状词、错误/状态词、对象类型、文件/工具和近义词，同时没有明显误触发。
- 正文足够短；长内容放到 references。
- 已检查高频加载面上下文预算：长解释、catalog、历史论证和命令全量说明已下沉到 reference、report 或 script help。
- 已检查跨工具兼容：公共规则对 Codex/Cursor 都成立；工具专属能力已移到 adapter、fallback 或 watch。
- 新增目录和文件符合 Hub 命名。
- 规则不包含项目私密信息或一次性业务决策。
- 已更新 `skills/README.md`、入口规则或相关 report。
- 已搜索旧/新触发词，确认可发现。
- 重要 reference 由 `SKILL.md` 直接链接；长 reference 有目录或适用场景。
- 高约束规则已考虑压力测试；如果宣称已验证 Agent 行为变化，必须有 RED baseline、GREEN re-test 和 rationalization 记录。
- 新增脚本或工具说明已声明依赖、权限、输入输出和验证信号。
- 已按 `Skill Change Deployment Gate` 完成单个 Skill 的索引、引用、触发和 diff 边界验证。
- 已跑 `git diff --check` 和必要的引用/空白检查。
