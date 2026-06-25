# Maxwell-AI

本文件是 Maxwell-AI 项目的固定上下文入口。后续 Agent 处理 Maxwell 相关任务时，先读取本文件，再按任务需要读取 `analysis/`、`runbooks/` 等报告；报告目录不是默认必读上下文。

## Loading Policy

- 默认只读本文件，确认任务属于项目定位、Studio UI、本地验证、知识库/RAG 测试，还是视频生成 Chat workflow。
- 命中具体场景后，再读取下方对应报告。
- `analysis/` 中的报告是专项方案或测试资料，不作为后续任务的默认上下文。

## Project Positioning

Maxwell 的核心定位是 **一站式中台能力**。底层能力应保持原子化、可组合、可按需启用，用于支持不同 Agent 的落地建设、维护和迭代。

Maxwell Studio 是集成 Maxwell 底层能力后，对外提供的友好交互层。它不是底层能力本身的全部，而是把复杂能力封装成低成本启用的工作台，让用户更容易配置、运行、观察和迭代 Agent。

## Component Boundaries

| 组件 | 定位 | 关键职责 | 生命周期语义 |
| --- | --- | --- | --- |
| Maxwell | 一站式中台能力 | 原子化能力、Agent 建设与维护、能力组合和迭代底座 | 平台级长期能力 |
| Maxwell Studio | 对外友好交互层 | 低成本启用、配置、调试、观察、管理和协作工作台 | 常驻产品体验 |
| backend server | 底层存储能力 | registry、状态、数据、artifact/source pointer、持久化读写 | 常驻基础设施 |
| admin-server | 管理与协同服务 | Studio 后端、管理面、数据同步、三方服务协调、常驻任务 | 常驻服务 |
| agent-server | Agent 运行时服务 | runtime-loop、`model.call`、tool call 编排、回执归一、trace/runtime 状态 | 可弹性创建和销毁 |
| mcp-server | 三方接口服务 | 业务 tool、外部 provider、私有适配、长任务 status/callback/artifact 语义 | 可有多个业务服务 |

当前 `agent-server` 同时承接 Studio 功能和 runtime-loop 职责。如果后续拆分，语义上更接近：

- `admin-server`：常驻服务，负责 Studio、管理面，以及需要与三方服务配合的功能，例如数据同步任务。
- `agent-server`：运行时服务，负责实际 loop、`model.call`、tool 调度和执行回执；理论上随 pod 启动、销毁、重建，不一定常驻。

当前 `mcp-server` 只有一个 Aion，但不应把它理解成唯一或通用的 MCP owner。未来可以存在 `xxx-mcp-server`、`b-mcp-server` 等多个三方接口服务；它们负责各自业务或 provider 的接口适配，Maxwell 通过稳定 tool/MCP 契约调用它们。

## Design Invariants

- Studio 是封装好的工作台，不是底层能力的唯一入口；底层 Maxwell capability 应保持可被不同 Agent 组合启用。
- backend server 承接底层存储能力；registry、状态、artifact/source pointer 和持久化读写不应散落到 runtime loop。
- 当前 agent-server 是职责混合态；讨论架构时要区分管理面常驻能力和运行时弹性 loop 能力。
- admin-server 负责 Studio 和需要三方服务协同的常驻任务；数据同步、管理动作、配置协调等不应绑定到短生命周期 runtime pod。
- agent-server 负责实际 loop、`model.call`、tool 调度和回执归一；不应依赖本地长期状态，也不应承接 Studio 管理面的长期任务。
- mcp-server 是三方接口服务族；Aion 只是当前一个实现，不是所有 MCP 能力的上限，也不应把 Aion 私有业务逻辑写进 Maxwell runtime core。
- Maxwell 的长期演进重点是能力原子化、按需启用、可观测、可维护和可迭代；Studio 重点是让这些能力对外变得易用。

## Placement Guide

后续在 Maxwell 相关需求里，如果出现“这个能力应该放在哪里”的判断，先按以下顺序定位：

1. 用户交互、配置、可视化、工作台操作：优先归属 Maxwell Studio / admin-server。
2. 持久化数据、状态、registry、artifact/source pointer：优先归属 backend server。
3. 单次 run 内的 agent loop、模型调用、tool call 编排和回执：优先归属 agent-server。
4. 外部 provider、业务接口、私有 tool、长任务 status/callback/artifact 语义：优先归属对应 mcp-server。
5. 跨 Agent 复用的底层能力：先抽象成 Maxwell 原子能力，再由 Studio 或其它入口按需启用。

## Scenario Reports

| 场景 | 读取文件 | 何时读取 |
| --- | --- | --- |
| 本地 UI 验证、Studio 联调、Knowledge 导入、接口 smoke | [2026-06-15-关于maxwell-ai-localRuntimeContext.md](runbooks/2026-06-15-关于maxwell-ai-localRuntimeContext.md) | 需要操作本地页面、复用运行态、确认测试业务空间或浏览器上下文时 |
| 视频生成 Chat workflow 和成本门禁 | [2026-06-24-关于视频生成ChatWorkflowCostGate.md](analysis/2026-06-24-关于视频生成ChatWorkflowCostGate.md) | 讨论 Maxwell Studio 宣传视频、Aion 信息范本、preview-first、overlay/timeline/cost gate 时 |
| RAG / Knowledge 导入测试背景 | [2026-06-15-关于音视频生成Agent系统-ragTestBackground.md](analysis/2026-06-15-关于音视频生成Agent系统-ragTestBackground.md) | 只在做知识库导入、切片、召回、混合检索或测试问答时读取；其业务内容是虚构测试资料 |
