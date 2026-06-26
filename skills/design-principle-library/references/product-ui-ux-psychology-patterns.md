# Product UI UX Psychology Patterns

日期：2026-06-25
类型：reference
归属 Skill：design-principle-library
适用：产品 UI、交互、配色、视觉样式、动效、等待反馈、表单、可访问性、设计评审
来源策略：优先采用官方标准、一手设计系统、NN/g、Baymard、IxDF 等可追溯资料；设计建议需要结合当前产品任务与用户场景再落地。

## 目的

这份 reference 用来把“界面感觉不对”转成可判断、可执行、可验证的设计动作。它不是视觉百科，也不是流行风格清单；使用时先用设计哲学确定体验方向，再用心理模型解释用户感受，最后按真实场景选择配色、样式、交互和动效。

## 触发

- 用户说 UI “太快”“太慢”“太急促”“太抢眼”“不舒服”“看不清”“不知道发生了什么”“像没反应”。
- 设计或修改 loading、thinking、running、progress、toast、error、empty、success、cancel、undo 等状态反馈。
- 做配色、暗色模式、状态色、视觉层级、按钮权重、卡片/面板/表格/表单样式。
- 做动效、过渡、hover、展开收起、列表变化、页面切换或沉浸式体验。
- 需要判断一个界面是“更好看”还是“更可用、更稳、更符合用户心理预期”。

## 使用顺序

1. 先定设计哲学：这个产品体验要更安静、更专业、更沉浸、更高效、更情绪化，还是更可靠。
2. 再定任务：用户此刻要完成什么，最怕什么失败，最需要什么反馈。
3. 再定状态：对象是 idle、thinking、running、success、warning、error、blocked、cancelable 还是 finished。
4. 再定视觉重量：信息的重要性、风险、持续时间和用户可操作性决定强调程度。
5. 再定执行搭配：颜色、文字、布局、组件和动效都必须服务同一个状态语义。
6. 最后验证：看截图、真实交互和 reduced motion；检查对比度、焦点、目标尺寸、等待反馈和错误恢复。

## 分层框架

设计判断不要从“用什么颜色、几百毫秒、圆角多大”开始。顶层是设计哲学，中层是用户心理和系统语义，底层才是具体样式、组件、动效和搭配。

| 层级 | 关注点 | 产物 |
| --- | --- | --- |
| 设计哲学 | 产品希望用户如何感受、如何判断可信度、如何形成节奏。 | 体验原则、气质方向、取舍边界。 |
| 用户心理 | 用户注意力、认知负荷、控制感、等待焦虑、错误恢复。 | 心理模型、风险判断、反馈强度。 |
| 系统语义 | 对象、状态、动作、结果、可取消性和责任归属。 | 状态机、信息层级、交互规则。 |
| 场景策略 | 当前场景是管理、创作、消费、协作、告警、支付还是生成任务。 | 布局密度、主次动作、状态表达。 |
| 执行搭配 | 配色、字体、边距、圆角、组件、图标、动效、文案。 | 设计稿、代码实现、动效参数和 QA 清单。 |

## 顶层设计哲学

- 先服务人，再服务界面。好的 UI 不只是漂亮，而是让用户更少猜测、更少焦虑、更少犯错，更快建立信任。
- 形式服从状态语义。颜色、动效、组件重量都必须表达真实状态；没有语义的装饰默认降权。
- 安静是一种能力。高频生产力界面要让用户长期使用不累，不能把每个状态都做成视觉事件。
- 强调必须稀缺。只有真正重要、危险、阻塞或需要立即行动的信息才配得上高饱和、高对比、大动效。
- 控制感优先于戏剧感。可取消、可恢复、可理解、可预测，通常比“酷炫”更能让用户放心。
- 一致性先于个性。只有当个性真正提升理解、品牌记忆或情绪价值时，才打破用户已有预期。
- 细节不是孤立参数。配色、间距、圆角、动效时长和文案语气要共同表达同一气质，而不是各自好看。
- 场景决定搭配。管理台、创作工具、消费内容、游戏、告警系统、AI 生成任务需要不同的信息密度和情绪强度。

## 核心心理模型

