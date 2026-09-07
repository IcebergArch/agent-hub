---
name: interface-contract-audit
description: 当任务涉及新增、删除、改名或暴露 API、Gateway、route、operation、tool surface、持久化操作或跨主体契约时使用。
---

# Purpose

审计对外契约面，避免局部修改把旧接口、fixture、mock 或错误可见范围带进正式 API。

# When to Use

- 新增、删除、改名或暴露 API/Gateway/route/operation/tool surface。
- 前后端、跨仓库、跨 agent、跨服务、SDK 或 MCP 对接。
- 产品概念、权限边界、可见范围、数据粒度或配置语义收口。
- 定时、排队、重试或补偿会驱动持久化副作用，需要冻结激活、授权、幂等和恢复语义。
- mutation 跨写库、读副本或异步投影，需要定义 canonical 结果与可容忍陈旧读。

# When NOT to Use

- 纯内部实现，不改变任何可发现、可调用、可导入或可复用 surface。
- 普通代码修复且契约不变。

# Inputs

- route/operation/tool/schema/client/UI/docs/tests/fixture 清单、真实消费者、owner matrix、目标状态和验证信号。

# Decision Principles

- 接口面必须完整，不以用户举例作为全量列表。
- 新功能或未发布契约明确不做兼容时，按首次发布基线一次收口；不得因开发迭代残留 `v2`、`legacy`、`compat`、双读或双写语义。只有存在已发布消费者、存量数据或明确迁移窗口时，才保留高版本号或兼容层。
- 新增或迁移可被其他主体发现的能力，必须证明目标消费者可见/可用，非目标消费者不可见/不可用。
- Create/import/update/delete 区分 system-generated ID、canonical ref、用户可编辑字段、source hint 和幂等键。
- 没真实能力时返回 unsupported，不补本地替代。
- UI/管理面预检不是执行授权；动态能力、权限、资源 owner 和 provider 支持必须在真实执行或副作用边界重新核对，TOCTOU 不一致时安全拒绝或显式降级。
- Durable 副作用若依赖会变化的配置、目录或显示名，必须在第一次副作用前解析并持久化 versioned canonical intent/payload 与幂等身份；同一逻辑操作重试复用已冻结语义，不重新读取最新状态后静默改变目标。确需采用最新状态时，必须新建 revision/intent 并重新验证。
- 持久化 workflow 要区分 `scheduled/eligible`、durable authorization 与 `executing/terminal`：时钟、队列消息或页面预检只能改变可运行性，不得产生新授权或绕过执行时复核。状态面只投影 canonical lifecycle owner，scheduler ledger 只记幂等、租约和补偿回执，不成为第二事实源。用户已对明确范围作出可持久预授权时，到期可无互动执行，但必须复用冻结的 intent 并确认权限、能力和影响边界仍有效。
- 写读分离或异步投影下先将读取分为 `canonical result / strong control read / stale-tolerant query`；当 mutation 契约承诺资源状态时，成功回执优先来自事务结果或写 owner 的 authoritative read，不用可能落后的副本重读“证明”写入。授权、CAS、幂等恢复和模糊提交核对属于 strong control read；只有明确容忍陈旧的查询才可使用 replica/cache/projection。异步命令可返回 durable accepted receipt 而非资源快照，但不得把投影可见冒充成功终态。
- 当同一资源可被多请求、多进程、多实例或多 storage adapter 并发修改时，丢失更新保护必须闭合在 owning store：使用事务、version/CAS 或等价条件写，不用单进程 mutex 伪装全局原子性。只有单进程本身是明确且可验证的部署不变量时，in-process lock 才可独立成立。

# Workflow

1. Inventory：列 route、operation、tool name、schema、generated client、UI、docs、tests、fixture。
2. Concepts：标记 canonical、retired、internal-only、deprecated/alias terms。
3. Consumers：核对真实消费者、测试、文档、fixture 和跨 agent/线程使用方。
4. Visibility：冻结 owning service、可见主体、授权来源和 scope 默认值；显示名/别名只作发现提示，provider-native identity 只能由 owning adapter 在已认证完整 scope 内解析。
5. Operations：逐项证明 request 字段必要性，删除/解绑默认只用 canonical ref。
6. Lifecycle：对定时、排队或可恢复操作冻结 canonical state、activation 条件、执行授权、幂等 identity、lease/concurrency、终态与补偿；状态投影不反向猜测。
7. Consistency：对 mutation 标出事务 canonical result、strong control reads、stale-tolerant reads 和异步 outcome；定义复制落后、模糊提交和投影延迟的恢复语义。
8. Capability：确认真实后端能力、稳定 owner 和产品入口；区分管理/预检结果与执行边界 final check，并定义不一致时的 unsupported/降级语义。
9. Closure：model disclosure、schema/build、executor、result/event 必须闭合；durable side effect 还要闭合 intent revision、幂等键、prepared payload/receipt 与 retry/recovery。
10. Decision：标记 supported / unsupported / deprecated / internal，并补验证。

# Checklist

- 是否搜索旧 route、operation、client 方法、文档、测试、helper/type/变量名。
- 未发布契约定版时，是否同时检查文件名、key namespace、consumer group、schema/type、常量、配置、文档、测试和 fixture，清除无真实前代的版本/兼容痕迹。
- 是否有 allow/deny 两侧的 visibility 证据。
- 是否没有把 mock、dry-run 或旧 fixture 升格成正式契约。
- 名称解析是否不会越过 provider adapter、认证 scope 和完整性门禁，歧义/截断时不会产生错误身份或副作用。
- preflight 与 execute 之间能力或权限变化时是否有安全结果；重试是否复用同一 canonical intent，而不是重新解析成另一目标。
- missed/duplicate/concurrent trigger、重启与租约回收是否不会扩张授权、重复副作用或把 scheduler ledger 变成状态事实源。
- 强制复制落后和模糊提交时，mutation 是否仍返回 canonical 结果或可幂等恢复，且陈旧投影不会把已提交成功改写为业务失败。
- 并发更新是否由 owning store 的 transaction/version/CAS 防止 lost update；若只用进程内锁，是否有单实例不变量与部署验证。

# Escalation

- 模型可调用工具：`skills/Engineering/agent-tool-design/WORKFLOW.md`
- 需要执行改动与验证闭环：`skills/Core/task-execution-lifecycle/WORKFLOW.md`

# References

- [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)（默认拒绝、逐请求校验和正确执行位置；访问 2026-08-29）
- [AWS Builders' Library — Making retries safe with idempotent APIs](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/)（显式请求身份、语义等价重试和副作用幂等；访问 2026-08-29）
- [Kubernetes CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)（调度可能重复或遗漏，Job 需幂等并显式定义 concurrency/deadline；访问 2026-09-05）
- [PostgreSQL `RETURNING`](https://www.postgresql.org/docs/current/dml-returning.html)（直接返回被修改的 canonical row，避免额外查询；访问 2026-09-05）
- [Amazon RDS for PostgreSQL read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PostgreSQL.Replication.ReadReplicas.Configuration.html)（副本通过异步 streaming replication 追赶写 owner；访问 2026-09-05）
- [RFC 9110 §13.1.1 `If-Match`](https://www.rfc-editor.org/rfc/rfc9110.html#section-13.1.1)（使用 strong validator 条件执行状态修改，防止 lost update；访问 2026-09-05）
