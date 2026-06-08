# auto-info UI / 链路 / 产品功能重构方案

日期：2026-06-04
类型：refactor
项目：auto-info
来源：auto-info 仓库现状调研（前端 apps/web-react + 后端 services/api）+ 对齐项目已有三轨重构规划
版式：金字塔结构，结论先行，图文并存

## 结论先行

auto-info 已处于「成熟功能原型期」，**不是前后端待建的原型**：六个用例域（UC-001~006）前端可用、后端 `services/api`（Node）完整实现并接通 MySQL / Neo4j / OpenRouter / 多信源，且已有 STDD 规格、测试门禁与三轨重构规划。因此本次「UI + 链路 + 产品功能」重构的正确定位不是另起炉灶，而是**落地项目已规划但未执行的三轨方案**：先稳定产品功能边界（Track 2），再重构界面交互（Track 1），最后收敛系统代码（Track 3）。

关键判断：

- 不需要新建后端，更不该迁移到 FastAPI；真后端是 `services/api` 的 Node 实现，`services/api-py` 是废弃空壳，应删除。
- 重构最大价值在三处：统一来源抓取与错误响应、前端报告/时间线/历史组件族复用、显式标注数据存储边界。
- 信息架构层面，日报 / 分析 / 科技雷达存在「时间线」概念重叠，可在展示层合并、业务数据层拆分，降低用户认知负担与代码重复。

## 一、现状（事实基线）

### 1.1 技术栈与目录

- 前端：`apps/web-react`，React 18 + Vite 5 + Zustand，纯手写（无路由、无数据层、无 UI 库），单文件 `app.css` 3593 行。
- 后端：`services/api`，Node 原生 HTTP（无框架），`server.mjs` 顺序挂载 6 个 UC handler，统一 `/api/v1/*` 前缀并兼容旧路由。
- 规格与流程：`spec/`（SSOT）、`docs/architecture`、`docs/rules`、`docs/iterations`；测试门禁 `npm test`（verify + 17 单测 + features）。
- 存储：MySQL（结构化事件 / 日报归档，CLI 调用）+ Neo4j（事件图谱，HTTP API，不可用时内存降级）+ 本地 JSON（`data/*.json` 降级与缓存）+ `system-config.json`（运行时配置）。

### 1.2 六个用例域

| UC | 职责 | 关键依赖 |
|---|---|---|
| UC-001 日报 | 当日重大事件日报，四块（国际/科技/金融/国内）组织、归档、补齐 | store.json、news-sources、MySQL |
| UC-002 分析 | 按主题构建事件关系图（节点/边/权重/维度）、历史、Neo4j 同步 | store.json、MySQL、Neo4j、intel |
| UC-003 阅读 | 网页/PDF/音视频/正文输入→中文摘要→历史 | source-fetch、LLM(OpenRouter) |
| UC-004 配置 | 读写系统配置、链路状态、降级标志 | system-config.json、连接检测 |
| UC-005 快访 | URL 启动器，新窗口打开（无 iframe 预览） | embed-proxy |
| UC-006 雷达 | AI 技术动态时间线，日/周/月/年分段、月报归档 | tech-radar-store、阅读简报 |

### 1.3 必须保留的技术壁垒

复杂度高、无现成替代，重构时整体封装保留、不重写算法：事件聚类（`event-lines-cluster.ts`）、非线性时间缩放（`event-lines-time-scale.ts` / 后端 `time-axis-scale.mjs`）、多线时间轴 SVG（`EventLinesChart.tsx`，600+ 行）、嵌套时间线组装（`TechRadarNestedTimeline.tsx`、`techRadarTimelineFromFeed.ts`）。

### 1.4 核心问题

1. 信息架构松散：6 个 tab 平铺；日报/分析/雷达三处「时间线」概念重叠；快访是本地工具混入情报产品。
2. 前端无复用：「Hero + 主体 + 历史侧栏」在 4 个页面各写一遍；报告/时间线/历史列表组件未抽象。
3. 数据流原始：`fetchJson` 裸调散落，无缓存/去重/统一错误；轮询每页手写。
4. 链路不统一：UC-001 爬虫、UC-003 抓取、UC-006 月报修复各自处理失败，无统一错误格式与来源抓取模块。
5. 存储边界不清：代码未显式标注「主存 vs 降级」，不利后续演进。
6. CSS 失控：3593 行单文件、400+ class、`#hex` 与 `color-mix()` 混用、重复 class。
7. 废弃残留：`services/api-py` 空壳、README 与实际产品曾经失真（已修正为 Nexus Auto-Info）。

## 二、对齐项目已有三轨规划

项目 `docs/iterations` 已定义三轨，推荐执行顺序 Track 2 → Track 1 → Track 3。本方案完全沿用，不另立体系：

- **Track 2 产品功能边界**（`PRODUCT_FUNCTION_RESTRUCTURE.md`）：明确 6 个 UC 职责、决策合并/拆分、冻结 API 契约与功能测试口径。
- **Track 1 界面交互**：功能地图稳定后重构布局、组件规范、交互状态。
- **Track 3 系统代码升级**（`SYSTEM_REFACTOR_UPGRADE.md`）：P1 API 错误/来源抓取统一 → P2 前端组件复用 → P3 数据层边界 → P4 测试自动化。

## 三、重构方案

### 3.1 产品与信息架构（Track 2）

