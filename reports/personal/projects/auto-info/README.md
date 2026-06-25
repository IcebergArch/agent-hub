# Auto-Info

本文件是 Auto-Info 项目的固定上下文入口。后续 Agent 处理 Auto-Info 相关任务时，先读取本文件，再按场景读取具体报告；历史阶段报告不是默认必读上下文。

## Loading Policy

- 默认只读本文件，确认当前任务属于产品设计、重建、前端呈现、代码审查还是历史追溯。
- 只有任务命中对应场景时，才读取下方报告。
- 2026-06-04 早期规划和 review 属于阶段性历史，除非任务明确追溯旧方案、旧 token 或旧 track，否则不默认加载。

## Current Project Context

Auto-Info 的长期方向是个人情报工作站。现有沉淀显示，项目从早期“多 tab 工具箱”逐步收敛到“议题为中心的情报操作系统”：信号持续流入，议题承接长期追踪，用户判断和行动沉淀到认知层。

后续任务若涉及产品主干，应优先按“议题 / 信号 / 认知”三层判断，而不是回到按页面或工具孤立优化。

## Scenario Reports

| 场景 | 读取文件 | 何时读取 |
| --- | --- | --- |
| 换机、重建或需要完整蓝图 | [2026-06-18-关于山海情报站-rebuildBlueprint.md](refactor/2026-06-18-关于山海情报站-rebuildBlueprint.md) | 需要从长期产品定义、数据模型、API、prompt 和算法重建项目时 |
| 议题中心化产品重设计 | [2026-06-17-关于auto-info-topicCentricRedesign.md](refactor/2026-06-17-关于auto-info-topicCentricRedesign.md) | 设计 Topic、Signal、Knowledge、持久追踪线或认知沉淀时 |
| 前端主题和三问透镜 | [2026-06-16-关于auto-info-themeSystemAndThreeQuestionLens.md](refactor/2026-06-16-关于auto-info-themeSystemAndThreeQuestionLens.md) | 处理纯前端呈现层、主题系统、首页三问透镜时 |
| 可复用前端设计机制 | [2026-06-16-关于前端设计-themingAndDerivedLensPatterns.md](analysis/2026-06-16-关于前端设计-themingAndDerivedLensPatterns.md) | 需要抽象 CSS 变量主题、派生透镜层等跨项目机制时 |
| 早期 UI / 链路 / track 历史 | `refactor/2026-06-04-*`、`review/2026-06-04-*` | 只在追溯旧方案、旧 review 或检查早期决策来源时读取 |
