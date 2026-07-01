---
name: codebase-navigation
description: 当用户要求理解陌生仓库、模块、文件关系、调用链、入口点、数据流、目录结构，或让 Agent 先读代码再解释时使用。
---

# Codebase Navigation

建立真实代码地图，避免凭命名猜结构。

## Trigger

- “先看仓库/模块”“梳理代码结构”“功能在哪里”。
- “解释调用链”“找入口”“数据怎么流”。
- “不要猜，读代码后告诉我”。

## Workflow

1. **Boundary**：用户给路径时当硬边界；没有路径时从 README、build/package 配置、路由/命令入口开始。
2. **Inventory**：列相关目录、入口文件、测试、配置和文档；优先看 router、handler、service、controller、command、job、schema、test。
3. **Trace**：从入口追到核心逻辑、数据访问、外部集成和测试边界。
4. **Summarize Ownership**：说明模块职责、依赖方向、事实源和不该改的边界。
5. **Validate**：用测试、fixture、文档、运行脚本或调用点验证关键推断。

## Output

默认结构：入口、主要调用链、关键模块职责、数据/状态流、测试与验证入口、不确定点。

## Quality Bar

- 不把文件名相似当调用关系。
- 不默认扫全仓；按用户目标和风险扩展。
- 不确定处明确标注，不用猜测补齐。
