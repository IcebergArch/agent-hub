# auto-info Track 3 系统清理 + Track 2 spec 收敛执行记录

日期：2026-06-04
类型：refactor
项目：auto-info
来源：全面重构第二阶段执行（承接 Track 1 令牌修复），对齐三轨规划 + 三重角色情报系统定位（开发者 / AgentOS 开发者 / 投资者）
版式：金字塔结构，结论先行

## 结论先行

承接 Track 1 令牌修复，本阶段完成了**沙盒环境内可确凿验证**的两类重构：Track 3 系统层清理（删废弃/重复/僵尸文件）与 Track 2 在 spec/SSOT 层的边界收敛（修规格冲突、补缺失契约）。全程 audit / verify 门禁通过。

关键判断：auto-info 的 spec 已高度成熟，绝大多数 Track 1/2 设计决策的代码已落地；本轮真正的缺口是**结构治理**与**规格自洽**，而非功能重写。情报结构（日报四块、分析三维泳道、雷达 AI 热词）天然贴合三重角色，后续收敛重点应是信号质量与优先级，而非改动数据结构。

## 一、Track 3 系统层清理（已执行）

| 删除项 | 依据 | 验证 |
|---|---|---|
| `services/api-py/`（7 个空壳 `__init__.py`） | DIRECTORY-STRUCTURE L77「禁止 api 与 api-py 双后端」；PROJECT-CLEANUP「禁止双份/僵尸」 | api-py 已无 |
| `apps/web-react/src/app.tsx`（与 App.tsx 大小写重复，main.tsx 仅引 App.tsx） | LAW/NORMS「唯一实现禁双份」；macOS 大小写不敏感下二者会互相覆盖 | app.tsx 已无 |
| 根目录 `REFACTOR-PLAN.md`（自述作废可删） | PROJECT-CLEANUP「禁止僵尸文件」；audit 曾对其告警 | 已删，audit 警告消失 |

结果：`npm run audit` 由「1 条警告」转为「通过，结构符合 STDD 规范」。

## 二、Track 2 spec 层收敛（已执行）

1. **修规格冲突**：REQUIREMENTS.md `REQ-F-005` 原称「优先在『感兴趣方面』中展示」，与 `UC-001`（已废除感兴趣方面、正文固定国际/科技/金融/国内/其他）冲突。已改写 REQ-F-005 为「关注类别仅用于 intake 检索与库内匹配的相关性加权，不再生成独立段落」，并加说明对齐 UC-001。
2. **补缺失契约**：此前 6 个 UC 中唯独 UC-005 无 API 契约文件。已新建 `spec/contracts/API-uc-005-quick-access.md`，记录其「前端 localStorage 为主 + 后端仅 localhost 调试反代」的边界，并明确 embed 端点不得作为主路径。

## 三、诊断结论（固化，供后续复用）

- **embed-proxy 不可删**：UC-005 spec 明确「embed 代理仅保留供兼容或调试」，是有意保留的 localhost 反代，前端不依赖即合规。
- **三重角色映射**：日报「金融」块 + 分析「金融」泳道 ↔ 投资者；日报「科技」块 + 分析「科技」泳道 + 科技雷达 ↔ 开发者 / AgentOS 开发者。结构已就位，收敛点在信号质量。
- **后端结构健康**：`services/api` 按 UC 清晰分层（routes/service + 领域 mjs），无需重构骨架。

## 四、门禁与环境约束

- `npm run audit` ✅、`npm run verify` ✅（全部核心接口 200，服务持续可用）。
- `npm run test:unit`：`test-tech-radar` 因沙盒为本地降级缓存数据（recent=0）而 fail，与本阶段改动无因果（纯后端数据测试）；本机真实数据下应通过。
- 环境受限：Cowork 沙盒断网（npm registry 403）无法 `vite build` / 装无头浏览器截图；浏览器扩展用 OpenRouter 无法接入。

## 五、补充执行（app.css 模块化拆分，已完成）

将 `app.css`（3597 行巨石文件）按**连续行区间字节等价**切分为 7 个子模块，主文件改为 7 条 `@import`（PostCSS/Vite 构建期按序内联，层叠顺序与原文件完全一致）：

| 子文件 | 行数 | 范围 |
|---|---|---|
| `_base.css` | 185 | 令牌(:root)、视口高度链、workspace-hero、app-brand |
| `_nav-controls.css` | 278 | topbar、segmented、panel、btn、card、input |
| `_quick-access.css` | 209 | 快捷访问页全部样式 |
| `_reading.css` | 166 | 阅读助手简报/列表 |
| `_layout-grids.css` | 551 | home/reading/analysis/tech-radar 栅格、搜索区、backfill |
| `_report.css` | 1421 | daily-card / report-* 卡片族（后续可再细分） |
| `_report-document.css` | 787 | 统一报告版式、时间线、event-lines、status-pill、modal |

