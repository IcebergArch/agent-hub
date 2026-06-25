# Case Execution Recovery Patterns

用于页面 case、Chat/MCP 工作流、多素材生成、演示验证或用户连续纠偏后的快速收敛。目标是先把上下文、工具契约和验收口径锁住，再执行；不要在旧假设上继续叠补丁。

## Trigger

- 用户说“不是”“在这里做”“空间错了”“怎么是这个 Agent”“用页面操作”“新窗口测试”“多图”“三方 MCP”“不要改不该改的内容”。
- 当前任务涉及页面、thread、business/workspace、agent/preset、上传附件、NAS/runtime storage、MCP/tool call 或最终媒体产物。
- 工具可用性、页面结果和用户预期出现分歧，且继续执行会消耗用户时间。

## Context Lock

继续执行前先用一句话或内部 checklist 锁定这些不变量：

- 当前 surface：页面 URL、业务/空间、thread、agent/preset、可见页面标题或用户点名位置。
- 素材来源：用户上传附件、runtime/NAS 可见路径、本地路径是否只是上传来源；新窗口是否需要重新上传。
- 工具契约：tool schema、必填字段、批量/多输入能力、返回产物格式、第三方还是本仓 adapter。
- 存储边界：当前 thread 绑定空间、NAS/runtime、是否禁止额外本地交互。
- 产物目标：最终要页面验证、直接 tool smoke、配置验证、视频/音频成品，还是给用户可复制文案。
- 验收信号：时长、素材数量、音画同步、画面质量、页面是否跑通、最终 URL/文件是否可播放。

用户再次切换空间、业务、agent、thread、素材或工具契约时，立即重新锁定；不要把旧 thread、旧业务或旧工具参数带到新场景。

## Tool Contract Guard

- 先做 **Run Case / Build Capability** 分流：如果用户的目标是用当前页面、当前 thread、测试 case 或既有平台能力完成演示、Chat/MCP 工作流或媒体产物，默认不改代码、schema、config 或系统能力；只做素材、参数、prompt、页面操作、tool 调用和验收。是否进入代码实现取决于当前任务的真实 owner 和验收目标：任务本身是 SDK/平台/MCP/前后端能力接入、缺陷修复、配置收口或架构调整时，按 Build Capability 执行；Run Case 过程中发现能力阻塞时，先确认 owner、影响面和最小改动，再实现。
- **Write Escalation Gate**：Run Case 中准备转代码、schema、config、SDK、MCP 或公共契约前，必须先证明非代码路径已排查：当前页面/thread/素材/tool schema/模型配置已刷新或确认，prompt/参数/素材顺序/确定性 overlay/timeline 方案已尝试或说明不可行；阻塞属于本仓 adapter、config wiring、SDK 或平台能力 owner；能列出最小改动文件和真实页面或真实调用链验证方式。任一项缺失时，继续 Run Case 调整或向用户说明阻塞，不写仓库。
- 批量/多素材能力是工具语义的一部分；工具支持 `image_urls`、batch 或多 tool call 时，默认保留多输入契约，不为了 smoke 简化成单输入。
- 第三方 MCP、外部 tool schema 或用户明确要求保持的工具契约，默认只做配置、参数、prompt、provider wiring 或页面刷新排查；不要临时改公开 schema 来让一次 case 过。
- 若确认为本仓 adapter/config wiring 缺陷，先说明 owner 和最小改动，改动后必须用真实调用链验证；临时脚本、调试产物和本地专用配置不能伪装成产品能力。
- 直接 API/curl/MCP 调用只能证明下游能力；用户要求“用页面操作”时，最终证明必须来自页面 workflow 或明确说明页面层仍未验证。

## Iteration Order

1. 先确认当前页面/业务/agent/thread 是用户要的目标。
2. 再确认素材是否已上传到当前 thread；新窗口默认不知道旧 thread、本地路径或旧附件。
3. 再确认 tool schema 和模型配置是否当前页面可见；必要时刷新 registry/provider，而不是让模型猜参数。
4. 再发起最小可验证 case：先证明音乐、视频、素材读取和渲染链路各自可达，再组合成最终产物。
5. 最后按要求逐项验收；不要用“任务开始了”“tool running”“局部 URL 返回”替代最终成品。

## Handoff Shape

当用户要换窗口或让别人复现时，直接给一整段可复制内容，至少包含：

- 需要上传的文件清单。
- 不依赖本地路径的说明。
- 目标产物和质量要求。
- 必须使用的工具契约，例如多图传 `image_urls`。
- 禁止事项，例如不输出素材清单、不用单图、不生成 0 秒音频、不依赖旧 thread。
- 验收标准。

## Failure Review

若用户指出体验极差，先把失败归类，不先辩解：

- Context failure：业务、agent、thread、素材、窗口或空间选错。
- Contract failure：改错 schema、降级工具能力、误用单输入、忽略三方 MCP 边界。
- Workflow failure：没按页面跑、直接 API 成功却页面没验证、registry/schema 没刷新。
- Product quality failure：分镜、转场、音画同步、视觉风格或清晰度不达标。
- Handoff failure：让用户拆段复制、没有给完整文件清单和最终文案。

每类失败都要对应一个下一步动作；没有 fresh evidence 前，不说“已打通”“OK”“可用”。
