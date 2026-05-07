# Prompt — Build a voter's brief for any UK council ward

Paste the block below into a fresh Claude Code session (or Claude.ai / ChatGPT chat with web browsing enabled) running in an empty or near-empty folder. The prompt is self-contained and asks you for a ward or postcode before doing anything else.

---

> **Note on the tooling.** This prompt was authored for Claude Code with Playwright MCP and Bash tools, where the agent has filesystem access and can drive a real browser. **It also runs in any other capable AI agent (Claude.ai, ChatGPT, OpenAI Codex CLI, etc.).** When running in a different tool, substitute equivalent capabilities: use whatever web browsing tool you have for fetches, your code-execution sandbox for PDF text extraction, and your filesystem if available (or in-chat output of the final files if not). The source-hierarchy and anti-hallucination rules below apply regardless of which tool is running you.

You are building a non-partisan, deeply-researched, single-page voter's brief for a UK local council election ward, paired with long-form markdown research files.

The repository at the current working directory already contains a finished example for **Bunhill ward, Islington (May 2026)**. Use it as the **canonical design and structure reference**:

- `index.html` — the single-page output. Match its civic-broadsheet aesthetic exactly: variable Fraunces display, Newsreader body, JetBrains Mono for labels; warm newsprint paper background `#f1ebde`; election-red accent `#d63d2c`; SVG-noise paper grain; ballot-card candidate UI with X-mark checkbox; examiner's-report scorecard; sticky § table of contents; staggered entrance reveals; `prefers-reduced-motion` respected.
- `research/` — the long-form markdown structure to mirror:
  - `research/README.md` (index + TL;DR)
  - `research/parties/*.md` (one per party fielding candidates)
  - `research/candidates/<ward>.md` (every ballot candidate)
  - `research/scorecard.md` (incumbent administration's last manifesto vs delivery)
  - `research/sources.md`
- `PROMPT.md` — this file (don't overwrite it).

## Step 1 — Ask first, research second

Before anything else, ask the user:
1. **Which ward or postcode are you voting in?** (If they give a postcode, use the council's official postcode→ward lookup or the Electoral Commission's "Who can I vote for" service to resolve the ward, then confirm with the user before proceeding.)
2. **Anything that should override the standard scope?** (Default is: same neutral, all-parties, all-candidates approach as Bunhill. Optional override only.)

Do NOT begin research until the ward is confirmed.

After the user answers, immediately re-read `index.html` and 2–3 files inside `research/` to reload the exact structure and tone you must match.

## Step 2 — Anti-hallucination protocol (mandatory)

This is the most important section. Hallucinating a candidate name, ward boundary, manifesto pledge, or council-record claim is a critical failure. Apply these rules without exception.

### Source hierarchy (always prefer the higher tier)
1. **Tier 1 — the council's official Statement of Persons Nominated PDF for that specific ward.** This is the *only* authoritative candidate list. Fetch it via the council elections page, download the PDF, and run `pdftotext` on it. Every candidate name, full registered party name, and home-address note in the brief MUST come from this PDF.
2. **Tier 2 — the parties' own primary materials.** Manifesto PDFs (extract via `pdftotext`), candidate pages on the party's official site, official press releases. Quote with exact attribution.
3. **Tier 3 — local press of record** (e.g. *Islington Tribune*, *Islington Gazette*, *Camden New Journal*) and the Electoral Commission / Democracy Club / whocanivotefor.co.uk.
4. **Tier 4 — Wikipedia, polling sites, blogs.** Useful for orientation only; never the sole source for a load-bearing claim.

### Hard rules
- **Never invent.** If a candidate has no public profile, write *"No public profile located in this research."* Do not infer or pad.
- **Never extrapolate.** If a party hasn't published a local manifesto, say so explicitly. Do not transcribe national policy as if it were local commitment.
- **Always run `pdftotext` on PDFs** rather than reading them as binary or letting WebFetch summarise them — you'll lose specifics otherwise. Fetch PDFs with `curl -sL -A 'Mozilla/5.0' <url> -o /tmp/<name>.pdf` then `pdftotext /tmp/<name>.pdf -`.
- **For sites that 403 to WebFetch**, use the chrome MCP browser tools (`mcp__chrome__browser_navigate` + `mcp__chrome__browser_evaluate`) and pull text via `document.body.innerText`.
- **Cross-check every numerical claim against at least two sources** before including it (e.g. a manifesto figure plus a press repeat, or a council document plus a press report).
- **Date-stamp anything time-sensitive.** Defection dates, peerage dates, council leadership changes, ombudsman ruling dates — write the absolute date, not "recently" or "this year".
- **Note dissent.** If parties contest a fact (e.g. "Conservatives say £89m on RTB buyback; Labour says £58m new spend"), present both attributed.
- **Treat your own memory as untrusted.** If you "know" a councillor or a manifesto pledge from training data, verify it against Tier 1–3 sources before writing it down. If it can't be verified live, drop it.
- **Verification log.** As you go, keep a running list (in a TodoWrite task or a scratch markdown file) of every claim you've put on the page that hasn't yet been re-checked against a primary source. Don't write the HTML until every load-bearing claim has been verified at least once.

### What absolutely must come from the official Statement of Persons Nominated
- The complete candidate roster (full names, surname capitalisation, ballot order)
- The full registered party description (e.g. "Islington Community Independents - for the Many", "Green Party first choice candidate")
- Each candidate's listed home address area (this often reveals out-of-ward candidates — a meaningful fact)
- Polling station addresses and register ranges
- Polling date and hours
- Number of seats up for election
- The Returning Officer name and dating of the notice

If you cannot find this PDF, halt and tell the user. Do not proceed from secondary sources.

## Step 3 — Research scope

For the chosen ward, deliver:

1. **Every candidate on the ballot** — name as printed, party as registered, what's publicly known about them (proposers/seconders from the Statement, party bio if any, sitting-councillor record from the council democracy site if applicable).
2. **Every party fielding a candidate** — their full local manifesto / pledges document with specifics, headline numbers, themes; the party leader / spokesperson; recent campaign positions from local press.
3. **Council scorecard** — identify the controlling group, find their last manifesto (search the party site, Wayback Machine if needed), score delivery against it using:
   - Council annual reports and budget papers
   - Housing Ombudsman / Local Government & Social Care Ombudsman rulings
   - Audit reports
   - Local press coverage
   - Their own current manifesto's restated vs new pledges (re-pledges often reveal what fell short)
   Use ✅ / 🟡 / 🔴 / ⬜ honestly. Cite the evidence per row.
4. **Polling context** — most-recent borough projection (PollCheck or equivalent), last election result for the ward, current sitting councillors and any defections since.
5. **Polling stations** — addresses, register ranges, hours, ID rules.

## Step 4 — Output

Mirror the existing structure exactly. Files to (over)write:

- `index.html` — single page; same aesthetic; same sections in the same order:
  1. Masthead with ward name, dateline, polling-day bulletin
  2. § I Overview — three big stats and an at-stake pull-quote
  3. § II The candidates — alphabetical ballot order, ballot cards with party colour, X-mark hover, surname kicker, party badge, special badges (incumbent / first-choice / out-of-ward / etc.)
  4. § III The parties — accordion deep-dives, headline pledges with bolded numbers
  5. § IV Council scorecard — examiner's report card with a one-paragraph summary
  6. § V Polling — layered 2022-vs-projection bars, central seat estimate
  7. § VI Polling stations — Fraunces italic ① ② ③ numerals, mono address blocks
  8. § VII Sources — footnoted §-marker list
  9. Imprint footer
- `research/README.md` updated for the new ward.
- `research/parties/<party>.md` for every party (replace existing if recycling, or add new).
- `research/candidates/<ward>.md`.
- `research/scorecard.md` updated for the new controlling group and election cycle.
- `research/sources.md` updated.

## Step 5 — What NOT to do

- **Do not** build the personalised questionnaire / who-to-vote-for selector. That's a follow-up task explicitly deferred until the user asks for it. Stop after the brief is complete.
- **Do not** insert political slant. Same template, same depth, same scrutiny for every party. Where information is missing, say so plainly.
- **Do not** delete `PROMPT.md`.
- **Do not** rely on prior session memory or training-data recall for any factual claim.
- **Do not** publish before re-running the verification log to zero unchecked claims.

## Step 6 — Definition of done

- The Statement of Persons Nominated PDF for the ward has been downloaded, text-extracted, and every name on the page traces back to it.
- Every party manifesto referenced has been opened (PDF text extracted, or page DOM read via the chrome MCP) and quotes are exact.
- Every numerical scorecard claim has at least one cited source.
- The HTML renders cleanly via `python3 -m http.server` (start it on a free port, smoke-test with chrome MCP, take a screenshot of each major section to confirm layout integrity).
- The verification log is empty (zero unchecked claims).
- The user has been shown a concise summary of: who's standing, what each party promises, the incumbent's grade, and known caveats / data gaps.

Tell the user when you're done, what you couldn't verify, and how to stop the local web server.

---

End of prompt. Ask the ward / postcode question now.
