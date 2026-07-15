# Mode: md — CV Markdown Draft Generator

## Purpose

Generates a tailored CV as a Markdown file for review and editing **before** PDF production.
Same tailoring pipeline as `pdf` mode — outputs a structured `.md` file instead of HTML/PDF.
The user edits the draft, then runs `/career-ops pdf <path>` to produce the final PDF.

## Pipeline

1. Read `cv.md` as source of truth
2. Ask for JD if not in context (text or URL)
3. Extract 15–20 keywords from JD
4. Detect JD language → CV language (EN default)
5. Detect company location → paper format (`letter` US/Canada, `a4` elsewhere) — store in frontmatter
6. Detect role archetype → adapt framing
7. Rewrite Professional Summary with JD keywords + exit narrative bridge
8. Select top 3–4 projects most relevant to the offer
9. Reorder experience bullets by JD relevance
10. Build competency grid from JD requirements (6–8 keyword phrases)
11. Inject keywords naturally into existing achievements (NEVER invent)
12. Read `name` from `config/profile.yml` → normalize to kebab-case lowercase → `{candidate}`
13. Determine `{company}` slug from JD
14. Build output path: `output/cv-{candidate}-{company}-{YYYY-MM-DD}.md`
15. Generate Markdown from `templates/cv-template.md` — replace all `{{PLACEHOLDER}}` tokens
16. Write file and report path, keyword coverage %, next step

## Frontmatter (required)

Every generated draft MUST start with YAML frontmatter.
The `pdf` mode reads this when converting an existing draft to PDF.

```yaml
---
candidate: {kebab-slug}
company: {company-slug}
role: {job-title}
date: {YYYY-MM-DD}
lang: {en|de|fr|es|ja}
format: {letter|a4}
keywords_matched: {n}/{total}
source_jd: {URL or "pasted"}
---
```

## Content Format per Placeholder

### `{{PHONE}}`
Omit this token and its surrounding ` | ` separators if `profile.yml` has no phone value.

### `{{COMPETENCIES}}`
Backtick-enclosed tags separated by ` · ` on one or two lines:
```
`Keyword 1` · `Keyword 2` · `Keyword 3` · `Keyword 4` · `Keyword 5` · `Keyword 6`
```

### `{{EXPERIENCE}}`
Each top-level role:
```markdown
### Company Name — Role Title
*Mon YYYY – Mon YYYY | City, Country*

Optional one-line context sentence.

- **Key achievement** with quantified result
- Supporting bullet
- Supporting bullet
```

Sub-roles (consulting / interim positions within one employer block):
```markdown
### Company Name — Managing Director / Senior Consultant
*Mon YYYY – present | City*

**Sub-role: Client Name** *(Mon YYYY – Mon YYYY, N months, City)*
- Bullet
- Bullet
*Tools: Tool1, Tool2 | Sector: Industry*
```

### `{{PROJECTS}}`
```markdown
**Project Name** *(YYYY–YYYY)*
Description of project and impact in 1–2 lines.
*Tech: Item1 · Item2 · Item3*
```

### `{{EDUCATION}}`
```markdown
**Degree** — Institution | YYYY–YYYY
Focus areas or thesis note
```

### `{{CERTIFICATIONS}}`
```markdown
- **Cert Name** — Issuer | YYYY
```

### `{{SKILLS}}`
```markdown
**Category:** Item1 · Item2 · Item3

**Category 2:** Item1 · Item2
```

## ATS Rules (same as pdf mode)

- Use standard English section labels in `{{SECTION_*}}` placeholders:
  "Professional Summary", "Core Competencies", "Work Experience", "Projects", "Education", "Certifications", "Skills"
  (or the correct language equivalent when `lang` ≠ `en`)
- Keywords distributed: Summary (top 5), first bullet of each role, Skills section
- No critical info hidden inside decorative backticks

## Post-generation

Report to user:
```
Draft saved: output/cv-{candidate}-{company}-{date}.md
Keywords matched: {n}/{total} ({%})

Review and edit the file, then run:
  /career-ops pdf output/cv-{candidate}-{company}-{date}.md
to generate the final PDF.
```

Do NOT update the applications tracker — the tracker PDF column is updated only after the PDF is confirmed.
