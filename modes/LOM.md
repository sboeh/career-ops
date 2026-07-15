# Mode: LOM — Letter of Motivation / Anschreiben Generator

## Purpose

Generates a tailored Letter of Motivation (English) or Anschreiben (German) from an existing evaluation report.
The report already contains match analysis, personalization hooks, and interview stories — this mode assembles them into a finished letter.
Output is a `.md` file for review; no submission happens without the user's explicit approval.

## Trigger

User says "write a cover letter", "Anschreiben schreiben", "draft my LOM", or references a report file path.

## Pipeline

1. Identify the source report
   - If the user supplies a path (e.g. `reports/026-...md`) → use it directly
   - If no path is given → ask: "Which report should I base the letter on? Paste the path or company name."
2. Read the report — extract the following fields:
   - **Header:** company slug, role title, contact name, URL, date, language (DE/EN from report prose)
   - **A) Rollenprofil / Role Profile:** domain, function, seniority, remote flag, duration
   - **B) Match mit CV / Match:** top 3 matched requirements (strongest signals only)
   - **C) Niveau und Strategie / Strategy:** the _"vender senior sin mentir"_ narrative quote — use it as the letter's central claim
   - **E) Plan de Personalización:** top 2–3 CV/framing changes — these are the proof points to foreground
   - **F) Interviewvorbereitung / Interview Prep:** Story #1 (strongest story) → condense to one concrete result sentence
   - **G) Legitimacy / Legitimität:** if `Ghost Posting` or `Do Not Apply` → STOP and warn the user before writing
3. Read `cv.md` — confirm proof point metrics are accurate (NEVER invent numbers)
4. Read `config/profile.yml` → `name`, `email`, `location`, `phone` (omit phone if absent)
5. Read `modes/_profile.md` → tone, narrative voice, deal-breakers (use to filter what NOT to say)
6. Detect letter language:
   - Report prose in German → write in German (formal Sie)
   - Report prose in English → write in English
   - User explicitly requests a language → override detection
7. Detect format:
   - German Anschreiben → DIN 5008-inspired block layout, max 1 page (~300–350 words body)
   - English cover letter → 3-paragraph format, max 1 page (~300–350 words body)
8. Compose letter (see structure below)
9. Build output path: `output/lom-{candidate}-{company}-{YYYY-MM-DD}.md`
10. Write file, report path and word count to user

## Letter Structure — German Anschreiben

```
{Sender block: Name, Address, Email, Phone}

{Date: City, DD. Month YYYY}

{Recipient block: Contact Name if known, Company if known, "Bewerbung über freelancermap" or platform}

Betreff: Bewerbung als {Role Title} — {Company or Projekt-ID if company unknown}

Sehr geehrte/r {Herr/Frau LastName | "Damen und Herren" if no contact},

[Paragraph 1 — Hook, max 3 sentences]
Why this role, right now. Lead with the strongest qualifier from Block C strategy quote.
No generic opener ("Hiermit bewerbe ich mich..."). Open with a concrete claim or result.

[Paragraph 2 — Proof, max 4–5 sentences]
Translate the top 2 Block E personalization items into prose.
Use one concrete metric from Block F Story #1 (one sentence, attributed to a real engagement).
Connect your background directly to the JD's core requirement (the one that appeared in Block B as "✅ Kern-Qualifier").

[Paragraph 3 — Fit and availability, max 3 sentences]
Remote / availability signal (if relevant — copy from Block A).
Rate: omit unless the report explicitly says to name a range; if so, use the exact range from Block D.
Close with a concrete next-step invitation, not a generic "I look forward to hearing from you."

Mit freundlichen Grüßen,

{Name}
{Email} | {Phone if available}
```

## Letter Structure — English Cover Letter

```
{Name}
{Email} | {Location} | {Phone if available}
{Date: Month DD, YYYY}

{Contact Name if known}
{Company if known}
Re: Application for {Role Title}

Dear {Mr./Ms. LastName | "Hiring Team" if no contact},

[Paragraph 1 — Hook, max 3 sentences]
Open with the central claim from Block C. Why you, for this role, now.
No "I am writing to apply for..." opener.

[Paragraph 2 — Proof, max 4–5 sentences]
Top 2 personalization items from Block E, rendered as prose achievements.
One concrete metric from Block F Story #1.
Direct link to the JD's hard requirement (Block B Kern-Qualifier).

[Paragraph 3 — Logistics + close, max 3 sentences]
Availability and remote status (from Block A if relevant).
Rate only if report says to; use Block D range exactly.
Concrete close: propose a call or brief intro meeting.

Sincerely,
{Name}
```

## Tone Rules

- **Never generic:** every sentence must be traceable to either the JD or the CV
- **No throat-clearing:** eliminate "I would like to", "I believe I am", "I am excited to"
- **Metrics over adjectives:** "€630M programme" beats "large-scale project"
- **One central claim:** the letter proves one thesis (the Block C strategy quote), not a list of traits
- **Mirror the JD's language:** use the exact terms from Block B match column (e.g. "Big4-Methodik", "Commercial Excellence") — these are ATS signals
- **Formal register (DE):** always `Sie`, never `du`; use `Sehr geehrte/r` not `Hallo`

## Hard Rules

- **NEVER submit or send.** Write and save the file. Stop. Tell the user to review it before sending.
- **NEVER invent metrics.** All numbers must appear in `cv.md` or the report's Block B/F.
- **NEVER write a letter for a Ghost Posting** (Block G = "Ghost Posting" or "Do Not Apply"). Warn the user instead.
- **NEVER exceed 1 page** (~350 words body max). If the draft is longer, cut Paragraph 2 first.
- **NEVER include a subject line in the English variant** — only in the German Anschreiben (Betreff).

## Rate / Compensation Handling

| Report Block D says | Action |
|---------------------|--------|
| "Rate not mentioned — omit" | Omit from letter |
| "Name range in letter" | Use exact range from Block D |
| "Discuss in conversation" | Add one sentence: "Meinen Tagessatz bespreche ich gerne im Gespräch." |

## Contact Block Handling

- Contact name in report (Block A `Kontakt`) → use in salutation
- No contact name → "Sehr geehrte Damen und Herren" / "Dear Hiring Team"
- Company name unknown (anonymous posting) → use platform + Projekt-ID in Betreff/Re line:
  `Bewerbung als {Role} — Projekt-ID {ID} via {Platform}`

## Post-Generation Report

```
Letter saved: output/lom-{candidate}-{company}-{date}.md
Language: {DE|EN}
Word count: {n} words
Source report: {report path}

STOP — review the file before sending. No application has been submitted.

Suggested next steps:
  1. Edit output/lom-{candidate}-{company}-{date}.md if needed
  2. Run /career-ops pdf to generate the tailored CV (if not done yet)
  3. Open {URL from report} and apply manually — paste or attach the letter
```

Do NOT update the applications tracker. The user updates status manually after submitting.
