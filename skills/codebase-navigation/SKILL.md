---
name: codebase-navigation
description: 当用户要求理解陌生仓库、模块、文件关系、调用链、入口点、数据流、目录结构或让 Agent 先读代码再解释时使用。
---

# Codebase Navigation

用这个技能快速建立代码地图。目标是先理解真实结构和调用关系，再回答、设计或动手改代码。

## Trigger

当用户说以下任一类需求时使用：

- “先看一下这个仓库/模块”“帮我梳理代码结构”“这个功能在哪里”
- “解释调用链”“找入口”“看一下数据怎么流”
- “不要猜，读代码后告诉我”

## Workflow

1. **Confirm Boundary**
   - 用户给了路径时，把路径当成硬边界。
   - 没有路径时，从当前仓库入口文件、README、package/build 配置和路由/命令入口开始。

2. **Inventory**
   - 用快速文件搜索列出相关目录、入口文件、测试、配置和文档。
   - 优先看命名稳定的入口：router、handler、service、controller、command、job、schema、test。

3. **Trace Entrypoints**
   - 找用户可见入口或外部调用入口。
   - 往下追到核心业务逻辑，再追到存储、外部 API、队列、工具或 UI。
   - 同时往上查真实消费者，避免只看单个函数。

4. **Separate Layers**
   - 标出 API/CLI/UI 入口层。
   - 标出领域逻辑层。
   - 标出数据访问、外部集成、工具调用和测试边界。

5. **Build A Map**
   - 用简短列表或 Mermaid 画出调用链。
   - 只在能降低理解成本时画图，不为了画图而画图。

6. **Validate Understanding**
   - 用测试、fixture、文档或运行脚本验证关键推断。
   - 不确定处明确标记，不把命名相似当作调用关系。

## Output

默认输出：

```markdown
## 入口

## 主要调用链

## 关键模块职责

## 数据 / 状态流

## 测试与验证入口

## 不确定点
```

## Quality Bar

- 每个关键结论都能指向文件或调用点。
- 不只看文件名，要确认导入、路由、注册或运行链路。
- 用户限定范围时，只读边界外内容，不写边界外文件。
