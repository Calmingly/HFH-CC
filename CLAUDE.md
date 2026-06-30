# HFH-CC Skill Repository

This repository contains Claude Code skills for Henry Ford Health Community Connect (HFCC) document workflows.

## Skills

### hfh-web-conversion-v02

**Location:** `hfh-web-conversion-v02/`

Converts any HFCC document (PDF, DOCX, PPTX, plain text, Markdown) into a self-contained, branded HTML page using the v02 Clinical Article design system.

**Trigger on:** any HFCC web conversion request, Epic release guides, upgrade summaries, training materials, policy docs, tip sheets, or when Tim says "web", "HTML", "article format", or drops an HFCC document without specifying Word.

**Key files:**
- `SKILL.md` — skill definition and full conversion workflow
- `checklist.md` — pre-output quality gate (run before every save)
- `template-sidebar.html` — HTML shell with sidebar nav
- `template-simple.html` — HTML shell, no sidebar
- `references/design-system.md` — color tokens, accent rotation, icon lookup tables, controlled vocabulary
- `references/content-mapping.md` — inference rules, image filter, PPTX extraction, filename logic
- `references/content-patterns.md` — HTML patterns: cards, steps, callouts, BYB, Key Takeaways, screenshots

**Icon assets** are at `/mnt/skills/user/hfh-docx-conversion-v01/assets/` — not bundled here.

**Output:** `/mnt/user-data/outputs/` — one self-contained `.html` file per source document.
