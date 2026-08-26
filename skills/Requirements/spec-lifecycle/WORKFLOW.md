---
name: spec-lifecycle
description: 当用户创建、更新、确认、执行、恢复、暂停、完成或归档正式 SPEC/IDEA/STDD 时使用；负责把需求事实与执行记录按文档工作区当前规则正确流转。
---

# Purpose

统一 SPEC 从 IDEA、draft、init、update、plan 到 execing、archive 的操作顺序，同时保持 Hub 与文档工作区职责分离。

# When to Use

- 用户要求创建、更新、查找、执行、恢复、暂停、完成或归档 SPEC。
- 用户给出 SPEC 路径并要求修改业务仓库。
- `/hub spec`、`/hub spec-idea` 或其它明确的 SPEC 生命周期操作。

# When NOT to Use

- 只需在对话中整理需求简报，不落正式文档。
- 已进入普通实现且本轮不改变 SPEC/STDD 状态。

# Inputs

- 用户目标、项目、SPEC 路径或 ID、目标生命周期动作、确认状态、当前文档与实现仓库状态。

# Decision Principles

- Hub 只拥有流程路由；目录、命名、状态和归档规则以 README 登记的文档工作区为唯一 owner。
- SPEC 是需求事实，STDD 是执行记录。不得把执行进度、Bug 详情或优化池反写成第二份 SPEC。
- SPECID 创建后稳定；同一需求更新原 SPEC，不用新 ID 制造重复 owner。
- `draft` 只登记不变量、背景、目标与核心问题，`init` 汇总适用维度，以及当前事实、历史经验、行业实践和专家判断形成的方案设计输入，`update` 是正式撰稿与评审修正态，`plan` 是唯一可进入执行的最终定稿。
- 参与 Skill/角色在 init 阶段共同形成设计约束、候选模式、取舍、风险和验收信号，update 正式撰稿并验收其落实情况；通过即停止，绝大多数方案应在 2-3 轮内收敛。第 3 轮后先做收敛检查，10 轮只是硬上限；未收敛时保持 update。
- 变更回到最早受影响阶段：不变量或问题定义变化回 draft，专家范围或意见变化回 init，方案正文修正留在 update；SPECID 始终不变。
- 已确认的实施单收到明确执行指令后直接流转；只有新增越界事项才重新确认。
- 文档状态不代替代码事实与执行授权。开始实现前仍需校验当前代码、配置、接口和外部状态。
- 完成归档不能留下无 owner 的已承诺优化：无遗留则直接归档；Bug 全部验证关闭但仍有可独立实施的优化时，先按工作区规则创建并评审成新的正式 plan SPEC、建立追踪，再归档原执行包。
- 任何未完成 Bug 都必须留在原执行包处理，阻止完成和归档；不得转移到新 SPEC、优化池或 IDEA。

# Workflow

1. Load Owner：读取 Hub README 登记的文档工作区入口，再读取该工作区的 README/AGENTS 和当前 SPEC lifecycle、naming、lookup 规则；不得凭 Hub 内旧示例猜路径。
2. Resolve Project：确认项目目录和长期背景；只扫描当前项目的 IDEA、draft、init、update、plan、待迁移旧 pending 或 execing 集合，不默认读取 archive。
3. Create Or Update：
   - IDEA 使用工作区当前 IDEA 规则，不提前分配正式 ID。
   - 创建正式 SPEC 前按 ID、domain、标题关键词查重；命中同一需求时更新原文并保留 ID。
   - IDEA 转正式 SPEC 时优先改名和重写原文件为 draft，避免保留两份需求 owner。
   - draft 只写不变量、背景、目标、核心问题与非目标；init 再判断七维适用性、选择 Skill/角色，并把已验证当前事实、历史运行/事故/项目经验、适用行业实践和专家判断提炼为方案设计输入；高风险、新颖或外部事实不确定时按 Research workflow 核对权威来源。update 正式撰写第一版完整方案，记录输入的吸收、调整或有理由拒绝，再评审、修正和复评。绝大多数方案应在 2-3 轮内收敛，第 3 轮后先做不收敛根因检查，单个实质变更周期最多 10 轮。全部适用维度无 blocker 才转为 plan。
   - plan 或中间态发生变化时回到最早受影响阶段：不变量/背景/目标/核心问题变化回 draft，专家范围或意见变化回 init，方案正文修正留在 update；只改链接、状态或不改变语义的勘误不重开。
   - 待迁移的旧 pending 在下次编写或执行触达时先转为 update 并完成评审，不直接执行，也不批量假定通过。
   - 高风险接口、配置、数据库或兼容变更的确认单放在背景之后。
4. Start Execution：
   - 只在用户明确要求执行且目标为 plan 时进入；draft、init、update、IDEA 和旧 pending 不是执行候选。
   - 核对确认单；已确认范围不重复提问，未确认或新增越界范围才暂停。
   - 严格按文档工作区规则变更 SPEC 状态并创建 STDD、Bug Pool、Optimization Pool；SPEC 与执行记录通过稳定 ID 关联。
   - 在首次实现修改前记录实现仓库与文档仓库边界。
5. Execute And Recover：SPEC 冻结；进度、决策、验证和提交写主 STDD，验收缺陷写 Bug Pool，非阻塞建议写 Optimization Pool。恢复时读取 SPEC、STDD、Bug Pool，按规则决定是否加载 Optimization Pool。
6. Finish Or Pause：
   - 按工作区 gate 核对冻结验收、Bug Pool、Optimization Pool、实现证据、提交和推送状态。
   - 无遗留工作时，按工作区规则直接归档完整执行包。
   - 存在任何未完成 Bug 时保持 execing，继续修复和验证；不得创建后续 SPEC 来转移 Bug，也不得归档原执行包。
   - Bug 全部验证关闭、唯一遗留项为明确仍需实施的优化时，先生成并评审成自包含的新 plan SPEC，在原 Optimization Pool 与新 SPEC 间记录双向追踪，再归档原执行包；不得只生成 IDEA 或只留问题池记录。
   - 未完成验证、未形成 owner 或新 SPEC 仍停在 draft/init/update 的遗留优化阻止完成；保持 execing 并写 checkpoint，不伪装完成或退回 plan。
7. Validate：检查同 ID 文件是否齐全、链接有效、无重复 SPEC、无过期状态名，并对文档 diff 执行格式/引用检查。

# Checklist

- 是否读取了文档工作区当前规则，而不是复制 Hub 记忆。
- 同一需求是否只有一个 active SPEC 和一个稳定 ID。
- draft/init/update/plan 是否只保留一个当前文件；每次实质变更是否有适用性、Skill/角色、方案设计输入及其吸收情况和评审轮次证据。
- SPEC 与 STDD 是否职责分离且状态/链接一致。
- 已确认事项是否没有被重复阻塞。
- 完成声明是否同时有实现与文档证据。
- 收尾是否读取两个问题池；是否保证未完成 Bug 仍阻止归档；仅有优化遗留时是否先创建并评审成自包含的新 plan SPEC 并建立双向追踪；无遗留时是否没有制造空后续 SPEC。

# Escalation

- 需求内容尚未收敛：`skills/Requirements/requirements-brief/WORKFLOW.md`
- 开始实现与验证：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 接口、配置或数据库门禁：加载对应项目规则及 Engineering Skill。
- 文档 owner 或项目背景损坏：`skills/Context/project-context-rebuild/WORKFLOW.md`

# References

- `skills/Requirements/requirements-brief/references/spec-review-gates.md`
- Hub README 登记的文档工作区入口及其现行 rules。
