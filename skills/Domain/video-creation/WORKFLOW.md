---
name: video-creation
description: 当用户要求制作、优化、拉片、规划或评审视频、storyboard、shot list、timeline、字幕/overlay 或视频生成 workflow 时使用。
---

# Purpose

把视频创作和生成 workflow 收敛到真实素材、真实主题、真实平台能力和可验证产物。

# When to Use

- 产品宣传片、发布片、教程、demo video、品牌短片、纪录片式案例、AI workflow 展示。
- 拉片、参考运镜、shot list、分镜、timeline、overlay、字幕、音乐节奏。
- 使用 AI 视频平台、MCP 或其他视频生成 workflow。

# When NOT to Use

- 普通图片生成、静态文案或不涉及视频结构的媒体任务。
- 效果差但目标只是 Run Case 时，不因此擅自改代码/schema/config。

# Inputs

- 产品/主题、目标观众、叙事目标、素材来源、参考视频、平台能力、预算/时长、产物格式和验收信号。

# Decision Principles

- 产品宣传优先于炫技；case 是承载，不是唯一目的。
- 真实产品 UI 和信息架构必须可辨认。
- 不让视频模型生成大量可读文字；关键文字用 deterministic overlay / timeline / 后期控制。
- 最终产物必须用实际媒体信息验证。

# Workflow

1. Freeze Context：确认主题、观众、叙事、素材、平台、工具和验收。
2. Mode Gate：区分 Run Case 与 Build Capability。
3. Creative Frame：按宣传、教程、case demo、纪录片、品牌片或平台验证选择结构。
4. Reference Study：能访问视频时看时长、节奏、镜头和音画关系。
5. Script / Shot / Timeline：每个镜头服务叙事或价值。
6. Generate / Assemble：确认模型能力、成本、时长、多图、timeline 和 approval state。
7. Validate Media：检查时长、分辨率、音轨、字幕/overlay、artifact 和质量问题。

# Checklist

- 是否冻结真实素材和禁止虚构边界。
- 关键文字是否由确定性 overlay 控制。
- 产物是否用真实媒体信息验证。

# Escalation

- 需要页面/Chat/tool 真实 smoke：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 涉及 tool/MCP 能力建设：Engineering 相关 Skill。

# References

- `references/product-launch-film-patterns.md`
