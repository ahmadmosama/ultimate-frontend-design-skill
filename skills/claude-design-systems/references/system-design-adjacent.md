# System Design Adjacent

System design here means software system design (architecture, data, scaling, governance), not visual UI design. These items live in the design skill because how a Claude Code repo is structured directly shapes the design output it produces. A messy repo produces inconsistent UI; a well architected repo with skills, memory, security, and governance produces consistent UI. Source: WhatsApp Feb to Apr 2026 intake.

## 1. Claude Code Repo Review: Complete System Design for AI (Alex Wang)

Source: Alex Wang LinkedIn post, 4/11/2026 ("One of the best Claude Code repos I've come across").
URL: https://www.linkedin.com/posts/alexwang2911_one-of-the-best-claude-code-repos-ive-come-share-7445727930829283328-PX_1

### What it covers

A walkthrough of a production grade Claude Code repo broken into 5 integrated layers. Each layer has its own files, conventions, and review process. Together they form the complete system that determines what Claude can do, remember, prevent, and ship in that repo.

| Layer | What lives there | Why it matters for design |
|-------|------------------|---------------------------|
| Config | `.claude/settings.json`, `CLAUDE.md`, `.claude/rules/` | Sets the operating constraints for every session. Design QA rules live here. |
| Workflows | `.claude/commands/`, slash commands, named pipelines | Codifies repeatable tasks like "scaffold new screen," "audit hierarchy," "ship release." |
| Memory | `~/.claude/memory/`, project memory, MEMORY.md indexes | Persistent design preferences, brand decisions, past audits |
| Security | hooks (PreToolUse, PostToolUse), gates, secret scanners | Prevents leaks, blocks destructive commands, enforces fact forcing before edits |
| Governance | review process, ownership, conventions, PR checks | Decides who can ship what, how design changes are reviewed |

### Why it belongs in the design skill

A design system without an owning repo is just a moodboard. The five layers determine whether DESIGN.md is enforced, whether the inspiration folder stays curated, whether QA actually runs, whether tokens are protected from drift. Skip any layer and design quality degrades over time even if the initial output was strong.

### How to apply

For Ahmad's existing setup the layers map to:

- Config: `~/.claude/CLAUDE.md`, `~/.claude/rules/`, the Ultimate Frontend Design Skill itself
- Workflows: 14 design skills already installed (refactoring-ui, top-design, etc.) plus the new `claude-design-systems` bundle
- Memory: `~/.claude/memory/feedback_no_dark_font_palette.md`, `feedback_writing_tone.md`, design related project memories
- Security: existing personal hooks (24 of them per memory project_hooks_personal_2026_04_18.md), the gateguard fact forcing gate
- Governance: PR review patterns, the auto-save Second Brain rule, the memory to wiki sync rule

Action when starting a new project: replicate this 5 layer structure scaled to the project size, do not just clone DESIGN.md and call it done.

### Recommended audit

Once a quarter, run a Claude Code session that walks each layer and asks: what is missing, what is stale, what is fighting the others. Repos drift; the audit catches the drift before it shows up in the UI.

## 2. system-design-academy (GitHub)

Source: WhatsApp 3/14/2026.
URL: https://github.com/systemdesign42/system-design-academy

### What it is

A learning resource for software system design (databases, scaling, caching, queues, distributed systems, common interview style design problems). The audience is engineers preparing for system design interviews or building scalable backends.

### Why it belongs in the design skill

Two reasons:

1. UI is downstream of system design. A list view's pagination behavior, a dashboard's refresh frequency, a feed's ranking signals are all design decisions whose surface manifestation is visual but whose root is architectural. Designers who do not understand the system design end up making promises the backend cannot keep (real time when the data is batched, infinite scroll on an unindexed table, optimistic UI on a non idempotent endpoint).
2. The Claude Code repo itself is a distributed system across config, memory, hooks, MCP servers, and external APIs. The patterns (caching, idempotency, retry, eventual consistency) apply to how Claude operates as a tool, not just to the apps Claude builds.

### How to use it

- Skim the table of contents to identify gaps. If a topic (e.g. CDC, event sourcing, vector search) is unfamiliar, that is the next study target.
- Before starting a new project, sketch the system design first (data flow, caching, persistence) and only then start designing the UI. UI shipped against an unsketched backend always needs rework.
- When designing an LLM heavy product, pay extra attention to: cost aware pipelines, prompt caching, batch vs. streaming, retrieval architecture. The relevant chapters bridge directly to Claude Code's `claude-api` and `cost-aware-llm-pipeline` skills.

## 3. Why these were almost left out

The intake originally flagged both as "system design (architecture), not visual design" and excluded them. On reflection that boundary is too narrow:

- Visual design without system design ships brittle UI.
- The Claude Code repo that produces the design is itself a system whose architecture matters.
- Many of the highest leverage "design" decisions Ahmad will make (memory layout, hook discipline, skill granularity) are architectural.

So both belong here, with the framing that this is meta design: designing the system that produces the design.

## Companion reading

- `everything-claude-code:agent-harness-construction` skill: how to design AI agent action pipelines
- `everything-claude-code:context-engineering` skill: how to optimize agent context setup
- `everything-claude-code:eval-harness` skill: how to evaluate Claude API apps
- `everything-claude-code:agentic-engineering` skill: operating model for engineering with agents

These four already installed skills overlap heavily with the Alex Wang 5 layer model. Use them in combination, not in isolation.
