---
name: refactor-hub
description: >-
  当用户说 project refactor、rule update、refactor hub、优化 hub、整理 agent-hub 或要求从当前对话中抽取可复用规则/技能/报告时使用；
  先回看当前对话消息和现有 hub 内容，分析可优化点，再按 Skill、Rule、Report、Keep、Delete 分类落位。
---

# Refactor Hub

本技能用于把当前协作过程中暴露出的稳定经验，反向沉淀到 agent-hub。目标不是记录过程日志，而是优化 hub 的长期指令、技能、规则、报告和入口结构。

## Trigger

用户出现以下表达时触发：

- `refactor hub`
- `project refactor`
- `rule update`
- `优化 hub`、`整理 hub`、`更新 hub 指令`
- 要求“翻阅/回看/复盘当前对话”，并抽取可复用规则、技能、注意点或工作方法
- 要求复盘、优化或纠正某类指令执行流程，例如 `git update`、`git diff`、验证、提交、发布或调试流程，并希望后续长期遵守
- 指出本轮协作暴露出某个流程缺口，并要求沉淀到 agent-hub

如果用户只是要求修改当前项目 README、代码或测试，不自动触发本技能；除非用户明确要求把经验沉淀到 hub。

## Inputs

- 当前对话消息：用户需求、纠错、命名偏好、流程问题、反复出现的质量门槛。
- agent-hub 现有入口：`AGENTS.md`、`README.md`、`agents/`、`skills/README.md`、相关 `skills/*/SKILL.md`、`reports/index.md`。
- 当前任务涉及的项目文件：只作为判断来源，不把一次性项目细节写入通用规则。

## Workflow

1. **Conversation Review**：先回看当前对话中用户的明确要求、纠偏点、反复强调的偏好和已做过的变更；如果上下文已压缩，只使用可见摘要和当前消息，不编造缺失细节。
2. **Hub Inventory**：读取 agent-hub 的入口文件和相关技能，确认现有规则是否已经覆盖；不要不读现有内容就新增重复规则。
3. **Gap Analysis**：把对话暴露的问题归类：触发词缺失、流程顺序缺失、命名审计缺失、验证缺失、文档边界缺失、文件过大、技能缺失、报告归档缺失、提交边界缺失或过期内容。特别检查 `README.md`、`AGENTS.md`、工具适配文件和 Skill 是否混入了具体项目、公司项目或业务域专属设计规则。
4. **Classify**：按 `project-content-curation` 的标准分类为 Skill、Rule、Report、Keep 或 Delete。可重复流程优先抽成 Skill；稳定约束写规则；项目结论写 reports；一次性过程不沉淀。
5. **Extract And Merge**：优先更新现有 Skill 或规则；只有现有位置承载不清时才新增 Skill。新增内容必须说明触发条件、步骤、输出和验证。若可复用经验来自单个项目，先抽象为流程、边界或检查项；无法抽象的项目内容只放入对应 reports，不写入 hub 通用入口。
6. **Sync Entrypoints**：同步 `AGENTS.md` loading order、`README.md` 目录图、`skills/README.md` 技能索引和任何旧触发词引用。
7. **Validate**：搜索新旧触发词、技能名、目录名和入口引用；确认没有重复、断链、旧称残留或空泛规则。

## Output

最终回复应包含：

- 从当前对话抽取了哪些 hub 优化点。
- 新增或调整了哪些 Skill、Rule、Report、入口说明。
- 哪些内容没有沉淀，以及原因。
- 已执行的引用搜索或验证命令；如果无法完整验证，说明缺口。

## Quality Bar

- 不把项目实现细节、临时命令、未验证猜测、个人隐私、公司项目内容或业务敏感信息写入 hub 通用规则。
- 不重复创建近义 Skill；能合并到现有技能时优先合并。
- 除报告文件外，不把入口文件、工具适配文件或 Skill 写成大而全的长文；保留短规则和跳转，让 LLM 通过索引逐步加载细节。
- 规则要短、可执行、可触发；避免“以后要注意”这类空泛表达。
- 新增规则必须先通过 Rule Shape Gate：明确触发条件、保护的不变量、需要检查的工件、允许的例外和验证方式；不能把一次事故、一次用户纠错或某个数字/名词直接写成规则。
- 若规则来自单个项目或单次失败，先抽象成机制名和检查流程，再决定落到现有 Skill、通用规则或项目报告；无法抽象时只写报告，不写通用规则。
- 对用户纠错必须抽象成流程改进，例如“结构调整必须同步命名审计”，而不是记录某次错误本身。
