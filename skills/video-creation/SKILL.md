---
name: video-creation
description: 当用户要求制作、优化、拉片、规划或评审产品宣传片、教程视频、发布视频、AI 工作流视频、storyboard、shot list、timeline、字幕/overlay 或视频生成 workflow 时使用。
---

# Video Creation

视频创作与视频生成 workflow Skill。先尊重真实素材、真实主题、真实风格目标、平台能力和产物校验。

## Trigger

- 产品宣传片、发布片、教程、demo video、品牌短片、纪录片式案例、AI workflow 展示。
- 拉片、参考运镜、shot list、分镜、timeline、overlay、字幕、音乐节奏。
- 使用 AI 视频平台、MCP 或其它视频生成 workflow。

## Workflow

1. **Freeze Context**：确认产品、目标观众、叙事、素材来源、运行平台、可用工具和验收信号；涉及真实 UI 时冻结页面、tab、关键文案和禁止虚构边界。
2. **Mode Gate**：先分清 Run Case 还是 Build Capability。Run Case 只做素材、剧本/分镜、prompt、页面/Chat 操作和产物验收；不因效果差擅自改代码/schema/config。
3. **Creative Frame**：按视频类型选择结构；产品宣传、教程、case demo、纪录片、品牌片、平台验证不能套同一模板。
4. **Reference Study**：能访问视频时优先抽帧、看时长、节奏、镜头和音画关系；不能访问时说明缺口。
5. **Script / Shot / Timeline**：每个镜头服务叙事或价值；关键文字用 deterministic overlay / timeline / 后期控制。
6. **Generate / Assemble**：先确认模型能力、成本、时长、多图、timeline、质量报告和 approval state。
7. **Validate Media**：用真实媒体信息检查时长、分辨率、音轨、字幕/overlay、artifact 和质量问题。

## References

- `references/product-launch-film-patterns.md`：场景纲要、拉片模式、shot gate、overlay gate、timeline 验证和反模式。

## Output

按任务裁剪：Creative brief、Storyboard / Shot list、Timeline / Overlay plan、Generation prompts、Blocked/gap、Artifact report。

## Quality Bar

- 产品宣传优先于炫技；case 是承载，不是唯一目的。
- 真实产品 UI 和信息架构必须可辨认。
- 不能让视频模型生成大量可读文字；关键文字由后期确定性控制。
- 最终产物必须用实际媒体信息验证。
