# Idea Shaping Patterns

日期：2026-06-11
来源：吸收 Superpowers `brainstorming`、`spec-document-reviewer-prompt.md` 和 `visual-companion.md` 的需求探索机制后整理。
用途：作为 `requirements-brief` 的按需参考；用于把模糊产品、工具、自动化、UI 或 workflow 想法收敛成可执行需求。

## Core Rule

先理解目标，再讨论方案；先收敛需求，再写实现计划。设计门禁按风险缩放：高模糊、高风险、多角色、多 UI/流程分支时需要明确设计确认；小修或边界清楚的任务只冻结不变量后直接执行。

## Discovery Flow

1. **Read context first**：先看当前代码、文档、历史报告或已有产品形态，避免问用户已经能从上下文发现的问题。
2. **Scope split early**：如果想法包含多个独立系统或产品面，先拆成可独立验收的子问题。
3. **Ask one key question**：一次只问一个会显著影响方向的问题；不把 6 个问题打包丢给用户。
4. **Separate goal from solution**：记录用户底层目标，并把用户提出的第一版方案视为候选方案之一。
5. **Offer 2-3 approaches**：给出不同路径、取舍和推荐；推荐要说明为什么适合当前约束。
6. **Confirm design at right fidelity**：简单任务用 3-7 条不变量确认；复杂任务用 brief、PRD、流程图或设计文档确认。
7. **Check unit boundaries**：对新增或重塑的对象、组件、模块、服务、工具和页面，先确认职责、接口、依赖和可验证性。
8. **Confirm complex design in sections**：复杂设计按架构、对象/组件、数据流、错误态、验证分段确认；不要让用户一次性批准没看清的整包方案。
9. **Self-review before handoff**：检查占位、矛盾、模糊需求、范围过大和 YAGNI。
10. **Transition deliberately**：需求清楚后再进入 `implementation-plan`；不要用工程步骤掩盖需求不清。

## Spec Readiness Gate

需求 brief、PRD 或设计 spec 进入 implementation plan 前，先确认它能支撑计划，而不是只看起来完整。这个 gate 吸收 Superpowers spec reviewer 的校准：只阻塞会让后续计划建错、卡住、越界或无法验收的问题。

| Category | Blocking issue | Advisory only |
| --- | --- | --- |
| Completeness | 缺核心用户/job、scope、workflow、验收、错误路径或非目标 | 章节顺序、标题命名、细节可更完整 |
| Consistency | 角色、状态、数据源、权限、入口或目标互相矛盾 | 表述风格不统一 |
| Clarity | 需求可被合理解释成两种不同实现，且会影响行为或计划 | 个别句子可以更顺 |
| Scope | 同一个 brief 覆盖多个独立子系统，无法形成一个可验收计划 | 后续路线图写得还不够细 |
| YAGNI | 加入未请求能力、过度架构或会扩大本轮交付的假设 | 记录了可选 follow-up |

Review 输出使用：

```markdown
## Spec Review

Status: Approved | Issues Found | Needs Clarification

Blocking Issues:
- [Section]: 问题 - 为什么会导致计划做错、卡住、越界或无法验收。

Advisory Recommendations:
- 建议 - 为什么有帮助，但为什么不阻塞。
```

如果出现 blocker，先修 brief 或问一个最小阻塞问题，再进入 `implementation-plan`。不要让实现计划替需求文档猜 scope、owner、权限或验收标准。

## Scope Decomposition Gate

当用户的想法像“做一个平台 / 系统 / 工作台 / 全套 workflow”，先判断它是否已经大到不能成为一个 brief 或一个 implementation plan。以下信号出现时，先拆范围，不继续追问局部细节：

- 包含 2 个以上独立用户任务，例如配置、执行、审查、计费、分析、协作、权限管理。
- 包含 2 个以上独立技术 owner 或数据源，例如前台 UI、后台任务、外部 API、存储、通知、权限。
- 每个模块都有自己的成功路径、错误路径和验收标准。
- 一个模块的决策会显著改变另一个模块的前提或优先级。
- 需求里出现“顺便”“也要”“完整平台”“全链路”“从 A 到 Z”等范围膨胀信号。

拆分时输出：

```markdown
## Decomposition

1. <子问题名>
   - User/job:
   - Output:
   - Depends on:
   - Acceptance:

## Build Order

1. First: <原因>
2. Later: <原因>
```

拆分后只选择第一个可独立验收的子问题进入正常 Discovery Flow；其它子问题进入 `Non-goals` 或 `Later`。如果用户明确要整体路线图，可以先给路线图，但不能把路线图当成本轮实现范围。

## Question Discipline

优先问会改变实现方向的问题：

| 问题类型 | 何时问 | 示例 |
| --- | --- | --- |
| Goal | 目标可能被方案遮住 | “用户最终要完成什么任务？” |
| User / job | 使用者和场景不清 | “这是给管理员日常管理，还是给首次配置使用？” |
| Scope | 范围可能膨胀 | “本轮必须包含创建/编辑/删除，还是先只做只读查看？” |
| Constraint | 技术、权限、时间、数据源会改变方案 | “数据源以现有 API 为准，还是允许新建后端能力？” |
| Acceptance | 完成标准不清 | “什么信号能证明这条 workflow 已经可用？” |
| Trade-off | 多个方向都可行 | “更优先快速上线，还是更优先后续可扩展？” |

少问不会改变决策的问题。能合理假设且风险低的内容，直接写入 `Assumptions`。

## Approach Options

给方案时使用固定结构：

```markdown
## Options

1. Recommended: [方案名]
   - Best when: ...
   - Trade-off: ...

2. Alternative: [方案名]
   - Best when: ...
   - Trade-off: ...

3. Minimal: [方案名]
   - Best when: ...
   - Trade-off: ...
```

