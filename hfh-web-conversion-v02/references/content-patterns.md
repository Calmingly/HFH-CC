# Content Patterns Reference

Complete HTML patterns for all recurring content blocks. Use these as build reference during Step 5.

---

## Article Prose

```html
<p class="article-body">Introductory or contextual prose. Max-width 680px.</p>
```

---

## Cards

Standard card:
```html
<div class="card" id="card-1-1">
  <div class="card-top">
    <span class="card-id">XX-01</span>  <!-- omit if not Epic/ID context -->
    <h3>Card Title</h3>
    <span class="card-ref">Ref</span>   <!-- omit if no meaningful ref -->
  </div>
  <div class="card-body">Content here.</div>
  <!-- screenshot block below if needed — see Screenshots section -->
</div>
```

Two-column grid:
```html
<div class="card-grid">
  <div class="card" id="card-1-1">...</div>
  <div class="card" id="card-1-2">...</div>
</div>
```

**Card ID convention:** `[2-letter section code]-[zero-padded number]` e.g. `CE-01`, `PA-02`. Omit `.card-id` entirely for non-Epic documents where IDs don't apply.

**Card grid usage rule:** Use `.card-grid` only when there are 2 or more cards. A single card renders as a plain `.card` at full width — do not wrap it in `.card-grid`.

**Long reference tables inside cards:** If a reference table exceeds 15 rows, split into two tables using `.sub-head` labels on each. Do not leave a single unsplit vertical wall of content.

---

## Steps (Procedure Sections)

Standard steps:
```html
<ol class="steps">
  <li><div>Step text here.</div></li>
  <li><div>
    Step text with sub-steps.
    <ol class="sub-steps">
      <li>Sub-step a.</li>
      <li>Sub-step b.</li>
    </ol>
  </div></li>
</ol>
```

Steps with screenshots — add a 1rem spacer after each step's last element when any step in the list contains a screenshot. Apply to the entire `<ol>`, not selectively:
```html
<ol class="steps">
  <li><div>
    Open the patient chart.
    <div class="shots">
      <img src="data:image/jpeg;base64,..." alt="Patient chart" style="max-width:min(100%,800px);height:auto;display:block;">
    </div>
    <p class="shot-caption">Patient chart open to the Summary tab.</p>
    <div style="margin-bottom:1rem;"></div>
  </div></li>
  <li><div>Click Schedule Appointment.</div></li>
</ol>
```

**Sub-step depth:** Never nest more than one level deep. For simple lettered sub-steps, use `<ol class="sub-steps">` with natural list rendering.

---

## Before You Begin

Use when source contains login/access prerequisites, required patient or encounter context, role or department requirements, or pre-task setup steps.

```html
<div class="before-you-begin">
  <div class="byb-head">
    <img src="data:image/png;base64,[BASE64:icon_EF4C_00205B_12.png]" alt="" width="16" height="16">
    Before You Begin
  </div>
  <ul>
    <li>You are logged into Epic.</li>
    <li>The patient chart is open.</li>
  </ul>
</div>
```

Icon and label text are both `#00205B`. The amber border and background are independent of this color.

---

## Key Takeaways

Use for Training Guides, Workflow Guides, and Release Guides. Omit for Tip Sheets under 2 sections, Job Aids, and Policies. Place at the end of the document, inside `.content`, after all sections.

```html
<div class="key-takeaways">
  <div class="kt-head">
    <img src="data:image/png;base64,[BASE64:icon_EB45_00205B_12.png]" alt="" width="16" height="16">
    Key Takeaways
  </div>
  <ul>
    <li>Check In Basket at the start and end of every session.</li>
  </ul>
</div>
```

---

## Callouts

Set `--callout-color` and `--callout-fill` via inline style on the `.callout` div. Values from the icon lookup table in `design-system.md`.

