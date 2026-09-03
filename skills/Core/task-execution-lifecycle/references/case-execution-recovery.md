# Case Execution Recovery Patterns

用于页面 case、Chat/MCP 工作流、多素材生成、演示验证或用户连续纠偏后的快速收敛。目标是先把上下文、工具契约和验收口径锁住，再执行；不要在旧假设上继续叠补丁。

## Trigger

- 用户说“不是”“在这里做”“空间错了”“怎么是这个 Agent”“用页面操作”“新窗口测试”“多图”“三方 MCP”“不要改不该改的内容”。
- 当前任务涉及页面、thread、business/workspace、agent/preset、上传附件、NAS/runtime storage、MCP/tool call 或最终媒体产物。
- 工具可用性、页面结果和用户预期出现分歧，且继续执行会消耗用户时间。

## Context Lock

继续执行前先用一句话或内部 checklist 锁定这些不变量：

- 当前 surface：页面 URL、业务/空间、thread、agent/preset、可见页面标题或用户点名位置。
- 当前认证边界：正在使用的登录身份、认证方式与授权 scope；用户明确排除某个账号或认证方式时，将其作为后续 case 的稳定约束，不得为推进流程静默切换或重新登录。允许身份无法验证时停在最小确认点。
- 素材来源：用户上传附件、runtime/NAS 可见路径、本地路径是否只是上传来源；新窗口是否需要重新上传。
- 工具契约：tool schema、必填字段、批量/多输入能力、返回产物格式、第三方还是本仓 adapter。
- 存储边界：当前 thread 绑定空间、NAS/runtime、是否禁止额外本地交互。
- 现有能力：页面/API 是否已有上传、prompt、preset、skill、tool 绑定或 middleware/visible 配置入口；SPEC 中的建议路径是否仍符合当前代码事实。
- 产物目标：最终要页面验证、直接 tool smoke、配置验证、视频/音频成品，还是给用户可复制文案。
- 验收信号：时长、素材数量、音画同步、画面质量、页面是否跑通、最终 URL/文件是否可播放。

用户再次切换登录身份、空间、业务、agent、thread、素材或工具契约时，立即重新锁定；不要把旧身份、旧 thread、旧业务或旧工具参数带到新场景。

## Tool Contract Guard

