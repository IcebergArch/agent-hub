---
name: paper-record
description: >-
  当用户消息包含 paper record（大小写不敏感）时，作为当前聊天归档的收尾动作，
  将当前对话整合为思考研究报告并归档到 reports/。
---

# Paper Record

## Trigger

用户消息包含 **`paper record`**（大小写不敏感，允许 `paper-record`）时立即执行本技能，无需二次确认。

`paper record` 是当前聊天归档的**收尾指令**：触发后冻结此前对话脉络，只做归档、索引更新和简短确认；不要继续展开新观点、新方案或新实现，除非用户在同条消息中明确要求。

若用户在同一条消息中给出 **topic / project-slug / kind**，优先采用；否则从对话主题推断。

## Output

生成一份 **思考研究报告**（非过程日志），写入：

```text
reports/personal/agent/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md
reports/personal/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md
reports/company/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md
```

- 个人单独与 Agent 探讨、设计或研究：`reports/personal/agent/<kind>/`
- 个人项目：`reports/personal/projects/<project-slug>/<kind>/`
- 公司项目：`reports/company/projects/<project-slug>/<kind>/`
- 默认 `kind`：**`chat`**（对话整理）；若对话以架构/调研为主且无项目归属，可用 **`analysis`**
- 文件名：日期前缀 + 语义标题，不放 kind；推荐 `YYYY-MM-DD-关于<领域>-<topic>.md`，例如 `2026-06-04-关于AgentOS-longToolRuntimeScheduling.md`、`2026-06-04-关于AI发展-codingLanguageFuture.md`；遵循 `AGENTS.md` 与 `reports/README.md`

## Report Template

正文使用中文（代码标识、路径、论文名保留原文），至少包含：

1. **元信息**：日期、类型、项目/范围、来源、版式、对话主题一句概括
2. **Executive Summary**：一句话结论与 3–5 条关键判断
3. **Decision / Thesis**：主要决策、立场或研究主张
4. **Why / Evidence**：证据、约束、事实依据和必要引用
5. **Architecture / Flow / Concept Map**：需要图示时使用 Mermaid；文字承担主要论证，图只解释结构、流程或关系
6. **Scenario Breakdown / Discussion**：按场景、角色或争议点展开
7. **Recommendations / Roadmap**：可执行建议与分阶段路线（如有）
8. **Open Questions**：未决问题与建议的下一步
9. **Follow-ups**：可选的后续归档、规则或实现项

模板项、图表、Mermaid、表格和章节名只是帮助表达的工具，不是为了凑齐而添加的目标。生成报告时优先选择最适合信息本身的表达方式，以人的可读性、清晰度、判断效率和可复用性作为核心质量指标。

删除：一次性命令输出、无关寒暄、未采纳草稿、冗长逐轮复述。


## Pyramid Structure

讨论型文档默认采用金字塔结构：

```text
结论先行
  -> 关键判断 / 决策
    -> 证据与约束
      -> 架构图 / 流程图 / 概念图
        -> 分场景展开
          -> 路线图 / 后续问题
```

图文并存，但文字大于图。该用图时用图，例如表达系统关系、状态流转、调度时序；该用文字时用文字，例如结论、取舍、风险、建议。避免为了图而图，也避免把讨论过程逐轮堆叠成日志。

## Workflow

1. **冻结**当前聊天上下文，将 `paper record` 视为本段讨论的结束信号。
2. **通读**当前会话全部用户与助手内容，提取问题链、结论、否定与修正。
3. **归纳**为研究报告，不逐条聊天记录粘贴。
4. **写入**目标路径；若目录不存在则创建。
5. **更新** `reports/index.md`：在对应项目或 General 下增加一条链接。
6. **回复用户**：给出报告路径、文件名、一句话摘要；不重复粘贴全文，不追加新的讨论分支。

## Keyword Hints（可选）

| 提示形式 | 含义 |
|----------|------|
| `project: maxwell-ai` | `project-slug` |
| `kind: analysis` | 目录 kind |
| `topic: intent-drift` | 文件名 scope-topic 部分 |

未提供时：topic 从对话主标题生成英文 slug。

## Relation to Other Rules

- 与 `AGENTS.md` 的 Plan Archiving 并存；**paper record 优先级更高**，且必须使用本模板。
- 长规则写在本技能；`.cursor/rules/paper-record.mdc` 仅作触发引用。
