---
name: self-evolution-engine
description: 当用户要求 Hub 具备自迭代、自优化、自升级、自我提升、持续升级，或当前任务暴露出可复用流程缺口/高风险经验需要沉淀为 repo 内长期机制时使用。
---

# Self Evolution Engine

本技能用于让 Agent Hub 在任务内完成最小必要的自我升级。目标不是让 Agent 每轮都扩写规则，而是在出现稳定、可复用、跨设备也应成立的经验时，把它沉淀为 Hub 内可携带的 Skill、Rule、Reference 或 Report。

## Core Invariants

- 自升级机制必须以仓库内容为事实来源：`AGENTS.md`、`README.md`、`agents/`、`skills/`、`reports/`。
- 机制不能依赖某台设备上的 automation memory、插件缓存路径、宿主线程状态或临时本地目录才成立。
- 自升级只做最小必要沉淀：能合并到现有 owner 就不新增 owner，能写短规则就不扩长文，能写 reference 就不把长流程塞进入口。
- 自升级的目标是提升 Skill 加载效率、触发准确度和迭代质量；不是把经验不断追加到同一入口。
- 报告无论个人还是公司都有生命周期；一次性、阶段性和冷历史报告不应因为存在而被默认加载。
- 主任务优先；只有用户明确要求、当前任务暴露稳定缺口，或高风险经验明显应长期复用时才触发。

## Trigger

以下情况触发：

- 用户明确说“让 hub 具备自迭代/自优化/自升级/自我提升/持续升级”。
- 用户要求“换设备后也能继续触发”“不要依赖本机状态”“要写进 hub 本身”。
- 当前任务完成后暴露出可复用流程缺口，且该缺口不应继续依赖用户手动 `receive` 才被修复。
- 外部方法论吸收、规则体系调整、设计原则沉淀完成后，需要补一层 repo-portable 的触发与落位机制。

触发判断不靠词表。用户措辞只是一种入口提示；真正的触发依据是任务中出现了可迁移的结构性信号。

## Structural Signals

出现以下信号时，把它作为 Hub 自升级候选，而不是等用户再次提示：

- **Cross-task invariant**：同一约束会影响多个项目、多个 case、多个工具或多轮任务，不是单次修补。
- **Rule bypass**：Hub 已有规则覆盖过该问题，但没有绑定到实际决策点，导致开始执行、写文件、改配置、调用工具或收尾判断时被绕过。
- **Owner drift**：任务模式、owner、数据源、执行层、资源层或验收信号反复混淆，用户不得不重新解释稳定边界。
- **Action mismatch**：当前目标只允许素材、页面、Chat、验证或报告动作，但 Agent 准备改代码、schema、config、平台资源或公共契约。
- **Semantic drift**：SDK、API、配置、目录、tool、prompt、skill、resource 等命名无法沿同一领域语义闭环理解。
- **Evidence gap**：规则写了但无法通过 diff、搜索、真实 workflow、媒体/接口产物或 checklist 证明下次会生效。

以下情况不触发：

- 一次性项目实现细节、临时命令、个人偏好草稿。
- typo、小范围机械改名、无需长期机制的单次修复。
- 只能在某个设备、本地路径、插件缓存或自动化实例里成立的临时 workaround。

## Workflow

1. **Check Need**
   - 确认这次升级是否真的在保护长期不变量，而不是记录一次过程。
   - 若没有明确触发词，则至少满足一项：反复缺口、高风险设计闭环、跨任务可复用流程、换设备后仍应成立。
   - 先写清候选信号属于 `cross-task invariant`、`rule bypass`、`owner drift`、`action mismatch`、`semantic drift` 还是 `evidence gap`；无法分类时默认不升级。

2. **Bind Decision Point**
   - 判断规则应该在哪个动作点生效：任务定位、计划审查、写文件前、配置/schema/API 变更前、工具调用前、收尾验收前、失败复盘后。
   - 如果规则只能在最终回复里提醒，而不能拦住某个错误动作或补上某个检查证据，优先写 report 或不沉淀。
   - 对已经存在但失效的规则，优先补动作点、owner 和验证，不新增近义规则。

3. **Run Repo-Portable Gate**
   - 检查候选机制是否依赖本机 memory、cache-id、automation、host UI、安装路径或临时环境。
   - 若依赖这些状态，先把它改写成 repo 内触发词、Skill owner、reference 检查表或工具 adapter 语义。
   - 无法改写为 repo-portable 时，不升级为通用规则；只保留为 report 或说明当前不沉淀。

4. **Choose Destination**
   - 规则体系或内容落位：优先路由到 `refactor-hub`、`project-content-curation`。
   - 设计/架构/验证原则：优先路由到 `design-principle-library`。
   - 外部方法论吸收：优先路由到 `methodology-harvest`。
   - 任务执行、写文件前检查、Run Case/Build Capability 分流优先落到 `task-execution-lifecycle` 或其 reference。
   - SDK/API/MCP/config 命名和跨服务契约优先落到 `interface-contract-audit`。
   - 只有当现有 owner 都无法承载时，才新增 Skill/Reference。

5. **Keep Structure Stable**
   - `AGENTS.md` 只写短触发和硬约束。
   - `README.md` 只写长期能力定义和协作预期。
   - `skills/<slug>/SKILL.md` 写触发、步骤、输出、stop rule。
   - 长判断矩阵、样例和执行细则放 `references/`。
   - 需要保留来源、证据、一次性取舍时写入 `reports/`。

6. **Apply Minimal Patch**
   - 优先更新现有文件和现有 owner。
   - 只有在缺 owner、缺触发链或缺 repo-portable 检查项时才新增文件。
   - 不顺手扩大到定时自动化、本机 memory、公司项目报告或其它未授权目录。

7. **Replay And Validate**
   - 用本次失败或候选场景做一次简短回放：新规则会在哪个动作点触发、会检查什么、会禁止或允许什么动作、会要求什么证据。
   - 如果回放后仍只能依赖用户提醒，说明规则无效，继续改 owner/动作点或不沉淀。
   - 当纯文字规则不足以稳定触发时，优先补 decision matrix、workflow diagram、coding example、replay checklist、template 或 validation snippet；这些资产归入对应 Skill/reference，不放 README。
   - 搜索新 trigger、owner、reference 路径和旧近义词，确认入口可发现。
   - 检查新增机制是否明确了 trigger、invariant、destination、exception、verification。
   - 运行 `git diff --check` 与必要的引用/空白搜索。

## References

- `references/evolution-decision-checklist.md`：判断是否值得沉淀，以及如何通过 repo-portable gate。
- `../refactor-hub/SKILL.md`：对话经验、Hub 规则和入口结构整理。
- `../project-content-curation/SKILL.md`：Skill / Rule / Reference / Report / Keep / Delete 分类。
- `../design-principle-library/SKILL.md`：设计与验证原则沉淀。
- `../methodology-harvest/SKILL.md`：外部方法论吸收与映射。

## Output

最终汇报至少说明：

- 这次自升级是否成立。
- 沉淀到了哪些 owner。
- 哪些内容刻意没有沉淀，以及原因。
- 已执行的最小验证。

## Quality Bar

- 不把“以后记得”这种空话当成自升级。
- 不把触发词表当成自升级；Hub 要沉淀的是判断链路、动作点和验证方式。
- 不把本机状态、缓存路径、单次 automation 结果写成 Hub 默认机制。
- 不新增重复 owner；先合并到已有 Skill/Rule/Reference。
- 不为了自升级破坏入口文件的短小和稳定加载面。
- 不做追加式堆积；每次升级都要检查能否删除、合并、下沉或降级旧内容。