| 模型 | 设计含义 | 设计动作 |
| --- | --- | --- |
| Visibility of system status | 用户需要知道系统是否收到指令、正在做什么、结果是什么。 | 关键动作后立即给反馈；长任务展示状态、进度或可取消入口；不要让用户猜。 |
| User control and freedom | 用户需要退出、撤销、取消和恢复。 | 长任务保留 Cancel；危险动作支持 undo 或二次确认；错误恢复路径要可见。 |
| Recognition over recall | 用户更容易识别眼前线索，而不是记住规则。 | 标签、图标、上下文和默认值要留在界面上；少让用户记命令、状态和上一步。 |
| Consistency and standards | 用户会带着其它产品的经验使用当前产品。 | 常见控件用常见行为；创新只放在真正有价值的地方。 |
| Hick's Law | 选择越多、越复杂，决策越慢。 | 降低同时可见选项；把复杂任务分步；突出推荐或默认项。 |
| Fitts's Law | 目标越大、越近，越容易点击。 | 高频或关键动作要足够大、离任务区域近；危险动作不要贴在高频动作旁。 |
| Gestalt grouping | 人会按接近、相似、边界和连续性理解关系。 | 相关元素靠近；无关元素分开；相同样式表示相同语义；避免卡片套卡片制造假分组。 |
| Visual hierarchy | 用户会按对比、尺寸、位置和分组扫描页面。 | 只让 1 到 2 个元素成为第一注意点；其它内容降低饱和度、尺寸或对比。 |
| Peak-end rule | 用户对体验的记忆常被最紧张和最后一刻影响。 | 等待、失败、支付、删除、发布完成等峰值时刻要格外稳；结束态要清楚、有余韵。 |
| Aesthetic-usability effect | 好看的界面会让用户更愿意尝试，但不能替代可用性。 | 视觉 polish 服务清晰和信任；不要用装饰盖住状态、错误和主任务。 |

## 配色

### 原则

- 颜色先是语义，再是审美。primary、secondary、surface、outline、success、warning、danger、info 应有稳定职责。
- 不用颜色承担唯一信息。错误、成功、选中、禁用、运行中都要有文字、图标、形状、位置或 aria 状态配合。
- 强色只给强语义。危险、阻塞、失败、主行动作可以强；普通 running、thinking、pending 默认轻。
- 色相少，层级多。产品界面通常靠中性色、表面层、边框、文字权重和少量 accent 建立层级，而不是把每块都染色。
- 语义色不能混用。红色不要用于普通强调，绿色不要用于普通激活，黄色/橙色不要用于常规进行中。
- 动态或品牌色要过可访问性，不要因为品牌色漂亮就牺牲文字、图标、边界和焦点可见性。

### 实用技巧

- 先建语义 token，再填 hex：`bg/surface/text-muted/text/primary/danger/warning/success/border/focus`。
- 一个组件至少分清 5 类色：背景、文字、边框、状态、交互反馈。
- 状态色采用“底色轻、边框中、文字稳、图标清”的组合。小面积可以更饱和，大面积必须降饱和。
- 进行中状态优先用 neutral + low-saturation accent。不要用危险色、强橙色或大面积进度条抢主内容。
- 暗色模式不是简单反相。需要重新校准 surface 层级、边框透明度、阴影替代方案和状态色亮度。
- 数据可视化色板要先保证类别可区分，再考虑品牌一致；避免只靠红绿区分。

### 快速检查

- 眯眼看页面，第一注意点是否真的是用户下一步要看的对象。
- 转灰度看状态，错误/选中/禁用/链接是否仍能分辨。
- 检查普通文本至少满足 WCAG AA 对比度；组件边界和图标也要有足够非文本对比。
- 追问每个强色：“它表示语义、品牌、层级，还是只是装饰？”装饰强色默认降权。

## 视觉样式与层级

### 布局

- 先组织对象关系，再做卡片。卡片用于重复项、模块化对象、模态内容和明确边界；不要把页面区域都做成浮动卡片。
- 接近性比颜色更强。相关信息靠近，无关信息拉开，很多时候比加边框更有效。
- 信息密度服务任务频率。管理台、CRM、SaaS、监控、配置页应更安静、更可扫描；营销页、作品页、游戏可以更戏剧化。
- 对齐是隐形质量。按钮、标签、输入、数值、状态 badge 要有稳定对齐线，减少视觉噪声。
- 留白是分组工具，不是装饰。留白应该表达层级和关系，不是让页面显得“高级”的空旷。

### 字体与文本

