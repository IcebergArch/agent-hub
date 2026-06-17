# auto-info 呈现层重构：氛围主题系统 + 三问透镜

日期：2026-06-16
类型：refactor
项目：auto-info
作者：claude
来源：auto-info 前端 apps/web-react 直读 + 用户对"个人情报工作站"产品形态的澄清
版式：金字塔结构，结论先行

## 结论先行

本次重构是**纯前端呈现层**改造，硬边界是「保留全部现有功能、不动后端」。两条主线：(1) 建立可一键换肤的**氛围主题系统**（4 套基调，CSS 变量驱动）；(2) 在首页叠加**三问透镜**，把日报数据归约到「发生了什么 / 影响是什么 / 我能做什么」的判断链路。

与 2026-06-04 旧方案（`2026-06-04-关于auto-info-uiLinkProductRefactor.md`）的关键差异——**旧方案未执行，本次方向已变**：

| 维度 | 6-04 旧方案 | 6-16 本次执行 |
|---|---|---|
| 样式体系 | 引入 Tailwind + shadcn/ui，整页迁移 | 不引入框架，复用现有 CSS 变量做主题令牌 |
| 信息架构 | 6 tab 收敛为 4 区 | 保留 6 区不动，首页内叠加三问层 |
| 快捷访问 UC-005 | 直接移除 | 保留 |
| 功能边界 | 允许合并/拆分 | 全部保留，只改呈现 |

本次定位为"低风险、可叠加、可回退"的呈现增强，不替代旧方案的中长期架构演进（Router / TanStack Query / 组件抽象仍可后续做）。

## 一、需求与硬边界

用户诉求：让工具真正辅佐个体做信息筛选与判断，降低检索与低密度信息抽取成本，让精力聚焦「发生了什么 / 影响是什么 / 能做什么」，并能持续积累。氛围"可像主题一样调整"。

硬边界（执行不变量）：
1. 保留全部现有功能（搜索 / 日报 / 时间线 / 历史 / 补齐 / 6 个页面）。
2. 不改后端 `services/api`。
3. 主题切换不破坏既有浅色界面（默认主题等同原样）。
4. 改动限定 `apps/web-react/src`，auto-info 仓库只含代码（沉淀进 Hub）。

## 二、方案与实现

### 2.1 氛围主题系统

机制：颜色/质感/字体令牌集中到 `src/styles/_theme.css`（令牌真源），通过 `<html data-theme="...">` 切换，整站 CSS 变量随之改变。可行前提是项目原本已大量使用 CSS 变量（`--bg/--surface/--ink/--accent` 等）。

4 套主题：
- `morning` 晨曦（默认浅色，等同原界面）
- `command` 指挥中心（深色科幻，青绿辉光 + 网格背景）
- `brief` 社讯干报（米白 editorial 衬线，重阅读）
- `terminal` 终端档案库（黑底磷光绿，全等宽）

扩展令牌（为收敛散落硬编码色而新增）：`--surface-raised/sunken/pill`、`--on-accent`、`--input-border`、`--warn / --warn-bg / --warn-border`、`--shadow-color`、`--glow`、`--backdrop-grid`、`--font-mono`、三问语义色 `--q-what/--q-impact/--q-action`。

状态与持久化：zustand store 增加 `theme` 字段 + `localStorage`（key `auto-info.theme`）；`ThemeSwitcher.tsx` 置于 topbar（原 `topbar-spacer` 占位改为 `topbar-tools`），切换器内含各主题三色预览 swatch。

收敛：脚本批量把 ~90 处硬编码浅色（`#fff / #fafafa / #eef0f3 / color-mix(...,#fff)` 等）替换为对应令牌，确保深色主题不露白。其中 `color-mix(..., #fff)` 这类"提亮剂"统一改为跟随 `var(--surface)`，实现浅亮深暗自适应。

陷阱（已修复）：`_base.css` 在 `@import` 链中位于 `_theme.css` 之后，其 `body { background: var(--bg) }`（shorthand）会覆盖 `_theme.css` 的 `background-image`（科幻网格叠层）。改为 `background-color` 单属性即可保留网格。

### 2.2 三问透镜

派生层 `src/lib/threeQuestions.ts`（纯函数，无副作用）从 report 真实字段提取，**不伪造数据**：
- 合并 `timeline[]` 与 `focusSections.*.events[]`，按 id/title 去重。
- 冲击分级：`severity >= 80` 高冲击 / `>= 55` 需关注 / 其余环境信息。
- 派生：headline、topSignals（按冲击排序）、impactBands（三波段计数+条形）、categoryShares、regions、actions（基于真实信号生成可执行项，指向分析/阅读/雷达/打开来源）。

视图 `src/components/ThreeQuestionLens.tsx`：三栏（01 发生了什么 / 02 影响是什么 / 03 能做什么），各栏用 `--q-*` 语义色做左边强调条。置于 `HomePage` 日报卡片**上方**，原有日报组件原样保留在下方。动作按钮通过 `setPage` 跳页或 `openExternalUrl` 打开来源。

## 三、交付物

新增：`_theme.css`、`_three-questions.css`、`ThemeSwitcher.tsx`、`ThreeQuestionLens.tsx`、`lib/threeQuestions.ts`。
改动：`useAppStore.ts`（+theme/持久化）、`App.tsx`（挂切换器+应用主题）、`HomePage.tsx`（插入透镜）、`app.css`（import 链）、`_base.css`（去重令牌+body背景修复）、6 个 `_*.css`（令牌收敛）。

## 四、验证状态（分层）

- **类型检查通过**：用 `node node_modules/typescript/lib/tsc.js -p <临时tsconfig>` 检查，本次新增/改动的 6 个 .ts/.tsx **零类型错误**；其余报错均来自未触碰的既有文件（项目历史遗留，因其用 esbuild 构建从不类型检查）。
- **未能本地构建**：node_modules 的 rollup/esbuild 是 macOS 原生二进制，在 Linux 工作区无法执行 `vite build`。
- **待用户验证**：最终视觉与交互需在 macOS 本机 `cd apps/web-react && npm run dev` 确认；4 套主题切换、三问透镜空态/有数据态、深色主题不露白。
- 离线主题预览 HTML 已单独产出供快速预览（不入库）。

## 五、约束与后续

- **构建环境约束**：Cowork Linux 工作区不能跑该项目的 vite/esbuild（原生二进制平台不符），且项目无 tsconfig.json。类型检查走 node 直跑 tsc.js。
- **遗留**：临时建的 `apps/web-react/tsconfig.check.json` 因沙箱限制未能删除，需本机 `rm` 清理。
- **后续可选**：旧方案的 Router / TanStack Query / 组件族抽象 / source-fetch 统一仍有价值，与本次呈现增强不冲突，可分阶段推进。
- 三问的冲击阈值（80/55）与 actions 派生规则为初版启发式，待真实数据回归后调参。
