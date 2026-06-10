---
name: interface-contract-audit
description: >-
  当任务涉及新增、删除、改名或暴露 API、Gateway、route、operation、tool surface、跨仓库/跨 agent
  对接契约时使用；先盘点完整接口面和真实消费者，再决定 supported、unsupported、deprecated 或 internal。
---

# Interface Contract Audit

本技能用于避免只按用户点名的单个接口做局部修改，导致历史接口、fixture 接口、隐藏页面接口或无真实能力接口混入 clean API 合约。

## Trigger

出现以下任一情况时触发：

- 新增、删除、改名或暴露 API/Gateway/route/operation。
- 前后端、跨仓库、跨 agent 或跨服务对接。
- 用户要求“接口干净”“不要提供没必要的接口”“举一反三”“不要 mock/fake 成功”。
- 某个接口被质疑来源、必要性、消费者或真实能力。
- 产品概念、信息架构、权限边界、可见范围或数据粒度发生收口、拆分、改名或重新分层。

## Workflow

1. **Surface Inventory**：列出完整对外契约面，包括 route、operation、tool name、schema、generated client、UI 入口、文档、测试和 fixture；不要只看用户点名项。
2. **Concept Contract**：先写清当前 canonical concepts、retired concepts、internal-only concepts 和 alias/deprecated terms。概念调整必须覆盖对外命名、类型名、helper/变量名、用户可见文案和主动加载流程。
3. **Consumer Check**：核对真实消费者代码、测试、文档和 fixture。消费者由其他 agent/thread 维护时，读取该侧上下文或询问该 agent/thread，再收敛契约。
4. **Operation Minimality Check**：为每个 supported operation 写清最小动作契约和每个 request 字段的必要性。更新/删除/解绑应优先只使用 canonical resource ref；然后检查服务端能否通过系统 ID、会话/账号授权、registry/access 关系、绑定索引或 owner store 自行定位对象和依赖。只有这些服务端事实都不能推导时，才允许把 scope、sourceScope、businessId、tenantId 等上下文字段放进调用方契约。
5. **Capability Check**：逐项确认是否有真实后端能力、稳定 owner 和当前产品入口；mock、dry-run、隐藏管理页、历史 fixture 不能自动成为正式合约。
6. **Selection Source Check**：对 tool/gateway/runtime/model disclosure 链路，确认选择来源是否单一、显式且可审计。模型可调用工具只应来自 `toolIds`、授权引用或等价配置；管理目录、全量 catalog、默认能力集合和运行时兜底不得通过 `IncludeDefault*`、`IncludeRuntime*`、`IncludeBackend*` 等布尔开关混入 model build contract。
7. **Call Path Closure**：对每个模型可见 tool，追踪 `schema/build -> model disclosure -> tool call -> executor -> result/event` 完整路径。schema 和 execute 不得分属两套语义入口；runtime core、controller 或 UI 不应按 tool name 直接 switch 具体工具。底层资源 client 可保留在 owner 模块，但必须通过 adapter/port 接入 tools runner。
8. **Layer Owner Matrix**：在给出 hook、接口或文件路径指导前，先列清每层职责 owner：catalog/schema、surface/API、runner/execution、provider/client、portal/factory、resource owner、consumer。必须用调用点和 package 职责验证推荐路径，不得只按文件名、目录名或当前已有实现位置推断。
9. **Decision**：给每个 surface 和 concept 标记 `supported`、`unsupported`、`deprecated` 或 `internal`。当前目标不需要或没有真实能力的接口，默认移除或返回明确 unsupported。
10. **Naming Closure**：被删除或降级的外部概念不得继续出现在 route、operation、generated client、页签、主动加载流程或泛化 helper/type 名中。若底层仍需保留同名存储、状态或兼容层，命名必须显式带出 `internal`、`storage`、`visibility`、`legacy`、`compat` 或具体聚合语义。
11. **Implementation**：保持机器契约稳定；不要为了兼容旧假接口新增业务逻辑。错误响应要清楚包含 code/message，避免 fake success。
12. **Tests**：为 supported 接口补正向测试；为 unsupported/deprecated 接口补 negative contract test，防止回归；为 naming closure 补搜索验证，证明旧外部概念没有残留在公开 surface。
13. **Report**：最终回复列出完整 supported/unsupported/deprecated/internal 面、概念边界、命名例外、contract matrix、修改文件和验证命令。

## Output Shape

