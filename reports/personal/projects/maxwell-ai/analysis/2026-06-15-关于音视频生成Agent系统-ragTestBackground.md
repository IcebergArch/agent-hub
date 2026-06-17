日期：2026-06-15
类型：analysis
项目：maxwell-ai
来源：模拟 RAG 导入测试语料
版式：系统背景手册，按定位、架构、流程、数据、故障、SOP 与检索样例组织

# 关于音视频生成 Agent 系统-ragTestBackground

> 本文是一份模拟背景文件，只用于知识库导入、切片、向量检索、混合检索和召回问答测试。文中系统、模块名、参数、SOP、故障码与业务规则均为虚构资料，不代表真实生产配置。

## 1. 系统概览

「AIVA Studio Agent」是一套面向企业内容生产团队的音视频生成 Agent 系统。系统目标是把「创意需求」自动拆解为脚本、分镜、声音、画面、合成、审核与导出任务，并通过多 Agent 协作完成短视频广告、产品演示、培训课程、角色对话剧和播客切片等内容。

系统当前主要服务三个场景：

- 营销短视频：根据产品卖点生成 15 秒、30 秒、60 秒视频，支持竖屏 9:16、横屏 16:9 和方形 1:1。
- 企业培训视频：把 SOP、知识库文章或产品说明转成讲解脚本，生成配音、字幕、章节封面和示意画面。
- 角色化内容生产：围绕固定虚拟角色生成连续剧集，要求人物设定、声音、口头禅、镜头风格和字幕样式保持一致。

系统默认工作语言为中文，支持中英双语字幕。所有生成任务必须保留审计记录，包括用户输入、召回知识片段、模型路由、生成参数、审核结果和导出版本。

## 2. Agent 角色

| Agent 名称 | 责任 | 关键输入 | 关键输出 |
| --- | --- | --- | --- |
| DirectorAgent | 理解创意目标，制定生成计划，拆解任务 | CreativeBrief、品牌规范、目标平台 | ProductionPlan |
| ScriptAgent | 生成脚本、旁白、对白和字幕草稿 | ProductionPlan、SOP、知识片段 | ScriptDraft |
| StoryboardAgent | 把脚本拆成镜头，定义景别、运动和画面提示词 | ScriptDraft、视觉风格规范 | ShotList |
| VoiceAgent | 选择音色、生成配音、校正停顿和语速 | ScriptDraft、VoiceProfile | AudioTrack |
| MusicAgent | 生成或选择背景音乐、转场音和音效 | 情绪标签、平台规范 | MusicCue、SfxCue |
| VideoAgent | 生成每个镜头的视频素材，处理续写和风格一致性 | ShotList、AssetPlan | VideoClip |
| SyncAgent | 做字幕对齐、口型同步和音画时序校准 | AudioTrack、VideoClip、Subtitle | SyncReport |
| ReviewAgent | 执行版权、品牌、肖像、合规和安全审核 | RenderArtifact、PolicyPack | SafetyDecision |
| ExportAgent | 按平台导出不同码率、封面、字幕和元数据 | ApprovedArtifact、ExportPreset | ExportBundle |
| SchedulerAgent | 管理任务队列、重试、优先级和资源预算 | RenderJob、QuotaPolicy | JobState |

DirectorAgent 是编排入口。其他 Agent 不直接决定最终发布，必须通过 ReviewAgent 的审核门禁。SchedulerAgent 不理解业务语义，只根据资源、优先级和失败策略调度。

## 3. 核心组件

### 3.1 Control Plane

Control Plane 负责项目、权限、任务状态、审批流和审计记录。每个生成项目会创建一个 Project ID，例如 `avp_20260615_demo_001`。所有 Agent 的中间产物都挂在 Project ID 下，便于回溯。

### 3.2 Knowledge Retriever

Knowledge Retriever 是 RAG 入口，负责从知识库召回品牌规范、角色设定、业务 SOP、镜头模板、声音规范和历史审核结论。默认检索策略是「向量检索 + 关键词过滤 + 权限过滤 + 重排」。初期可以只使用 PostgreSQL + pgvector 实现向量召回。

Knowledge Retriever 的核心约束：

- 只能召回当前用户、当前业务空间和当前项目可见的知识片段。
- 召回结果必须写入审计日志，字段包括 chunk_id、document_id、score、引用文本摘要和用途标签。
- 当知识片段标记为 `must_follow` 时，Agent 不能把它降级为普通参考。
- 当知识片段互相冲突时，优先级为：项目级规范 > 品牌级规范 > 团队 SOP > 通用模板。