- 字体层级少而明确。常规产品 UI 通常 2 到 3 个字号层级就够；用 weight、颜色和间距补充层级。
- 小组件内不要用 hero 字号。工具条、表格、卡片、sidebar 内的标题应紧凑，避免压迫。
- 长文本控制行宽。正文太长会难读，太短会割裂；深阅读内容优先控制在舒适行长。
- 标签要说用户语言，不说实现语言。状态和错误文案要让用户知道“发生什么、为什么、下一步做什么”。
- 图标按钮要有 tooltip 或可访问 label；不熟悉的图标不要单独承担关键动作。

### 组件视觉重量

- 页面主操作可以强，局部操作要贴近对象且轻。
- 同一行里只有一个主要按钮；次要、取消、更多操作降低权重。
- 危险操作既要可见，也要避免误触。不要把红色主按钮放在用户高频确认路径上。
- Badge 只表达短状态，不要塞长句；长解释放在详情、tooltip 或行内辅助文本。
- 边框、阴影、背景、圆角同时使用时要克制；每加一层都应有明确分组或层级原因。

## 交互反馈

### 状态反馈

- 立即反馈：点击、提交、复制、保存、切换后，界面应在感知上马上变化。
- 合理延迟：真正结果未返回时，用 pending/running/thinking 表达“已收到，正在处理”。
- 明确结果：成功、失败、取消、超时和部分成功必须有不同表达。
- 反馈贴近对象：哪个对象在运行，状态就靠近哪个对象；全局 toast 只适合补充，不替代对象状态。
- 同一状态不要叠太多信号。badge、进度条、动画、文字、toast 全开会变成噪声。

### 等待与进度

| 时长 | 用户感受 | 建议反馈 |
| --- | --- | --- |
| 小于 0.1s | 接近即时。 | 直接显示结果，不额外加 loading。 |
| 0.1s 到 1s | 用户能察觉，但思路不断。 | 轻量按钮态、局部 skeleton 或短 pending；避免大动效。 |
| 1s 到 10s | 用户会觉得系统在处理。 | 明确 running/thinking；可用 subtle spinner、骨架屏或低权重进度线。 |
| 超过 10s | 注意力会流失。 | 给进度、预计时间、后台执行提示、取消或稍后查看入口。 |
| 不确定长任务 | 用户最容易焦虑。 | 告知“仍在进行”，展示最近更新时间、阶段名、日志摘要或可取消控制。 |

长时媒体或生成任务要主动建立等待预期。若任务通常需要几十秒到几分钟，UI 应用低权重文字说明预期耗时和完成后的更新方式；只放 spinner 或无语义进度线，会让用户误判为卡死或失败。

### Running / Thinking 细则

- running 只需要证明“进行中”，不需要成为视觉主角。
- 进度条有真实百分比时才强调；没有真实百分比时用低对比度 indeterminate signal。
- 点、波纹、扫光、进度条、文字循环同时存在会显得急促；保留一个主信号即可。
- 进行中但可取消时，Cancel 要比 running 状态更可操作，但视觉重量低于主任务内容。
- 长时生成任务要补充“预计较久”的低权重说明；这不是抢眼，而是在建立控制感和信任。
- 如果 Agent 暂时没回复也没报错，显示 thinking 或 pending card，避免用户误以为发送失败。

## 动效

### 动效的正当理由

- 反馈：告诉用户动作已被系统识别。
- 连续性：解释元素从哪里来、到哪里去。
- 层级：说明哪个面板在前、哪个对象被展开。
- 方向：引导用户理解导航、步骤或空间关系。
- 情绪：在完成、奖励或品牌时刻增加愉悦感，但不能干扰任务。

### 动效调参

- 高频微交互要短、轻、可忽略。hover、pressed、toggle、checkbox 通常更适合 100ms 级别。
- 面板、弹窗、展开收起可稍长，但通常保持在 200ms 到 300ms 左右。
- 大跨度移动、沉浸式场景或复杂转场才考虑更长，但需要确认不会拖慢任务。
- 大多数产品 UI 动效应控制在 100ms 到 500ms 范围内；越常见越短，越大范围越要平滑。
- 进入可稍慢，退出可稍快；进入让用户看清，退出不拖用户节奏。
- 避免线性运动。进入常用 ease-out，退出常用 ease-in，保持响应感和自然停靠。
- 动画不能由后端速度直接决定。结果很快时也要有最短可感知反馈；结果很慢时用状态而不是拉长动画。

### 无障碍与舒适度

