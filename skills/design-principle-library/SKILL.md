---
name: design-principle-library
description: 当完成或复盘架构、coding 分层、系统、UI/交互、数据体系、工具协议等高风险设计后，用于抽取可复用原则和检查项。
---

# Design Principle Library

沉淀设计原则的路由 Skill。它只保留跨领域 meta 判断；具体细则优先回到专门 Skill 或 reference。

## Trigger

- 用户明确说 `receive`、复盘设计质量、指出“为什么没想到”“应该自我提升”。
- 完成高风险架构、系统、UI/交互、数据、工具协议或运行时设计后。
- 同类问题被连续纠偏，或一次问题暴露明显通用反模式。
- 外部优秀实践、本地历史文档和当前项目适配之间出现可复用取舍。

普通 typo、机械重命名、一次性实现细节不触发。

## Workflow

1. **Classify Domain**：architecture、coding-layering、system-design、product-ui、interaction、data-modeling、tool-protocol、runtime、security、observability、testing。
2. **Find Owner**：Product UI 走 `agent-team-router` / UI references；接口和工具走 `interface-contract-audit` / `agent-tool-design`；测试走 `test-strategy`；执行控制走 `task-execution-lifecycle`；外部方法走 `methodology-harvest`。
3. **Extract Invariant**：只沉淀能保护 owner 边界、用户任务流、状态语义、系统 ID、真实链路、证据层级或跨工具兼容性的原则。
4. **Ground**：依据必须来自本地历史文档、当前项目真实约束、官方/优秀实践之一；先去掉项目名、业务方案和一次性上下文。
5. **Patch Destination**：优先更新已有 Skill/reference；原则不稳定时写文档工作区报告；只有跨领域且无 owner 时才留在本技能或 `AGENTS.md`。
6. **Validate**：搜索近义触发词和旧规则，确认没有重复、断链或高频入口膨胀。

## Principle Seeds

- 先找 owner，再写原则；有专门 Skill 时不在这里重复长规则。
- 原则必须保护不变量；只像提醒口号的内容不沉淀。
- 设计先判断对象、任务、状态、动作和反馈归属；视觉、结构、命名、接口和验证都是后续表达。
- 系统、数据和工具协议先保护 owner、ID、source of truth、contract closure、错误回执和验证信号。
- 完成状态、review 和测试必须绑定 fresh evidence；mock、旧输出、summary 或积极语气不能替代验收。
- 高频加载面只放触发、硬约束、stop rule、输出形态和指针。

## References

- `references/product-ui-ux-psychology-patterns.md`：UI/交互、心理模型、状态反馈。
- `references/platform-resource-fidelity.md`：平台级 Skill/Prompt/Tool/Resource 质量门。

## Output

说明新增/调整的 owner、触发条件、自检方式，以及未沉淀的项目细节和原因。
