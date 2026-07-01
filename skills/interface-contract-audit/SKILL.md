---
name: interface-contract-audit
description: 当任务涉及新增、删除、改名或暴露 API、Gateway、route、operation、tool surface、跨仓库/跨 agent 对接契约时使用。
---

# Interface Contract Audit

接口契约审计。目标是避免只按用户点名项局部修改，导致历史接口、fixture、隐藏页面或无真实能力接口混入 clean API。

## Trigger

- 新增、删除、改名或暴露 API/Gateway/route/operation/tool surface。
- 前后端、跨仓库、跨 agent、跨服务或 SDK 对接。
- 用户要求接口干净、不要 mock/fake 成功、举一反三。
- 产品概念、权限边界、可见范围、数据粒度、配置语义发生收口或改名。

## Workflow

1. **Surface Inventory**：列 route、operation、tool name、schema、generated client、UI 入口、文档、测试和 fixture。
2. **Concept Contract**：写清 canonical、retired、internal-only、deprecated/alias terms。
3. **Consumer Check**：核对真实消费者、测试、文档和 fixture；跨 agent/线程消费者要读或问。
4. **Operation Minimality**：为每个 supported operation 证明 request 字段必要性；更新/删除/解绑默认只用 canonical resource ref。
5. **Capability Check**：逐项确认真实后端能力、稳定 owner 和当前产品入口；mock、dry-run、旧 fixture 不自动成为正式合约。
6. **Tool/Gateway Closure**：模型披露、schema/build、tool call、executor、result/event 必须闭合在同一 surface。
7. **Owner Matrix**：跨服务接口、SDK、MCP 或 runtime/gateway 链路先冻结 public surface、application owner、data/source owner、consumer。
8. **Decision / Tests**：标记 supported / unsupported / deprecated / internal；supported 补正向测试，unsupported/deprecated 补 negative contract test 或搜索守卫。

## Output

说明 supported / unsupported / deprecated / internal 面、概念边界、owner matrix、命名闭环、修改文件和验证命令。

## Quality Bar

- 接口面必须完整，不以用户举例作为全量列表。
- Create/import/update/delete 要区分 system-generated ID、canonical ref、用户可编辑字段、source hint 和幂等键。
- 没真实能力时返回 unsupported，不补本地替代。
- 删除或收口后搜索旧 route、operation、client 方法、文档、测试、helper/type/变量名。
