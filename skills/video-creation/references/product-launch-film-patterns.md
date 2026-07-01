# Product Launch Film Patterns

日期：2026-06-24
来源：本地参考视频拉片与 Maxwell Studio 视频生成任务复盘。
用途：作为 `video-creation` 的按需参考；用于产品宣传片、AI workflow、教程、case demo、纪录片式案例、品牌短片、概念片、平台能力验证和 blocked 导演稿。

## Source Scope

参考样本：

- Apple launch film：产品实体感、暗场光线、慢推、宏观细节、发布片收束。
- Google Workspace AI film：真实工作流、UI 任务路径、用户价值、短字卡、AI 作为业务连接器。

只吸收创作机制，不复制品牌资产、镜头画面、口号、人物或 visual identity。

## Core Rule: 实事求是

视频创作先尊重真实素材、真实主题、真实风格、真实场景、真实平台能力和真实结果。

反面是漂浮：抽象光效、随机 UI、虚构能力、无验收成功声明、为通用抹掉具体场景。

## Creative Variables

写 shot list 前先冻结：

| Variable | Questions |
| --- | --- |
| 视频类型 | 宣传、教程、发布、品牌、纪录、case demo、概念、社媒、能力验证？ |
| 观众关系 | 第一次认识、学习、购买、内部评审、情绪共鸣？ |
| 叙事结构 | 教程、问题-解决、宏观到微观、人物带入、before/after？ |
| 镜头语言 | 慢推、横移、手持、微距、俯视、屏幕录制、快切？ |
| 光线/色彩 | 暗场、自然光、办公、舞台、品牌点缀、纪实低饱和？ |
| 节奏/声音 | 克制、递进、快节奏、旁白、音乐驱动、同期声？ |
| 信息密度 | 大字少信息、教程步骤、UI 细节、业务上下文、CTA？ |

## Scenario Outlines

| 类型 | 目标 | 结构 |
| --- | --- | --- |
| 产品宣传片 | 记住产品是什么、在哪工作、为什么可信 | 产品名/总览 -> 入口 -> 能力链路 -> 控制点 -> 产物 -> 收束 |
| 品牌短片 | 记住气质、价值观或情绪主张 | 命题 -> 真实细节 -> 价值与行动 -> 峰值 -> 标识 |
| 纪录片式案例 | 通过人物、问题和过程相信结果 | 人/团队 -> 问题 -> 过程/选择 -> 工具介入 -> 结果 |
| 概念片/愿景片 | 表达未来方向但保留锚点 | 问题/愿景 -> 抽象与真实交替 -> 能力显形 -> 回到产品 |
| 社媒短视频 | 一个记忆点和快速传播 | 1-3s hook -> 反差/问题 -> 解决动作/结果 -> CTA |
| 轻教程 | 看完知道怎么做一件事 | 目标 -> Step 1/2/3 -> review/refine -> export/approve |
| Case demo | 用具体业务 case 证明能力 | case brief -> 输入材料 -> Agent 执行 -> 工具/状态 -> artifacts -> 人审 |
| Launch film | 发布质感和产品愿景 | 识别开场 -> 产品空间 -> 3-5 能力段 -> proof montage -> 产品名 |
| 能力验证 | 证明生成/timeline/音频/字幕/artifact 链路 | tool gate -> clip -> timeline -> artifact -> media probe -> verdict |
| Blocked 导演稿 | 能力不足时交付可继续方案 | 阻塞层 -> 证据 -> 已有素材 -> shot list -> prompt -> timeline plan -> 最小解除动作 |

不要把所有视频套成暗色科技 UI 或教程录屏。

## Product Film Principles

- 产品认知先于 case：前 5-10 秒让观众知道产品名、类别、工作场景。
- 每个镜头绑定产品问题：入口、上下文、编排、工具、状态、产物、审批、部署。
- 真实 UI 是主角：导航、面板、状态、artifact 标题来自真实产品或 deterministic overlay。
- 视频模型做运动和氛围，文字、步骤、产品名、状态和 CTA 用后期确定性控制。
- 参考片拆机制，不拆表象：学产品叙事、信息层级、节奏，不学随机光效。

## 45-60s Product + Light Tutorial Structure