- 尊重 `prefers-reduced-motion`，交互触发的非必要 motion 应能关闭或显著弱化。
- 避免闪烁、强扫光、连续抖动、scroll-jacking、大面积视差和无法停止的循环动画。
- 不用颜色突变和频繁闪烁表达普通状态；这会制造焦虑，也可能有可访问性风险。
- 动效 QA 必须看真实设备和真实刷新率；只看设计稿很容易漏掉眩晕、卡顿和时长问题。

## 表单与错误恢复

- 错误预防优先于错误提示。示例、格式、输入掩码、默认值和约束提示能减少后续失败。
- 不要过早报错。用户刚 focus 或正在输入时就报错，会像界面在“责备”用户。
- inline validation 更适合在 blur、达到有效长度或用户停止输入后触发。
- 输入修正后应立即移除错误，避免用户误以为仍然失败。
- 错误文案要具体说明问题和修复方式；不要只有 “Invalid” 或 “Something went wrong”。
- 提交失败后保留用户输入，尤其是长文本、地址、支付和账号信息。
- 安全相关错误需要平衡具体性。登录、密码重置、账号存在性等场景不应泄露敏感判断。

## 信息架构与流程

- 常态管理页与创建/onboarding 态分离。常态页面服务重复管理，创建流程用 modal、drawer、wizard 或独立 route。
- 复杂任务分步，但不要无意义分页。每一步应降低认知负担或风险，而不是为了显得有流程。
- 推荐路径可突出，但保留专家入口。新手需要默认项，熟练用户需要捷径。
- 进度感能降低焦虑。多步流程展示当前位置、已完成和下一步。
- 关键流程的最后一步要清楚。发布、支付、删除、生成、提交后必须有可验证结果。

## 可访问性底线

- 文本对比度：普通文本按 WCAG AA 至少 4.5:1，大文本至少 3:1。
- 非文本对比度：有意义的图标、边框、控件状态和焦点边界应有足够对比。
- 目标尺寸：指针目标至少按 WCAG 2.2 的 24x24 CSS px 底线考虑；移动端和高频动作通常应更大。
- 键盘焦点必须可见，焦点顺序要符合视觉和任务顺序。
- 文本可缩放，布局不能在 200% 文本或用户调整文本间距后失去内容或功能。
- 不仅支持鼠标。hover 信息要有键盘和触屏替代路径。

## 常见反模式

- 用强进度条、跳动点和高饱和色同时表达普通 running。
- 把 warning/danger 色用于日常进行中，导致真正危险状态失去区分度。
- 组件为了“丰富”而每块都有边框、阴影、底色和 badge。
- Loading 太短还强行展示大动画，用户只看到闪烁。
- Loading 太长只放 spinner，没有阶段、取消、预计或后台处理线索。
- 表单提交失败后清空用户输入。
- 错误文案只描述系统失败，不告诉用户下一步。
- 设计稿里好看，真实数据长文本、空数据、错误态、移动端和 reduced motion 没验证。
- 按钮视觉重量和作用域不匹配：局部刷新做成页面主按钮，危险动作放成默认确认。
- 所有卡片圆角、阴影、颜色都一样，导致对象关系看不出来。

## 快速设计配方

### 让 running 不抢眼

1. 保留对象标题和 `RUNNING` badge。
2. 移除重复的 `Executing...`、跳动点、强扫光和大面积填充。
3. 使用 1 到 2px 低对比度 indeterminate line，放在对象内部靠下或靠近状态区域。
4. 颜色采用 surface/border/primary-muted，不使用 warning/danger。
5. 如果可取消，Cancel 放在右上或状态附近，样式低权重但可识别。

### 让 thinking 感觉“有人在处理”

1. 显示简短文案，例如“正在思考”或“正在处理你的消息”。
2. 动效只保留一个细微节奏，速度不要让人焦虑。
3. 超过长时间后切换为更具体的状态，例如“仍在等待工具结果”。
4. 保持输入区可用状态清楚：能继续输入、不能输入、可取消要明确区分。

### 调配产品色板

1. 先确定品牌色和中性色，不先挑满屏渐变。
2. 建立语义色：primary、info、success、warning、danger。
3. 建立 surface 层：page、panel、raised、sunken、border。
4. 建立文本层：strong、default、muted、disabled、inverse。
5. 做 light/dark 两套对比测试。
6. 用真实页面截图做 squint test 和灰度测试。

