# SPEC 方案评审门禁

日期：2026-08-26

来源：正式 SPEC 建立流程与长期系统质量要求

用途：作为 `requirements-brief` 的按需 reference；指导 `draft -> init -> update -> plan` 的范围适配建设与评审，不作为新的用户命令或独立文档 owner。

## 核心规则

正式 SPEC 使用同一稳定 SPECID 和单一文件 owner 完成四阶段：`draft` 只登记不变量、背景、目标、核心问题和非目标；`init` 再判断影响维度、选择参与 Skill/角色并形成方案设计输入；`update` 进入正式撰稿，形成第一版完整方案并评审、修正和复评；`plan` 表示所有适用维度已无 blocker 的最终定稿。

业界方案与既有经验必须在 init 参与方案设计，而不是在 update 成稿后作为装饰性检查表。init 要从当前事实、历史运行/事故/项目经验、权威行业实践和专家判断中提取会改变方案的机制；update 再把这些输入落实为本地设计决策。单次 SPEC 解决的是有上下文的“术”，长期 Hub 沉淀的是跨场景成立的“道”，两者不得混写。

评审通过即停止，不以轮次多少衡量质量。绝大多数方案应在 2-3 轮内收敛；第 3 轮后仍未通过时先做收敛检查，不能原样继续挑错。单个实质变更周期最多 10 轮，这只是异常情况下的硬上限。达到上限、关键证据缺失或需要用户决策时保持 `update`，不得把轮次用尽写成通过。

`plan` 只表示方案定稿，不表示实现已获授权。变更使用同一 SPECID 回到最早受影响阶段：不变量、背景、目标或核心问题变化回 draft；专家范围或意见变化回 init；方案正文修正留在 update；不改变语义的链接、状态和文字勘误可以直接修正。

## 业界参考进入方案设计

init 先回答“参考方案中什么机制值得采用”，不按品牌、产品功能或目录结构照搬。每项关键参考都压缩为一张设计输入：

| 字段 | 要求 |
| --- | --- |
| 设计问题 | 当前方案真正要解决的产品、架构或运行问题 |
| 来源与证据 | 当前事实、历史经验、权威行业实践或专家判断；外部来源记录 URL、版本/日期和访问日期 |
| 优秀机制 | 该方案为什么有效，保护了什么不变量，而不是“别人这样做” |
| 适用边界 | 成立所需的规模、组织能力、技术前提，以及至少一个不适用或失败场景 |
| 本地取舍 | `adopted / adapted / rejected`，说明收益、成本、复杂度和替代方案 |
| 方案落点 | 具体进入 update 的边界、流程、架构、策略或验收信号 |

证据强度按本地适用性判断：已验证当前事实与用户不变量优先；直接相关的运行数据、事故复盘和项目经验次之；权威标准、官方框架、研究和一手案例用于补充机制与反例；专家类比和未验证假设必须显式标注。经验可以暴露模式，不能因“以前这么做”自动升级为约束。

- 低风险、成熟且已有充分本地证据的设计可以引用稳定基线后停止，不为形式反复联网。
- 高风险、新颖、版本敏感、供应商能力不确定，或本地经验与行业实践冲突时，必须加载 `source-grounded-research`，用权威来源交叉验证。
- 调研停止条件是关键设计问题已有可解释的机制、边界和取舍；不是链接数量达到某个值。
- 行业框架只提供设计视角和候选机制，不替代业务上下文。不同实践冲突时，按当前目标、不变量、风险容忍度和验证证据作出本地取舍。

### 默认行业锚点

这些来源帮助快速选视角，不是每份 SPEC 必须逐项扫描的固定清单（访问日期：2026-08-26）：