| Time | Role | Question | Pattern |
| --- | --- | --- | --- |
| 0-6s | Product identity | 这是什么？ | 产品名/Studio 总览 |
| 6-12s | Entry | 从哪里开始？ | Chat 入口、用户 prompt |
| 12-20s | Context | 如何理解资料？ | Files / Knowledge / source context |
| 20-30s | Orchestration | Agent 如何执行？ | preset、thread、tool calls、trace |
| 30-40s | Output | 产物如何出现？ | artifacts、版本、变体 |
| 40-50s | Control | 人如何 review？ | refine、approve、状态变化 |
| 50-60s | System view | 为什么可信？ | Skills / Tools / Deploy / end card |

只在当前任务是产品宣传 + 轻教程混合片时使用；其它类型回到 Scenario Outlines。

## Camera And Overlay

| Pattern | Use |
| --- | --- |
| Slow push-in | 建立产品实体或工作台可信感 |
| Horizontal glide | 串联 Chat、Studio、Agent、Tools |
| Layer reveal | 前景 UI、中景状态/工具、远景总览 |
| Action-driven transition | send、tool call、artifact ready、approve 触发转场 |
| Hold for readability | UI 文字镜头停到能识别标题和状态 |
| End pull-back | 从细节拉回系统总览和产品名 |

Overlay 规则：

- 2-5 个词优先。
- 使用真实产品词：Chat、Agent Presets、Knowledge、Tools、Skills、Deploy、Artifacts。
- 不遮挡 UI 主路径。
- 关键文字不交给视频模型生成。

## Generation Prompt Pattern

```text
目标镜头：
真实素材：
产品价值：
镜头运动：
可见 UI 锚点：
确定性 overlay 计划：
风格约束：
negative prompt：
```

常用约束：

- UI stays sharp and recognizable.
- Do not invent navigation labels.
- Readable product text is provided by overlay, not generated in-scene.
- No abstract sci-fi, no humanoid robots, no random English UI.

## Tool And Timeline Gate

调用视频平台/MCP 前确认：

- 单次生成时长、start image、多图、video-to-video、音频、字幕、timeline。
- 成本估算、质量报告、source manifest、approval state。
- `render_video` 是生成模型还是确定性合成器。
- 是否能真正串联 clips，不能只看工具返回 `duration`。

成品必须用媒体探针或等价工具校验真实文件：

```bash
ffprobe -hide_banner -v error \
  -show_entries format=duration,size,bit_rate \
  -show_entries stream=index,codec_type,codec_name,width,height,avg_frame_rate,duration \
  -of json /path/to/final.mp4
```

结论：

- `Artifact valid`：真实时长、分辨率、音视频轨满足目标。
- `Partial`：clip 成功，但长片、音频、overlay 或 timeline 不完整。
- `Blocked`：平台能力、schema、成本/审批或素材权限缺失。

## Maxwell / Aion Lessons

- `generate_video` 只能短 clip 时，先分 shot，再 timeline。
- `render_video` 声称 60 秒但 artifact 只有 10 秒时，以媒体校验为准。
- 不把 video URL 误传为 `audio_url`；无真实音乐先省略或用音频工具。
- timeline renderer 不能串联多 clip 时，返回 structured blocked。
- 用户要求“通过平台做”时，优先平台 Chat/MCP；本地 ffmpeg 只做校验或用户允许的补救合成。

## Review Checklist

- 是否先选择视频类型、风格和观看场景？
- 前 10 秒是否能识别产品名、类别和工作场景？
- 每个主要镜头是否绑定真实产品 UI 或信息架构？
- case 是否服务产品认知，而不是抢走主线？
- 是否有随机 UI、乱码、虚构 tab、抽象科幻转场？
- 关键文字是否 deterministic overlay 控制？
- 是否检查真实时长、分辨率、帧率、音视频轨和文件大小？
- blocked 时是否给出阻塞层、证据和下一步最小动作？

## Anti-Patterns

- 把宣传片做成纯教程录屏，缺少品牌和系统认知。
- 把教程 case 做成唯一目的，观众看完不知道平台是什么。
- 所有视频都套同一种暗色科技 UI。
- 大量抽象光效，真实 UI 一闪而过。
- 让视频模型生成长段可读文字。
- 工具返回成功后不下载、不探针、不检查媒体属性。
