---
name: interface-contract-audit
description: 当任务涉及新增、删除、改名或暴露 API、Gateway、route、operation、tool surface 或跨主体契约时使用。
---

# Purpose

审计对外契约面，避免局部修改把旧接口、fixture、mock 或错误可见范围带进正式 API。

# When to Use

- 新增、删除、改名或暴露 API/Gateway/route/operation/tool surface。
- 前后端、跨仓库、跨 agent、跨服务、SDK 或 MCP 对接。
- 产品概念、权限边界、可见范围、数据粒度或配置语义收口。

# When NOT to Use

- 纯内部实现，不改变任何可发现、可调用、可导入或可复用 surface。
- 普通代码修复且契约不变。

# Inputs

- route/operation/tool/schema/client/UI/docs/tests/fixture 清单、真实消费者、owner matrix、目标状态和验证信号。

# Decision Principles

- 接口面必须完整，不以用户举例作为全量列表。
- 新增或迁移可被其他主体发现的能力，必须证明目标消费者可见/可用，非目标消费者不可见/不可用。
- Create/import/update/delete 区分 system-generated ID、canonical ref、用户可编辑字段、source hint 和幂等键。
- 没真实能力时返回 unsupported，不补本地替代。

# Workflow

1. Inventory：列 route、operation、tool name、schema、generated client、UI、docs、tests、fixture。
2. Concepts：标记 canonical、retired、internal-only、deprecated/alias terms。
3. Consumers：核对真实消费者、测试、文档、fixture 和跨 agent/线程使用方。
4. Visibility：冻结 owning service、可见主体、授权来源和 scope 默认值。
5. Operations：逐项证明 request 字段必要性，删除/解绑默认只用 canonical ref。
6. Capability：确认真实后端能力、稳定 owner 和产品入口。
7. Closure：model disclosure、schema/build、executor、result/event 必须闭合。
8. Decision：标记 supported / unsupported / deprecated / internal，并补验证。

# Checklist

- 是否搜索旧 route、operation、client 方法、文档、测试、helper/type/变量名。
- 是否有 allow/deny 两侧的 visibility 证据。
- 是否没有把 mock、dry-run 或旧 fixture 升格成正式契约。

# Escalation

- 模型可调用工具：`skills/Engineering/agent-tool-design/WORKFLOW.md`
- 需要执行改动与验证闭环：`skills/Core/task-execution-lifecycle/WORKFLOW.md`

# References

- None.
