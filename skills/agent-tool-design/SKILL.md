---
name: agent-tool-design
description: 当用户设计 AI agent 工具、MCP/server 工具、function calling、tool gateway、工具 schema、执行回执、权限或 agent workflow 工具接口时使用。
---

# Agent Tool Design

用这个技能设计 agent 可调用工具。目标是让模型容易正确调用，让系统能授权、审计、验证和恢复。

## Trigger

当用户说以下任一类需求时使用：

- “设计一个 tool/function/MCP 工具”“工具 schema 怎么写”
- “agent 怎么调用这个能力”“tool gateway”“工具执行回执”
- “把 API 暴露给模型”“让模型能操作某个系统”

## Workflow

1. **Define Capability**
   - 工具只暴露一个清晰动作。
   - 写清工具解决什么任务，不解决什么任务。
   - 不把管理目录视图和模型可调用工具混在一起。
   - 先区分管理操作、模型可调用动作和 runtime/middleware 内部辅助动作；三者可以复用 owner service 或 adapter，但不共享同一个 tool surface。
   - 先画清角色拓扑：tool host / MCP server 实例、provider registry、gateway/proxy/policy、runner/scheduler、runtime loop 和下游 provider/server 分别是谁的 owner；当前模块只实现自己负责的段，跨 owner 需求写成对接契约或 follow-up。

2. **Design Inputs**
   - 参数名贴近模型自然理解。
   - 必填字段越少越好；能由服务端凭授权和资源关系推导的 scope 不让调用方传。
   - 使用枚举、对象、数组和格式约束，而不是自由文本承载关键状态。

3. **Design Outputs**
   - 返回结构化结果。
   - 包含 `status`、`message`、`resourceRef` 或 `receipt`、必要诊断。
   - 对异步或长任务返回 task/session/receipt，不伪装成同步成功。
   - 异步工具的 retry、等待、轮询、回调恢复和 long-running policy 默认属于 runner/scheduler/runtime 的职责；tool host / MCP server 只暴露可执行能力、任务状态、回执字段和回调元数据，除非它就是被明确授权的调度 owner。

4. **Design Errors**
   - 区分 validation、permission、not_found、conflict、unsupported、rate_limit、downstream_failure。
   - 错误要能帮助模型下一步行动，但不能泄露敏感信息。

5. **Add Safety**
   - 高风险动作需要审批、确认、dry-run 或 least-privilege token。
   - 写入、删除、外部发送、花钱、访问敏感数据默认高风险。
   - 防 prompt injection、工具名混淆、过度授权和越权 scope。

6. **Plan Observability And Eval**
   - 记录调用者、输入摘要、资源引用、结果、耗时和错误码。
   - 为常见用户请求写 tool-use eval：是否选对工具、参数是否对、错误后是否恢复。

## Output

默认输出：

```markdown
## Capability

## Tool Contract

## Input Schema

## Output / Receipt

## Error Model

## Permission And Approval

## Observability

## Eval Cases
```

## Quality Bar

- 一个工具一个职责；不要暴露“万能执行器”。
- 工具描述要像给初级工程师写 docstring 一样明确。
- schema/build 和 execute 语义必须闭合；不要在 runtime loop 里按 tool name 绕过 gateway。
- 没有真实后端能力的工具要返回明确 unsupported，不能用 mock 假装成功。
- 资源 CRUD、导入、更新和内容存取默认走 owner service 或管理 API；不要为了复用工具执行器而让管理台调用模型 tool runner。
- middleware/runtime 为模型补充的内部辅助动作只有在明确成为稳定授权工具后，才进入 tool catalog 或 registry。
- 中立 registry、具体 tool server、执行 runner 和上游 agent 是不同角色；模块命名、目录位置、API 和验证链路都要反映真实上下游关系，不能因为当前接入方便把中立或实例角色塞进错误 owner。
