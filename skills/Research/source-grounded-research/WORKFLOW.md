---
name: source-grounded-research
description: 当用户要求研究、最新资料、官方文档、论文、竞品或带来源结论时使用。
---

# Purpose

用可信来源形成可追溯结论，区分事实、推断和项目取舍。

# When to Use

- 用户要求研究、业界优秀实践、最新资料、官方文档、论文、竞品或来源链接。
- 结论依赖版本、政策、价格、公司产品、标准或快速变化事实。
- 需要把外部方法转成项目可采纳建议。

# When NOT to Use

- 只需本地代码事实或用户已提供完整资料。
- 不需要来源支撑的普通实现判断。

# Inputs

- 研究问题、时效要求、地区/版本边界、交付格式、本地约束和已知来源。

# Decision Principles

- 官方文档、RFC/标准、论文、release notes 和一手案例优先。
- GitHub issue/PR 可证明实践和问题；Blog/AI 回答只能作为线索，不能单独作为关键事实。
- 冲突来源必须交叉验证，并说明采信理由。
- 搜索停止条件：关键问题已有高可信来源覆盖，冲突已解释，新增搜索只会重复同类信息。

# Workflow

1. Scope：把主题拆成 3-6 个可回答问题。
2. Source Plan：按官方文档、RFC/标准、论文、GitHub、Issue、Blog、AI 回答的可信度排序。
3. Collect：记录标题、URL、发布时间/访问日期、版本和可信度。
4. Cross-check：关键事实至少用一个高可信来源支撑；争议点找第二来源。
5. Synthesize：先给结论，再列事实依据和推断链。
6. Apply Locally：说明本项目采纳、调整或不采纳的原因。
7. Stop：达到停止条件后不继续堆资料。

# Checklist

- 最新事实是否已联网核对并写明日期。
- 每个关键判断是否能回到来源。
- 是否区分本地约束、外部事实和项目取舍。

# Escalation

- 研究结果要沉淀到 Hub：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`
- 涉及接口或 tool 设计：Engineering 相关 Skill。

# References

- None.
