日期：2026-06-24
类型：analysis
项目：agent-hub
来源：用户提供两条本地高级产品宣传参考视频，并要求吸纳其优点到 Hub，用于后期视频创作
版式：结论先行，按创作机制映射到 Hub 资产

# 产品宣传视频创作经验吸收

## Summary

本次把两个参考视频视为“产品宣传片方法论样本”，而不是要复制的视觉模板。结合用户补充的“视频创作是复杂的，不同视频和风格有不同叙事、镜头语言、光线和代入感”，Hub 采纳更上层的稳定机制：实事求是、按类型/风格/场景选择纲要、产品认知优先、真实 UI/真实工作流优先、镜头服务价值、少字确定性 overlay、动作驱动转场、最终媒体探针校验。

落地方式：

- 新增 `skills/video-creation/SKILL.md`，作为后续视频创作、拉片、分镜、timeline 和视频生成 workflow 的触发入口。
- 新增 `skills/video-creation/references/product-launch-film-patterns.md`，承载“实事求是”最高纲要、视频创作变量、分场景结构、参考片模式、shot 架构、overlay gate、工具 gate 和 Maxwell/Aion 经验。
- 更新 `skills/README.md` 与 `AGENTS.md`，让“视频创作/宣传片/教程视频/shot list/timeline”任务能被发现。

## Source Scope

本次只分析用户提供的本地视频文件与当前 Maxwell Studio 视频生成任务反馈，未联网补充外部资料：

- `/Users/shatang/Downloads/Introducing iPhone 17 Pro  Apple_1080p.mp4`
  - `ffprobe`：1920x1080，24 fps，约 237.26 秒，含 H.264 视频与 AAC 音频。
  - 观察重点：产品实体感、暗场光线、材质/微距、慢推镜头、发布片收束。
- `/Users/shatang/Downloads/Google Workspace enables the future of AI-powered work for every business_1080p.mp4`
  - `ffprobe`：1920x1080，30 fps，约 90.05 秒，含 H.264 视频与 AAC 音频。
  - 观察重点：真实工作流、产品 UI、用户任务、输入到输出、少字大字卡、AI 帮助业务动作落地。
- 参考抽帧 contact sheet：
  - `/Users/shatang/Documents/maxwell-studio-product-video-assets-2026-06-24/reference-analysis/apple-contact-sheet.jpg`
  - `/Users/shatang/Documents/maxwell-studio-product-video-assets-2026-06-24/reference-analysis/google-contact-sheet.jpg`

## Adopted Mechanisms

### 1. 实事求是 And Scenario First

视频创作先看真实素材、真实主题、真实风格目标、真实平台能力和真实产物，不为了泛化而泛化。产品宣传、轻教程、case demo、发布片、品牌短片、纪录片式案例、概念片、社媒短视频、平台能力验证和 blocked 导演稿应有不同结构。不同视频的叙事、镜头语言、光线、色彩、声音、节奏和代入感都可以不同。

### 2. Product First

宣传片开头必须先建立产品认知：产品名、产品类别、工作场景和核心价值。具体 case 只是证明产品能力的载体，不能抢走主线。

### 3. Real Workflow Over Abstract AI

AI 产品视频必须把“用户输入、上下文进入、Agent 执行、工具调用、状态追踪、artifact 产出、review/approve、部署/导出”串成真实工作流。抽象科技感只能增强空间，不替代产品信息架构。

### 4. Motion Serves Meaning

镜头运动必须对应产品动作：

- 慢推建立可信产品实体。
- 横移表达从入口到工作台的流程。
- layer reveal 表示上下文、工具、状态和产物之间的关系。
- action-driven transition 用发送、调用、完成、批准等真实动作触发转场。
- 结尾 pull-back 回到系统总览和产品名。

### 5. Deterministic Text Layer

视频模型不负责大量可读文字。产品名、导航 tab、步骤字幕、artifact 标题、状态标签和 CTA 必须通过 deterministic overlay / timeline / 后期合成控制。

### 6. Evidence Before Success

视频平台声明“生成成功”不等于产物满足目标。最终必须下载或打开 artifact，并用 `ffprobe` 校验真实时长、分辨率、帧率、音视频轨和文件大小。

## Adapted To Hub

| 机制 | Hub 落位 | 说明 |
| --- | --- | --- |
| 视频创作触发和工作流 | `skills/video-creation/SKILL.md` | 新增可重复触发技能 |
| 高级产品宣传片模式 | `skills/video-creation/references/product-launch-film-patterns.md` | 长模式、检查表和反模式放 reference |
| 来源和取舍证据 | 本报告 | 记录来源、分析范围和采纳/不采纳 |
| 入口发现 | `AGENTS.md`、`skills/README.md` | 只放短触发，不把长拉片规则塞入口 |

## Not Adopted

- 不复制 Apple / Google 的品牌、人物、画面、口号、配色或资产。
- 不把某个 Maxwell Studio case 业务文案写入 Hub 通用规则。
- 不把本地下载路径写成长期运行依赖；路径只作为本次来源证据。
- 不承诺任何视频平台都能长片生成；必须先过工具 gate 和 artifact 校验。

## Verification

本次验证应覆盖：

- 新技能目录、frontmatter name、`skills/README.md` 登记一致。
- `AGENTS.md` 有短触发路由，能从“视频创作/宣传片/教程视频/shot list/timeline”等表达找到技能。
- reference 路径由 `SKILL.md` 直接链接。
- `git diff --check` 通过。
- 搜索 `video-creation`、`产品宣传片`、`timeline` 能追到入口与 reference。

## Follow-ups

- 后续如果用户继续提供更多参考片，可以在同一 reference 中追加“模式”，或另建 dated analysis 报告记录来源证据。
- 如果未来形成可运行的视频 QA 脚本，可放入 `skills/video-creation/scripts/`，但当前先保持为流程和检查表。