| 设计领域 | 默认权威锚点 | 主要提取内容 |
| --- | --- | --- |
| 产品与服务设计 | [GOV.UK Service Standard](https://www.gov.uk/service-manual/service-standard) | 用户真实需要、端到端问题、持续迭代、成功度量与可靠运营 |
| 综合架构 | [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)、[Google Cloud Well-Architected Framework](https://docs.cloud.google.com/architecture/framework) | 按业务上下文权衡运营、安全、可靠性、性能、成本与可持续性 |
| 可靠性与运行经验 | [Google SRE Books](https://sre.google/books/) | SLI/SLO、错误预算、过载与级联故障、容量、监控、事故复盘与持续学习 |
| 弹性与自愈 | [Kubernetes Autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)、[Kubernetes Self-Healing](https://kubernetes.io/docs/concepts/architecture/self-healing/) | 指标驱动控制环、扩缩容稳定窗口、健康替换与自愈边界；不代表必须采用 Kubernetes |
| 安全 | [NIST Cybersecurity Framework 2.0](https://www.nist.gov/publications/nist-cybersecurity-framework-csf-20) | Govern、Identify、Protect、Detect、Respond、Recover 的结果导向风险治理 |
| UI 与可达性 | [W3C WCAG 2.2](https://www.w3.org/TR/WCAG22/) | 可感知、可操作、可理解、健壮以及可测试的成功标准 |
| 成本与资源效率 | [FinOps Framework](https://www.finops.org/framework/) | 单位经济性、用量优化、需求与资源匹配、工程/产品/财务协作 |
| 交付与演进 | [DORA Capabilities](https://dora.dev/capabilities/) | 松耦合、独立交付、持续验证、可观测性和交付性能 |

## 建设与验收循环

1. **Build Draft**：登记必须保留的不变量、背景、目标、核心问题、非目标、事实源和仍属假设的内容；draft 不写专家意见或具体方案。
2. **Mark Applicability**：进入 init 后，对七个维度逐项标记 `required / conditional / N/A`。N/A 必须说明影响面理由，不能因为时间紧或没有专门 Skill 而跳过。
3. **Route Experts And Research**：以 `requirements-brief` 为主 Skill，用 `skill-router` 选择最小辅助 Skill；复杂产品/系统方案由 `agent-team-router` 汇聚必要角色。需要比较业界方案或核对外部能力时加载 `source-grounded-research`，不为七个维度各造一个 Skill。
4. **Build Init**：每个参与 Skill/角色结合当前事实、历史经验与适用行业实践，给出会改变方案的设计约束、候选机制、取舍、风险和验收信号；init 记录设计输入，但不提前展开完整方案正文。
5. **Author Update**：进入 update 后正式撰写第一版完整方案，并逐项标记设计输入与专家意见为 `adopted / adapted / rejected` 及理由。不同来源或参与方有冲突时先作出综合取舍，不把分歧原样留给实现者。
6. **Acceptance Review**：优先验收优秀机制是否已按本地边界正确转化、专家意见是否被落实、跨维度是否一致、验收是否可执行；只补充由具体成稿或新证据暴露的 blocker，不每轮重新发散方案。
7. **Revise And Re-review**：修正文档后只复核未关闭 blocker、被改动维度和连锁影响；出现新影响面时才补选 Skill/角色。
8. **Convergence Check**：第 3 轮后仍未通过时，先判断根因是范围过大、证据不足、用户决策缺失、参与方冲突还是方案方向错误；选择缩小范围、补证据、请求决策、调整参与方或回到 init。只有下一轮有明确新输入或修改目标时才继续。
9. **Finalize Or Stop**：全部适用维度无 blocker、跨维度一致且验收可执行时转 plan；否则保持 update，并记录 owner、最小解除条件和当前轮次。第 10 轮必须停止，不强制定稿。

## 七个评审维度

| 维度 | 何时适用 | 核心检查 | Skill / 角色路由 |
| --- | --- | --- | --- |
| 产品价值与功能边界 | 所有正式 SPEC | 目标用户与 job、问题价值、主路径、范围/非目标、状态与反馈、失败/取消/恢复、是否把第一版方案误当目标 | `requirements-brief`；复杂产品用 `agent-team-router` 的 Product 角色 |
| UI、交互与可达性 | 新增或改变页面、操作流、信息架构、用户可见状态 | 任务流、信息层级、密度与一致性、空/错/loading/disabled 状态、可学习性、键盘/无障碍、响应式及高风险操作确认 | `agent-team-router` + `product-ui-quality-gates` |
| 架构与长期演进 | 改系统、模块、数据流、持久化、部署或跨主体关系 | owner 与事实源、模块边界、依赖方向、稳定契约、可替换/可组合性、数据一致性、版本/迁移/回滚；容量增长时是否能沿明确维度动态扩缩容且缩容安全 | `codebase-navigation`；复杂系统用架构角色；接口/tool 命中 Engineering Skill |
| 稳定性与可运维性 | 服务、runtime、后台任务、队列、缓存、长任务或生产链路 | 无用户增长时内存/线程/连接/队列/缓存/临时数据是否有上限与释放；增长时容量模型、热点、背压、限流、降载和扩缩容是否闭合；超时、幂等重试、隔离、熔断、故障自愈、降级与灾难恢复；日志/指标/trace/审计、SLI/SLO、告警和诊断 owner | `agent-team-router` 的 Architecture + SRE 角色；外部能力事实不明时加 Research |
| 安全与数据保护 | 存在身份、权限、租户/空间、数据、密钥、外部调用或副作用 | 当前最低基线是明确 authentication；再按影响检查 authorization、最小权限、租户隔离、输入与执行边界、密钥/凭据、敏感数据、审计、保留/删除和滥用防护 | Security 角色；公共契约或可见范围变化加 `interface-contract-audit`；模型工具加 `agent-tool-design` |
| 效率、容量与成本 | 占用计算/存储/连接/队列/模型/API/媒体资源，或长期保留对象 | 单位工作成本与容量预算、配额、速率、并发、闲置回收、过期清理、重复工作、资源泄漏、无效占用、恶意/误用、供应商成本和成本可观测性；限制必须有需求/契约/运行事实来源 | `agent-team-router` 的 FinOps + SRE/架构角色 |
| 交付、兼容与验证 | 所有正式 SPEC | 分阶段交付、依赖与 owner、迁移/回滚/兼容边界、feature flag 或灰度需要、可测试性、成功/失败/边界验收、真实证据入口、运维和文档交接 | `requirements-brief`；接口/外部事实/领域场景按需追加对应 Skill |

七维是稳定评估框架，不是要求每份 SPEC 平均写七章。正文只展开会改变设计或验收的内容；评审记录保留每个维度的适用性和结论即可。

## Skill 选择规则

- 基础组合：`requirements-brief` + `spec-lifecycle`；前者评内容，后者管状态。
- 产品、UI、架构、稳定性、安全或成本命中时加载 `agent-team-router`；单一明确维度可选 1-2 个角色，复杂跨域方案选 3-6 个，并由它综合结论。
- owner、事实源或影响面不清时加载 `codebase-navigation`。
- API/Gateway/route/SDK/可见范围加载 `interface-contract-audit`；模型工具/MCP/function calling 再加 `agent-tool-design`。
- 方案需要比较业界做法、提取优秀机制，或结论依赖最新外部事实、标准、供应商能力或价格时加载 `source-grounded-research`。
- Prompt、视频或其它已登记领域只在方案真实涉及时加载对应 Skill。
- 默认总量保持 2-4 个 Skill。多个维度可由同一 Skill/角色组合覆盖；如果某个 Skill 不会改变判断，不加载。
- 选中的 Skill/角色同时提供 init 专家意见并参与 update 验收；除非影响面变化，不在每轮重新选择一批评审者。

## Plan 批准门禁

只有同时满足以下条件才可从 update 转为 plan：

- 七维适用性已记录，所有 required/conditional 维度都有参与 Skill 或角色 owner。
- init 专家意见均有 `adopted / adapted / rejected` 处置，update 已验收其落实；rejected 有明确取舍依据。
- 关键设计决策已有事实、经验或行业参考支撑，并说明优秀机制、适用边界和本地取舍；没有把未验证“最佳实践”直接写成约束。
- 没有未解除 blocker；advisory 已采纳、明确拒绝并记录理由，或作为不影响当前验收的后续项处置。
- 产品目标、架构边界、权限、容量/成本假设和验收之间没有矛盾。
- 动态扩缩容、自愈、观测或成本治理若不适用，已有具体影响面理由；若适用，至少写明 owner、边界和验证信号，而不是只写原则口号。
- 方案能独立执行，关键假设可验证，高风险实施确认单与用户真实确认状态一致。

## SPEC 内评审证据

SPEC 正文结构由 Hub README 登记的文档工作区唯一拥有，本 reference 不新增固定一级章节或并行模板。评审证据放入文档工作区模板指定的“其他”或等价附属位置，并压缩为：

- 阶段、当前实质变更周期与轮次。
- 参与的最小 Skills / 角色及综合结论。
- 仍未关闭的 blocker；已解决问题不保留逐轮对话，只保留改变最终方案的决定。
- 非显然设计输入的 `adopted / adapted / rejected` 结论与一句理由；已直接体现在正文、无需解释的输入不再重复制表。
- 七维评审复用正文的新旧方案对比表或文档工作区指定载体，不另建第二张完整性检查表。
- 0–3 条设计沉淀候选；没有则写 `None`。

这些内容只用于解释方案为何可定稿。完整证据回到正文事实与明确来源，执行进度、命令、Bug、验证和提交进入 STDD 执行包，不复制成第二份评审报告。

## 周维度沉淀跟进

plan 定稿时只登记 0-3 条“设计沉淀候选”，不在单次 SPEC 内直接扩写 Hub，也不阻塞 plan。候选写清本次采用的术、背后的可能机制、适用边界和证据；没有跨场景价值时明确写 `None`。

每周统一扫描当周 plan 及其 init/update 记录，合并重复候选并用 `knowledge-evolution` 判断：`强化已有指导思想 / 新增跨场景策略 / 继续观察 / 淘汰`。只有脱离具体项目仍成立，且触发条件、不变量、因果机制、边界、反例与验证方式完整的内容才自动更新 Hub；其余留在项目 owner。周沉淀优先改进已有 Rule/Skill/Reference，不为每个案例新建条目，不把问题穷举成“道”。
