# Pre-Output Checklist

Run this before saving any final HTML output. Each item is a binary pass/fail. Do not deliver the file until all items pass.

---

## Structure

- [ ] Hero contains only the logo — no title, tags, or labels
- [ ] Archive banner is present if archive status is "legacy"; absent if "current"
- [ ] `doc-type` label uses `var(--cyan)` — not the section accent color
- [ ] `doc-meta` has audience, version, and date — no blank fields (use "v1.0" and current date as fallback)
- [ ] 1–3 `doc-tags` present, using controlled vocabulary values
- [ ] Sidebar nav (if sidebar layout) has one `.nav-group` per section with correct `--ac` inline style
- [ ] All section `id` attributes are unique and match sidebar `href` anchors

## Branding

- [ ] No HaBITTS name, Helios branding, Alliance logo, or legacy org names in output
- [ ] No gradient banner images carried through from source
- [ ] No page numbers, running headers, or boilerplate footer text

## Icons

- [ ] Every section heading has an icon PNG (`sec-head-icon`)
- [ ] Section heading icon color matches `--ac` for that section
- [ ] BYB icon and label text are both `#00205B`
- [ ] Key Takeaways icon and label text are both `#00205B`
- [ ] Every callout icon color matches the callout border color (`--callout-color`)

## Accent Colors

- [ ] Accent map matches the mapping declaration — no section uses the wrong color
- [ ] Section inline `--ac` values match the nav-group `--ac` values (sidebar layout)
- [ ] Accent rotation follows the correct cycle order

## Content

- [ ] No empty sections (heading with no body)
- [ ] No two callouts placed back to back without intervening content
- [ ] Multi-audience content is split or consolidated per the multi-audience rules

## Images

- [ ] All passing images are base64-embedded
- [ ] No `.shots img` has a border
- [ ] No `.shots img` has an explicit `height` attribute
- [ ] Every `.shots` block has a caption immediately after it
- [ ] Skipped and placeholdered images are accounted for in the mapping declaration log

## Output

- [ ] File is self-contained (no external asset references except Google Fonts CDN)
- [ ] Output filename follows `HFCC_[DocumentType]_[Subject].html` convention
- [ ] File saved to `/mnt/user-data/outputs/`