### 3.3 Prompt Composer

Prompt Composer 把用户需求、系统策略、知识片段和模型参数合成为模型调用请求。它不直接生成内容，而是负责提示词模板、引用注入、长度控制、敏感词替换和输出格式约束。

### 3.4 Model Gateway

Model Gateway 对接文本、图像、视频、音频和审核模型。系统中的模拟模型路由如下：

| 路由名 | 用途 | 默认超时 | 备注 |
| --- | --- | --- | --- |
| `text.plan-pro` | 需求理解、脚本规划、分镜规划 | 45 秒 | 高质量文本规划 |
| `image.style-ref` | 角色参考图、场景参考图生成 | 90 秒 | 用于视觉锚点 |
| `video.fast-preview` | 快速视频预览 | 180 秒 | 低成本草稿 |
| `video.cinematic-xl` | 高质量最终视频 | 900 秒 | 需要排队资源 |
| `audio.voice-stable` | 旁白、对白、角色音色 | 120 秒 | 支持情绪标签 |
| `music.loop-maker` | BGM、短循环音乐 | 150 秒 | 支持无缝循环 |
| `sync.lipsync-v2` | 口型同步和表情微调 | 240 秒 | 依赖清晰人脸 |
| `review.policy-check` | 内容安全、版权和品牌审核 | 60 秒 | 必经门禁 |

### 3.5 Asset Registry

Asset Registry 保存用户上传素材、生成素材、参考图、声音样本、字幕、导出文件和审核证据。预览素材保留 14 天，最终导出文件保留 90 天，源需求和审计记录保留 365 天。

### 3.6 Render Queue

Render Queue 执行生成任务。默认优先级为 `interactive > preview > batch > archive`。交互式任务最多占用总资源的 40%，批处理任务最多占用 50%，归档重渲染最多占用 10%。

## 4. 数据对象

### 4.1 CreativeBrief

CreativeBrief 是用户输入的创意简报，包含目标、受众、平台、时长、风格、禁止事项和参考资料。

关键字段：

- `brief_id`：创意简报 ID。
- `target_platform`：目标平台，例如 `douyin`、`bilibili`、`youtube`、`internal_lms`。
- `duration_sec`：目标时长，允许偏差默认是正负 8%。
- `tone`：语气，例如专业、轻松、热血、克制、教学。
- `must_include`：必须出现的信息。
- `must_avoid`：禁止出现的信息。
- `knowledge_scope`：允许召回的知识库范围。

### 4.2 ProductionPlan

ProductionPlan 是 DirectorAgent 的输出，定义内容结构、任务拆解、模型路由和质量目标。它必须列出每个 Agent 的输入输出边界。

### 4.3 Shot

Shot 表示一个镜头。营销短视频单镜头建议 2 到 6 秒，教学视频单镜头建议 4 到 12 秒。竖屏短视频超过 8 秒的静态镜头会被 ReviewAgent 标记为 `LOW_VISUAL_DENSITY`。

Shot 的重要字段：

- `shot_id`
- `scene_description`
- `camera_type`
- `motion`
- `visual_prompt`
- `negative_prompt`
- `duration_sec`
- `required_assets`
- `continuity_anchor`

### 4.4 AudioTrack

AudioTrack 包含配音、背景音乐和音效轨道。配音目标响度为 `-16 LUFS`，True Peak 不高于 `-1 dBTP`。背景音乐不得持续遮盖人声，旁白段落中 BGM 应比人声低至少 12 dB。

### 4.5 RenderJob

RenderJob 是队列任务，状态包括 `pending`、`running`、`waiting_review`、`succeeded`、`failed`、`cancelled`。失败任务默认最多重试 2 次。视频最终渲染超过 900 秒仍未完成时标记为 `RENDER_TIMEOUT`。

## 5. 标准生成流程

1. 用户提交 CreativeBrief，选择目标平台、时长、风格和知识库范围。
2. Knowledge Retriever 召回品牌规范、产品信息、禁用表达、历史示例和 SOP。
3. DirectorAgent 生成 ProductionPlan，明确脚本结构、镜头数量、音频策略和模型预算。
4. ScriptAgent 生成 ScriptDraft，输出旁白、对白、字幕草案和章节结构。
5. StoryboardAgent 把 ScriptDraft 拆成 ShotList，并为每个镜头生成视觉提示词。
6. VoiceAgent 生成配音，MusicAgent 生成或选择背景音乐与音效。
7. VideoAgent 生成每个镜头的视频片段，必要时用参考图保持角色和风格一致。
8. SyncAgent 对齐字幕、口型、音乐入点和镜头切换点。
9. ReviewAgent 执行审核，发现问题时回写 ReviewNote。
10. ExportAgent 生成平台导出包，包括视频文件、字幕文件、封面图和元数据。

