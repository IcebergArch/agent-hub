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

## Workflow

1. **Inventory**：列出当前完整接口面，包括 operation/route/method、入参、出参、状态码、调用入口和测试覆盖；不要只看用户点名项。
2. **Consumer Check**：核对真实消费者代码、测试、文档和 fixture。消费者由其他 agent/thread 维护时，读取该侧上下文或询问该 agent/thread，再收敛契约。
3. **Capability Check**：逐项确认是否有真实后端能力、稳定 owner 和当前产品入口；mock、dry-run、隐藏管理页、历史 fixture 不能自动成为正式合约。
4. **Decision**：给每个接口标记 `supported`、`unsupported`、`deprecated` 或 `internal`。当前目标不需要或没有真实能力的接口，默认移除或返回明确 unsupported。
5. **Implementation**：保持机器契约稳定；不要为了兼容旧假接口新增业务逻辑。错误响应要清楚包含 code/message，避免 fake success。
6. **Tests**：为 supported 接口补正向测试；为 unsupported/deprecated 接口补 negative contract test，防止回归。
7. **Report**：最终回复列出完整 supported/unsupported/deprecated/internal 面、原因、修改文件和验证命令。

## Output Shape

- 已支持接口：逐项列出。
- 未支持/已删除接口：逐项列出原因，例如“当前消费者不需要”“无真实服务能力”“仅旧 fixture 使用”。
- 保留为 internal/deprecated 的接口：说明兼容边界和迁移策略。
- 验证：列出跑过的测试或无法验证的缺口。

## Quality Bar

- 接口面必须完整，不以用户举例作为全量列表。
- clean API 优先表达真实能力；没有真实能力时返回 unsupported，不补本地替代。
- 不把一次性项目名称、临时方案或业务私密信息沉淀进技能。
