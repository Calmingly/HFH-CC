# Content Mapping Reference

Inference rules for document type, structure, callouts, and metadata. Apply these during Step 1 (Mapping Declaration) without asking for confirmation unless a decision would materially change the document type or audience.

---

## Document Type Inference

| Source characteristics | Document type |
|---|---|
| Numbered procedures + screenshots | Tip Sheet or Training Script / Guide |
| Epic version context + feature changes | Release Guide |
| Policy language, compliance, governance | Policy |
| End-to-end workflow across roles | Workflow Guide |
| Quick lookup reference, tables, cheat sheet | Reference or Job Aid |
| New staff context + orientation content | Onboarding |
| Announcement, memo, communication | Communication |

---

## Section Mode Inference

| Source pattern | Section mode |
|---|---|
| Numbered steps | procedure |
| Paragraphs + embedded procedure | mixed |
| Role-specific headers (e.g., "For Providers", "RN Only") | role-specific |
| Overview or background paragraphs only | narrative |
| Lookup tables, reference lists | reference |
| Configuration, setup steps | settings |
| Timing, deadlines, recurring tasks | schedule |
| Q&A, common questions | faq |
| Best practices, efficiency notes | tip |
| Critical notices, compliance content | warning |
| Who to contact, escalation paths | contact |

---

## Callout Inference

| Source pattern | Callout type |
|---|---|
| "Note:", "Note —", bold side remarks | NOTE |
| "Tip:", "Shortcut:", "Best practice:" | TIP |
| "Reminder:", "Per policy:", recurring obligation language | REMINDER |
| "See also:", "Reference:", links to other docs | REFERENCE |
| "Warning:", "Important:", data integrity risk language | IMPORTANT |
| "Required:", "Action required:", mandatory directives | ACTION REQUIRED |

---

## Before You Begin Inference

Generate a Before You Begin block if source contains any of:
- Login or access prerequisites
- Required patient or encounter context
- Role or department requirements
- Pre-task setup steps

---

## Key Takeaways Inference

Generate for: Training Guides, Workflow Guides, Release Guides.
Omit for: Tip Sheets under 2 sections, Job Aids, Policies.

---

## Archive Banner Rule

Apply the amber archive banner if ANY of the following are true:
- Source contains Helios branding, logos, or verbiage
- Source is dated before January 2023
- Source uses "archived" or "legacy" in its title, header, or metadata
- Source filename contains "archive", "legacy", "old", or "helios" (case-insensitive)

Do not apply to current HFH-branded documents.

---

## Multi-Audience Section Splitting

- Role-specific content under distinct headers ("For Providers", "RN Only") → keep as separate role-specific sections, one per role.
- Role-specific content interleaved within shared procedures → extract and consolidate into a dedicated role-specific section at the end. Do not scatter role callouts throughout shared steps.
- Content applying to all roles equally → single shared section, do not split.
- Minimum threshold for a role-specific section: 3 or more distinct items scoped to that role. Do not create a section for a single bullet or sentence.

---

## PPTX Extraction

```python
from pptx import Presentation
import json

prs = Presentation("source.pptx")
slides = []
for i, slide in enumerate(prs.slides):
    title = ""
    body_parts = []
    notes = ""
    for shape in slide.shapes:
        if shape.shape_type == 13:  # Picture
            body_parts.append({"type": "image", "shape_id": shape.shape_id})
        elif shape.has_text_frame:
            text = shape.text_frame.text.strip()
            if shape.name.lower().startswith("title") or (
                hasattr(shape, "placeholder_format")
                and shape.placeholder_format
                and shape.placeholder_format.idx == 0
            ):
                title = text
            else:
                body_parts.append({"type": "text", "content": text})
    if slide.has_notes_slide:
        notes = slide.notes_slide.notes_text_frame.text.strip()
    slides.append({"index": i + 1, "title": title, "body": body_parts, "notes": notes})

print(json.dumps(slides, indent=2))
```

**PPTX linearization rules:**
- Slide title → section heading
- Slide body bullets → body text or numbered steps depending on content
- Slide images → extract via `shape_type == 13`, apply standard filter and compression rules
- Speaker notes → apply Speaker Notes Filter below

