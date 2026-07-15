# Mode: pdf-dach — Executive DACH PDF (photo header)

A second CV output format alongside `modes/pdf.md`. Same tailoring logic,
different look: a polished, human-facing single-column layout for **DACH /
continental-European** applications where a professional photo is expected —
rounded blue header box with photo, icon-tagged section headers, centered
competency pills, colored project titles, and a two-column skills table.

It reproduces a designed PowerPoint/Deckblatt CV while staying ATS-conscious
(single column, real selectable text, static sans font, ligatures disabled).

> **When to use which template**
> - `cv-template-dach.html` (this mode) — Germany, Austria, Switzerland, and
>   markets where a photo is standard. Human-facing, design-forward. Rounded
>   header card inside normal print margins; works with the default 0.6in
>   margins and automatic pagination (safest choice).
> - `cv-template-styled.html` (variant of this mode) — same DACH audience, but
>   a **full-bleed replica of the designed PowerPoint reference CV**: curved
>   light-blue header band running to the physical page edges, photo flush with
>   the top-right corner, and a repeated mini header band on page 2. Uses
>   `<meta name="pdf-margin" content="0">` (generate-pdf.mjs then renders with
>   zero margins) and **manual pagination**: insert
>   `<div class="page-break"></div>` followed by a `.mini-band` block (copy it
>   from `templates/cv-template-styled.example.html`) at the desired page
>   boundary inside `{{EXPERIENCE}}`. A fully filled reference CV lives at
>   `templates/cv-template-styled.example.html` — use it as the ground truth
>   for the expected markup of every section (job blocks with `.lead`
>   bullet lead-ins, `.skill-row` categories, `.edu`/`.cert-row` entries,
>   photo `<img class="cv-photo">`, mini-band snippet). Design decisions
>   baked into the current version: uniform 8mm band radius, photo with
>   equal top/bottom gaps + 10px corner radius + right edge on the text
>   margin, single-framed light-blue competency pills (inline-block, not
>   flex — flex triggers a double-border artifact in WeasyPrint), body 11pt
>   / minimum 10pt, name 36pt + headline 14pt identical on both pages.
>   Placeholders follow `modes/pdf.md`
>   (`{{TITLE}}` added for the headline; `{{SECTION_COMPETENCIES}}`/`{{PROJECTS}}`
>   dropped — summary and pills share one section; `{{PHOTO}}` → `<img
>   class="cv-photo" src="…">`, data: URL or path relative to `output/`).
>   Pick this when the CV should look identical to the reference design.
> - `cv-template.html` (`modes/pdf.md`) — US / UK / Canada / Australia and any
>   ATS-first pipeline. Photoless, maximally parse-safe. **Photos are penalized
>   by many-market ATS — never use the DACH templates outside DACH/Europe.**

## Full pipeline

Follow every content step from `modes/pdf.md` (steps 1–13: read `cv.md`, JD
keywords, language/format detection, risk map, summary rewrite, project
selection, bullet reordering, competency grid, keyword injection, clarity
gate). Only the rendering template and a few structural mappings change:

14. Fill `templates/cv-template-dach.html` (not `cv-template.html`) with the
    personalized content — see the placeholder table below.
15. `name` → kebab-case → `{candidate}` (as in `modes/pdf.md` step 15).
16. Write HTML to `output/cv-{candidate}-{company}.html`.
17. Execute:
    `node generate-pdf.mjs output/cv-{candidate}-{company}.html output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf --format={letter|a4} --report={NNN}`
    (DACH is almost always `--format=a4`).
18. Report: PDF path, page count (target: 2 pages), keyword coverage %.

## Structural differences vs. `cv-template.html`

- **Header** is a rounded blue box: name + subtitle (headline/qualification),
  a 2×2 contact grid with icons, and the profile photo on the right.
- **Profil & Kompetenzen are one section**: summary paragraph immediately
  followed by centered competency pills (no separate "Core Competencies"
  heading).
- **No separate "Projects" section.** Proof points live inside the relevant
  role's bullets. (If you want a projects block, add it under Berufserfahrung —
  but the reference format folds them in.)
- **Skills (Kenntnisse) is a two-column label/value table** — one row per
  category (e.g. Methoden, IT Werkzeuge, Sektoren, Sprachen).
- Section order: Profil & Kompetenzen → Berufserfahrung → Kenntnisse →
  Ausbildung → Zertifizierungen.

## Profile photo (required for this template)

This layout always shows a photo. Set `candidate.photo` in
`config/profile.yml` to a local image path (a repo ships `config/profile-photo.jpg`).
When filling `{{PHOTO_SRC}}`, **read the file and inline it as a base64
`data:` URL** — the renderer writes a temp HTML into `output/`, so a bare
relative path would resolve against the wrong directory; a `data:` URL is
render-location independent and always works:

```
{{PHOTO_SRC}} → data:image/jpeg;base64,<base64 of config/profile-photo.jpg>
```

A square headshot (≈300×300) crops cleanly into the 104×118 frame.

## Placeholder table