系统要求所有可发布内容必须经过第 9 步审核。若用户只生成草稿预览，可以跳过 ExportAgent，但不能跳过安全审核。

## 6. RAG 使用规则

RAG 召回只在需要事实一致性、品牌一致性、角色一致性或流程一致性时启用。对于纯创意发散任务，可以降低召回权重，但仍需保留用户指定的 `must_include` 和 `must_avoid`。

推荐召回类型：

- 品牌规范：品牌语气、色彩、Logo 使用、禁用词、免责声明。
- 产品事实：功能参数、价格区间、版本差异、支持平台。
- 角色设定：角色姓名、身份、声音、口头禅、服装、性格边界。
- 业务 SOP：审核流程、发布流程、素材归档流程。
- 历史示例：高点击脚本、失败案例、审核驳回原因。
- 平台规范：视频比例、时长、字幕安全区、封面尺寸、码率要求。

召回片段注入 Prompt 时必须带来源标签。示例标签格式：

```text
[knowledge:brand_guideline:v3:chunk_018]
[knowledge:product_manual:2026q2:chunk_044]
[knowledge:character_bible:mei:chunk_007]
```

当召回结果低于阈值 `0.72` 时，Prompt Composer 应标记 `knowledge_confidence=low`，并要求 Agent 输出「需要人工确认」字段。初期 PostgreSQL 向量库可以先使用 cosine distance，TopK 默认取 6，最大取 20。

## 7. PostgreSQL 向量库模拟设计

初期可以使用 PostgreSQL + pgvector 作为向量库能力，不额外引入独立向量数据库。模拟表以 `knowledge_` 开头：

| 表名 | 用途 |
| --- | --- |
| `knowledge_bases` | 知识库基本信息，例如名称、业务空间、可见范围 |
| `knowledge_documents` | 导入文档元数据，例如文件名、mime、hash、导入状态 |
| `knowledge_chunks` | 文档切片、embedding、token 数、标题路径和召回元数据 |
| `knowledge_import_jobs` | 导入任务状态、错误信息和耗时 |

`knowledge_chunks` 建议字段包括 `chunk_id`、`document_id`、`content`、`content_hash`、`embedding`、`metadata`、`token_count`、`created_at`。metadata 中保存页码、章节标题、来源类型、业务标签和 ACL 快照。

推荐检索顺序：

1. 按业务空间、知识库、文档状态和权限过滤候选片段。
2. 用 pgvector 对 query embedding 做近邻搜索。
3. 对 TopK 结果做关键词加权，优先匹配标题、故障码、参数名和 SOP 步骤名。
4. 合并相邻 chunk，避免把同一段流程拆碎。
5. 返回给 Agent 时附带来源和 score，不直接拼成长文本。

## 8. 质量标准

### 8.1 视频质量

- 分辨率必须符合导出预设，竖屏短视频默认 1080x1920。
- 画面主体不能被字幕遮挡，字幕安全区底部至少留 120 像素。
- 连续镜头的人物发色、服装主色、Logo 朝向和产品外观应保持一致。
- 最终视频不得包含闪烁、重影、明显肢体异常或无法解释的物体漂移。
- 如果角色脸部连续 2 秒以上不可见，SyncAgent 不应启用口型同步。

### 8.2 音频质量

- 旁白目标响度为 `-16 LUFS`，True Peak 不高于 `-1 dBTP`。
- 角色对白的平均语速建议为每分钟 220 到 280 个中文字符。
- 教学类视频每 45 到 60 秒应有一次自然停顿或章节切换。
- BGM 不得覆盖人声，旁白段落中 BGM 应比人声低至少 12 dB。
- 声音克隆必须有授权标记 `voice_consent=true`。

### 8.3 字幕质量

- 中文字幕每行不超过 18 个汉字，英文字幕每行不超过 42 个字符。
- 字幕入点允许比语音提前 80 毫秒，出点允许比语音延后 120 毫秒。
- 双语字幕必须避免覆盖产品按钮、价格、二维码和警示文字。
- 平台为 `internal_lms` 时，必须额外导出 `.srt` 和 `.vtt`。

