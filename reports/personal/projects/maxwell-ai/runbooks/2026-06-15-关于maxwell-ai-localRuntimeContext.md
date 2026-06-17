日期：2026-06-15
类型：runbooks
项目：maxwell-ai
来源：用户在 maxwell-ai Studio / Knowledge UI 联调中的项目背景补充
版式：短背景记录，结论先行

# 关于 maxwell-ai-localRuntimeContext

## 结论

maxwell-ai 项目始终有在运行的线程或本地运行上下文。后续处理 maxwell-ai 的 UI 验证、Studio 联调、Knowledge 导入、接口 smoke 或浏览器操作时，不默认单独启动新的前端、后端或运行线程。

默认测试业务空间是「沙棠的测试业务」。后续 maxwell-ai 的 Studio UI 联调、Knowledge 导入测试和数据链路验证，除非用户另行指定，默认在该业务空间内执行。

## 后续执行约束

- 进入 maxwell-ai UI 验证前，先复用当前 in-app browser、已有 localhost 页面和已有监听端口。
- 只有当前运行态不可达、端口无响应、版本明显不是预期代码，或用户明确要求重启/另起服务时，才启动新的服务进程。
- 如果需要启动、重启或停止服务，先说明原因，避免破坏用户正在观察的页面状态。
- UI 联调优先从用户当前打开的页面继续，例如 `http://localhost:5173/businesses` 或当前打开的业务页。
- 验证结论要区分「复用了现有运行态」「请求到达后端」「数据落库或召回成功」三层，不把页面能打开误写成端到端已打通。

## 当前已知入口

- 2026-06-15，用户说明 in-app browser 当前位于 `http://localhost:5173/businesses`。
- 2026-06-15，用户确认测试空间为「沙棠的测试业务」。
- 当前已知业务页为 `http://localhost:5173/agents?businessId=e5c02d90-b348-413f-b477-9c4fef268c78`。
- 该背景只适用于 maxwell-ai 项目，不升级为所有项目的通用启动规则。
