# Reports Index

本索引是发现入口，不是全量报告清单。Agent 默认先读取项目 `README.md` 或相关 Skill，再按场景读取具体报告；一次性、阶段性和历史证据文件保留在目录中，但不在根索引高频展示。

## Project Entrypoints

### Personal Projects

- [Maxwell-AI](personal/projects/maxwell-ai/README.md)
- [Auto-Info](personal/projects/auto-info/README.md)

### Company Projects

公司项目报告位于 `reports/company/projects/`，默认本地隔离且不进入 git；根索引不列具体公司项目条目。

## Agent Hub References

- [Project Refactor 常用指令摘要](personal/agent/runbooks/2026-06-08-关于AgentHub-projectRefactor.md)
- [Agent Hub 吸收 Superpowers 方法论](personal/agent/analysis/2026-06-11-关于AgentHub-superpowersMethodologyHarvest.md)
- [产品宣传视频创作经验吸收](personal/agent/analysis/2026-06-24-关于视频创作-productLaunchFilmPatterns.md)

## Loading Notes

- 项目固定事实、owner、定位和加载策略进入项目 `README.md`。
- 当前仍会影响决策的专项报告由项目 `README.md` 按场景索引。
- 阶段性方案、一次性 review、历史聊天归档和证据驻留默认不列入根索引；需要时用 `rg` 按项目、日期、主题或关键词搜索。
- 附件、fixtures、PDF、docx 等素材不默认加载，只在知识库导入、RAG smoke、文档处理或用户明确点名时读取。
