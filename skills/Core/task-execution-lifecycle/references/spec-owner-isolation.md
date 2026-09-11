# SPEC Owner Isolation Contract

## Purpose

`spec-owner` 是单个 SPEC 的端到端隔离执行 owner，不是“只审文档”的角色别名。它持有需求 owner、代码改动、隔离部署、回测证据和交付恢复入口，直到任务移交、暂停或完成。

## Trigger And Scope

- 用户使用 `/hub spec-owner [<SPEC/需求>]`，或在 Agent Hub 插件中传入 `spec-owner`。
- 每次只绑定一个主 SPEC/需求。新增且改变冻结验收的范围另建 SPEC，不静默扩进当前 owner。
- 没有 plan 时，owner 可在隔离空间内完成 SPEC 生命周期；高风险确认仍按项目与 doc-hub 门禁执行。只有 plan 且实施确认满足时才能进入实现。

## Isolation Binding

开始修改前建立并向用户回报一张绑定清单：

| Binding | Required evidence |
| --- | --- |
| Source | 项目、SPECID、目标仓库和目标基线 SHA。 |
| Workspace | 每个会写入的仓库使用专属 workspace/worktree；不得复用承载其它任务 dirty diff 的主工作区。 |
| Branch | 每个可变仓库绑定一个可识别 work branch；不得直接在 target/main 修改。 |
| Documentation | SPEC/STDD 与代码分开记录，但共享稳定 SPECID；文档空间也不得混入其它执行批次。 |
| Service | 进入运行验证后，为每个会启动的进程分配独立实例名、端口、日志、PID/会话和临时/cache 路径。 |
| Data scope | 明确数据库、KV、对象存储和外部服务是隔离、只读还是共享；共享写入必须另有授权和可回滚身份。 |

纯 SPEC 编写阶段可以不启动服务，但 workspace/worktree 与 branch 绑定仍为必需。若宿主或仓库明确禁止某类 linked worktree，必须在不混入其它任务的前提下提供等价隔离；用户明确要求 linked isolation 时，以用户指定边界为准并记录例外。

## Execution Flow

1. Inventory：核对当前主工作区、已有 worktree、branch、服务、端口和 dirty diff，标出其它 owner，禁止借用或清理。
2. Bind：从最新目标基线创建隔离 worktree/work branch；记录绝对路径、branch 和 base SHA。
3. Freeze：正式任务按 doc-hub 规则把 plan 转为 execing，创建 STDD、Bug Pool、Optimization Pool，并登记验收场景；实现前冻结范围。
4. Implement：只在绑定空间修改，按纵向切片保持每片可验证；跨模块/接口/数据门禁逐项遵守。
5. Deploy：用独立端口、实例名、日志和临时目录启动所需服务；不 kill、重启或复用其它 owner 的进程。
6. Regress：同时验证新增路径、受影响路径和项目适用存量基线；真实页面 smoke 由主 owner 完成，mock/空数据/错误暴露不能冒充可用。
7. Record：STDD 记录命令、环境、SHA、服务 URL、断言、结果和证据；失败进入 Bug Pool，非阻塞优化进入 Optimization Pool。
8. Handoff：回报 workspace、branch、服务、验证、未完成项和恢复命令。没有 commit/push/PR/merge 授权时停在本地隔离分支。

## Service Isolation

- 端口必须在启动前检查占用并显式分配；前后端、worker 和依赖代理分别记录。
- 服务使用 worktree 内或任务专属 `.tmp/` cache/log/pid 路径；不得复用通用 `$HOME`、共享 pidfile 或其它任务缓存目录。
- 配置只覆盖实例拓扑和外部依赖地址等真实环境差异；不要为了隔离新增产品 YAML 字段。优先使用既有 CLI/env override。
- 外部数据库或共享服务无法隔离时，默认只读验证；需要写入时使用已授权测试 scope，并记录清理/回滚方式。
- 暂停或交付时只停止归属明确且 PID/会话可验证的实例；工作区和分支在形成可恢复入口前不得删除。

## Completion Evidence

`spec-owner` 只有在以下事实均可验证时才能声明实施完成或可验收：

- 所有改动只存在于登记的隔离 workspace/work branch，未污染其它 owner。
- SPEC 执行包、代码 diff 和实际影响范围一致。
- 聚焦测试、项目质量门禁和隔离部署 smoke 均有 fresh evidence。
- 服务 URL、端口、日志和停止方式可恢复；失败与未覆盖项明确。
- 未获授权的 commit、push、PR、merge、共享数据写入或服务清理均未执行。

## Counterexamples

- 只要求讨论、只读 review 或普通 `/hub spec` 时，不启动 `spec-owner` 隔离执行。
- 用户只要求在现有已绑定 worktree 继续时，先核对其确属同一 SPEC；通过后复用，不机械创建第二套空间。
- 隔离服务对纯文档改动没有验证价值时保持未启动，并在绑定清单标记 N/A；不能为形式完整启动无消费者进程。

## Validation Replay

- 正向：用户说 `spec-owner` 后，Agent 在修改前返回 worktree/branch/service binding，并只在该空间工作。
- 反向：用户只说 `spec` 时，Agent只走文档生命周期，不创建服务或业务代码分支。
- 相邻分支：用户说 `spec 执行` 但未指定 `spec-owner` 时，仍建立执行包并遵守普通仓库隔离规则，但不自动声称拥有独立部署环境。
- 纠偏：用户否认或取消 `spec-owner` 时，停止新修改，保留可恢复 checkpoint，并回报已绑定资源，不继续部署或回测。
