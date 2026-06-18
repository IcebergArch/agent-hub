# 山海·情报站 Rebuild 蓝图（换机可重建级）

日期：2026-06-18
类型：refactor
项目：auto-info
作者：claude
来源：auto-info monorepo 全栈代码逐文件盘点（services/api + apps/web-react）
版式：蓝图文档，结论先行，含数据模型/API契约/提示词内核/算法，达到 Codex 或 Claude 可据此重建的程度

> 用途：换台电脑、仅凭此文档 + 一个 LLM Agent（Codex / Claude），即可重建"山海·情报站"。文档固化产品哲学、信息架构、数据模型、API 契约、LLM 提示词内核、关键算法、构建运行方式。代码细节以本文为准；与记忆冲突时以仓库实际代码为准。

---

## 〇、一句话定义

山海·情报站是一个**个人情报分析引擎**，核心信念是：**它是替决策者做判断的代理人，不是给决策者布置作业的信息展示工具**。两种使用姿势——

- **烛龙·情报**（推/自动）：为关注的议题持续、定时、自动地拉取更新，主动汇集重大事件，并产出"情报秘书呈报"。
- **白泽·分析**（拉/触发）：用户抛一个对象（公司/股票/政策/市场/资料链接），系统检索关联，以"资深分析师"身份产出三维研判。

命名取自《山海经》：烛龙衔烛照幽冥（主动照见外部世界），白泽通晓万物情状（关联推演）。

---

## 一、产品哲学（重建时必须贯彻的不变量）

这是整个产品的"魂"，所有 LLM 提示词、UI 文案、信息组织都必须遵守：

1. **代理人，不是顾问**。输出必须是"我（代理人）的结论"，禁止"建议你关注/跟踪/留意/观察 X"这类把活推回用户的句式。用户只负责拍板。
   - 反例：「关注 AI 芯片供需」
   - 正例：「AI 芯片 Q3 大概率紧缺，利好相关板块，判断偏多」

2. **分析而非收集**。不能只罗列检索到的新闻。LLM 必须动用领域专业知识讲清**本质 / 运行逻辑 / 认知误区 / 风险**，即使没有近期新闻也要扎实、敢下判断。检索到的素材仅作"近期动态"分层补充，且必须与"通识判断"区分开。

3. **信息的价值在于指向行动与认知更新**（用户核心洞察）。每份研判必须包含三类决策价值：
   - **预判 → 提前布局**：接下来大概率会 X，现在可提前做的准备。
   - **感知动向 → 进退判断**：现在偏多/偏空，或"出现 Y 则切入/出现 Z 则退出"的可观察触发条件。
   - **验证逻辑框架 → 微调世界模型**：把用户已沉淀的判断（认知）喂给 LLM，对照新信息指出哪条被印证/挑战/需微调。用户写得越多，这块越准，形成正循环。

4. **近期优先，可溯历史，有脉络**。信号默认近期优先，但保留历史并按时间线呈现演进。

5. **诚实**。抓不到资料就说抓不到（标"未核实"），无 key 就提示配置，不用占位废话糊弄。

---

## 二、信息架构

最终形态 = 两个能力 part + 配置，**砍掉**早期的日报/科技雷达/快捷访问独立 tab（阅读助手并入分析）。

```
顶栏导航（仅 3 项）：
  烛龙 · 情报   (topics)    ← 默认落地页，推/自动
  白泽 · 分析   (analysis)  ← 拉/触发
  配置          (config)

烛龙·情报 页（三栏）：
  左：议题导航   — 新建议题 + 活跃议题列表 + 归档折叠
  中：信号主区   — 情报秘书呈报(置顶) → 影响指标带 → 双轴信号流(近期动态/事件脉络)
  右：认知沉淀   — 结论/逻辑/决策 输入 + 已沉淀展示

白泽·分析 页：
  主区：输入对象 → 执行 → 三维研判(分析/逻辑/操作) + 相关事件 + 关联
  侧栏：历史分析记录
```

主题系统：4 套可切换氛围（晨曦 morning 浅色默认 / 指挥中心 command 深色科幻青光 / 社讯干报 brief 米白衬线 / 终端档案库 terminal 磷光绿等宽），默认 command（暗色）。

---

## 三、技术栈与仓库结构