## 9. 审核门禁

ReviewAgent 使用五类审核门禁：

| 门禁 | 说明 | 失败示例 |
| --- | --- | --- |
| ContentSafetyGate | 内容安全、仇恨、成人、暴力、自伤等 | `UNSAFE_CONTENT` |
| BrandGate | 品牌语气、Logo、免责声明、禁用表达 | `BRAND_TONE_MISMATCH` |
| CopyrightGate | 音乐、素材、人物肖像和引用版权 | `COPYRIGHT_RISK` |
| LikenessGate | 真人声音、脸部和身份模仿授权 | `VOICE_CONSENT_MISSING` |
| PlatformGate | 平台时长、比例、字幕安全区、码率 | `PLATFORM_SPEC_VIOLATION` |

审核失败时，ReviewAgent 不直接重写全部内容，而是生成 ReviewNote。DirectorAgent 根据 ReviewNote 决定重试局部镜头、替换音轨、调整脚本或要求人工确认。

## 10. 常见故障码

| 故障码 | 含义 | 首选处理 |
| --- | --- | --- |
| `AUDIO_DESYNC` | 音频与字幕或口型不同步 | 重新运行 SyncAgent，锁定字幕时间轴 |
| `VIDEO_FLICKER` | 连续帧闪烁明显 | 降低运动幅度，使用同一参考图重渲染 |
| `STYLE_DRIFT` | 角色或品牌风格漂移 | 增加 continuity_anchor，召回角色设定 |
| `LIP_SYNC_LOW_CONFIDENCE` | 口型置信度低 | 检查人脸清晰度，必要时改用旁白镜头 |
| `RENDER_TIMEOUT` | 渲染超过模型路由超时 | 降级到 fast-preview 或拆分镜头 |
| `ASSET_MISSING` | 必要素材不存在或无权限 | 提醒用户上传或替换素材 |
| `BRAND_TONE_MISMATCH` | 文案不符合品牌语气 | 召回品牌规范并重写脚本 |
| `LOW_VISUAL_DENSITY` | 画面变化不足 | 增加镜头切换、字幕强调或 B-roll |
| `VOICE_CONSENT_MISSING` | 声音克隆缺少授权 | 禁用该声音并要求上传授权 |
| `PLATFORM_SPEC_VIOLATION` | 导出不符合平台规范 | 重新选择 ExportPreset |

故障处理优先级为：安全与授权问题 > 平台硬性规范 > 事实和品牌一致性 > 画质音质优化 > 创意偏好。

## 11. 权限与合规

系统采用空间级和项目级双层权限。知识库、素材库和导出文件均需要 ACL 检查。被标记为 `restricted` 的知识片段不能进入通用模型提示词，只能在专用企业模型路由中使用。

合规原则：

- 不允许无授权克隆真实人物声音。
- 不允许生成误导性新闻、伪造采访或伪造官方声明。
- 使用客户 Logo 时必须召回品牌规范。
- 引用第三方音乐或图片时必须记录来源和授权状态。
- 最终发布包必须包含模型生成标记和审计 ID。

当用户要求「去掉水印」「模仿某真人声音」「伪造会议录像」等高风险操作时，ReviewAgent 应返回 `blocked`，并说明需要授权或拒绝原因。

## 12. 业务 SOP 示例

### 12.1 营销短视频 SOP

1. 读取产品卖点、目标受众、价格和促销限制。
2. 召回品牌语气和禁用表达。
3. 生成 3 个脚本方向：痛点型、场景型、对比型。
4. 每个方向生成 5 到 8 个镜头。
5. 先用 `video.fast-preview` 生成预览。
6. 用户选择方向后，使用 `video.cinematic-xl` 生成最终画面。
7. 执行 BrandGate、PlatformGate 和 ContentSafetyGate。
8. 导出 9:16 主版本、1:1 裁切版本和封面图。

### 12.2 企业培训视频 SOP

1. 导入 SOP 文档或知识库文章。
2. ScriptAgent 提取学习目标、关键步骤、风险提醒和测试题。
3. StoryboardAgent 为每个章节生成标题页、流程图画面和操作示意镜头。
4. VoiceAgent 使用清晰、稳定、低情绪波动的讲解音色。
5. 字幕必须导出 `.srt` 和 `.vtt`。
6. ReviewAgent 检查步骤遗漏、术语一致性和风险提示。
7. ExportAgent 输出 LMS 版本和可下载讲义摘要。