定位：「我的情报台——每天自动汇集值得关注的事件与技术动态，可深读、可追溯、可分析。」

导航建议（6 tab → 收敛）：

```
今日 Today      ← 默认落地页，聚合 UC-001 日报 + UC-006 今日雷达
时间线 Timeline ← 展示层合并 UC-002 分析 + UC-006 往期雷达（业务数据仍分开）
阅读 Reading    ← UC-003，保持独立
设置 Settings   ← UC-004 配置 +（UC-005 快访降级为「常用链接」）
```

跨域串联提升价值：今日/雷达条目一键「深读」生成简报（强化现有 `openReadingSession`）；简报实体一键「在时间线展开」；全局命令面板（⌘K）统一「检索入库 / 分析主题 / 读链接」入口。

> 合并力度需产品决策：若分析与雷达业务差异过大，可保留「今日 / 分析 / 科技雷达 / 阅读 + 设置」四 tab。

### 3.2 前端架构与 UI（Track 1，允许引入主流库）

| 关注点 | 选型 | 理由 |
|---|---|---|
| 路由 | React Router v6 | tab → 真实路由，支持深链/前进后退/分享 |
| 数据层 | TanStack Query | 统一缓存/去重/轮询/loading/error，替代裸 fetch 与手写 setInterval |
| 样式 | Tailwind + 设计 token | 收敛 3593 行 CSS；现有 CSS 变量映射为 theme |
| 通用组件 | shadcn/ui（按需） | Modal/Tabs/Button/Toast 不再手写 |
| 全局状态 | 保留 Zustand | 仅管 UI 态（命令面板/主题）；服务端态交给 Query |
| 图表 | 保留现有 SVG | 壁垒资产，封装为受控组件 |

目录重组（feature-based）：`app/`（路由+Provider+布局）、`features/{today,timeline,reading,settings}/`、`shared/{api(zod schema 类型层),components(PageShell/HistoryRail/WorkspaceHero),viz(保留壁垒),hooks}`、`styles/`。

复用抽取（对齐 Track 3 P2）：从至少两个真实调用点抽 `<ReportSection>`、`<EventTimeline>`、`<HistoryList>`、`<SourceLinks>`。

### 3.3 链路与系统代码（Track 3）

- P1 统一来源抓取：UC-001/003/006 的抓取收敛到共享 `source-fetch` 模块；错误统一为 `{ error, reason, fallback }`。
- P1 前端组件族复用：同 3.2 抽取。
- P3 存储边界：在 `store.mjs` 与各 UC service 显式标注「主存（MySQL/Neo4j）vs 降级（JSON）」，为后续迁移留接口。
- P4 测试自动化：新增组件/接口纳入 `npm test` 门禁，保证不回归。
- 清理：删除 `services/api-py` 废弃空壳；收敛重复 class 与旧组件。

## 四、分阶段实施计划

| 阶段 | 对应轨道 | 内容 | 可验证 |
|---|---|---|---|
| P0 | — | 冻结 API 契约（基于现有 6 UC）+ 设计 token；删 api-py | 评审通过、npm test 绿 |
| P1 | Track 2 | 产品功能边界与导航收敛（今日/时间线/阅读/设置） | 功能地图评审 |
| P2 | Track 1 | 前端地基：Router + TanStack Query + shared/api 类型层 + PageShell/HistoryRail | 旧功能不回归 |
| P3 | Track 1 | UI/视觉重构：Tailwind 化 + 命令面板 + 组件族复用 | 截图对比、交互走查 |
| P4 | Track 3 | 链路统一：source-fetch 收敛 + 错误格式 + 存储边界标注 | 各 UC 端到端 + npm test |
| P5 | Track 3 | 收尾：清理旧 CSS/组件、补文档、自测 | 构建通过、无死代码 |

每阶段先做、再交付验证，确认后进下一阶段；遵循项目铁律「先 spec 后代码、密钥不入库、npm test + 双端在线、唯一实现禁双份」。

## 五、决策结论（2026-06-04 已拍板）

1. 信息架构：**合并为 4 区 —— 今日 / 时间线 / 阅读 / 设置**。今日 = UC-001 日报 + UC-006 今日雷达；时间线 = UC-002 分析 + UC-006 往期雷达（展示层合并、业务数据分开）；阅读 = UC-003；设置 = UC-004。
2. CSS：**引入 Tailwind + shadcn/ui**，整页迁移、收敛 3593 行 app.css。
3. 起步顺序：**前端地基先行**（React Router + TanStack Query + 组件抽象），再做 UI 重构，最后链路收尾。即实际顺序 P0 → 前端地基 → UI 重构（含 4 区导航）→ 链路收尾。
4. 快捷访问（UC-005）：**直接移除**（前端页面 + 后端 uc-005 路由一并清理）。

> 因「快访移除」，4 区不再包含快访任何形态；UC-005 后端 `services/api/src/use-cases/uc-005-quick-access` 在链路收尾阶段删除。

## 验证与后续事项

- 本方案基于直读 `services/api`（server.mjs + 10 个 shared 模块 + 6 UC routes/service）、前端 6 页面与核心组件、以及 `docs/iterations` 三轨规划得出，已更正早期「后端为空、需建 FastAPI」的错误判断。
- 后续：待用户对「五、待决策项」拍板后，从 P0 落地；落地过程中的执行明细按项目约定写入 `docs/iterations/`，长期可复用结论回流本报告。
