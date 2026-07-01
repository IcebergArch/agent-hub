# Skill Authoring Patterns

日期：2026-06-11
来源：吸收外部 agent 方法论中的 Skill 写作、压力测试、说服边界和指令最佳实践后整理。
用途：作为新增或显著修改 Skill 前的按需参考；检查触发、渐进加载、压力测试、跨工具适配和抗跑偏。

## Core Rule

Skill 是可重复触发的工作方法，不是一次性项目复盘，也不是场景条件树。先定义它保护的行为不变量和少数可组合 SOP，再决定触发、正文、references、脚本和验证。

## Composable SOP Gate

优先把 Skill 写成少数通用动作原语，例如定位、对照、实验、修正、验证、收口；具体场景通过参数、stop rule、reference 示例或领域 Skill 组合表达。

新增规则前先问：

- 它是一个新的方法原语，还是既有 SOP 在某个子场景的重复展开？
- 它能否作为输入参数、风险升级条件、输出约束或 stop rule 放入现有 Skill？
- 它是否只是为了列尽 “A 场景下 B 情况的 C 处理”？
- 模型能力、通用指令或更高阶 Skill 是否已经能覆盖它？

只有出现新的 owner、新的证据门、新的不可替代 workflow，才考虑新增或保留独立 Skill。

## Creation Gate

| 适合写成 Skill | 不适合写成 Skill |
| --- | --- |
| 会重复触发 | 一次性结论、过程日志、临时计划 |
| 有触发、步骤、输出或检查点 | 单个项目业务决策或私有上下文 |
| 需要判断和流程约束 | 标准常识或纯机械操作 |
| 跨项目/跨任务适用 | 更适合 report、adapter、script 或测试的内容 |

新增 Skill 前先找现有 owner；能合并、下沉、重命名或由更高阶 Skill 覆盖就不新增。

## Description Discipline

`description` 是触发器，不是摘要。

检查：

- 只写什么时候读：场景、症状、对象、关键词。
- 不写完整 workflow。
- 覆盖真实用户表达、中英混用、缩写和反向信号。
- 足够短，避免 Agent 只读 description 就执行。

坏信号：太泛、像营销句、包含大量步骤、只覆盖作者心里的抽象分类。

## Trigger Coverage

| 维度 | 示例 |
| --- | --- |
| 用户表达 | “报错了”“review 一下”“先给方案”“paper record” |
| 症状词 | flaky、hang、timeout、权限不对、mock 误报 |
| 状态词 | `unauthorized`、`DONE_WITH_CONCERNS`、`root cause unresolved` |
| 对象类型 | API、route、tool surface、PR、UI workflow |
| 文件/工具 | `.docx`、`go test`、`openapi.yaml`、`SKILL.md` |
| 近义词 | debug/root cause，计划/方案，验证/smoke/回归 |

不要堆关键词 catalog；关键词必须帮助触发判断。

## Body Shape

`SKILL.md` 保持短卡片：

1. Trigger：什么时候用，什么时候不用。
2. Workflow：短步骤、决策点、stop rule。
3. References：长表、模板、反模式、脚本说明。
4. Output：默认交付形态。
5. Quality Bar：最低标准。

正文不要复述其它 Skill 的完整流程；用相对路径或技能名指向即可。

## Progressive Loading

| 内容 | 位置 |
| --- | --- |
| 触发、核心流程、输出 | `SKILL.md` |
| 长模式表、模板、检查清单 | `references/*.md` |
| 可运行工具、校验脚本 | `scripts/` |
| 来源盘点、取舍、历史证据 | 文档工作区报告 |
| 跨工具硬规则 | `AGENTS.md` / `agents/<tool>.md` |

高频加载面只保留触发、硬约束、stop rule、输出形态和指针。不能说明“触发当下必须读”的内容，下沉。

## Cross-Tool Gate

公共 Skill/Rule 默认服务 Codex 和 Cursor：

- 依赖宿主工具、浏览器、MCP、subagent、线程或 UI 指令的内容，放 adapter 或写 fallback/watch。
- 外部 manifest、prompt、tool call 语义抽象为 checklist/brief，不伪造成 Hub 入口。
- 无法跨工具验证的能力，不写成默认 workflow。

## Reference Topology

- `SKILL.md` 直接链接重要 reference / script。
- 避免 reference 再要求读另一个必读 reference。
- 超过约 100 行的 reference 顶部写用途或目录。
- 同一概念用同一术语。
- 外部链接、缓存路径和版本只作为来源证据，不作为运行入口。

## Guidance Form

按失败类型选择指导形式：

| Failure | Better Form |
| --- | --- |
| 明知规则但绕开 | red flag、不可接受替代、stop rule |
| 不知道何时触发 | 更具体 description、Trigger、反向排除 |
| 输出形态不稳 | 模板、字段表、verdict 枚举 |
| 缺工具/权限/上下文 | fallback、unsupported/watch、最小问题 |
| 概念边界混淆 | owner/destination matrix、正反例 |

规则强度匹配风险：删除、权限、安全、git、验证用低自由度；创意和探索用取舍标准。

## Pressure Test

高约束 Skill/Rule 需要压力场景，而不是只检查文案顺眼。

| Code TDD | Skill TDD |
| --- | --- |
| failing test | 没有 Skill 时 Agent 会跑偏的场景 |
| implementation | `SKILL.md` 和 references |
| passing test | 有 Skill 时仍按规则行动 |
| refactor | 补 counter、red flag、output gate |

最小记录：

```markdown
Scenario:
Baseline behavior:
New wording behavior:
Remaining loophole:
Decision: keep / revise / move to reference / no-change
```

不能只有场景草稿就声称“已验证会改变 Agent 行为”。

## Skill Deployment Gate

新增或显著修改单个 Skill 后先检查：

1. Identity：目录名、frontmatter、`skills/README.md` 一致。
2. Discovery：入口和触发词能追到 `SKILL.md`。
3. References：链接存在，路径相对且无二跳必读。
4. Placement：长内容已下沉。
5. Cross-tool：公共规则对 Codex/Cursor 成立；工具差异在 adapter。
6. Pressure：高约束规则有场景或说明为何暂不需要。
7. Diff：`git diff --check`、引用搜索、空白搜索通过。

批量改多个 Skill 时，也要逐个过 owner、触发、reference 和索引检查。

## Scripts

脚本只在能降低重复错误或提供真实验证时引入。必须说明依赖、输入、输出、权限假设和失败处理；不要假设 node、python、浏览器、Graphviz、MCP 或网络一定可用。

## Authoring Checklist

- 触发明确，description 没有流程摘要。
- Skill 表达的是可组合 SOP，不是场景条件树。
- 正文短，长内容进入 references。
- 已检查是否可被模型能力、通用规则或更高阶 Skill 覆盖。
- 没有项目私密信息、一次性业务决策或历史长论证。
- 相关入口、`skills/README.md` 和旧触发词已同步。
- 公共规则跨 Codex/Cursor 成立。
- 高约束变更有压力测试或风险说明。
- 已跑 diff check、引用搜索和尾随空白搜索。
