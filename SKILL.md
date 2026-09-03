---
name: agent-hub
description: Use the canonical Agent Hub when the user invokes /hub, explicitly requests Agent Hub, or asks to route work through its reusable workflows, project context, and lifecycle conventions.
---

# Agent Hub

Treat this repository as the canonical, live Agent Hub source. Do not copy Hub
content into a generated cache or expose its internal workflows as separate
user-facing skills.

The canonical main worktree is the only Agent Hub rule owner. If the current
task runs in a detached or generated worktree, load and maintain the canonical
main worktree instead; do not create or continue an Agent Hub work branch or
linked worktree.

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
- Always dispatch `/hub get` asynchronously: return a receipt immediately,
  keep the current conversation unblocked, and report the background result
  separately. Never silently fall back to synchronous extraction.
- Make durable Hub improvements directly in the canonical main worktree so
  every registered client and conversation sees the same current source.
- Preserve project boundaries and existing user changes when applying Hub
  workflows to another repository.