- **后端** `services/api`：原生 Node HTTP（`.mjs` ES Module，无框架），端口默认 4173。
- **前端** `apps/web-react`：React 18 + TypeScript + Vite 5 + zustand 4，dev 端口 5174，`/api` 代理到 4173。
- **存储**：JSON 文件为主（`data/*.json`），可选 MySQL/Neo4j（system-config 配置，不可用时降级，重建可忽略）。
- **LLM**：OpenRouter（`config.llm.apiKey`，默认模型 `openai/gpt-4.1-mini`）。
- **数据源**：免费无 key 的 Google News RSS + GDELT（核心）；可选 Google Search/Brave/Twitter（需 key）。

```
auto-info/
  package.json              # start / start:watch / start:dev / start:web-react
  server.mjs                # 转引 services/api/src/server.mjs
  scripts/start-dev.mjs     # 同时起前后端(后端带 --watch)
  data/                     # topics.json / auto-info-store.json / system-config.json ...
  services/api/src/
    server.mjs              # HTTP 入口 + 路由分发 + startAutoPull + buildTag 探针
    shared/                 # store / event-model / openrouter / utils / http ...
    use-cases/
      uc-001-daily-events/  # intake / news-sources（信息搜集源）
      uc-002-event-analysis/# buildGraph(关系图) + object-briefing(白泽研判)
      uc-007-topics/        # 山海核心：topic-model / topics-store / service / routes
                            #   / signal-match / topic-briefing / scheduler
  apps/web-react/src/
    App.tsx                 # 导航 NAV_ITEMS + PAGE_VIEWS
    stores/useAppStore.ts   # page/theme 状态 + THEMES
    pages/TopicsPage.tsx    # 烛龙·情报 工作台
    pages/AnalysisPage.tsx  # 白泽·分析
    components/TopicBriefingReport.tsx  # 秘书呈报 + 分层关联图(SVG)
    lib/topicsApi.ts        # 类型 + API 调用
    styles/_theme.css       # 4 套主题令牌
```

---

## 四、数据模型

### 4.1 Topic 实体（议题，`data/topics.json`）

```jsonc
{
  "id": "topic-<slug>-<base36时间戳>",   // 系统生成，不可变
  "title": "俄乌冲突",                    // 用户可编辑，改时重算 slug
  "slug": "俄乌冲突",
  "aliases": ["俄乌","乌克兰",...],       // 别名，用于信号匹配
  "query": "俄乌冲突",                    // 检索查询词，默认=title
  "status": "active|watching|archived",
  "createdAt": "ISO", "updatedAt": "ISO",
  "signalRefs": ["evt-id-1", ...],        // 匹配命中的事件 id（持久化）
  "pinnedSignalIds": [...],               // 用户置顶
  "knowledge": {                          // 认知沉淀（三类）
    "takeaways": [{ "id","text","createdAt","sourceSignalIds":[] }],  // 结论
    "reasoning": [...],                   // 逻辑
    "decisions": [...]                    // 决策
  },
  "lastReviewedAt": "ISO|null",           // 上次刷新/回溯
  "briefing": TopicBriefing | null        // LLM 秘书呈报(见 6.1)，无 key 时保留上次
}
```

约束（资源身份规范）：`id` 由 owning service 生成、不可变；用户输入只进 `title/query/aliases`。Topic 不复制 event 内容，只存 `signalRefs`（id 引用），事实源是 event store。

种子议题（`topics.json` 不存在时写入）：俄乌冲突 / 中东局势 / 台海局势 / AI技术 / 关税与贸易（各带 aliases，见仓库 SEED_TOPICS）。

存储：原子写（临时文件 + rename）+ 串行写锁（`mutateTopicsStore` 读-改-写在同一 Promise 链）。

### 4.2 Event 实体（信号源，`data/auto-info-store.json`，沿用既有）

字段：`id / category / region / title / summary / tags[] / severity(0-100) / special / occurredAt / timeline[] / impacts[] / references[]`。
- 冲击分级：`severity>=80` 高冲击，`>=55` 需关注，其余环境信息。
- 抓取的新闻经 intake 入库为 event；无真实来源的兜底占位事件打 `tags:["未核实"]`，前端近期榜过滤它。

### 4.3 system-config.json（关键配置）

