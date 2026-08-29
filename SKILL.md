---
name: agent-hub
description: Use the canonical Agent Hub when the user invokes /hub, explicitly requests Agent Hub, or asks to route work through its reusable workflows, project context, and lifecycle conventions.
---

# Agent Hub

Treat this repository as the canonical, live Agent Hub source. Do not copy Hub
content into a generated cache or expose its internal workflows as separate
user-facing skills.

## Load the Hub

1. Read `AGENTS.md` for repository-wide rules.
2. Read `README.md` for commands, routing, and workspace entry points.
3. Read `agents/codex.md` for the Codex adapter.
4. Read only the `skills/<category>/<skill>/WORKFLOW.md` selected by Hub
   routing, plus references that workflow explicitly requires.
5. For project, SPEC, helper, report, or background work, follow the document
   workspace entry declared by `README.md` and load only the relevant context.

## Operating rules

- Keep Agent Hub as a reusable agent-asset and workflow layer; do not turn it
  into the product or service being designed.
- Follow the Hub's command semantics and lifecycle rules rather than inventing
  parallel conventions.
- Make durable Hub improvements in this repository so every registered client
  sees the same source.
- Preserve project boundaries and existing user changes when applying Hub
  workflows to another repository.
