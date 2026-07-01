# Product UI UX Psychology Patterns

日期：2026-06-25
类型：reference
归属 Skill：design-principle-library
适用：产品 UI、交互、配色、视觉样式、动效、等待反馈、表单、可访问性、设计评审
来源策略：优先采用官方标准、一手设计系统、NN/g、Baymard、IxDF 等可追溯资料；结论必须结合当前产品任务落地。

## Core Rule

先判断产品任务和状态语义，再选视觉表达。颜色、动效、布局和文案都必须服务真实对象、状态、动作和反馈；没有语义的装饰默认降权。

## Use Order

1. 体验方向：安静、专业、沉浸、高效、情绪化、可靠？
2. 用户任务：此刻要完成什么，最怕什么失败，最需要什么反馈？
3. 状态语义：idle、thinking、running、success、warning、error、blocked、cancelable、finished。
4. 视觉重量：由重要性、风险、持续时间和可操作性决定。
5. 执行搭配：颜色、布局、组件、文字、动效共同表达同一状态。
6. 验证：截图、真实交互、reduced motion、对比度、焦点、长文本、移动端、错误态。

## Judgment Layers

| 层级 | 关注 | 输出 |
| --- | --- | --- |
| 设计哲学 | 用户如何感受可信、可控、清楚 | 体验原则 |
| 用户心理 | 注意力、认知负荷、等待焦虑、错误恢复 | 反馈强度 |
| 系统语义 | 对象、状态、动作、结果、责任归属 | 状态机和信息层级 |
| 场景策略 | 管理、创作、消费、协作、告警、生成任务 | 密度和主次动作 |
| 执行搭配 | 配色、字重、间距、圆角、图标、动效 | UI 实现和 QA |

## Psychology Cheatsheet

| 模型 | 设计动作 |
| --- | --- |
| Visibility of system status | 动作后立即反馈；长任务展示状态、阶段或可取消入口 |
| User control and freedom | 提供取消、撤销、恢复和清楚退出 |
| Recognition over recall | 标签、上下文、默认值留在界面上 |
| Consistency and standards | 常见控件用常见行为；创新只放在有价值处 |
| Hick's Law | 降低同时可见选项，突出推荐或默认 |
| Fitts's Law | 高频/关键动作足够大且靠近任务区域 |
| Gestalt grouping | 接近、相似和边界表达关系；避免卡片套卡片 |
| Visual hierarchy | 页面只允许 1 到 2 个第一注意点 |
| Peak-end rule | 等待、失败、支付、删除、发布完成等峰值时刻要稳 |

## Color

- 颜色先是语义，再是审美：primary、surface、success、warning、danger、info 要有稳定职责。
- 不用颜色承担唯一信息；状态必须有文字、图标、形状、位置或 aria 配合。
- 强色只给危险、阻塞、失败、主行动作或真正重要的信息。
- 日常 running / thinking / pending 用 neutral + low-saturation accent。
- 暗色模式要重校 surface、边框、状态色亮度，不是反相。

快速检查：

- 眯眼看，第一注意点是否支持下一步任务。
- 转灰度看，错误、选中、禁用、链接是否仍能分辨。
- 普通文本按 WCAG AA 对比度；图标、边框、焦点也要有非文本对比。

## Layout And Components

- 先组织对象关系，再决定是否用卡片；卡片服务重复项、明确对象和模态内容。
- 管理台、SaaS、监控、配置页要安静、密集、可扫描；营销、作品、游戏可更戏剧化。
- 同一行只有一个主要按钮；局部操作贴近对象且低权重。
- Badge 只表达短状态；长解释进详情、tooltip 或辅助文本。
- 小组件内不用 hero 字号；工具条、表格、sidebar 标题要紧凑。
- 图标按钮要有 tooltip 或可访问 label。

## Feedback And Waiting

| 时长 | 反馈 |
| --- | --- |
| < 0.1s | 直接显示结果，不额外 loading |
| 0.1-1s | 轻量按钮态、局部 skeleton 或短 pending |
| 1-10s | running / thinking，低权重 spinner、骨架屏或进度线 |
| > 10s | 阶段、预计、后台执行、取消或稍后查看 |
| 不确定长任务 | 最近更新时间、阶段名、日志摘要或可取消控制 |

规则：

- 反馈贴近对象；toast 只补充，不替代对象状态。
- 同一状态不要同时用 badge、进度条、动画、文字和 toast。
- 无真实百分比时，不强调进度条；用低对比 indeterminate signal。

## Motion

动效只服务反馈、连续性、层级、方向或情绪。高频微交互短、轻、可忽略；面板/弹窗一般 200-300ms；大范围沉浸场景才更长。

要求：

- 尊重 `prefers-reduced-motion`。
- 避免闪烁、强扫光、抖动、scroll-jacking、大面积视差和无法停止的循环动画。
- 结果很快时给最短可感知反馈；结果很慢时展示状态，不拉长动画。

## Forms And Errors

- 错误预防优先：示例、格式、默认值、约束提示。
- 不要在用户刚 focus 或正在输入时过早报错。
- blur、有效长度或停止输入后再验证。
- 修正后立即清错。
- 文案说明发生什么、为什么、下一步怎么做。
- 提交失败保留用户输入。
- 安全场景避免泄露账号存在性等敏感判断。

## Accessibility Baseline

- 普通文本至少 4.5:1，大文本至少 3:1。
- 有意义图标、边框、控件状态和焦点边界要有非文本对比。
- 指针目标至少按 WCAG 2.2 的 24x24 CSS px 底线考虑。
- 键盘焦点可见，顺序符合视觉和任务。
- 200% 文本或用户调整文本间距后，内容和功能不丢失。

## Review Checklist

- 任务：第一注意点是否支持用户此刻最重要目标？
- 状态：收到、进行中、成功、失败、取消是否清楚？
- 配色：强色是否有语义，是否有颜色之外的表达？
- 层级：眯眼后能否看出 1-2 个主要注意点？
- 分组：相关元素是否靠近，无关元素是否拉开？
- 交互：关键动作是否可撤销、可取消或可恢复？
- 等待：不同时长是否使用不同反馈强度？
- 动效：是否有触发、目标、时长、easing 和 reduced motion？
- 表单：错误是否具体、及时、可恢复并保留输入？
- 真实数据：长文本、空态、异常、慢网络、移动端、暗色模式是否看过？

## Source Pointers

- NN/g usability heuristics、response times、progress indicators、animation duration、visual hierarchy。
- W3C WCAG 2.2、WAI text spacing、WebAIM contrast。
- Material Design 3 color roles、motion、typography。
- Apple HIG color、typography、motion。
- Baymard checkout、inline validation、adaptive error messages。
- Laws of UX: Hick's Law、Fitts's Law、Jakob's Law、Peak-End Rule。
