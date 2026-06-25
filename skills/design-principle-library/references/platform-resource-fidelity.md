# Platform Resource Fidelity

用途：作为 `design-principle-library` 的按需参考；用于迁移、导入、生成或挂载平台级 Skill、Prompt、Tool、Resource 前的质量门。

## Trigger

- 把外部系统、旧项目、临时包或手工整理内容导入平台 Skill / Prompt / Tool / Resource registry。
- 把 smoke case、调试脚本、示例 prompt、工具占位或待迁移清单升级为用户或 Agent 默认可见能力。
- 用户质疑资源是否通用、是否稳定、是否有本机路径、是否只是半成品。

## Invariant

平台资源是可复用基座能力，不是一次任务的过程证据。默认可见资源必须同时满足：

- **完整内容**：Skill 有可执行流程，Prompt 有实际正文或明确 owner 加载链路，Tool 有真实 schema / runtime / error contract，Resource 有事实源。
- **通用语义**：不绑定单个用户、项目、业务 case、线程、artifact、临时目录或本机绝对路径。
- **边界真实**：名称、描述、状态和挂载范围准确表达 `stable`、`draft`、`smoke`、`unsupported` 或 `catalog-only`。
- **分层正确**：Prompt 放 prompt registry，Skill 放流程契约，Tool 放执行面，provider/model/payload 适配放对应 service 或 adapter。
- **可验收**：有最小 workflow、正负向信号、失败回执和 owner 追溯方式。

## Red Flags

- Skill 里出现本机绝对路径、个人目录、临时 zip 路径、线程 ID、一次性 artifact URL。
- 用“待补齐清单”“external prompt references”“TODO”“reference only”冒充已导入内容。
- 把 smoke 成功描述成完整能力，把 wiring 可达描述成端到端可用。
- 为了适配当前 case，把 provider 私有参数、model ID、临时 token、旧系统 loader 或 runtime plumbing 写进通用 Skill。
- 创建 duplicate / near-duplicate Skill，只是换了业务例子或目标产品名。

## Placement

| 内容类型 | 放置位置 |
| --- | --- |
| 通用执行流程、决策树、质量契约 | Platform Skill |
| 真实 prompt 正文、prompt 版本、prompt owner | Platform Prompt registry |
| 可调用工具 schema、执行回执、错误契约 | Tool / MCP / runtime registry |
| provider 鉴权、payload 适配、model 路由 | Model service / adapter |
| 项目示例、验收 case、调试证据 | Report / test fixture / backlog |
| 缺失资源清单 | Backlog / todo，不挂成默认能力 |

## Validation

1. 搜索本机路径、临时目录、线程 ID、artifact URL、TODO、reference-only 词。
2. 对每个资源标明 owner、source、version/status、runtime dependency 和验收信号。
3. 用一个非原始项目的泛化 case 做 sanity check：资源仍应成立。
4. 若只能服务单个 case，把它降级为 report、fixture 或 draft，并限制挂载范围。
