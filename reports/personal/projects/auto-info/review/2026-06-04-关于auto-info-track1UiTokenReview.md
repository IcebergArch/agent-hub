# auto-info Track 1 界面交互审查与令牌修复

日期：2026-06-04
类型：review
项目：auto-info
来源：Track 1 静态代码审查（6 个页面 + 共用组件 + apps/web-react/src/styles/app.css）对照 spec/constraints/UI-DESIGN.md；环境受限（浏览器扩展未在线 + 沙盒断网无法装无头浏览器），未做截图复核
版式：金字塔结构，结论先行

## 结论先行

Track 1（界面交互重构）的**框架层已基本落地**，本轮真正的缺口在**设计令牌层**。6 个页面的布局比例（首页/阅读/分析/科技雷达 8:2、快捷访问 2:8）、WorkspaceHero、ReportSection、SourceJumpLink、固定输入区、移动端导航横滚不换行、配置页 h2 版式，均已符合 UI-DESIGN.md，无需改动。

审出并已修复 3 个令牌层问题（均为「代码偏离 spec 令牌表」，UI-DESIGN.md 令牌表本身正确，故只改代码不改 spec）：

- **P0 真实视觉 bug**：`--positive` / `--negative` / `--neutral` 三个极性令牌在 `:root` 中从未定义，却被状态 pill、事件线极性箭头、关系正负向标签等大量 `var(--positive)` 引用 → CSS 变量无值导致整条声明被丢弃，「以颜色表达积极/消极影响」的设计意图实际失效。
- **P1 字体令牌缺失 + 非苹果栈**：令牌表规定 `--font` 用 SF/PingFang 系统栈，但 `:root` 无 `--font` 令牌；字体栈以 `Inter` 打头（且 index.html 未加载，等于加载不到的字体当首选），全中文产品却缺中文苹果字体 PingFang。
- **P1 硬编码 hex**：三处硬编码 `#d70015`，与「禁止随意 hex、统一用令牌」冲突，且与未定义的 `--negative` 并存。

## 一、审查范围与方法

- 视角：按 agent-hub `agent-team-router` 路由，命中 `product_ui_feature_architecture`，以 UX_Product_Design / Product_Workflow / Application_Architect 视角审查。
- 范围：`pages/`（HomePage、AnalysisPage、ReadingPage、TechRadarPage、ConfigPage、QuickAccessPage）+ `components/report/*`（WorkspaceHero、ReportSection、SourceJumpLink）+ `styles/app.css`（3592 行）。
- 基准：`spec/constraints/UI-DESIGN.md`（NORMS 层，SSOT）。

## 二、符合约束（无需改动）

- 布局比例：home-grid `1fr minmax(220px,280px)`、reading/analysis/tech-radar `8fr 2fr`、quick-access `2fr 8fr`，均符合 8:2 / 2:8。
- 共用组件：WorkspaceHero（tone: default/accent/muted）、ReportSection、SourceJumpLink（仅 ↗ 跳转来源）已统一使用。
- 输入区固定、侧栏仅放历史/筛选、不在侧栏重复正文：HomePage / ReadingPage / AnalysisPage 均落地。
- 移动端顶栏导航：`overflow-x:auto` + `white-space:nowrap`，符合「横向滚动、不拆字换行」。
- 配置页：`panel` + `h2` + `result-text` 版式，符合令牌表对配置页的专门规定（不需要 WorkspaceHero）。

## 三、问题与修复（均在 app.css）

| 问题 | 修复 |
|---|---|
| `--positive`/`--negative`/`--neutral` 未定义 | `:root` 补 `--positive:#248a3d`、`--negative:#d70015`、`--neutral:#6e6e73`（对齐令牌表） |
| `--font` 未定义、字体栈以 Inter 打头、缺 PingFang | 新增 `--font` 令牌（-apple-system / SF Pro Text / PingFang SC / …），`font-family: var(--font)` |
| 三处硬编码 `#d70015`（quick-access-dir-remove:hover、error-text、event-lines-node-core） | 统一改为 `var(--negative)` |

## 四、验证

- `npm run audit`：通过（唯一警告为待删的 REFACTOR-PLAN.md，与本轮无关）。
- `npm run verify`：通过，全部核心接口 200，服务持续可用。
- CSS 静态校验：大括号配平（541/541）；全文件**零「引用但未定义」令牌**（修复前 `--positive`/`--negative` 即为缺失项）。
- `npm run test:unit`：除 `test-tech-radar`（纯后端数据测试，因沙盒降级缓存数据 recent=0 而 fail，与 CSS 改动无因果）外均通过。
- 受限项：前端 `vite build` 因沙盒缺 Linux 版 rollup 原生模块未能执行；截图复核因浏览器扩展未在线未能进行。建议在本机补 `npm --prefix apps/web-react run build` 与逐页截图 Design 审查。

## 五、后续建议

- warn 态仍有硬编码（`#9a6700` / `#fff6e8` 等），令牌表未列对应令牌，未扩范围；若要彻底令牌化，建议先在 UI-DESIGN.md 增补 `--warn` 类令牌再收口。
- `App.tsx` 与 `app.tsx` 为大小写重复的同一文件，违反「唯一实现禁双份」铁律，建议归入 Track 3 系统清理。
- 本机环境下补齐 build + 截图 Design 审查，确认极性色与字体在真实渲染中生效后，将 UI-DESIGN.md 审查记录由「待执行」更新为「通过」。
