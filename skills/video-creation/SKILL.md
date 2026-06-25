---
name: video-creation
description: 当用户要求制作、优化、拉片、规划或评审产品宣传片、教程视频、发布视频、AI 工作流视频、视频 storyboard、shot list、timeline、字幕/overlay 或视频生成 workflow 时使用。
---

# Video Creation

用这个技能处理复杂视频创作，包括产品宣传片、教程视频、发布片、纪录片式案例、品牌短片、AI workflow 展示片和生成式视频 workflow。继承 Hub 级“实事求是”纲要：先尊重真实素材、真实主题、真实风格目标、真实平台能力和真实产物校验，再决定创作方法；不要为了泛化而泛化。

## Trigger

当用户说以下任一类需求时使用：

- “做一条产品宣传视频 / 发布视频 / 教程视频 / demo video”
- “参考这个视频学习运镜 / 拉片 / 吸收优点”
- “写 shot list / 分镜 / timeline / overlay / 字幕 / 音乐节奏”
- “用 AI 生成视频 / 通过某个平台生成视频 / MCP 视频 workflow”
- “让画面高级但贴真实产品 UI”

## Workflow

1. **Freeze Context**
   - 确认产品、目标观众、主叙事、素材来源、运行平台、可用视频/音频/timeline 工具和最终验收信号。
   - 如果涉及真实产品 UI，冻结真实页面、tab、导航、面板标题、关键文案和禁止虚构的边界。
   - 先分清本轮主目标是 **Run Case** 还是 **Build Capability**：当目标是用当前测试 case、当前 chat 和既有平台能力出片时，默认只做素材储备、剧本/分镜、prompt、页面/Chat 操作和产物验收；不得因为产物效果差就擅自切到代码、schema、config 或系统能力改造。是否进入 Build Capability 取决于当前任务的真实 owner 和验收目标：如果任务本身就是 SDK/平台/MCP/前后端能力接入、缺陷修复、配置收口或架构调整，或者 Run Case 发现能力阻塞且已和用户确认 owner、影响面和最小改动，则可以进入实现；不要用固定关键词判断。
   - 视频 Run Case 涉及页面、MCP/tool schema、多素材、artifact 存储或最终媒体验收时，提前按 `../task-execution-lifecycle/references/case-execution-recovery.md` 做 context/tool/artifact gate；不要等用户纠偏后才加载恢复规则。

2. **Choose Creative Frame**
   - 先判断视频类型、风格和观看场景：产品宣传、轻教程、品牌片、发布片、case demo、纪录片式讲述、概念片、社媒短视频、平台能力验证或 blocked 导演稿。
   - 明确本片的创作变量：叙事结构、镜头语言、光线、色彩、节奏、声音、代入感、画面真实度、信息密度和情绪曲线。
   - 不同视频可以有完全不同的纲要；不要把“产品宣传 UI 片”的结构套给所有视频。

3. **Study References**
   - 对用户给的视频、截图或链接做来源确认；可访问本地视频时优先抽帧、看时长、帧率和节奏。
   - 只吸收机制：叙事、构图、镜头运动、光线、色彩、节奏、代入感、信息层级、转场逻辑、声音功能；不复制品牌资产、口号或外观装饰。

4. **Define Narrative And Value**
   - 先选择当前场景的主纲要，不把所有视频都套成一种模板。
   - 产品宣传场景先写清产品认知主线，再让 case 服务主线；教程场景先写清用户操作路径；平台验证场景先写清工具能力和失败边界。
   - 非产品视频也要写清观众为什么会进入、跟随、相信和记住这条片。
   - 每个主要镜头必须回答一个叙事或价值问题，而不是只提供气氛。

5. **Plan Deterministic Layers**
   - 视频模型负责运动、氛围和镜头，不负责大量可读文字。
   - 产品名、真实 UI 文案、步骤字幕、artifact 标题、状态标签、采访字幕、章节标题和 CTA 优先用 deterministic overlay / timeline / 后期合成控制。

6. **Generate And Assemble**
   - 先确认工具 gate：模型时长、单图/多图能力、成本、质量报告、source manifest、approval state、timeline 合成能力。
   - 生成时按 shot 分短 clip；合成时用 timeline 串联、字幕、转场、音频和结尾标识。
   - 如果平台能力不足，返回结构化 blocked/gap、可执行 shot prompt 和 timeline plan，不把未验证能力说成已完成。
   - Run Case 模式下遇到能力限制时，优先调整脚本、素材、shot prompt、deterministic overlay、素材顺序、模型参数和 Chat 指令；不要把“为当前 case 临时补能力”当作默认下一步。

7. **Verify Artifact**
   - 下载或打开最终产物，用媒体探针检查真实时长、分辨率、帧率、音视频轨和文件大小。
   - 视觉上检查 UI 是否可辨认、文字是否由确定性层控制、是否偏离产品信息架构、是否只有抽象特效。

## References

- `references/product-launch-film-patterns.md`：视频创作变量、按场景选择纲要、高级产品宣传片和 AI 工作流视频的拉片模式、shot gate、overlay gate、timeline 验证和反模式。

## Output

根据任务阶段输出其一或组合：

- 分镜表：时间码、画面、产品价值、素材、运动、overlay、转场、声音。
- 生成提示词：每个 shot 的 prompt、negative prompt、素材引用和工具参数。
- Timeline plan：clip 顺序、时长、overlay、音频、转场和验收检查。
- Blocked/gap：阻塞层、证据、可继续的最小动作。
- Artifact report：产物路径/URL、媒体校验、质量问题和下一轮修正建议。

## Quality Bar

- 产品宣传优先于炫技；case 是承载，不是唯一目的。
- 按真实类型和风格选方法；不要把产品宣传、教程、case demo、纪录片、品牌片、概念片、平台验证和 blocked 导演稿硬套成同一套结构。
- 真实产品 UI 和信息架构必须可辨认；不要用抽象科幻画面替代产品工作流。
- 参考片只借镜头语言，不复制品牌、人物、画面或营销文案。
- 不能让视频模型生成大量可读文字；关键文字由后期确定性控制。
- 最终产物必须用实际媒体信息验证；不能只相信工具返回的声明。