| Placeholder | Content |
|-------------|---------|
| `{{LANG}}` | CV language code (`de`, `en`, …). Drives RTL/CJK fallbacks. |
| `{{PAGE_WIDTH}}` | `210mm` (A4) or `8.5in` (letter) |
| `{{NAME}}` | Full name (from `profile.yml`) |
| `{{SUBTITLE}}` | Qualification / headline line, e.g. "Wirtschaftsingenieur Maschinenbau (Dipl.-Ing.)" |
| `{{PHONE}}` | Display phone; `{{PHONE_TEL}}` = digits only for the `tel:` link |
| `{{EMAIL}}` | Email |
| `{{LOCATION}}` | e.g. "D-20249 Hamburg" |
| `{{LINKEDIN_URL}}` / `{{LINKEDIN_DISPLAY}}` | LinkedIn href / display text |
| `{{PHOTO_SRC}}` | base64 `data:` URL of the photo (see above) |
| `{{SECTION_SUMMARY}}` | Section label, e.g. "Profil & Kompetenzen" |
| `{{SUMMARY_TEXT}}` | Personalized, keyword-dense summary |
| `{{COMPETENCIES}}` | `<span class="competency-tag">…</span>` × 6–8 |
| `{{SECTION_EXPERIENCE}}` | e.g. "Berufserfahrung" |
| `{{EXPERIENCE}}` | One `.job` block per role (see snippet) |
| `{{SECTION_SKILLS}}` | e.g. "Kenntnisse" |
| `{{SKILLS_ROWS}}` | One `.skill-row` per category (see snippet) |
| `{{SECTION_EDUCATION}}` | e.g. "Ausbildung" |
| `{{EDUCATION}}` | `.edu-item` blocks |
| `{{SECTION_CERTIFICATIONS}}` | e.g. "Zertifizierungen" |
| `{{CERTIFICATIONS}}` | `.cert-item` rows |

### `{{EXPERIENCE}}` — one block per role

```html
<div class="job">
  <div class="job-header">
    <span class="job-company">Simpla Solutions GmbH <span class="role">— Gründer &amp; Geschäftsführer (Unternehmensberatung)</span></span>
    <span class="job-period">Hamburg | Jan 2019 – heute</span>
  </div>
  <ul>
    <li><span class="p">Kunde/Kontext – Thema:</span> Ergebnis-orientierte Beschreibung. <span class="dur">(19 Monate)</span></li>
  </ul>
</div>
```

### `{{SKILLS_ROWS}}` — one row per category

```html
<div class="skill-row"><div class="skill-label">Methoden</div><div class="skill-val">A | B | C</div></div>
```

### `{{CERTIFICATIONS}}` — one row per cert

```html
<div class="cert-item"><span class="cert-title">Professional Scrum Master I (PSM I)</span><span class="cert-org">— scrum.org</span><span class="cert-year">2018</span></div>
```

## Styled cover letter (companion to the styled CV)

`templates/cover-letter-template-styled.html` renders a cover letter in the
same design language as `cv-template-styled.html` (navy name header, rounded
8mm light-blue boxes, Calibri, blue accents) so CV + letter form a matching
set. Fully filled reference: `templates/cover-letter-styled.example.html`
(the real VaroPreem letter) — use it as ground truth for the markup.

Layout: name + headline on white → rounded recipient box (address left,
`{{DATELINE}}` right; `{{GREETING_BLOCK}}` left, `Re: {{ROLE_TITLE}}` right)
→ justified body → `ul.achievements` bullets with `<span class="lead">`
navy lead-ins → closing/signature → fixed footer bar with the CV contact
icons (repeats on every page).

Placeholders: `{{LANG}}`, `{{PAGE_WIDTH}}`, `{{NAME}}`, `{{TITLE}}`,
`{{RECIPIENT_BLOCK}}` (newline-separated address; bold lines wrapped in
`<span class="addr-bold">`), `{{DATELINE}}`, `{{GREETING_BLOCK}}`,
`{{ROLE_TITLE}}`, `{{OPENING}}`, `{{PROFILE_INTRO}}`,
`{{ACHIEVEMENTS_BLOCK}}`, `{{PROBLEMS_BLOCK}}`,
`{{LANGUAGE_CLOSING_BLOCK}}`, `{{CLOSING_BLOCK}}` (sign-off + optional
`<img class="signature-img" src="../config/signature.png">` + typed name),
`{{FOOTNOTES_BLOCK}}`, and CV-style contact placeholders `{{PHONE}}`,
`{{EMAIL}}`, `{{LOCATION}}`, `{{LINKEDIN_URL}}`, `{{LINKEDIN_DISPLAY}}`
(replacing `{{CONTACT_LINE}}`/`{{CREDENTIALS_BLOCK}}` of the plain
`cover-letter-template.html`). Unused optional blocks: remove the line.
Target: one page. Assets: `config/signature.png` (opt-in, like the photo).

## ATS notes

The template keeps single-column flow, standard section labels, and
selectable UTF-8 text. The only ATS caveat is the photo — acceptable and
expected in DACH, but the reason this template is region-scoped. Everything
else follows the ATS rules in `modes/pdf.md`.