```jsonc
{
  "sources": {
    "googleSearch": {"enabled":false,"cx":"","apiKey":""},  // 需 key，可选
    "braveSearch": {"enabled":false,"apiKey":""},           // 需 key，可选
    "twitterSearch": {"enabled":false,"bearerToken":""}     // 可选，无则走 Nitter
    // GoogleNews / GDELT 无需配置，始终可用
  },
  "llm": { "provider":"openrouter", "model":"openai/gpt-4.1-mini", "apiKey":"sk-or-..." },
  "product": { "refreshIntervalHours": 1 }   // 自动拉取间隔
}
```
LLM key 解析优先级：`config.llm.apiKey` → 环境变量 `OPENROUTER_API_KEY` → macOS keychain。

---

## 五、API 契约（uc-007-topics，PREFIX=`/api/v1/topics`）

| 方法 | 路径 | 函数 | 说明 |
|---|---|---|---|
| GET | `/topics` | listTopics | 轻量列表（含 signalCount/knowledgeCount，不含正文） |
| POST | `/topics` | createTopic | 建议题 + **立即触发 intake 搜集** + 匹配，返回 `{topic, intake}` |
| GET | `/topics/:id` | getTopic | 完整实体 |
| PATCH | `/topics/:id` | updateTopic | 改 title/query/aliases/status/pinned |
| DELETE | `/topics/:id[?hard=1]` | deleteTopic | 默认归档，hard=1 物理删除 |
| POST | `/topics/:id/refresh` | refreshTopicSignals | 仅对现有 event store 重新匹配 + 生成 briefing |
| POST | `/topics/:id/backfill` | backfillTopicSignals | **先 intake 抓新资料**再匹配（回溯历史） |
| GET | `/topics/:id/signals` | getTopicSignals | 信号集（toPublicEvent，按 severity 降序，标 pinned） |
| GET | `/topics/:id/graph` | getTopicGraph | 转调 uc-002 buildGraph(query) 关系图 |
| POST | `/topics/:id/knowledge` | addKnowledge | 追加认知 `{kind, text, sourceSignalIds?}` |
| GET | `/topics/auto-pull` | getAutoPullStatus | 自动拉取状态 |
| POST | `/topics/auto-pull` | runAutoPullOnce | 手动触发一轮 |

健康/版本探针：`GET /` 返回 `{service, status, buildTag, ui}`，buildTag 用于确认后端跑的是哪版代码。

后端路由分发顺序（server.mjs handleApi）：uc-001→002→003→004→005→006→007，逐个 `handler(...)` 返回非 false 即命中。

---

## 六、LLM 提示词内核（产品的灵魂，重建时一字不可走样地贯彻哲学）

两个 LLM 调用：议题秘书呈报（topic-briefing）、白泽对象研判（object-briefing）。都走 OpenRouter chat/completions，`temperature` 0.25~0.35，要求只返回 JSON，失败/无 key 返回 `{available:false, reason}` 由前端降级。

### 6.1 议题秘书呈报 buildTopicBriefing(topic, signals)

- 先 `splitRecentHistory`：以 `Date.now()-30天` 为界把 signals 分 recent / history。
- 取 `topic.knowledge` 的 takeaways/reasoning/decisions（前缀"结论/逻辑/决策"，最多20条）作为"待校验认知"。
- `renderMaterial`：每条 `序号.[日期 冲击N] 标题：摘要(≤160字)`，最多 24 条。

System：`你是资深领域分析师兼情报秘书，只输出合法 JSON，简体中文，动用专业知识研判而非罗列，敢下判断但不杜撰。`

