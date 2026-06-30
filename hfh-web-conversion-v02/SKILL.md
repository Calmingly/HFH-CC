---
name: hfh-web-conversion-v02
description: "Converts any Henry Ford Health Community Connect (HFCC) document — PDF, Word (.docx), PowerPoint (.pptx), plain text, or Markdown — into a branded HFCC HTML page using the v02 Clinical Article design system. Use this skill by default for any HFCC web conversion request. Trigger on: Epic release guides, upgrade summaries, training materials, policy docs, tip sheets, department communications, onboarding docs, or any HFCC document where a modern long-form article layout is needed. Also trigger when Tim says 'web', 'HTML', 'article format', or drops any HFCC document without specifying Word."
---

# HFCC Web Document — HTML Conversion Skill v02

Converts any Henry Ford Health document into a self-contained, responsive, branded HTML file using the v02 Clinical Article design system.

---

## Files

| File | Purpose | Read when |
|---|---|---|
| `template-sidebar.html` | Full HTML shell with sidebar nav | Gate 1 result is sidebar layout |
| `template-simple.html` | Full HTML shell, no sidebar | Gate 1 result is simplified layout |
| `references/design-system.md` | Color tokens, accent rotation, icon lookup tables, controlled vocabulary | Start of every conversion |
| `references/content-patterns.md` | HTML patterns: cards, steps, callouts, BYB, Key Takeaways, screenshots | During Step 5 (build) |
| `references/content-mapping.md` | Inference rules, image filter, PPTX extraction, filename logic | During Step 1 (mapping declaration) |
| `checklist.md` | Pre-output quality gate | Step 6 (verify), before saving final output |
| `/mnt/skills/user/hfh-shared/session-handoff.md` | Batch resume format and trigger rules | End of session or mid-batch |

**Always read `references/design-system.md` first.** It contains icon filenames, color values, and controlled vocabulary needed for the mapping declaration and build.

**Icon assets** are at `/mnt/skills/user/hfh-docx-conversion-v01/assets/`. Do not copy or bundle them into this skill.

---

## Pre-Build Gates

Run all three gates before writing any markup.

### Gate 1 — Layout Selection

Use **simplified layout** (`template-simple.html`) when the document meets ANY of:
- Fewer than 3 logical sections
- Fewer than 6 cards total
- Primarily procedural steps rather than reference content
- Tip sheets, quick reference, or single-workflow documents

Use **sidebar layout** (`template-sidebar.html`) for everything else.

**Downstream effects when simplified layout is selected:**
- Omit Key Takeaways unless document type explicitly warrants a summary (Training Guide, Workflow Guide)
- Section `margin-bottom`: `1.5rem` (not `2.5rem`)
- Omit section number badges if there are only 2 sections

### Gate 2 — Accent Map

Write this before any markup:
```
Section 1 [Title]: cyan   #0091E1
Section 2 [Title]: pink   #DC0096
Section 3 [Title]: purple #7D50AC
...
```

### Gate 3 — Section Mode

Decide per section before writing HTML. Valid modes: `narrative`, `procedure`, `mixed`, `role-specific`, `reference`, `overview`, `warning`, `faq`, `tip`, `settings`, `schedule`, `contact`.

See `references/content-mapping.md` for mode inference rules.

---

## Conversion Workflow

### Step 0 — Strip Source Content

Before anything else, remove:
- HaBITTS name, logo, or "Health and Business IT Training & Support"
- Helios logo, wordmark, or any Helios-identifying text
- Henry Ford Health Alliance logo or Alliance verbiage
- Legacy org name variants: "Henry Ford Health System", "Henry Ford Hospital"
- Any header logo or banner that does not match current HFH Community Connect branding
- Decorative blue/white gradient banner images used as page or section headers
- Page numbers, running headers, running footers, boilerplate footer text
- Collapse 3+ consecutive blank lines to 1

### Step 1 — Mapping Declaration

**Do not write any markup until this declaration is complete.**

Read `references/design-system.md` and `references/content-mapping.md` now if not already loaded.

State the following before proceeding:

```
Document type:      [controlled vocabulary value]
Audience:           [controlled vocabulary value]
Output filename:    [derived per filename inference rules]
Template:           [sidebar / simplified]
Archive status:     [legacy / current]

Accent map:
  [#]. [Section Title] — [mode] — [color name / hex]

Callouts mapped:
  "[source text excerpt]" → [callout type]

Images found: [count] — [n embedded, n skipped (reason), n placeholdered (reason)]
```