**Speaker Notes Filter:** Include a note as a Note callout (labeled "Presenter Note:") only if ALL of the following are true:
- Length is 15 words or more
- Does not consist solely of logistics phrases — skip notes matching: "pause here", "ask audience", "transition to", "next slide", "click to advance", "demo", "questions?", "take a break", "timing:", or any note that is purely a speaker cue with no instructional content

When in doubt, include it. A false positive is less harmful than dropping substantive content.

---

## Image Filter

Run all extracted images through this filter before embedding. Target JPEG quality 80 for screenshots. PNG acceptable for diagrams with hard edges.

```python
from PIL import Image
import numpy as np

def prepare_screenshot(path, out_path):
    img = Image.open(path).convert('RGB')
    arr = np.array(img)
    w, h = img.size

    # Filter 1 — near-uniform fill (gradient banners, solid backgrounds)
    brightness = arr.mean(axis=2)
    if brightness.std() < 10:
        return ('skip', 'near-uniform fill')

    # Filter 2 — near-blank white (border artifacts, divider lines)
    non_white = np.sum(brightness < 240)
    if non_white / brightness.size < 0.03:
        return ('skip', 'near-blank image, insufficient content pixels')

    # Filter 3 — icon-scale dark background (Epic One-Click buttons, small UI icons)
    short_side = min(w, h)
    long_side = max(w, h)
    aspect = short_side / long_side
    mean_brightness = brightness.mean()
    if mean_brightness < 30 and aspect > 0.75 and short_side < 250:
        return ('skip', 'icon-scale dark background image')

    # Filter 4 — probable full-page document scan
    portrait_ratio = h / w if h > w else w / h
    if 0.65 <= (w / h if w < h else h / w) <= 0.85 and long_side > 900:
        top_band = arr[:max(1, h // 10), :, :]
        top_brightness = top_band.mean(axis=2)
        if top_brightness.std() < 25:
            return ('placeholder', 'probable full-page document scan')

    img.save(out_path, 'JPEG', quality=80)
    return ('ok', out_path)
```

**Filter results:**
- `('skip', reason)` — omit entirely. Log under "Images found" in the mapping declaration.
- `('placeholder', reason)` — omit but insert: `<p class="shot-caption" style="font-style:italic;color:var(--muted);">[IMAGE SKIPPED: probable full-page document scan]</p>`
- `('ok', out_path)` — compress and embed normally.

**Skew correction:** Before embedding, check extracted images for DPI skew (common with PDF rasterization at non-standard DPI). Log visibly skewed images for manual review.

**Secondary size filter — run after `prepare_screenshot()`:**

DOCX and PDF sources commonly embed hundreds of small inline UI icons (Epic toolbar buttons, status indicators, checkboxes) as image objects. These pass the pixel-content filters above but are not screenshots. Apply this size gate after `prepare_screenshot()` returns `('ok', ...)`:

```python
def is_meaningful_screenshot(path):
    img = Image.open(path)
    w, h = img.size
    short_side = min(w, h)
    long_side = max(w, h)
    # Must have short side >= 50px AND at least one dimension >= 200px
    return short_side >= 50 and long_side >= 200
```

Images that fail this gate are excluded entirely (not placeholdered). Log them as "excluded (inline icon)" in the mapping declaration image count.

Apply this gate even if `prepare_screenshot()` returned `('ok', ...)` — the size gate is a post-filter, not a replacement.

---

## Filename Inference

1. If source filename is meaningful (not a UUID, timestamp, or generic name) — use the stem, strip spaces and special characters, apply PascalCase. Example: `epic-in-basket-guide.pdf` → `HFCC_TipSheet_EpicInBasketGuide.html`
2. If source filename is not meaningful — derive from document title. Strip articles and "HFH" / "Community Connect" prefixes, apply PascalCase. Example: "Henry Ford Health — MyChart Video Visit Training" → `HFCC_TrainingGuide_MyChartVideoVisit.html`
3. State the derived filename in the mapping declaration before building.

---

## Source Extraction by Format

- **PDF:** `pdftoppm -r 150 -png` for pages; `pdfimages -png` for embedded images.
- **DOCX:** pandoc for text; unzip `word/media/` for images.
- **Plain text / Markdown:** use directly.
- **PPTX:** use the extraction script above.