### 12.3 角色对话剧 SOP

1. 召回角色设定、关系设定、口头禅和上一集摘要。
2. ScriptAgent 生成对白，避免角色说出不符合人设的表达。
3. StoryboardAgent 使用 continuity_anchor 锁定角色外观。
4. VoiceAgent 使用角色专属 VoiceProfile。
5. SyncAgent 对角色近景启用 `sync.lipsync-v2`。
6. ReviewAgent 检查 STYLE_DRIFT、人物身份混淆和对白违和。
7. 若角色一致性低于 0.82，必须局部重渲染。

## 13. 可用于召回测试的事实点

以下事实点适合用作 RAG 检索测试：

- AIVA Studio Agent 的编排入口是 DirectorAgent。
- Render Queue 默认优先级是 `interactive > preview > batch > archive`。
- 配音目标响度是 `-16 LUFS`，True Peak 不高于 `-1 dBTP`。
- 预览素材保留 14 天，最终导出文件保留 90 天，源需求和审计记录保留 365 天。
- `STYLE_DRIFT` 表示角色或品牌风格漂移，首选处理是增加 continuity_anchor 并召回角色设定。
- 竖屏短视频默认分辨率是 1080x1920，字幕安全区底部至少留 120 像素。
- 当召回分数低于 0.72 时，Prompt Composer 应标记 `knowledge_confidence=low`。
- `voice_consent=true` 是声音克隆授权标记。
- 企业培训视频必须导出 `.srt` 和 `.vtt` 字幕。
- 角色一致性低于 0.82 时，角色对话剧必须局部重渲染。

## 14. 检索测试问题

可以使用以下问题检查知识库导入后的召回效果：

1. 音视频生成系统里哪个 Agent 负责任务编排入口？
2. `STYLE_DRIFT` 的含义是什么，应该怎么处理？
3. 初期如果只使用 PostgreSQL 做向量库，建议建立哪些 `knowledge_` 表？
4. 配音响度和 True Peak 的默认标准是什么？
5. 预览素材、最终导出文件和审计记录分别保留多久？
6. 营销短视频 SOP 中，什么时候使用 `video.cinematic-xl`？
7. 企业培训视频为什么必须导出 `.srt` 和 `.vtt`？
8. RAG 召回片段低于 0.72 分时，Prompt Composer 应该如何标记？
9. ReviewAgent 有哪些审核门禁？
10. 声音克隆缺少授权时对应的故障码是什么？

## 15. 术语表

| 术语 | 定义 |
| --- | --- |
| CreativeBrief | 用户提交的创意简报，描述目标、受众、平台、风格和限制 |
| ProductionPlan | DirectorAgent 生成的生产计划 |
| ShotList | 分镜列表，每个 Shot 对应一个可生成的视频片段 |
| continuity_anchor | 保持角色、产品和场景一致性的连续性锚点 |
| VoiceProfile | 声音配置，包括音色、语速、情绪范围和授权状态 |
| ExportPreset | 导出预设，包括平台、比例、码率、字幕和封面规则 |
| ReviewNote | 审核失败或需修改时产生的结构化反馈 |
| SafetyDecision | ReviewAgent 的审核结论，可以是 approved、needs_revision 或 blocked |
| Knowledge Retriever | 负责知识召回、权限过滤和来源记录的 RAG 组件 |
| Prompt Composer | 负责把需求、知识片段、策略和格式约束组装成模型请求的组件 |

## 16. 导入建议

这份文件适合按 Markdown 标题切片。建议切片大小为 500 到 900 中文字符，重叠 80 到 120 字符。表格应尽量作为整体保留，尤其是 Agent 角色表、模型路由表、故障码表和检索测试问题。

导入时可以把 metadata 设置为：

```json
{
  "source_type": "simulated_background",
  "domain": "audio_video_generation_agent",
  "project": "maxwell-ai",
  "language": "zh-CN",
  "test_purpose": "rag_import_and_retrieval",
  "contains_tables": true
}
```

建议用以下查询覆盖不同检索类型：

- 精确术语查询：`AUDIO_DESYNC 怎么处理`
- 参数查询：`旁白目标响度是多少`
- 流程查询：`营销短视频 SOP 的步骤`
- 架构查询：`Knowledge Retriever 负责什么`
- 权限查询：`restricted 知识片段能不能进通用模型提示词`
- 表结构查询：`knowledge_chunks 应该有哪些字段`
