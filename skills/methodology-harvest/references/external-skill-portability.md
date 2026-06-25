# External Skill Portability

日期：2026-06-11
来源：吸收 Superpowers `using-superpowers/references/*-tools.md`、`agents/openai.yaml`、`writing-skills/render-graphs.js`、`graphviz-conventions.dot`、系统调试压力测试与 creation log 后整理。
用途：当 Agent Hub 导入外部 skill、plugin 或 agent workflow 时，用于把平台绑定资源转成 Hub-native 规则、reference 或 no-copy 决策。

## Core Rule

外部 skill 的方法论可以迁移，外部工具名、agent manifest、运行时脚本和视觉生成链路不能直接当成 Hub 事实。先识别资源承担的功能，再映射到当前工具的真实能力；没有等价能力时写成 `watch` 或 checklist，不伪造执行入口。

## Cross-Tool Compatibility Gate

Hub 公共资产默认要同时服务 Codex 和 Cursor。迁移外部 skill 时，先把平台能力分成三类：

| Class | Landing |
| --- | --- |
| Codex/Cursor 都成立的不变量、触发、输出和验证语义 | 公共 `SKILL.md`、`AGENTS.md` 或 reference |
| 只有某个工具具备的 UI、MCP、浏览器、线程、插件或权限能力 | `agents/<tool>.md` 或对应工具规则 |
| 当前工具都没有同等执行语义，但未来可能接入 | `watch`，并写清复核触发条件 |

不要把外部平台的强能力伪装成 Hub 公共能力；公共文档应写能力语义和 fallback，真实工具名由 adapter 承接。

## Portability Classes

| External resource | Function | Hub treatment |
| --- | --- | --- |
| Tool mapping docs | 把 `Task`、`TodoWrite`、`Read/Edit/Bash/WebSearch` 等外部工具名映射到当前平台 | 抽成能力语义：计划、文件读写、搜索、shell、联网、subagent、后台进程；由当前 agent adapter 决定具体工具 |
| Agent manifests | 给 skill 提供 UI 名称、短描述或可派发 agent 类型 | 不复制 manifest；Hub 使用 `SKILL.md` frontmatter、`skills/README.md` 和现有工具适配文件表达可发现性 |
| Prompt templates | 给 subagent/reviewer/implementer 填充角色、输入、输出格式 | 先抽成 checklist、brief schema 或 review rubric；只有 Hub 具备同等 subagent 调度语义时才模板化 |
| Runtime scripts | 浏览器 companion、graph render、测试 helper、polluter finder 等外部执行脚本 | 默认不复制；抽成诊断流程、视觉判断、验证步骤或脚本接口要求 |
| Scratch artifacts | task brief、review diff、implementer report、progress ledger 等委派过程文件 | 若文件化交接确有价值，落到工作树内的自忽略 scratch 目录；不写进 `.git/`、宿主受保护路径或会被误提交的位置 |
| Pressure tests | 验证 skill 在时间压力、沉没成本、权威压力下是否仍遵守流程 | 抽成 `skill-pressure-test-template.md` 场景模式；高约束 Skill/Rule 可复用 |
| Creation logs | 记录外部 skill 如何从材料提炼、压缩、bulletproof | 抽成 skill authoring checklist；不把历史过程日志搬进 Hub |
| Visualization conventions | Graphviz/DOT、渲染脚本、流程图形状约定 | Hub 优先用 Mermaid 或当前文档可渲染格式；只有确有 Graphviz runtime 和复用需求时才新增脚本 |
| Install/community/license docs | 安装、社群、许可和分发信息 | 只记录来源、版本和 no-copy 理由；不作为 Hub 运行入口 |

## Tool Capability Map

移植外部 skill 时，先映射能力，不映射名称：

| Capability | Hub question | Default handling |
| --- | --- | --- |
| File read/search/edit | 当前工具是否有受控读写和 patch 能力？ | 按当前工具规则使用 `rg`、读文件工具和 patch；不沿用外部 `Read/Edit/Write` 名称 |
| Shell execution | 命令是否需要网络、写越界、GUI 或破坏性权限？ | 按当前 sandbox/approval 规则执行；外部脚本命令只作参考 |
| Planning | 当前环境是否有计划状态工具？ | 有则用计划工具；没有则在回复或文档中维护短 checklist |
| Subagent dispatch | 当前环境是否真的支持多 agent、结果等待和资源释放？ | 支持时可并行独立域；不支持时降级为串行执行、review checklist 或明确 `watch` |
| Browser / visual inspection | 当前工具是否能打开、截图、点击或渲染？ | 使用当前浏览器/截图/图像工具；不复制外部 browser companion runtime |
| Persistent memory | 当前 Hub 允许把什么写进 README、AGENTS、Skill 或 reports？ | 按 Hub 信息分类写入；不写私密、一时过程或平台缓存 |
| Git / branch lifecycle | 当前 workspace 是否由宿主隔离、是否 dirty、是否允许 branch/push/PR？ | 先读状态和权限；不照搬外部自动 worktree、commit 或 PR 流程 |

