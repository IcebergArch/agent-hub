# SIMP_README — 项目任务路由入口

本文件唯一目的：引导 Agent 找到"当前应该执行的项目任务"。不需要用户手动导入或引用本文件；只要任务涉及具体项目（doc-hub 下 `projects/<project>/` 对应的项目，例如 nextplay）的改动或调整，默认先读本文件。

`agent-hub` 目录下的其它内容（`skills/`、`agents/`、`README.md`、`AGENTS.md` 等）与本文件目的无关，不因为读了本文件而连带加载。

## 三份必读文件

1. `/Users/shatang/Project/nexus-os/doc-hub/README.md`
   目录规范：文件放哪、怎么命名。规范以该文件为准，本文件不重复其内容。
2. `/Users/shatang/Project/nexus-os/doc-hub/docs/spec/SPEC_<project>_*.md`
   历史迭代任务规格清单；当前要执行的任务就是其中一份，用下面的规则挑出来。
3. `/Users/shatang/Project/nexus-os/doc-hub/docs/spec/SPEC_<project>.md`（如存在）
   该项目长期有效的公共背景，先读它建立上下文，再看具体日期版 SPEC 的增量任务。

## 任务识别规则（哪一份 SPEC 是"新增待执行任务"）

一份 `docs/spec/SPEC_<project>_YYYY_MM_DD_NN.md` 被视为**待执行的活跃任务**，当且仅当：

- `docs/stdd/` 下不存在对应的 `STDD_<project>_YYYY_MM_DD_NN.md`（即这份 SPEC 尚无执行记录）。

判定步骤：

1. 在 `docs/spec/` 下按项目名筛出所有 `SPEC_<project>_*.md`（带日期后缀的，不含公共背景版）。
2. 按日期+序号从新到旧排序。
3. 依次检查每一份是否已有同名对应的 `STDD_<project>_*.md`；第一份"没有对应 STDD"的即为当前任务。
4. 如果全部都已有对应 STDD，说明当前没有待执行任务，等待新的 SPEC 出现。

## 使用方式（工程流程）

1. **Claude（提出任务方）**：基于用户需求，结合 `doc-hub/README.md` 的目录规范，在 `docs/spec/` 下新写一份 `SPEC_<project>_YYYY_MM_DD_NN.md`，作为本轮要执行的任务定义。
2. **Codex（执行方）**：用户在 Codex 里发起执行时，Codex 先读本文件 → 按"任务识别规则"找到待执行的 SPEC → 执行该 SPEC 描述的任务 → 执行完成后在 `docs/stdd/` 写对应的 `STDD_<project>_YYYY_MM_DD_NN.md` 作为执行记录（这份 STDD 的出现本身也标记该 SPEC 已完成，供下次识别规则使用）。
3. 执行过程中的关键日志、汇报写入 `projects/<project>/` 下对应子目录（`reports/<kind>/`、`logs/`、`runbooks/`），不写入代码仓库、不写入 `agent-hub`。
4. 各项目在 `projects/<project>/` 下的目录结构保持一致，方便跨项目浏览：

   ```text
   projects/<project>/
   ├── <project>_proj_info.md
   ├── reports/<kind>/
   ├── runbooks/
   ├── logs/
   └── assets/<collection>/
   ```