验证（沙盒内）：7 文件拼接**逐字节等于原文件**；各文件大括号配平；跨文件零未定义令牌；`@import` 目标齐全；audit/verify 通过。**仍需本机 `vite build` 最终确认 `@import` 渲染产物。**

文案一致性：盘点 6 页面 + 组件的空/错/载文案，现状已达标（加载「加载中…」、空「暂无 X」、错误走 `error-text`+后端 message），符合 UI-DESIGN 状态表，无需强改。

## 六、视觉风格升级（已执行，肉眼可见）

应用户「页面看不到变化」反馈，做了一组**可见的**情报台质感升级（守苹果克制 + 令牌）：

- **workspace-hero**（所有主任务页顶部）：浅品牌蓝 135° 渐变底 + 左侧 3px accent 强调竖条 + 阴影；eyebrow 改为蓝底胶囊标签（大写）。
- **数据胶囊 focus-pill**：卡片浮起感（阴影）+ hover 上移；数值用等宽数字（tabular-nums），情报数字对齐更专业。
- **历史项 history-item-btn**：hover 浅蓝底 + 上移 + 阴影；选中态(is-active)加 3px accent 左条（inset shadow）+ 浅蓝底。
- **事件卡片 report-event-card**：hover 上移 + 强化阴影（合并了原有重复的 :hover 规则）。
- **令牌化**：tag 及 7 处 `#eef4fc` 高亮底统一改为 `color-mix(var(--accent) 10%, #fff)`，消除硬编码浅蓝。

验证：各子文件大括号配平、跨文件零未定义令牌、零 `#eef4fc` 残留、audit/verify 通过。

## 七、Track 2 合并/拆分决策表落地核查（重要更正）

经逐条核查代码（非凭印象），Track 2 决策表 6 条**合并/拆分早已在此前迭代落地**，本次确认其符合：

| # | 决策 | 证据 |
|---|---|---|
| 1 | 时间线展示合并 / 业务模型拆分 | 雷达 `TechRadarNestedTimeline` 复用 `event-timeline-*` CSS 类 + `TimelineRefreshCutoff` 组件，但保留嵌套树业务结构（日报为扁平）——正是「合并展示、拆分模型」 |
| 2 | 阅读助手输入合并 / 来源类型拆分 | 单输入框；后端按 web/pdf/video/audio 显式分支 sourceType |
| 3 | 雷达↔阅读简报复用 / 入口拆分 | briefingSessionId 深链 + openReadingSession / consumePendingReadingSession |
| 4 | 日报↔分析关联 / 任务拆分 | 各自独立页，无互嵌 |
| 5 | 配置与链路状态同页 / 独立 Tab | ConfigPage 链路状态 + 配置弹窗 |
| 6 | 快捷访问拆分 / 禁 iframe | 前端纯 window.open，不依赖 embed |

结论：**Track 2 无待执行的合并/拆分**；强行把 EventTimeline 与雷达时间线塞进单组件会破坏 spec 要求的「业务模型拆分」，属为改而改。

## 八、死样式清理（_report.css 僵尸 daily-* 类）

核查发现 `_report.css` 存在一整套 `daily-*` 样式（daily-event-card / daily-overview-* / daily-direction-* / daily-focus-* / daily-interest-* / daily-ref-* / daily-report-section* / daily-footnote-list / daily-report-lead / daily-event-summary / daily-event-pill），在所有 tsx 中**零引用**——早被 `report-*` 取代的遗留僵尸样式（违反 PROJECT-CLEANUP「禁止僵尸文件」、唯一实现原则）。

已精确按规则块删除 31+3 处，保留仍在用的 daily-card 外壳系列 + daily-report-detail/timeline。`_report.css` 由 **1421 → 1182 行**（-239）。验证：残留 12 个 daily-* 类全部仍被 tsx 使用、配平 OK、零未定义令牌、audit/verify 通过。

## 九、后续（需在本机执行）

- **重启前端 dev server**（从单文件改为 @import 多文件结构后，HMR 可能未热重载，需重启 + 硬刷新才能看到上述视觉变化）。
- 本机 `npm --prefix apps/web-react run build` 确认 CSS @import 拆分渲染无误 + 逐页截图 Design 审查。
- Track 2 续：日报与科技雷达的信号排序 / 过滤可向三重角色加权（需真实数据验证效果）。
- 收尾：本机 `npm --prefix apps/web-react run build` + 逐页截图 Design 审查；确认无回退后，把三轨 spec 文档（THREE_TRACK / Track1 README / PRODUCT_FUNCTION_RESTRUCTURE）审查记录从「待执行」更新为实际结论。