This declaration is the contract between extraction and build. If it is wrong, the build will be wrong.

### Step 2 — Extract Source Content

See `references/content-mapping.md` for extraction commands by format (PDF, DOCX, plain text, PPTX).

### Step 3 — Run Pre-Build Gates

Evaluate Gate 1, write Gate 2 accent map, decide Gate 3 section modes. All three must be complete before markup starts.

### Step 4 — Filter and Compress Images

Run all extracted images through `prepare_screenshot()` in `references/content-mapping.md`. Log all skipped and placeholdered images in the mapping declaration. Target JPEG quality 80.

### Step 5 — Build HTML

Open the appropriate template (`template-sidebar.html` or `template-simple.html`). Fill all `[PLACEHOLDER]` values. Remove unused commented blocks. All images base64-embedded. One self-contained `.html` file per source document.

Read `references/content-patterns.md` for HTML patterns during this step.

### Step 6 — Verify

Run `checklist.md` before saving output. Save to `/mnt/user-data/outputs/`.

---

## Hero Layout — Strict Rule

The `.hero` block contains **only** the logo. No title, no tags, no labels.

```html
<div class="hero">
  <img class="hero-logo" src="data:image/png;base64,[LOGO_BASE64]"
       alt="Henry Ford Health | Community Connect">
</div>
```

- Logo: `HFH_CC_Logo.png` from `/mnt/skills/user/hfh-docx-conversion-v01/assets/`
- Background: always `#FFFFFF`
- Height: 72px desktop, max-width 560px

---

## Doc Header

Immediately follows the hero (or archive banner, if present).

```html
<div class="doc-header">
  <div class="doc-type">TIP SHEET</div>
  <h1>Document Title</h1>
  <p class="doc-desc">One-sentence description of document purpose.</p>
  <div class="doc-meta">All Staff &nbsp;&middot;&nbsp; v1.0 &nbsp;&middot;&nbsp; Updated June 2026</div>
</div>
```

**Field rules:**
- `doc-type` — always `var(--cyan)`, always uppercase tracked, always bold. Fixed to cyan regardless of section accent rotation.
- `h1` — navy. Not repeated in the hero.
- `doc-desc` — one sentence only. If the source has no obvious summary sentence, derive one from the document title: strip the doc type label and rephrase as a purpose statement. Example: "Epic In Basket Guide" → "A complete guide to managing messages, results, and tasks in Epic In Basket."
- `doc-meta` — `Audience · Version · Last Updated` separated by `·`. Use "v1.0" and current date if source has no version or date.
- No `doc-tags` block. Tags were removed — the `doc-type` label and `doc-meta` line carry sufficient metadata.
- `doc-header` padding: `1.25rem 2.5rem 1rem` (not `1.75rem 2.5rem 1.5rem`).

---

## Archive Banner

Insert immediately below `.hero`, above `.doc-header`, when archive status is "legacy":

```html
<div class="archive-banner">ARCHIVE: This document was produced under a previous branding system. Content may be outdated. Verify with your supervisor before use.</div>
```

Archive trigger rules are in `references/content-mapping.md`.

---

## Sidebar Nav (Sidebar Layout Only)

One `.nav-group` per section. Set `--ac` inline to match the section's accent color. Add `.nav-sub` anchors for cards or subsections when the section has meaningful sub-navigation.

```html
<div class="nav-group" style="--ac:#0091E1">
  <a class="nav-head" href="#section-1">Section Title</a>
  <a class="nav-sub" href="#card-1-1">Card Title</a>
</div>
```

**Sidebar appearance:**
- Background: `#D6E4F2`
- Nav head text: `var(--ac)` — accent color per section, matches the left pip
- Nav sub text: `#3A4F6B`
- Nav sub hover text: `#000064`
- Footer text: `#5A6880`
- Footer border: `1px solid #B8CDE0`
- No logo in the sidebar

**Accent cycle repeat note:** The six-color cycle (cyan → pink → purple → green → orange → blue) repeats for documents with more than 6 sections. For documents with 7–12 sections, sections 7–12 will share accent colors with sections 1–6. This is expected behavior. Do not attempt to invent new accent colors to avoid repetition.

---

## Output Naming

`HFCC_[DocumentType]_[Subject].html`

Save to `/mnt/user-data/outputs/`. One output file per source document unless consolidation is explicitly confirmed.

---

## Session Handoff

See `/mnt/skills/user/hfh-shared/session-handoff.md` for trigger rules and format.
