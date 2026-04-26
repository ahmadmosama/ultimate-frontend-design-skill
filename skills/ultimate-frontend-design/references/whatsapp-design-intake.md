# WhatsApp Design Intake (Feb to Apr 2026)

Master log of every design related signal pulled from Ahmad's WhatsApp self chat between 2/22/2026 and 4/13/2026. Each entry has date, source, summary, and where it landed in the design skill.

## Source data

- `Desktop/Claude/WhatsApp/messages-2026-categorized.json` (196 messages, Feb 22 to Apr 13, original Apr 13 categorization pass)
- `Desktop/Claude/WhatsApp/messages-2026-analysis.md` (curated analysis, ratings, actions, original pass)
- `Desktop/Claude/whatsapp-scraper/messages-jan1-to-apr26.json` (807 messages, Jan 1 to Apr 26, refreshed Apr 26 12:43, raw export, no categorization)
- 8 messages in the explicit Design/UI category from the original pass, plus 7 design adjacent items in AI/Claude Code, AI/General, Education, plus 6 newly surfaced in the Apr 26 refresh (5 Notion playbooks + Swishy.ai from a 3/21 burst the original pass bundled into one entry, plus Ahmad's own 4/8 todo confirming Figma in the stack)

## Apr 26 resolution sweep

After the refresh, 80 unique URLs in the new 4/14 to 4/26 range required title resolution. A one shot resolver was run against the new file (`messages-jan1-to-apr26.json`), pre filtering 5 obvious noise URLs (LinkedIn jobs, Drive links, Maps, sweet onboarding ad, fb.watch). 75 URLs were fetched in batches of 8.

Output saved to `Desktop/Claude/whatsapp-scraper/messages-apr14-to-apr26-resolved.json`.

Results:

- **0 net new visual UI or UX design items confirmed**
- 4 keyword "design" hits were all false positives:
  - `mbagalman/lattice-doe` GitHub: statistical experimental design (already in memory as Tabby A/B testing reference)
  - Tenfold Free Resources page: generic AI guides email signup
  - AI Vantage Point: generic AI community
  - LinkedIn Sign Up: redirect from a deleted post
- 3 Claude or MCP hits, all classified earlier as non visual design:
  - vincenzobelpiede 4/16: Multica (open source Anthropic Managed Agents alternative, backend infra)
  - juliocasal 4/17: Anthropic certification announcement
  - sjsyrek 4/22: design-council plugin (11 agent technical debate, architectural)
- 68 other items: ~30 Instagram URLs returned only "Instagram" (Instagram blocks OG metadata for unauthenticated fetches, known limitation), ~25 Facebook shares returned 403 (auth wall), ~13 properly resolved LinkedIn job listings, hiring posts, infra news, and Egyptian Arabic content (none design related)

Conclusion: the 4/14 to 4/26 range adds zero design signals to the intake. The Instagram reel content remains unresolvable via title scraping. To extract design signals from those, a future pass would need to either click through each reel manually, use a logged in scraper, or apply visual classification against captured screenshots.

## Chronological timeline

### March 2026

**3/5/2026, Jens Heitmann IG reel**
3 design systems for Claude Code that compound across a project (Visual Reference + Component Memory + Design QA Loop).
URL: https://www.instagram.com/reel/DVbfcdTkZ7R/
Landed in: `claude-design-systems/references/jens-heitmann-three-systems.md`

**3/11/2026, 21st.dev**
AI Agent Registry plus UI Components plus Developer Tools. Magic MCP installed.
URL: https://21st.dev/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**3/14/2026, Seb Intel IG reel**
Free AI for clean UI layouts that exposes the underlying prompts. Pattern: emit prompts with designs.
URL: https://www.instagram.com/reel/DV3rnwGvSD1/
Landed in: `claude-design-systems/references/visual-prompt-workflows.md`

**3/14/2026, Marc Cleroux YouTube**
Claude Code plus Nano Banana 2 = "Insane $10K Websites." Image first, code second loop.
URL: https://youtu.be/TZUTe7s11-I
Landed in: `claude-design-systems/references/nano-banana-workflow.md`

**3/14/2026, system-design-academy GitHub**
Software system design learning resource (databases, scaling, distributed systems). Included as meta design: UI is downstream of system design.
URL: https://github.com/systemdesign42/system-design-academy
Landed in: `claude-design-systems/references/system-design-adjacent.md`

**3/15/2026, Kyle Whitrow IG reel**
"Claude Code just killed website designers" packaged design skill.
URL: https://www.instagram.com/reel/DV4OGI0D1hS/
Landed in: `claude-design-systems/references/visual-prompt-workflows.md`

**3/21/2026 14:22, Tenfold Marc Notion**
"How to Build a $10,000 Website With One Line of Code." Premium build playbook chaining DESIGN.md, Nano Banana 2, and components into a single Claude Code prompt structure.
URL: https://tenfoldmarc.notion.site/How-to-Build-a-10-000-Website-With-One-Line-of-Code-327c2d7ca4cf81d7896ec03fa2da1041
Landed in: `claude-design-systems/references/premium-build-playbooks.md`

**3/21/2026 14:25, Joey Mulcahy Guides Notion**
"The Claude Code Product Visual Workflow." Product UI focused playbook with state coverage and screenshot diffing emphasis.
URL: https://joeymulcahyguides.notion.site/The-Claude-Code-Product-Visual-Workflow-3262b10bd51680d6ba29e2072a5ba78e
Landed in: `claude-design-systems/references/premium-build-playbooks.md`

**3/21/2026 14:27, Swishy.ai**
AI motion graphics and kinetic typography platform. Free Mini tier. Hero motion without writing Framer Motion or GSAP by hand.
URL: https://www.swishy.ai/
Landed in: `claude-design-systems/references/premium-build-playbooks.md`

**3/21/2026, Unicorn Studio**
No code WebGL effects, motion, and interactivity for hero sections.
URL: https://www.unicorn.studio/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**3/21/2026 14:32, Tim Koda Notion**
"The Claude Code Creative Studio Stack: Full Setup Guide." End to end creative production rig (video, design, brand, asset pipelines, MCPs, hooks).
URL: https://www.notion.so/The-Claude-Code-Creative-Studio-Stack-Full-Setup-Guide-by-timkoda_-326ac4ebbbda81de8bb5fd9e1fa96c38
Landed in: `claude-design-systems/references/premium-build-playbooks.md`

**3/21/2026 14:45, Striped Thief Notion**
"The 10k Vibecode Blueprint." Vibecode genre playbook for fast premium full stack delivery via Claude Code.
URL: https://striped-thief-c4a.notion.site/The-10k-Vibecode-Blueprint-31773bd1b02b80828553f5b68bb95f6e
Landed in: `claude-design-systems/references/premium-build-playbooks.md`

**3/21/2026, Aceternity UI**
React, Next.js, Tailwind, Framer Motion. 200+ copy paste components with signature motion.
URL: https://ui.aceternity.com/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**3/21/2026, Open Session Link Portal**
Curated portal: design systems, AI advice, Fortune 500 design veterans.
URL: https://opensesh.github.io/OS_our-links/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**3/21/2026, Design Arena**
Largest global crowdsourced design benchmark. Submit, get voted on.
URL: https://designarena.ai/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**3/30/2026, Julia Product Designer IG reel**
MediaPipe gesture tracking driving an interactive UI in one evening.
URL: https://www.instagram.com/reel/DWgkUK9CIiE/
Landed in: `claude-design-systems/references/visual-prompt-workflows.md`

### April 2026

**4/2/2026, Github Projects IG reel**
Google Stitch introduces DESIGN.md (README.md but for design systems).
URL: https://www.instagram.com/reel/DWlVvL9j0MG/
Landed in: `claude-design-systems/references/design-md-pattern.md`

**4/4/2026, Github Awesome IG reel**
Boneyard: auto generated skeleton screens that do not break on layout changes.
URL: https://www.instagram.com/reel/DWq5ARgEwsB/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**4/6/2026, Joshua Stevenson IG reel** (HIGH priority in original analysis)
Claude Code plus Google Stitch DESIGN.md end to end workflow for professional websites.
URL: https://www.instagram.com/reel/DWucfVJzQgU/
Landed in: `claude-design-systems/references/design-md-pattern.md`

**4/8/2026 00:24, Ahmad's own todo list**
Item 10: "get ready with obsedian / slack / jira / notion / figma / everything related to claude from my chat." Confirms Figma is part of the declared design stack alongside Notion. Worth wiring Figma exports into the DESIGN.md and inspiration folder workflow.
Landed in: this intake log (no external URL)

**4/11/2026, Marc Kaz IG reel**
HTML breaking free from the DOM with shaders and WebGL composited above semantic markup.
URL: https://www.instagram.com/reel/DW34yHrjkcf/
Landed in: `ultimate-frontend-design/references/component-libraries-and-tools.md`

**4/11/2026, Alex Wang LinkedIn (HIGH priority in original analysis)**
"Claude Code Repo Review: Complete System Design for AI." 5 layer model: Config, Workflows, Memory, Security, Governance. Included as meta design.
URL: https://www.linkedin.com/posts/alexwang2911_one-of-the-best-claude-code-repos-ive-come-share-7445727930829283328-PX_1
Landed in: `claude-design-systems/references/system-design-adjacent.md`

**4/13/2026, Notion: Claude Code for Designers**
Hub page for designers using Claude Code.
URL: https://band-spike-a6d.notion.site/Claude-Code-for-Designers-32a519264d8e8014860ee41ca47b7b8a
Landed in: top of mind reference, included in `claude-design-systems/SKILL.md` Sources

## What got added to the skill

```
Ultimate Frontend Design Skill/.claude/skills/
  ultimate-frontend-design/references/
    component-libraries-and-tools.md   (new)
    whatsapp-design-intake.md          (this file)
  claude-design-systems/               (new bundle)
    SKILL.md
    references/
      design-md-pattern.md
      jens-heitmann-three-systems.md
      nano-banana-workflow.md
      visual-prompt-workflows.md
      system-design-adjacent.md
      premium-build-playbooks.md
```

## Concepts vs. tools vs. systems matrix

| Item | Type | Where |
|------|------|-------|
| DESIGN.md | system + concept | claude-design-systems/references/design-md-pattern.md |
| Jens 3 systems | system | claude-design-systems/references/jens-heitmann-three-systems.md |
| Nano Banana 2 loop | workflow | claude-design-systems/references/nano-banana-workflow.md |
| Joshua Stevenson Stitch loop | workflow | claude-design-systems/references/design-md-pattern.md |
| Kyle Whitrow design skill | concept (skill packaging) | claude-design-systems/references/visual-prompt-workflows.md |
| Seb Intel prompt sharing | pattern | claude-design-systems/references/visual-prompt-workflows.md |
| Julia MediaPipe sketch to UI | pattern | claude-design-systems/references/visual-prompt-workflows.md |
| Aceternity UI | component library | ultimate-frontend-design/references/component-libraries-and-tools.md |
| 21st.dev | component library + Magic MCP | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Unicorn Studio | tool (WebGL) | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Boneyard | tool + pattern | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Design Arena | benchmark | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Open Session Link Portal | research hub | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Marc Kaz HTML/WebGL | concept | ultimate-frontend-design/references/component-libraries-and-tools.md |
| Notion Claude Code for Designers | reference hub | claude-design-systems/SKILL.md |
| Alex Wang 5 layer Claude Code repo | meta system | claude-design-systems/references/system-design-adjacent.md |
| system-design-academy | learning resource (architecture) | claude-design-systems/references/system-design-adjacent.md |
| Tenfold Marc $10K Website | premium build playbook | claude-design-systems/references/premium-build-playbooks.md |
| Joey Mulcahy Product Visual Workflow | product UI playbook | claude-design-systems/references/premium-build-playbooks.md |
| Tim Koda Creative Studio Stack | studio rig setup guide | claude-design-systems/references/premium-build-playbooks.md |
| Vibecode Blueprint | vibecode genre playbook | claude-design-systems/references/premium-build-playbooks.md |
| Swishy.ai | tool (motion graphics, kinetic type) | claude-design-systems/references/premium-build-playbooks.md |
| Figma in stack | personal stack note | this intake log (4/8 todo) |

## What was deliberately not added

- Generic comment-bait IG reels with no concrete artifact (caught by the original analysis as LOW priority).

Note: the original draft excluded "Claude Code Repo Review: Complete System Design for AI" (Alex Wang, 4/11/2026) and "system-design-academy" (3/14/2026) on the grounds they are software architecture rather than visual design. On reflection both belong here as meta design (designing the system that produces the design) and were added to `claude-design-systems/references/system-design-adjacent.md`.

## How to refresh this log

1. Run the WhatsApp scraper to pull latest messages (`Desktop/Claude/whatsapp-scraper/scrape-whatsapp-7d.mjs` or longer range)
2. Categorize new messages (script in same folder)
3. Filter for design related items (Design/UI category plus design adjacent in AI/Claude Code, AI/General)
4. Append new entries to the Chronological timeline above
5. If a new system or concept emerges, add a new reference doc in the appropriate skill bundle
6. Update the Concepts vs. tools vs. systems matrix
