# Premium Build Playbooks

Five Claude Code playbooks for shipping premium ($5K to $10K+ tier) websites and creative production rigs. All sourced from a single 3/21/2026 burst in WhatsApp where Ahmad collected the full set in one sitting. Each is a Notion guide or tool that complements Nano Banana 2 and the DESIGN.md pattern.

## 1. Tenfold Marc: "How to Build a $10,000 Website With One Line of Code"

URL: https://tenfoldmarc.notion.site/How-to-Build-a-10-000-Website-With-One-Line-of-Code-327c2d7ca4cf81d7896ec03fa2da1041
Date saved: 3/21/2026 14:22

The premise (per the headline and corroborating WhatsApp/YouTube content from Marc Cleroux): a single Claude Code prompt scaffolds a high price tier marketing site by chaining DESIGN.md, Nano Banana 2, and a curated component library. The "one line of code" framing is marketing, the underlying loop is the same image first then implementation pattern documented in `nano-banana-workflow.md`.

How to use:

- Treat the playbook as a structured version of the Nano Banana loop with named milestones
- Steal the prompt structure for your own client deliverables
- Pair with the Stitch DESIGN.md export so the contract survives across screens

## 2. Joey Mulcahy Guides: "The Claude Code Product Visual Workflow"

URL: https://joeymulcahyguides.notion.site/The-Claude-Code-Product-Visual-Workflow-3262b10bd51680d6ba29e2072a5ba78e
Date saved: 3/21/2026 14:25

Aimed at product designers and PMs using Claude Code to ship product UI (not just marketing pages). The "visual workflow" framing implies a heavier emphasis on screenshot diffing, component states, and design QA than on hero generation.

How to use:

- Compare against the Jens Heitmann three systems (visual reference, component memory, design QA) for overlap and additions
- Mine for any product specific additions (state coverage, empty states, error states) the marketing focused playbooks tend to skip

## 3. Tim Koda: "The Claude Code Creative Studio Stack: Full Setup Guide"

URL: https://www.notion.so/The-Claude-Code-Creative-Studio-Stack-Full-Setup-Guide-by-timkoda_-326ac4ebbbda81de8bb5fd9e1fa96c38
Date saved: 3/21/2026 14:32

A full setup guide for running a creative production studio inside Claude Code: video, design, brand, and asset pipelines all wired together. The "stack" framing means it covers MCP servers, skills, hooks, and external tool integrations.

How to use:

- Use as a checklist for what is missing from your current Claude Code setup
- Cherry pick the MCP servers that fit your workflow (likely overlap with your existing Nano Banana, Stitch, Pencil, fal.ai, Remotion stack)
- Borrow folder conventions if cleaner than yours

## 4. Striped Thief: "The 10k Vibecode Blueprint"

URL: https://striped-thief-c4a.notion.site/The-10k-Vibecode-Blueprint-31773bd1b02b80828553f5b68bb95f6e
Date saved: 3/21/2026 14:45

Same family as the Tenfold Marc $10K guide but framed as a blueprint rather than a tutorial. "Vibecode" is the genre name for fast, vibe driven full stack delivery using Claude Code as the primary interface.

How to use:

- Read alongside the Tenfold Marc guide to triangulate the consensus pattern
- Watch for divergence on stack choices (Next.js vs. Astro, Tailwind vs. CSS modules, etc.)

## 5. Swishy.ai

URL: https://www.swishy.ai/
Date saved: 3/21/2026 14:27

AI powered platform for kinetic typography, animations, and motion graphics. Free Mini tier, paid upgrades. Generates motion content without animation expertise required.

How to use:

- Use for hero motion when you do not want to write Framer Motion or GSAP by hand
- Pair with Aceternity (composition) and Unicorn Studio (ambient WebGL) so each tool covers a different motion job
- Output can be exported and dropped into the project as a component

## When to reach for each

| Need | Playbook |
|------|----------|
| Marketing site, premium tier, fast | Tenfold Marc OR Vibecode Blueprint |
| Product UI with deep state coverage | Joey Mulcahy Visual Workflow |
| Full creative studio (video, brand, design) | Tim Koda Creative Studio Stack |
| Drop in motion graphic for hero or feature card | Swishy.ai |
| Locked design contract that survives sessions | DESIGN.md (see design-md-pattern.md) |
| Image first iteration loop | Nano Banana 2 (see nano-banana-workflow.md) |

## Pattern across all 5

All five share the same core stance:

1. Start with a visual or design contract, never an open ended prompt
2. Use Claude Code as the orchestrator, not the designer
3. Bind each pipeline to repeatable artifacts (DESIGN.md, components/README.md, design/inspiration/)
4. Premium output comes from the loop, not from a single prompt

The unifying takeaway: there is no "best" playbook. Pick the one that matches the project type, but apply the same loop discipline regardless.

## Source note

All 5 items appeared in a single WhatsApp burst on 3/21/2026 between 14:22 and 14:45. The original intake categorization bundled them into the catch all 16:30 message bag, so they were missed in the first pass. Surfaced during the 4/26 refresh of the WhatsApp scrape (`messages-jan1-to-apr26.json`).
