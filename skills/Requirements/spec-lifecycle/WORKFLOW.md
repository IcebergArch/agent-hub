---
name: spec-lifecycle
description: 当用户创建、更新、确认、执行、恢复、暂停、完成或归档正式 SPEC/IDEA/STDD，或使用 `/hub record` 维护项目观察记录时使用；只负责状态、owner 与文档工作区路由。
---

# Purpose

统一观察、IDEA、SPEC 与执行包的状态流转；内容评审、实现/回测和 Git/PR 分别交给其唯一 workflow。

# When to Use

- 创建、更新、查找、执行、恢复、暂停、完成或归档 SPEC。
- `/hub record`、`/hub spec-idea`、`/hub spec`、`/hub spec-exec`、`/hub spec-smoke`，以及正式 SPEC 上下文中的 `/hub pr`。

# When NOT to Use

- 只在对话中整理普通需求简报，不落正式文档。
- 普通实现且本轮不改变 SPEC/STDD 状态。

# Inputs

- 用户目标、项目、SPEC 路径或 ID、目标动作、确认状态、当前文档和实现仓库事实。

# Decision Principles

- Hub 只拥有流程路由；目录、命名、状态、模板和归档规则以 README 登记的文档工作区为唯一 owner。
- SPEC 保存需求事实，STDD 保存执行进度、决策和验证；Bug Pool 与 Optimization Pool 各守其责，不复制成第二份 SPEC。
- `spec-record.md` 只承载有证据的已发生观察；可关联 IDEA、SPEC 或 Bug，但不取代它们，也不产生实现授权。
- SPECID 创建后稳定，同一需求只有一个 active owner；IDEA 升级时改写原 owner，不保留重复需求。
- 正式状态为 `draft -> init -> update -> plan -> execing -> archive`。`plan` 是唯一执行入口；实质变更回到最早受影响阶段，SPECID 不变。
- 相邻命令不继承权限：`spec` 不授权实现，`spec-exec`/`spec-smoke` 不授权 Git/PR，`pr` 不重新验收或授权 merge。
- 未完成 Bug 留在原执行包并阻止完成。仅剩仍需实施的优化时，先创建、评审并追踪新的 plan SPEC，再归档原包；无遗留时不制造空后续 SPEC。

# Command Loading

先读文档工作区入口及其 lookup、naming、lifecycle 规则，再只加载当前命令所需 owner：

| 命令/动作 | 追加加载 |
| --- | --- |
| `/hub record` | 当前项目背景和唯一观察账本；不加载方案评审或执行 reference |
| `/hub spec-idea` | `requirements-brief`；输入模糊时才读 idea shaping reference |
| `/hub spec` | `requirements-brief` 与其 `spec-review-gates`；领域、导航、Engineering、Research 仅按适用性追加 |
| `/hub spec-exec`、`/hub spec-smoke` | `task-execution-lifecycle` 的 spec execution/smoke reference；smoke 成功还要记录已分配隔离空间已安全移除或本项为 N/A；不重读方案评审，除非范围实质变化 |
| `/hub pr` | review/git reference 与当前 STDD、问题池、fresh smoke evidence；不重读实现/验收流程 |
| 恢复、完成、归档 | 当前 SPEC、STDD、问题池和文档工作区 lifecycle；只在其记录指向时追加其它材料 |

# Workflow

1. **Load Owner**：从 Hub README 定位文档工作区，读取其当前入口和当前动作所需 rules；不得凭 Hub 记忆猜路径、状态或模板。
2. **Resolve Project**：确认唯一项目和目标记录；只扫描当前项目的相关 active 集合，默认不读 archive。按稳定 ID、标题和领域查重。
3. **Record Observation**：`/hub record` 按稳定证据 ID、对象和标题更新项目唯一观察账本；区分已确认事实、当前判断、开放问题、状态、证据、下一 owner 和关联条件。纯需求或方案构想改用 `/hub spec-idea`。项目不唯一时只问一个最小问题。
4. **Shape IDEA Or SPEC**：IDEA 补齐项目、问题/背景、目标结果、初步边界、升级价值和开放问题；正式 SPEC 由 `requirements-brief` 建设并评审 `draft -> init -> update -> plan`。待迁移旧态被触达时先转为当前评审态，不批量假定通过。
5. **Apply State Change**：按最早受影响内容移动唯一文件并维护稳定 ID 与链接；只改链接、状态或不改变语义的勘误不重开评审。高风险实施确认只在真实未确认或新增越界时阻塞。
6. **Start Or Resume Execution**：只有明确执行指令和 plan/execing 目标才创建或恢复 STDD、Bug Pool、Optimization Pool；首次实现修改前记录代码/文档边界、资源 owner、占用和回收入口。实现与 smoke 细节交给 execution reference；smoke 只有在主工作空间证据通过，且当前 SPEC 已分配的隔离空间安全移除或经核验为 N/A 后才记录成功。
7. **Package PR**：只引用前序 fresh evidence，按 review/git reference 完成 Git/PR 收尾；证据缺失或失效时记录风险，不伪造通过，也不在此重跑测试或验收。
8. **Finish Or Pause**：核对冻结验收、两个问题池、实现证据及提交/推送状态。未满足门禁则保持可恢复 checkpoint；满足文档工作区完成门禁后归档完整执行包。
9. **Validate**：检查记录去重、单一 active SPEC、稳定 ID、状态/链接、exec/smoke/PR 证据顺序和文档 diff；禁止过期状态名和无 owner 遗留。

# Checklist

- 是否读取文档工作区当前规则，并只加载本命令需要的 reference。
- 观察、IDEA、SPEC、STDD 和两个问题池是否各有唯一 owner，没有互相代写。
- 同一需求是否只有一个 active SPEC 和稳定 ID；实质变更是否回到最早受影响阶段。
- 是否只有 plan 进入执行，且 `spec-exec -> spec-smoke -> pr` 的权限和 fresh evidence 没有串段；smoke 成功时已分配隔离空间是否移除或核验为 N/A、已有 work branch/ref 是否仍可恢复。
- 未完成 Bug 是否仍阻止归档；仅有优化时是否先形成可执行的新 plan SPEC；无遗留时是否未制造空 follow-up。

# Escalation

- 需求内容建设：`skills/Requirements/requirements-brief/WORKFLOW.md`
- 实现、回测与 Git/PR：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 文档 owner 或项目背景损坏：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 接口、配置、数据库或外部事实：只加载实际命中的 Engineering / Research Skill。

# References

- Hub README 登记的文档工作区入口及其现行 rules；其它 reference 按 Command Loading 表读取。
