# Reports

报告是长期保留的输出，不区分由哪个工具生成。

推荐路径：

```text
reports/
  personal/
    agent/
      feature/
      fix/
      refactor/
      analysis/
      review/
      runbooks/
      chat/
    projects/
      <project-slug>/
        README.md
        feature/
        fix/
        refactor/
        analysis/
        review/
        runbooks/
        chat/
  company/
    projects/
      <project-slug>/
        README.md
        feature/
        fix/
        refactor/
        analysis/
        review/
        runbooks/
        chat/
```

项目目录下的 `README.md` 是该项目的固定上下文入口，用于保存 Agent 处理该项目时应先知道的定位、owner、长期边界和加载说明；`analysis/`、`runbooks/` 等报告目录不默认成为必读上下文，只在任务需要历史证据、专项方案或用户明确要求时按需读取。

## Loading Levels

| 层级 | 文件 | 是否高频加载 | 用途 |
| --- | --- | --- | --- |
| Hub 入口 | `AGENTS.md`、根 `README.md`、`agents/<tool>.md` | 是 | 通用规则、长期协作上下文和工具差异 |
| 项目入口 | `reports/*/projects/<project-slug>/README.md` | 项目相关任务时加载 | 项目定位、owner、固定边界、场景报告索引 |
| 精选索引 | `reports/index.md` | 低频发现入口 | 指向项目入口和少数仍有决策价值的 Agent Hub 参考 |
| 场景报告 | `analysis/`、`runbooks/`、`feature/`、`review/` | 否，按场景加载 | 专项方案、操作手册、审查报告、阶段结论 |
| 冷历史 | 早期方案、阶段性 review、聊天归档、证据驻留 | 否，仅搜索读取 | 保留历史证据，不作为默认工作上下文 |
| 附件与 fixtures | `attachments/`、`knowledge-fixtures/` | 否，仅点名读取 | 文档处理、RAG smoke、知识库导入测试素材 |

根 `reports/index.md` 不是全量 catalog。一次性、阶段性、后续不会频繁使用的报告应保留在目录中，依靠文件名、front matter 和 `rg` 搜索发现，不进入根索引高频展示。

## Report Lifecycle

个人报告和公司报告都必须有生命周期：

- `active`：当前仍会影响默认判断，可进入项目 `README.md` 场景索引。
- `scenario`：只在特定任务、关键词或用户点名时读取。
- `cold`：一次性、阶段性、历史证据或低概率复用内容，不进根索引。
- `deprecated`：已被新结论替代，保留前应说明替代来源；确认无引用后可删除。

维护报告目录时，优先更新项目 `README.md` 的场景索引和生命周期说明；不要把所有报告都追加到根索引。

`kind` 用来表达报告主意图：新增能力用 `feature`，缺陷修复用 `fix`，结构调整用 `refactor`，事实调研用 `analysis`，代码审查用 `review`，操作手册用 `runbooks`，对话整理用 `chat`。

新文件命名使用语义标题 `YYYY-MM-DD-关于<领域>-<topic>.md`，例如 `2026-06-04-关于AgentOS-longToolRuntimeScheduling.md`、`2026-06-04-关于AI发展-codingLanguageFuture.md`；日期、类型、项目、来源、版式等元信息写入文档顶部。个人单独与 Agent 探讨、设计或研究使用 `reports/personal/agent/<kind>/`；个人项目使用 `reports/personal/projects/<project-slug>/<kind>/`；公司项目使用 `reports/company/projects/<project-slug>/<kind>/`。存量报告在整理时也应同步迁移到该命名，避免同一目录混用两套规则。

`reports/company/projects/` 默认不进入 git 提交；只有报告对应开源项目，且经过明确 diff 审查确认不含私有信息时，才允许单独纳入提交。`reports/personal/agent/` 与 `reports/personal/projects/` 保留为普通可提交内容。

归档内容应保留可复用结论、范围、验证和后续事项；不要把过程日志、本地路径、临时命令输出或未采纳草稿写进长期报告。

## Front Matter

新归档报告顶部应包含：

```text
日期：YYYY-MM-DD
类型：analysis|review|chat|feature|fix|refactor|runbooks
项目：personal-agent|<project-slug>
来源：<对话、调研、审查或执行来源>
版式：金字塔结构，结论先行，图文并存
```

讨论型文档默认使用金字塔结构，先给结论和关键判断，再给证据、图示、分场景分析和路线图。图文并存，但文字应承担主要论证，图只用于解释结构、流程或关系。