```html
<div class="callout" style="--callout-color:#1464B9;--callout-fill:#D6E4F7;">
  <div class="callout-label">
    <img src="data:image/png;base64,[BASE64:icon_F028_1464B9_12.png]" alt="" width="16" height="16">
    <strong>NOTE</strong>
  </div>
  Important context here.
</div>
```

**Placement rule:** Never place two callouts back to back without at least one content element between them. If source has consecutive callouts, consolidate into one with a combined body, or insert a bridging sentence.

---

## Sub-Heads Inside Card Body

```html
<div class="sub-head">Sub-section Label</div>
```

---

## Screenshots

**Include-all policy:** Embed all images from source that pass the `prepare_screenshot()` filter. The filter is the gate — do not apply additional judgment to borderline images.

**No-border rule:** `.shots img` must never have a border. Do not add one via inline styles or CSS overrides.

**No-distortion rule:** Never set `height` on `.shots img`. Always use `height:auto`.

Standard screenshot with caption:
```html
<div class="shots">
  <img src="data:image/jpeg;base64,..." alt="Brief description" style="max-width:min(100%,800px);height:auto;display:block;">
</div>
<p class="shot-caption">Sentence case caption. No trailing period unless a full sentence.</p>
```

**Caption rule:** Every `.shots` block gets a caption. No exceptions. Derive from source if one exists; otherwise write a brief descriptive label based on what the screenshot shows. Keep captions factual — describe what is shown, not what to do. Always use sentence case (capitalize first word only, plus proper nouns). No trailing period unless the caption is a full sentence.

Side-by-side comparison:
```html
<div class="shots-compare">
  <div class="shots-compare-col">
    <div class="shots-compare-label">Before</div>
    <img src="data:image/jpeg;base64,..." alt="Before" style="max-width:min(100%,800px);height:auto;display:block;">
  </div>
  <div class="shots-compare-col">
    <div class="shots-compare-label">After</div>
    <img src="data:image/jpeg;base64,..." alt="After" style="max-width:min(100%,800px);height:auto;display:block;">
  </div>
</div>
```

Override "Before" / "After" labels with descriptive text when the comparison is not temporal (e.g. "Provider View" / "MA View", "Step A" / "Step B").

---

## Tables

Use for comparison data, lookup grids, or structured reference content from source. Do not use for step lists or prose that happens to be formatted as a table in the source.

```html
<div class="tbl-wrap">
  <table class="data-table">
    <thead>
      <tr>
        <th>Column A</th>
        <th>Column B</th>
        <th>Column C</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Value</td>
        <td>Value</td>
        <td>Value</td>
      </tr>
    </tbody>
  </table>
</div>
```

**CSS to add inline in the `<style>` block when a table is present:**

```css
.tbl-wrap { overflow-x: auto; margin: 1rem 0; }
.data-table { width: 100%; border-collapse: collapse; font-size: 13px; }
.data-table th { background: var(--blue-lt); color: var(--navy); font-weight: 700; text-align: left; padding: .5rem .75rem; border-bottom: 2px solid var(--blue-md); }
.data-table td { padding: .45rem .75rem; border-bottom: 1px solid var(--border); vertical-align: top; }
.data-table tbody tr:hover { background: var(--blue-lt); }
```

**Table rules:**
- Wrap every table in `.tbl-wrap` for horizontal scroll on narrow screens.
- Never embed a table inside a `.card-body` if it has more than 5 columns — use a standalone table below the card instead.
- If a source table has more than 15 rows, apply a `.sub-head` label above it and consider splitting at a logical boundary.
- Do not recreate tables that are purely decorative or contain only images.

**Empty sections:** If a source section has a heading but no usable content after stripping legacy branding and boilerplate, omit the section entirely.

**Vertical rhythm (sidebar layout):** One `2.5rem` margin-bottom between sections. Do not stack multiple spacers.

**Vertical rhythm (simplified layout):** One `1.5rem` margin-bottom between sections.
