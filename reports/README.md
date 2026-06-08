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
        feature/
        fix/
        refactor/
        analysis/
        review/
        runbooks/
        chat/
```

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
