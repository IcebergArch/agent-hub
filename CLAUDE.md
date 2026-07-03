# Claude Adapter

- 先遵守根目录 `AGENTS.md`。
- 读取 `README.md` 作为长期协作上下文，不要求用户每次手动附带背景。
- 任务涉及 doc-hub 下具体项目（`projects/<project>/`）的改动、调整或执行某个 SPEC 时，无需用户手动引用或导入，默认先读 `SIMP_README.md` 定位当前应执行的任务；该文件只做路由。
- 需要使用技能时，优先读取 `skills/<skill-slug>/SKILL.md`。
- 生成可沉淀的分析、审查、运行记录或需要更新项目 helper 时，按文档工作区（doc-hub）的 `README.md` 的目录树落位；日期、类型、项目、来源写入文档顶部。
- 需要项目背景时，只读取与当前任务直接相关的少数项目 helper（`<project>_proj_info.md` 或 `SPEC_<project>.md`）；报告目录只作为写入位置，不作为默认上下文来源。
- 不新增 `claude/` 目录；Claude 专属差异只维护在本文件。
