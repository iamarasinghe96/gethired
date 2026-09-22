# DOCX Export Specification

Reverse-engineered from the approved final templates (`CV.docx`, `Cover_Letter2.docx`)
and their PDF outputs. The generator in `index.html` must reproduce these values exactly.

---

## 1. Shared document setup

| Property | Value |
|---|---|
| Page size | 12240 × 15840 twips (US Letter) |
| Margins | `top/right/bottom/left = 720` twips (0.5"), `header = 720`, `footer = 720` |
| Content width | 10800 twips = **6,858,000 EMU** |
| Body font | theme `majorBidi` → `<w:rFonts w:asciiTheme="majorBidi" w:hAnsiTheme="majorBidi" w:cstheme="majorBidi"/>` |
| Default size | `sz=22` (11 pt) from docDefaults |
| Small size | `sz=21` (10.5 pt) — used in all table cells |
| Accent (headings + rules) | `#156082` (theme accent1) |
| Accent (CL table header text) | `#215E99` (theme text2, tint BF) |
| Hyperlink runs | `<w:rStyle w:val="Hyperlink"/>` (blue + underlined) |
| docDefaults paragraph | `spacing after=160 line=259 auto` |

---

## 2. Letterhead (identical in CV and Cover Letter; lives in the header part)

Two paragraphs, both centred:

1. **Name line** — `jc=center`, `spacing line=360 auto`
   run: **bold**, `sz=24` (12 pt), `color=156082`, text = **FULL NAME IN CAPITALS**
2. **Contact line** — `jc=center`, no spacing override, `sz=20` (10 pt)
   Seven inline icon images interleaved with text, in this order:
   `phone` · `email` · `location` · `LinkedIn` · `Portfolio` · `SEEK` · `Right to Work`
   The last four labels are hyperlinks (Hyperlink style).

The letterhead is **not** regenerated — it is inherited from the template header and only
has its `[YOUR_*]` placeholder text/targets substituted.

---

## 3. The blue rule (line shape) — exact spec

Every horizontal blue line is an **anchored Straight Connector drawing**, not a paragraph border.

| Property | Value |
|---|---|
| Geometry | `<a:prstGeom prst="line"/>` |
| Thickness | `<a:ln w="12700"/>` = **1.0 pt** |
| Colour | theme `accent1` → `#156082` |
| Width (`cx`) | `6855873` EMU (full content width) |
| Height (`cy`) | `12227` EMU |
| Section rules | `positionH posOffset=0`, `positionV posOffset=-635` |
| Headline bar | `cx=6855460`, `cy=12065`, `posH=1905`, `posV=148694` |

**Placement:** the drawing sits in its **own paragraph** with `jc=both`, placed *immediately
after* the heading paragraph. The headline bar is the very first paragraph of the CV body.

Total in the approved CV: **8 lines** = 1 headline bar + 7 section rules.

---

## 4. Bullet numbering

All bullet lists share this definition:

```
numFmt   = bullet
lvlText  =        (Symbol font bullet •)
rFonts   = Symbol
ind      = left="360" hanging="360"
```

Use a `numId` whose abstract definition has **`left=360`** (not 720).
Each experience role uses its **own `numId`** so lists never merge.
`pStyle=ListParagraph`, `ilvl=0`, and `jc=both` on every bullet paragraph.

> Note: CORE COMPETENCIES does **not** use numbering — see §5.2.

---

## 5. CV body structure

Order: `PROFESSIONAL SUMMARY` → `CORE COMPETENCIES` → `PROFESSIONAL EXPERIENCE` →
`EDUCATION` → `CERTIFICATIONS` → `KEY ACHIEVEMENTS` → `REFEREES`

**Section pattern (every section):**

1. Heading paragraph — `spacing before=240` (omit on the first section), run: **bold**,
   `color=156082`, default size (11 pt), text in CAPITALS.
2. Rule paragraph — `jc=both`, containing the connector drawing (§3).
3. Content.

### 5.1 PROFESSIONAL SUMMARY
- One paragraph: `after=40 line=240 auto jc=both`, prose with inline hyperlinks.
- Followed by an empty paragraph `after=40 line=240 auto jc=both`.

### 5.2 CORE COMPETENCIES
- Table `grid=[5395, 5395]`, `tblW=auto`, **all borders `none`**.
- One row, two cells; items split evenly left/right.
- Each item is a paragraph with `spacing line=276 auto` containing **two runs**:
  a literal `"• "` run, then the item text run. **Not** a numbered list.

### 5.3 PROFESSIONAL EXPERIENCE
- Table `grid=[8455, 2335]`, `tblW=auto`, **all borders `none`**.
- Per role, three rows:

| Row | Content |
|---|---|
| **A** | CELL0 (8455): **bold** job title, then optional link-label run e.g. `[Responsibilities]` / `[Website]` (Hyperlink style, underlined). CELL1 (2335): `jc=right`, **bold + italic** date range. |
| **B** | Single cell `gridSpan=2` (w=10790): *italic* organisation/location + optional `[Milestones]` hyperlink; optional *italic* `Previously: …` line; then bullet paragraphs (`ListParagraph`, own `numId`, `ilvl=0`, `jc=both`). |
| **C** | Single cell `gridSpan=2`, empty — spacer between roles. |

### 5.4 EDUCATION
- Table `grid=[8455, 2335]`, borders `none`.
- Per qualification, two rows:
  - Row A: CELL0 **bold** degree; CELL1 `jc=right` **bold** years.
  - Row B: `gridSpan=2`, *italic* institution + optional `[Credentials]` hyperlink.

### 5.5 CERTIFICATIONS
- Table `grid=[5395, 5395]`, borders `none`; items split left/right.
- Each item: `ListParagraph`, `numId`, `ilvl=0`, `jc=both`; the run itself is the
  **hyperlink** to the credential (underlined).

### 5.6 KEY ACHIEVEMENTS
- Plain bullet paragraphs (`ListParagraph`, `numId`, `ilvl=0`, `jc=both`),
  text plus an optional trailing `[View]` hyperlink.

### 5.7 REFEREES
- Table `grid=[5395, 5395]`, borders `none`; one row, two cells (two referees side by side).
- Each cell, three paragraphs:
  1. **bold** `Mrs./Mr. Full Name` + `" | "` + hyperlink `LinkedIn`
  2. Position, Organisation
  3. `email` + `phone`

---

## 6. Cover letter body structure

1. Headline bar paragraph (connector drawing, §3).
2. Salutation — `after=60 line=240 auto`, `Dear <Name>,`
3. Intro paragraph — `after=60 line=240 auto jc=both`, inline hyperlinks allowed.
4. About paragraph — same formatting.
5. **Responsibility table** — `grid=[3505, 7285]`, `tblW=auto`,
   style `TableGrid` with **visible borders** (no border override — this is the one
   table that keeps its grid lines).
   - Header row: both cells `jc=center`, **bold**, `color=215E99`, `sz=21`.
   - Data rows: CELL0 left-aligned `sz=21`; CELL1 `jc=both` `sz=21`, inline links allowed.
6. Empty paragraph `after=60 line=240 auto jc=both`.
7. Closing paragraphs `after=60 line=240 auto jc=both`, with an empty paragraph between them.
8. `Kind regards,` — `after=20 line=240 auto` (no justification).
9. Signature paragraph — no spacing override; inline signature image
   `cx=858129 cy=447087` EMU (≈ 2.38 cm × 1.24 cm), followed by the name text.

---

## 7. Prompt → DOCX field mapping

The AI emits tagged plain text; the parser maps it onto the structure above.

### Cover letter

```
[SALUTATION]      → §6.2
[INTRO]           → §6.3
[ABOUT]           → §6.4
[TABLE]           → §6.5   one row per line: "Requirement :: Contribution"
[CLOSING]         → §6.7   paragraphs separated by a blank line
[SIGNOFF]         → §6.8/6.9
```

### CV

```
[SUMMARY]         → §5.1
[COMPETENCIES]    → §5.2   "- item" per line, split evenly across two columns
[EXPERIENCE]      → §5.3
[EDUCATION]       → §5.4
[CERTIFICATIONS]  → §5.5   "- [Name](url)"
[ACHIEVEMENTS]    → §5.6   "- text [View](url)"
[REFEREES]        → §5.7
```

**Extended block syntax** (carries the link labels the template needs):

```
[EXPERIENCE]
### <Job Title> | <Dates> | [Responsibilities](url)
<Organisation, Location> | [Milestones](url)
Previously: <earlier title and dates>
- bullet
- bullet

[EDUCATION]
### <Degree> | <Years>
<Institution> | [Credentials](url)

[REFEREES]
### <Mrs./Mr. Full Name> | [LinkedIn](url)
<Position, Organisation>
<email> <phone>
```

- The 3rd `|` field on an experience header and the 2nd on an org/institution line are
  **optional** link labels rendered as underlined Hyperlink runs.
- Inline `[label](url)` is supported anywhere in prose, bullets, and table cells.
- Trailing report sections after the last tag are cut (markdown rules, `**A. …**`,
  `KEYWORD…`, `WHAT WAS…`, etc.).

---

## 8. Substitutions applied at export time

| Placeholder | Source |
|---|---|
| `[YOUR_FULL_NAME]` | Profile → short name (text in parentheses if present), UPPERCASED in the letterhead |
| `[YOUR_PHONE]`, `[YOUR_EMAIL]`, `[YOUR_LOCATION]` | Profile personal fields |
| `[YOUR_LINKEDIN_URL]`, `[YOUR_PORTFOLIO_URL]`, `[YOUR_RIGHT_TO_WORK_URL]`, `[YOUR_SEEK_URL]` | Profile URLs (header rels) |
| Signature image | Uploaded PNG/JPG stored in `localStorage`, embedded at §6.9 size |

---

## 9. Input sanitisation (required)

Clipboard / AI-reply text frequently carries characters that XML 1.0 forbids. A single
one makes `word/document.xml` malformed and Word refuses the file with
*"Word experienced an error trying to open the file."*

Before parsing, every input is passed through `_sanitize()`:

- strip a leading BOM (`U+FEFF`)
- normalise `\r\n` / `\r` → `\n`
- remove `U+0000–U+0008`, `U+000B`, `U+000C`, `U+000E–U+001F`, `U+FFFE`, `U+FFFF`
- drop unpaired surrogates

`xesc()` applies the same filter as defence in depth. Additionally, the assembled
`document.xml` is parsed with `DOMParser` **before** zipping; if it is not well-formed
the export aborts with an explicit message rather than producing a broken `.docx`.