### 做一个舒服的展开/收起

1. 内容高度变化小：150ms 到 220ms。
2. 内容高度变化中等：200ms 到 300ms。
3. 大范围页面结构变化：优先 fade/opacity/clip，谨慎大位移。
4. 进入 ease-out，退出略短。
5. reduced motion 下改为即时状态或短 fade。

### 做表单错误

1. 输入前给格式提示和示例。
2. 输入中不急着报错。
3. blur 或达到有效长度后验证。
4. 错误文案指出具体字段、具体问题、修复方式。
5. 用户修正时及时清错。
6. submit 失败保留所有可保留输入。

## 设计评审 Checklist

- 任务：用户此刻最重要的目标是什么，页面第一注意点是否支持它？
- 状态：系统是否在合理时间内反馈“收到、进行中、成功、失败、取消”？
- 配色：每个强色是否有语义职责，是否有颜色之外的表达？
- 层级：眯眼后能否看出 1 到 2 个主要注意点？
- 分组：相关元素是否靠近，无关元素是否拉开，卡片和边界是否真的表达对象关系？
- 交互：关键动作是否可撤销、可取消或可恢复？
- 等待：不同等待时长是否采用不同反馈强度？
- 动效：每个动效是否有触发、目标、时长、easing 和 reduced motion 方案？
- 表单：错误是否可预防、具体、及时、保留输入并支持恢复？
- 可访问性：对比度、目标尺寸、键盘焦点、文本缩放和 reduced motion 是否检查过？
- 真实数据：长文本、空数据、异常、慢网络、移动端和暗色模式是否看过？

## 来源

- Nielsen Norman Group, 10 Usability Heuristics for User Interface Design, updated 2024-01-30: https://www.nngroup.com/articles/ten-usability-heuristics/
- Nielsen Norman Group, Response Time Limits, 0.1s / 1s / 10s: https://www.nngroup.com/articles/response-times-3-important-limits/
- Nielsen Norman Group, Mask Interaction Delays with Progress Indicators: https://www.nngroup.com/videos/progress-indicators/
- Nielsen Norman Group, Executing UX Animations, Duration and Motion Characteristics: https://www.nngroup.com/articles/animation-duration/
- Nielsen Norman Group, Visual Hierarchy in UX: https://www.nngroup.com/articles/visual-hierarchy-ux-definition/
- Nielsen Norman Group, 5 Principles of Visual Design in UX: https://www.nngroup.com/articles/principles-visual-design/
- Nielsen Norman Group, Proximity Principle in Visual Design: https://www.nngroup.com/articles/gestalt-proximity/
- W3C, Web Content Accessibility Guidelines 2.2: https://www.w3.org/TR/WCAG22/
- W3C WAI, Understanding Text Spacing: https://www.w3.org/WAI/WCAG22/Understanding/text-spacing
- WebAIM, Contrast and Color Accessibility: https://webaim.org/articles/contrast/
- Material Design 3, Color roles: https://m3.material.io/styles/color/roles
- Material Design 3, Easing and duration: https://m3.material.io/styles/motion/easing-and-duration
- Material Design 3, Typography: https://m3.material.io/styles/typography/applying-type
- Apple Human Interface Guidelines, Color: https://developer.apple.com/design/human-interface-guidelines/color
- Apple Human Interface Guidelines, Typography: https://developer.apple.com/design/human-interface-guidelines/typography
- Apple Human Interface Guidelines, Motion: https://developer.apple.com/design/human-interface-guidelines/motion
- Microsoft Fluent 2, Motion: https://fluent2.microsoft.design/motion
- Baymard, Checkout UX Guide: https://baymard.com/learn/checkout-flow-ux-optimization
- Baymard, Inline Form Validation: https://baymard.com/blog/inline-form-validation
- Baymard, Adaptive Validation Error Messages: https://baymard.com/blog/adaptive-validation-error-messages
- Interaction Design Foundation, Gestalt Principles: https://ixdf.org/literature/topics/gestalt-principles
- Laws of UX, Hick's Law: https://lawsofux.com/hicks-law/
- Laws of UX, Fitts's Law: https://lawsofux.com/fittss-law/
- Laws of UX, Jakob's Law: https://lawsofux.com/jakobs-law/
- Laws of UX, Peak-End Rule: https://lawsofux.com/peak-end-rule/

访问日期：2026-06-25。