- 先做 **Run Case / Build Capability** 分流：如果用户的目标是用当前页面、当前 thread、测试 case 或既有平台能力完成演示、Chat/MCP 工作流或媒体产物，默认不改代码、schema、config 或系统能力；只做素材、参数、prompt、页面操作、tool 调用和验收。是否进入代码实现取决于当前任务的真实 owner 和验收目标：任务本身是 SDK/平台/MCP/前后端能力接入、缺陷修复、配置收口或架构调整时，按 Build Capability 执行；Run Case 过程中发现能力阻塞时，先确认 owner、影响面和最小改动，再实现。
- **Spec Reality Gate**：SPEC、历史计划或交接文案只提供候选路径；若当前页面/API/代码已有配置能力，或用户明确说“spec 只是引导”“现成接口能做”，必须按当前事实重定边界。不能因为 SPEC 提到某 service、route、resource init 或 MCP，就新增入口、自动初始化、跨模块创建链路或改工具 schema。
- **Write Escalation Gate**：Run Case 中准备转代码、schema、config、SDK、MCP 或公共契约前，必须先证明非代码路径已排查：当前页面/thread/素材/tool schema/模型配置已刷新或确认，prompt/参数/素材顺序/确定性 overlay/timeline 方案已尝试或说明不可行；阻塞属于本仓 adapter、config wiring、SDK 或平台能力 owner；能列出最小改动文件和真实页面或真实调用链验证方式。任一项缺失时，继续 Run Case 调整或向用户说明阻塞，不写仓库。
- **Configuration First Gate**：用户要求“配置”且已有上传、prompt、preset、skill、tool refs 或页面/API 可以完成目标时，先盘点并复用用户点名或当前 scope 内的现成资源，只做配置和可见验证。禁止在 GET 列表接口、普通页面加载、业务初始化入口或后台 service 中偷偷创建业务、prompt、preset、skill 或工具绑定；只有现成资源确实不能满足、缺口有证据且用户明确授权创建时，才新增持久资源，并登记用途与回收边界。
- **Runtime Storage Gate**：Canvas、附件、artifact 或 thread 文件读写类任务，先沿真实消费链确认：页面读取哪个 runtime/backend 目录、附件上传后的 runtimePath、当前 thread scope、preset 中模型可用的 read/write/edit/list/upload/download 类 tool refs。只有证据表明 MCP server/project store 是页面事实源时，才考虑改 MCP；否则 MCP 只作为外部工具通道，不承载页面显示修复。
- 批量/多素材能力是工具语义的一部分；工具支持 `image_urls`、batch 或多 tool call 时，默认保留多输入契约，不为了 smoke 简化成单输入。
- 第三方 MCP、外部 tool schema 或用户明确要求保持的工具契约，默认只做配置、参数、prompt、provider wiring 或页面刷新排查；不要临时改公开 schema 来让一次 case 过。
- 若确认为本仓 adapter/config wiring 缺陷，先说明 owner 和最小改动，改动后必须用真实调用链验证；临时脚本、调试产物和本地专用配置不能伪装成产品能力。
- 直接 API/curl/MCP 调用只能证明下游能力；用户要求“用页面操作”时，最终证明必须来自页面 workflow 或明确说明页面层仍未验证。
- **No Codex-Dependent Completion**：当用户目标是建设系统、验证真实链路或要求“重新生成/用链路补齐”时，禁止用 Codex 专属手段推进业务状态，包括 internal complete、DB 手改、本地假素材、mock/fallback、一次性修补脚本、直接写入 selected 结果或跳过上游失败。若真实链路返回空数据、pending、failed、4xx/5xx 或结果未回写，结论应是“链路缺口已确认”，下一步只能定位 owning service / adapter / config / callback / persistence 并修系统，或按真实入口重新触发。
- **Boundary Simulation Gate**：只有用户明确接受仿真某个外部边界时，才可用等价事件替代该边界的真实发送方；必须冻结并披露唯一仿真边界，边界后的入口、队列/worker、公开协议、runtime、模型、副作用、持久化和用户可见结果等验收范围必须全部走真实链路，并用同一业务身份关联的回执、task/thread/run、审计或最终产物证明闭环。仿真边界之后再出现 mock、手工状态推进或伪成功，只能判为局部测试；即使下游全真，也只能称为“边界仿真闭环”，不能称为真实来源端到端。纯单元/契约测试可继续使用 mock，但不得承担系统闭环验收结论。

## Iteration Order

1. 先确认当前页面、登录身份、业务/空间、agent/thread 和要复用的现成资源都属于用户指定目标。
2. 再确认素材是否已上传到当前 thread；新窗口默认不知道旧 thread、本地路径或旧附件。
3. 再确认 tool schema 和模型配置是否当前页面可见；必要时刷新 registry/provider，而不是让模型猜参数。
4. 再发起最小可验证 case：先证明音乐、视频、素材读取和渲染链路各自可达，再组合成最终产物。
5. 最后按要求逐项验收；不要用“任务开始了”“tool running”“局部 URL 返回”替代最终成品。

## Handoff Shape

当用户要换窗口或让别人复现时，直接给一整段可复制内容，至少包含：

- 现有系统事实：真实页面/API、runtime/backend 路径、thread scope、preset/tool refs、附件或素材可见位置。
- 需要上传的文件清单。
- 不依赖本地路径的说明。
- 目标产物和质量要求。
- 必须使用的工具契约，例如多图传 `image_urls`。
- 禁止事项和不会做什么，例如不新增服务入口、不自动初始化业务数据、不改 MCP/tool schema、不依赖旧 thread。
- 验收标准。

## Failure Review

若用户指出体验极差，先把失败归类，不先辩解：

- Context failure：业务、agent、thread、素材、窗口或空间选错。
- Contract failure：改错 schema、降级工具能力、误用单输入、忽略三方 MCP 边界。
- Workflow failure：没按页面跑、直接 API 成功却页面没验证、registry/schema 没刷新。
- Product quality failure：分镜、转场、音画同步、视觉风格或清晰度不达标。
- Handoff failure：让用户拆段复制、没有给完整文件清单和最终文案。

每类失败都要对应一个下一步动作；没有 fresh evidence 前，不说“已打通”“OK”“可用”。
