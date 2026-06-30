# Design System Reference

## Color Tokens

```
--navy:    #000064   headings, brand text
--blue:    #1464B9   links, primary accent (also accent slot 6)
--blue-lt: #F0F5FB   callout bg, step badge bg
--blue-md: #D6E4F2   sidebar bg, dividers
--cyan:    #0091E1   accent slot 1
--pink:    #DC0096   accent slot 2
--purple:  #7D50AC   accent slot 3
--green:   #00C896   accent slot 4
--orange:  #FA550A   accent slot 5
--bg:      #F7F9FC   page background
--surface: #FFFFFF   card and hero background
--text:    #0D1B2E   body text
--muted:   #5A6880   secondary text, captions, sub-heads
--border:  #E2EAF3   card and section borders
--amber:   #B45309   Before You Begin block
```

> Note: `--blue` (#1464B9) serves dual purpose — it is both the link/UI color and accent slot 6 in the rotation. There is no separate CSS variable for slot 6; use `style="--ac:#1464B9"` inline.

## Accent Rotation

Assign `--ac` per section via inline style: `style="--ac:#0091E1"`

Cycle order:
```
cyan #0091E1 → pink #DC0096 → purple #7D50AC → green #00C896 → orange #FA550A → blue #1464B9 → repeat
```

`--ac` drives: sidebar nav pip, section heading text and rule, card ID badge color, card hover border.

## Typography

- Body/UI: Inter (Google Fonts)
- IDs/code/refs: JetBrains Mono (Google Fonts)

## Icon Color Rule

Icon PNG color must match the adjacent text label color exactly.

- Section heading icons: match `var(--ac)` — icon and h2 are the same color
- Callout icons: match `--callout-color` — icon and label `<strong>` are the same color
- BYB icon: `#00205B` — matches the BYB label text; amber border/background are independent
- Key Takeaways icon: `#00205B` — same rule as BYB

## Section Heading Icons

Pre-rendered Tabler Icon PNGs at size 15. Shared with DOCX skill.
Path: `/mnt/skills/user/hfh-docx-conversion-v01/assets/`
Naming: `icon_{CODEPOINT}_{ACCENT_HEX_NO_HASH}_15.png`

| Mode | Codepoint | Use when |
|---|---|---|
| overview | F07D | orientation, what-is-this |
| narrative | EA98 | prose, background, policy |
| procedure | EB6A | step-by-step workflows |
| mixed | ED9F | prose intro + procedural steps |
| reference | EF86 | lookup tables, cheat sheets |
| settings | EBCA | configuration, system setup |
| schedule | F621 | timing, deadlines, recurring tasks |
| role-specific | F076 | content scoped to a role or audience |
| warning | F708 | critical notices, compliance callouts |
| faq | F31F | Q&A, common questions |
| tip | F66A | best practices, efficiency notes |
| contact | F4B8 | who to contact, escalation paths |
| screen | EA89 | screen workflows, Epic navigation |
| guide | EAD9 | multi-panel or sidebar guides |
| watch | EE88 | key notices, eyes-on-this |

**Icon selection rule:** Mode glyphs are a starting point. Prefer a glyph that reflects the section's *subject* when a more specific option exists. A scheduling section should use `F621` (calendar) even if its mode is `procedure`. A data review section should use `EE88` even if its mode is `reference`. Reserve the same glyph across multiple sections only when the subject is genuinely the same.

Example filename resolution: procedure section with `--ac:#0091E1` → `icon_EB6A_0091E1_15.png`

## Callout Icons

Pre-rendered Tabler Icon PNGs at size 12. Shared with DOCX skill.
Path: `/mnt/skills/user/hfh-docx-conversion-v01/assets/`
Naming: `icon_{CODEPOINT}_{BORDER_COLOR_NO_HASH}_12.png`

| Type | Codepoint | Border color | Fill |
|---|---|---|---|
| NOTE | F028 | #1464B9 | #D6E4F7 |
| TIP | F66A | #00C896 | #D0F5EC |
| TIP / NOTE | F6A6 | #00C896 | #D0F5EC |
| REMINDER | F725 | #7D50AC | #E8DCFA |
| REFERENCE | EA39 | #0091E1 | #CCF0FF |
| IMPORTANT | F6EE | #FA550A | #FFE4D6 |
| ACTION REQUIRED | F819 | #DC0096 | #FFD6F2 |
| WARNING | F6F0 | #CC0000 | #FFE0E0 |
| SAVE | EB62 | #007A4D | #D0F5E8 |
| BEFORE YOU BEGIN | EF4C | #00205B | #D6E0F0 |
| KEY TAKEAWAYS | EB45 | #00205B | #D6E0F0 |

## Controlled Vocabulary

**Document type labels** (for `doc-type`):
Tip Sheet, Training Script / Guide, Release Guide, Workflow Guide, Policy, Reference, Job Aid, Onboarding, Communication

**Audience values** (for `doc-meta`):
All Staff, All Clinical Staff, Providers, Advanced Practice Providers (APP), Providers / APP, Nursing Staff (RN), Medical Assistants (MA), RN / MA, Nursing Staff (RN / MA), Front Desk / Scheduling, Professional Billing (PB), Health Information Management (HIM), Practice Administrators, Community Connect Practices, New Hires