User prompt 关键结构（逐行）：
```
你是决策者的资深领域分析师 + 专属情报代理人，就议题「{title}」（检索词：{query}）交付呈报。
决策者只负责拍板，你负责把功课做完、直接给判断与结论。
【铁律】禁止'建议你关注/跟踪/留意X'这类把活推回决策者的句式；你已替他把调研判断做完，直接下结论：'判断是X'、'倾向Y'、'已查到Z'。

【近期信号 · 最近30天，{n}条】 {renderMaterial(recent) 或 （近期无新信号）}
【历史/背景信号，{n}条】 {renderMaterial(history) 或 （无历史背景）}
【决策者已沉淀的认知/判断，需校验】 {priorNotes} 或 【决策者尚未沉淀认知】

信息的价值在于指向行动与认知更新。请输出 JSON（简体中文，信息密度高，禁套话，敢下判断但不杜撰具体数字/事件）：
```
输出 JSON 字段：
```
verdict        一句话研判：当前态势与最要紧的事
clusters       [{theme,gist}] 2-5个主题簇，每簇一句话
trend          态势变化：近期vs历史，升级/降温/转向
foresight      string[] 预判：1-3条，"接下来大概率会X"，不是"建议关注"
entryExit      string[] 进退判断：1-3条，"现在偏多/偏空，理由X"或"出现Y则切入/出现Z则退出"
cognitiveCheck string[] 认知校验：1-3条，对照既有判断指出被印证/挑战/需微调；无则给"我替你确立的初始判断"
conclusion     一句话拍板建议
mermaid        关联图 flowchart 源码，节点文本用["中文"]包裹，关系-->加标签，≤8节点
```
`sanitizeMermaid`：去 ```mermaid fence，必须以 graph/flowchart 开头，>1500 字则丢弃。

### 6.2 白泽对象研判 buildObjectBriefing(query, items, {sourceCount})

- `inferObjectType(query)` 分类 → 选 `TYPE_ANALYST` 人设（关键：让 LLM 用对的专业身份）：
  - company → 资深产业/商业分析师（商业模式、护城河、收入风险结构、常见误区）
  - stock → 资深二级市场策略分析师（**股价驱动逻辑：资金面/政策面 vs 基本面**，价值 vs 价格，风险）
  - policy → 资深政策研究员（真实意图、传导路径、利好谁/利空谁、误读）
  - market → 资深宏观/行业研究员（参与者结构、定价机制、周期、共识盲点）
  - url → 资深领域编辑（核心论点、证据、立场局限）
  - general → 资深情报分析师
- `isFinance = type∈{stock,market}` → 强制"价格 vs 价值 + 不构成投资建议"。
- 分类规则：url(http) → stock(股票/A股/港股/创业板/科创板/6位数字 + KNOWN_STOCKS词典:茅台/宁德时代/比亚迪/英伟达/特斯拉…) → policy(政策/新政/法规/税/补贴/关税) → market(市场/行业/赛道/指数) → company(公司/集团/科技/生物/医药/新能源) → general。

System：`你是一名{persona}，输出合法 JSON，简体中文。动用你的专业知识做研判，区分通识判断与检索事实，敢下结论但不杜撰具体数字/事件。`

User prompt 关键结构：
```
你是决策者的{persona}，也是其专属情报代理人。决策者只负责拍板，你负责把功课做完、直接给判断与结论。
就分析对象「{query}」交付研判。分析重点：{angle}
【铁律：你是代理人，不是布置作业的顾问】
- 禁止'建议你关注/跟踪/留意/观察/注意X'这类把活推回决策者的句式。
- 你已替决策者把调研和判断做完。直接下结论：'判断是X'、'倾向Y'、'已查到Z'。
- 反例：不要写'关注AI芯片供需'；正例：'AI芯片Q3大概率紧缺，利好相关板块，判断偏多'。
【两类信息来源，必须区分】A.你的专业知识(讲本质/逻辑/误区,无新闻也要扎实) B.检索到的近期资料(不相关的忽略)
{金融标的必须点明价格驱动与价值关系+不构成投资建议}
```
输出 JSON 字段（分析/逻辑/操作 三维）：
```
[分析] whatItIs(本质,2-4句) / recent(近期动态,只写相关的,0-4) / risks(关键风险,1-3)
[逻辑] logic(运行逻辑,2-5) / misconceptions(认知误区,1-3)
[操作] foresight(预判,"大概率会X",1-3) / entryExit(进退判断,1-3) / conclusion(一句话拍板)
```

### 6.3 信息搜集（news-sources.searchLatestNews）

并行调用所有 provider，去重按时间降序：
- **免费无需 key（核心，重建必备）**：Google News RSS（`news.google.com/rss/search`）、GDELT（`api.gdeltproject.org/api/v2/doc/doc`）。
- 可选需 key：Google Custom Search、Brave；Twitter 无 key 时走 Nitter 免费实例。
- `cleanLatestQuery`：用 `QUERY_NOISE_RE` 剥离口语噪声（"介绍下/分析下/这家公司/怎么样/是什么/的情况…"），让核心实体浮现（"介绍下珀乐互动这家公司"→"珀乐互动"）；"ai/人工智能"扩展为 OR 查询。
- 无外网/抓不到时 intake 兜底建一条 `tags:["未核实"]` 占位 event，保证议题非空。

---

## 七、关键算法

### 7.1 信号匹配 matchSignalsForTopic(topic)
事件语料 = `title+summary+region+category+tags` 小写。主查询走 uc-002 `matchesAnalysisQuery`；别名走本地 `aliasHitsEvent`：纯英文 ≤3 字母（ai/eu/us）用 `\b词边界` 防子串误命中，其余 `includes`。按 severity 降序取前 60。
> 注意规避：uc-002 的 `graphQueryTerms` 对别名有"ukraine 含 ai 子串触发 AI 别名误展开"的既有缺陷，所以议题别名匹配自己实现、不复用那条路径。

### 7.2 双轴信号流（前端 TopicsPage）
- 近期动态：`occurredAt >= now-30天` 且 **非"未核实"**，按 severity 降序，取前 10。
- 事件脉络：全部信号按 occurredAt 从早到晚（含历史种子如 2014/2022），带日期。
- 影响指标带：high(≥80)/watch(55-80)/ambient(<55) 计数 + 类别 top5 占比。

### 7.3 分层关联图（前端 TopicBriefingReport，无 mermaid 库）
- `parseMermaid`：先扫全图建 `节点id→中文标签` 字典（节点常单独定义、边用字母 id 引用，**必须先建字典否则显示成 D/F/H 字母**），再按 id 解析有向边。
- `layeredLayout`：拓扑最长路径分层（Kahn 变体），原因在左/结果在右，同层纵向铺开 → 因果链清晰，**不用圆环布局**（圆环会堆叠交叉）。
- SVG 渲染：贝塞尔曲线连边 + 箭头 marker + 矩形标签框（文字描边防压线）。

### 7.4 自动拉取调度（scheduler）
server 启动时 `startAutoPull`：首轮延迟 1 分钟，间隔取 `config.product.refreshIntervalHours`（默认 1h，下限 10 分钟），只为 active 议题串行 `backfillTopicSignals`，议题间隔 3 秒错峰，`timer.unref()` 不阻塞退出。`TOPIC_AUTO_PULL=off` 关闭。

---

## 八、构建与运行

```bash
# 安装
cd auto-info && npm install
cd apps/web-react && npm install && cd ../..