- 已支持接口：逐项列出。
- 未支持/已删除接口：逐项列出原因，例如“当前消费者不需要”“无真实服务能力”“仅旧 fixture 使用”。
- 保留为 internal/deprecated 的接口：说明兼容边界和迁移策略。
- 概念边界：列出 canonical、retired、internal-only 和 deprecated/alias terms。
- Contract matrix：至少覆盖 `old concept/name`、`new concept/name`、`surface`、`consumer`、`decision`、`reason`、`test guard`。
- Layer Owner Matrix：涉及 hook/handoff 时列出 `layer`、`owner`、`file/package`、`responsibility`、`implementation guidance`、`do-not-touch`。
- 命名闭环：说明旧外部概念的残留搜索结果，以及哪些旧名因内部存储、可见范围、兼容或迁移原因被明确保留。
- 验证：列出跑过的测试或无法验证的缺口。

## Quality Bar

- Create/import/update 契约必须先分类 identifier：system-generated/output-only ID、canonical ref、用户可编辑 name/title/slug/alias、source hint 和幂等键。管理 UI 与 generated client 不应要求创建时输入主 ID；更新/删除携带的 ID 只是已有资源引用，并必须按 scope 做对象级授权。
- Delete/disable/unbind 这类动作契约必须比 create/import 更窄：默认只接受被操作资源的 canonical ref。不得把 `scope`、`sourceScope`、`businessId`、`tenantId`、`status`、`mode` 等相邻流程字段照搬进删除请求；“服务端需要做授权/绑定检测”不是字段存在理由，只有“服务端没有可靠索引或当前会话上下文无法唯一定位”才是字段存在理由，且必须有测试覆盖。
- 删除阻断检测必须走 access/relationship guard、binding index 或 owner store 的单一查询入口；不要在 route/handler/UI 中临时遍历其它模块。若现有 access 表尚未表达具体引用关系，也先封装为可替换 guard，后续再把关系维护和检测迁入 access 层。
- 如果删除阻断检测当前只需要轻量能力，可只检查已有配置索引或 owner store；不要为了“未来完整关系图”新增跨域模块、表或沉重抽象。允许存量配置暂时保留 stale ref 时，runtime/调用明细必须把 unresolved ref 暴露出来，而不是静默成功或伪装成已加载。
- 涉及 registry/cache/source 的接口必须写清事实源边界：registry 负责发现、权限、状态、索引、版本和轻量缓存，source/NAS/backend 负责真实内容；写入先落 source 再刷新 registry，读取先查 registry 再按需加载 source。
- 对 seed、fixture、已有数据和跨表引用，接口清理不得顺手换 ID 或迁移历史记录；除非任务明确是数据迁移，否则只修正本轮必要的字段、meta、access 或状态。确需改 ID 时，必须作为 migration surface 单独盘点消费者、回滚和验证。
- 接口面必须完整，不以用户举例作为全量列表。
- clean API 优先表达真实能力；没有真实能力时返回 unsupported，不补本地替代。
- 模型披露、运行时执行和管理目录必须分 surface 表达；不要用额外 include 布尔字段在同一个 build request 中切换“看全量目录”和“给模型调用”两种语义。
- tool schema 披露和 tool 执行必须用同一套 gateway 管理边界闭环；如果发现“schema 走 tools、execute 走 runtime/core 直连”这类分叉，优先视为架构漏洞而不是命名问题。
- 外层 composition root 应依赖统一 portal/gateway/facade，不直接拼具体 executor、adapter 或 provider 细节；底层资源 client 的差异由 portal/factory 内部转成工具执行能力，并通过 schema、description、result、metadata 或 error model 反馈给模型和调用方。
- 当需要给其他团队留下 hook、接口或实现指导时，必须先做 Layer Owner Matrix，并区分 `extension point`、`execution owner`、`adapter/factory`、`existing implementation`、`consumer surface` 和 `resource owner`。交付内容要给出可实现的接口、推荐文件路径、装配点、禁止修改的旁路位置和最小验证命令，不能把“现有实现目录”“surface 目录”或“资源 owner 目录”误说成“执行扩展入口”。
- 当模型协议或上游编排允许一轮返回多个 tool call 时，公开 gateway/surface 执行契约应优先采用 batch-first 入口；单个 tool call 只作为 batch 长度为 1 的调用场景或私有便捷封装，不另立对外语义。
- 概念迁移必须先定义术语表，再改实现；不得将单次失败转写为只有提醒、没有触发/检查/验证的规则。
- 删除或收口接口后必须搜索旧 route、operation、client 方法、测试、文档、helper/type/变量名；旧名只允许以明确 internal、storage、visibility、legacy、compat 或 deprecated 语义保留。
- 删除或收口目标链路时必须有 ownership 边界清单：哪些 route/schema/client/UI/测试属于本轮领域内 retired surface，哪些属于其他模块、fixture、静态 option、mock 数据或既有产品占位。领域内废弃内容要删干净；其他 owner 的内容不得顺手删除。确需跨 owner 删除时必须先说明消费者检查结果和授权来源。
- 不把一次性项目名称、临时方案或业务私密信息沉淀进技能。