## Agent Manifest Handling

看到外部 `agents/*.yaml`、agent type、persona 或 subagent prompt 时，按这个顺序处理：

1. 先判断它只是 UI metadata、可发现性配置，还是运行时调度契约。
2. UI metadata 不复制；如有价值，合并到 Hub skill description 或 `skills/README.md`。
3. 调度契约不直接复制；先确认当前工具是否存在同等 agent 类型、并行语义、权限边界和结果回收方式。
4. 没有同等运行时时，把角色目标抽成 brief/checklist，状态标记为 `adapted` 或 `watch`。
5. 不为了迁移外部 manifest 而新增 Hub 目录、虚假 agent type 或未验证执行入口。

## Pressure Test Handling

外部压力测试的价值通常不在题目文本，而在诱发跑偏的压力类型：

- 时间/业务损失压力：诱导跳过根因、测试或设计门。
- 沉没成本/疲劳压力：诱导保留错误方向或临时 workaround。
- 权威/社交压力：诱导无验证地接受 reviewer、lead 或 senior 的说法。
- 熟悉/自信压力：诱导靠经验跳过当前 Skill。

Hub 处理方式：

1. 只保留压力类型、预期正确行为和失败信号。
2. 对应写入 `skill-pressure-test-template.md` 或专门 Skill 的 anti-rationalization。
3. 不把长场景原文当成长期规则；需要证据时在 report 里说明来源。

## Scratch Artifact Placement

当外部 workflow 依赖 task brief、review package、implementer report、progress ledger 或类似 scratch 文件时，先判断这些文件是不是稳定机制，还是外部运行时细节。

迁移规则：

1. 机制可以保留“文件化交接比长对话粘贴更稳”的原则，但不复制外部目录名、helper 脚本或缓存路径。
2. 需要本地落文件时，优先使用工作树内、被 `.gitignore` 覆盖、不会进入提交的 scratch 目录；不要把 agent 可写状态放进 `.git/`、宿主保护目录或其它默认不可写路径。
3. scratch 文件应视为可丢弃状态，不承载唯一事实源；如果 `git clean -fdx` 或宿主清理会删除它，必须同时写清恢复路径，例如从 commit、diff、验证输出或主计划重新生成。
4. 如果当前工具没有稳定的 file-based handoff 需求，就把它保留为 `watch` 或 report 结论，不为了对齐外部 workflow 人为制造 scratch 目录。

## Visualization Handling

当外部 skill 带 Graphviz、HTML companion、browser helper 或渲染脚本时，先问：

1. 这个视觉资源是在帮助用户理解流程，还是外部 runtime 的必要部分？
2. Hub 当前文档、报告或工具是否能稳定渲染该格式？
3. 是否有比复制脚本更轻的表达，例如 Mermaid、表格、截图或短 checklist？
4. 复制脚本是否会引入新依赖、写输出目录、平台命令或维护负担？

默认策略：报告和长期文档用 Mermaid 或 Markdown；运行时视觉验证使用当前工具提供的浏览器、截图、图像或本地渲染能力；Graphviz/DOT 脚本保持 no-copy，除非未来有多个 Hub skill 需要同一渲染链路。

## Verification

完成外部 skill portability 处理前，至少检查：

- 覆盖审计表是否有 tool mapping、agent manifest、prompt、script、visual、pressure test、asset/license 的状态。
- 新增 Hub 入口是否指向当前可用工具能力，而不是外部工具名。
- 公共规则是否已过 Codex/Cursor 跨工具兼容门；工具专属内容是否落到 adapter、fallback 或 watch。
- 没有把外部平台缓存路径写成运行依赖。
- 没有复制品牌资产、安装文档或原 prompt 大段文本。
- 对 `watch` 项写清触发复核的条件。