# 开发（同时起前后端，后端 --watch 自动重载）
npm run start:dev
#   后端 http://127.0.0.1:4173   前端 http://127.0.0.1:5174

# 单独/生产
npm start                    # 仅后端
npm run start:watch          # 后端带热重载
npm --prefix apps/web-react run build   # 前端构建

# 验证后端版本（确认跑的是新代码）
curl http://127.0.0.1:4173/   # 看 buildTag
```

**配置 LLM key**（研判/呈报必需）：写入 `data/system-config.json` 的 `llm.apiKey`，或设环境变量 `OPENROUTER_API_KEY`。数据源 Google News/GDELT 免费、无需配置。

**踩坑提醒**（重建调试必看）：
- 改后端代码必须重载才生效；用 `start:watch`/`start:dev`(已带 --watch)。判断"新前端+旧后端"的症状：白泽标题是"研判·"但内容只剩一句话（旧后端字段 whatsHappening/impact 与新前端 risks/logic/foresight 对不上，全被隐藏）。
- 端口 4173 被旧进程占用会导致新进程起到别的端口、前端代理连到旧后端 → `lsof -ti:4173 | xargs kill -9` 后重启。

---

## 九、重建验收（端到端闭环）

按此顺序验证重建成功：
1. `npm run start:dev` → `curl /` 见 buildTag。
2. 烛龙·情报：默认见 5 个种子议题；新建一个议题 → 自动触发搜集（联网时抓到真实新闻，否则兜底"未核实"占位）。
3. 配 LLM key 后点议题"刷新信号"→ 顶部出现秘书呈报（verdict/clusters/trend + 决策视角三段 + SVG 关联图，无堆叠、节点为中文）。
4. 在认知栏写一条判断 → 再刷新 → 呈报"认知校验"对照你写的判断给印证/挑战。
5. 白泽·分析输入"贵州茅台"→ 识别为 stock → 三维研判（分析/逻辑/操作，操作含拍板 conclusion + 不构成投资建议）。
6. 全程无"建议你关注X"式甩活句子；信息近期优先、脉络可溯。

> 只有"在议题内 看见信号→研判→沉淀认知→下次打开认知仍在且被校验"的完整闭环跑通，才算重建成功。
