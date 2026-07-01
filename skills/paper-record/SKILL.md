---
name: paper-record
description: 当用户消息包含 paper record（大小写不敏感）时，将当前对话整理为思考研究报告并归档到文档工作区。
---

# Paper Record

聊天归档收尾 Skill。触发后冻结当前讨论，只做报告整理、写入、索引更新和简短确认。

## Trigger

用户消息包含 `paper record` 或 `paper-record` 时立即执行，无需二次确认。

同条消息可附 `project:`、`kind:`、`topic:`；未给出时从对话主题推断。除非用户同条消息明确要求，否则不继续展开新方案或新实现。

## Output Contract

- 先读取文档工作区 `README.md`，按其目录树选择报告位置。
- 默认 `kind` 为 `chat`；若讨论以架构/调研为主且无项目归属，可用 `analysis`。
- 文件名使用 `YYYY-MM-DD-关于<领域>-<topic>.md`。
- 正文中文，代码标识、路径、论文名保留原文。
- 顶部至少写：日期、类型、项目/范围、来源、版式。

## Report Shape

报告是思考研究报告，不是逐轮聊天日志。默认采用金字塔结构：

1. Executive Summary：一句话结论和 3-5 条关键判断。
2. Decision / Thesis：主要决策、立场或研究主张。
3. Why / Evidence：证据、约束、事实依据和必要引用。
4. Architecture / Flow / Concept Map：需要图示时用 Mermaid，图只服务理解。
5. Scenario Breakdown / Discussion：按场景、角色或争议展开。
6. Recommendations / Roadmap：建议和路线。
7. Open Questions / Follow-ups：未决问题和后续项。

按内容选择章节，不为了模板凑项。

## Workflow

1. 冻结当前聊天上下文。
2. 提取问题链、结论、否定与修正。
3. 写成研究报告，删除寒暄、过程日志、未采纳草稿和长命令输出。
4. 写入目标路径并更新文档工作区对应索引。
5. 回复报告路径、文件名和一句话摘要。

## Quality Bar

- 不把报告写入 Agent Hub 仓库内旧 `reports/`。
- 不在最终回复粘贴全文，除非用户明确要求。
- `paper record` 优先于普通归档规则。