推荐方案不等于替用户决定；它用于降低认知成本。用户选择不同方向时，更新需求和非目标，而不是继续按旧推荐实现。

## Sectioned Design Approval Gate

高模糊、高风险或跨多个对象的设计，应该分段确认，而不是一次性给一份大方案后直接进入实现。

触发信号：

- 同时涉及架构、UI/流程、数据模型、权限、错误态、测试或运营流程中的 2 个以上。
- 任一章节的决策会改变其它章节，例如数据模型影响 UI、权限影响 workflow、错误态影响验收。
- 用户已经多次纠正语义、优先级、scope 或 owner。
- 方案篇幅较长，用户很难在一次回复里审完所有影响面。

建议分段：

| Section | 需要确认什么 |
| --- | --- |
| Architecture / ownership | owner、边界、主要模块和非目标 |
| Objects / components | 核心对象、页面/组件、职责和交互点 |
| Data / state flow | 输入、状态变化、source of truth、同步或缓存 |
| Errors / permissions | 失败路径、权限、空态、取消、重试和 unsupported |
| Validation | 成功信号、负向验收、测试或手动 smoke |

分段确认规则：

- 每段只问“这一段是否符合目标/约束”，不要混入下一段新问题。
- 用户修改某段时，回写对其它段的影响；不要只修文字。
- 已确认的段落可以作为后续计划输入，但如果上游段落被改动，相关下游段落要重新确认。
- 对小修或边界清楚的任务，用 3-7 条不变量确认即可；不要强行制造长 spec。

## Unit Boundary Design Gate

新增功能、组件、服务、工具、页面或工作流步骤时，先证明边界清楚，再进入实现计划。边界不清时，后续计划往往会膨胀成大文件、隐藏依赖、难测行为或重复入口。

对每个核心单元回答四个问题：

| Question | 需要写清什么 |
| --- | --- |
| Purpose | 它负责什么用户任务、系统职责或状态转换 |
| Interface | 外部如何使用它：输入、输出、事件、route、API、props、command 或 tool schema |
| Dependencies | 它依赖哪些 owner、数据源、权限、状态、外部服务或 UI 容器 |
| Testability | 如何不读内部实现就验证它：单测、契约测试、workflow smoke、手动验收或负向断言 |

通过信号：

- 一个单元能用 1-2 句话说明用途和非目标。
- 消费者可以通过接口理解如何使用它，不需要读内部代码猜副作用。
- 修改内部实现不应改变消费者契约，除非本轮明确改契约并盘点调用者。
- 测试或 smoke 能从边界验证行为，而不是只能 mock 多层内部细节。

需要重新拆边界的信号：

- 一个对象同时承担展示、持久化、权限、调度、网络请求和错误解释中的多项。
- 新文件或现有文件显著变大，但没有新的 owner、接口或测试边界。
- 需求只能写成“这里顺便处理一下”“沿用上面逻辑”“在组件里判断所有情况”。
- 为了测试不得不暴露生产不需要的入口，或 mock 三层以上依赖才能覆盖一个行为。
- 两个单元互相知道对方内部状态，或同一状态在多个地方各自解释。

如果这些信号出现，先调整 design：拆成更小单元、收窄职责、把依赖反转为接口、把共享状态明确归 owner，或把非本轮能力放入 Non-goals。不要把边界不清留给 implementation plan 执行者猜。

## Visual Decision Gate

视觉辅助只在“看比读更清楚”时使用：

- 适合视觉：布局、导航结构、UI mockup、状态流、关系图、信息层级、视觉风格对比。
- 适合文字：范围、验收标准、API/数据建模、权限语义、技术取舍、非目标。

Hub 不复制 Superpowers 的 browser companion 脚本。需要视觉探索时，按当前工具可用能力选择：Mermaid、截图、in-app browser、临时 HTML、设计稿或 image generation。使用前先判断是否真的能降低理解成本。

视觉辅助的使用要逐题判断，而不是一旦开启就全程使用：

- 每个问题先问“用户看图会不会比读文字更快理解或选择？”答案是否定时回到文字。
- 视觉问题一次只展示当前选择，不把概念问题、范围问题和视觉偏好混在同一屏。
- 从视觉讨论回到文字需求时，明确当前视觉已冻结、废弃或等待后续；不要让旧 mockup、旧流程图或旧对比继续暗示它仍是最新方案。
- 用户的文字反馈优先于点击/截图等结构化信号；视觉交互只作为补充证据。
- 视觉产物若要长期保留，归档到 report 或项目设计文档；临时 mockup 不升级为需求事实源。

## Brief Self-Review

需求 brief 交给实现计划前，检查：

- 是否还有 `TBD`、`TODO`、占位描述或“体验更好”这类不可验证表述。
- 是否有相互矛盾的目标、角色、状态或数据源。
- 是否把多个独立产品/系统塞进一个验收范围。
- 是否把未确认的技术实现写死成需求。
- 是否至少覆盖成功路径、错误路径、边界输入和权限/状态变化。
- 是否明确了本轮非目标，防止实现时自然膨胀。
- 是否已把阻塞问题和建议项分开，避免用文字 polish 阻塞实现，也避免把真实歧义丢给计划阶段。

## Output Pattern

模糊想法收敛后，优先输出：

```markdown
## Summary

## Confirmed Invariants

## Options

## Scope

## Workflow

## Acceptance Criteria

## Non-goals

## Open Questions
```

如果 `Open Questions` 仍会改变核心方案，就不要进入实现计划；只剩低风险细节时，可以用假设继续推进。
