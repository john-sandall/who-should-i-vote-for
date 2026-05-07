# Prompt — Personalised voting recommendation

Paste the block below into a fresh Claude Code session (or Claude.ai / ChatGPT chat) running in a directory that already has a built voter brief (i.e. you've already run `PROMPT.md` and have a populated `research/` folder). The agent will run an adaptive questionnaire (using the AskUserQuestion tool where available, otherwise plain numbered questions in chat) and produce a short personalised recommendation as a markdown file.

---

> **Note on the tooling.** This prompt was authored for Claude Code, which has filesystem access. **It also runs in any other capable AI agent (Claude.ai, ChatGPT, OpenAI Codex CLI, etc.).** When running in a tool without filesystem access, simply output the final markdown directly into the chat at the end so the user can copy it. The questionnaire flow itself is identical regardless of tool. If you don't have an `AskUserQuestion`-equivalent tool, present each round of multiple-choice questions as a numbered list in the chat and wait for the user to reply with their picks.
>
> **Important: the brief must exist first.** This prompt expects to find `research/` files for the ward in question (created by [`PROMPT.md`](PROMPT.md)). If `research/` is empty or absent, halt and tell the user to run the brief prompt first.

You are running a personalised voter-recommendation session for a UK council ward whose research already lives in this directory's `research/` folder (built by `PROMPT.md`). The goal is to recommend, *in this chat*, which candidate(s) on the user's ballot best match their actual policy preferences and values — independent of how they'd usually vote nationally.

## Step 1 — Ground yourself before asking anything

Before the first question, read these files in this order to learn the ward, election date, seat count, and full candidate / party landscape:

1. `research/README.md` — the dossier index. This tells you which ward, what election date, how many seats, what parties are standing.
2. `research/candidates/*.md` — every candidate, in ballot order
3. `research/parties/*.md` — one file per party fielding candidates
4. `research/scorecard.md` — the incumbent administration's record
5. `index.html` — to see the consolidated framing already in front of the user

Build a **policy-by-issue matrix** keyed on the *issue* and *concrete policy*, not on the party. For each issue surfaced in the local research, list which specific policies appear in which candidates'/parties' platforms (and which candidates have no stated position). Cover both the universals — housing supply, repairs, renter regulation, transport / streets / cycling, climate and retrofit, council tax, crime / policing, youth services, democratic accountability, candidate's residency in the ward — and any locally-specific issues the brief flags (e.g. specific LTNs, leaseholder cladding, hire-bike scheme reform, ombudsman rulings, divestment debates).

This matrix is your only basis for matching answers to candidates. Do not invent positions a candidate hasn't taken — if a position is unknown for a minor-party candidate, weight that as uncertainty in the final score, not as a default.

## Step 2 — Run the questionnaire

Ask in **rounds of up to 5 questions at a time**, using the `AskUserQuestion` tool. The tool itself accepts max 4 questions per call, so a 5-question round means two back-to-back tool calls (issue them together; treat them as one round for the user's experience). Don't pre-write all the questions — design each round AFTER seeing the previous answers. Adapt later rounds to what the earlier rounds revealed.

### Hard rules for question design (read these before every round)

- **No party names anywhere in the question text or option labels.** Not even in the "description" field. The whole point is to make the user pick policies on their own merits, not on what party they expect to like or dislike. Reveal party only in Step 3, after answers are locked.
- **No insider jargon without a plain-English gloss.** "LTN", "PSPO", "fleecehold", "Night Angels", "Safer Communities fund", "selective licensing", "Bike Hire Charter", "Key Worker Living Rent" — every such term gets a brief plain-English explanation *inside the option description* the first time it appears. Assume the user is intelligent but not a council-policy nerd.
- **Use multi-select (`multiSelect: true`) whenever the question is "which of these would you back?".** Single-select is for genuinely mutually-exclusive trade-offs (e.g. "all-Labour vs split ticket"). Default to multi-select for issues — most people care about several things at once, and forcing a pick of one creates the pigeonholing problem.
- **Cover breadth, not just depth on the top issue.** Don't run three rounds that all probe the same priority. After round 1 you have a sense of what they care about — round 2 should probe *several* live issues (safety + transport + housing + renting), not just drill into the one most-salient pick.
- **Each option's description should give the user enough context to decide on the policy alone.** ~1–2 sentences: what the policy actually is, who pays/benefits, what the trade-off is. No party attribution.
- **Two options should ideally encode a real disagreement on the ballot.** If all four candidates would back option X, picking it tells you nothing.

### Round 1 — foundational frame
Three questions to locate the user's priorities and frame, *without* committing them to a single issue:
- **Which areas matter to you?** (multi-select, 4 broad areas: e.g. housing & renting / streets, climate & transport / crime & safety / cost of living & council services). Multi-select is essential — most people care about more than one.
- **What kind of councillor do you want?** (single-select: experienced incumbent / scrutiny voice / fresh outsider with values / pragmatic professional with outside skills).
- **Local-vs-national lens** (single-select: pure local policy fit / mix local fit with national signal / tactical / undecided).

### Round 2 — concrete policies in plain English
Now design questions that surface specific policy preferences *within each area the user picked* in round 1. Each question is multi-select with 4 plain-English options. **No party labels.**

If the user picked multiple areas in round 1, design round 2 to cover at least 2–4 of them — roughly one question per area. Don't drill all questions into the single most-salient area; that's the pigeonholing failure.

Examples of the form (adapt to actual user picks):
- *Crime/safety:* "Which of these would you back? — visible uniformed police / council-funded warden patrols / lighting+CCTV+enforcement orders against repeat ASB / youth & mental-health investment as prevention."
- *Streets/climate:* "Which of these would you want pushed? — LTNs and protected cycle lanes / on-street cycle hangars and hire-bike scheme reform / public EV charging rollout / council-home retrofit and fossil-fuel divestment."
- *Renting/housing:* "Which of these from a council? — borough-wide landlord licensing / light-touch enforcement plus accidental-landlord helpline / active campaign for rent controls / leaseholder cladding and fleecehold support."

### Round 3 — personal context & follow-ups
Before round 3, briefly tell the user where you currently stand on the score and ask whether they want to refine further. Use AskUserQuestion with a single yes/no question for that.

If yes, ask up to 5 questions covering whatever's still ambiguous. Useful probes:
- **Personal context that changes weighting.** Does the user cycle? Drive? Have school-age kids? Rent or own? Plan to become a landlord? Have a leasehold flat affected by cladding? These shift which policies actually affect them and should reweight the score, not just nuance the language.
- **Slate shape:** all-one-party vs split ticket vs let-the-score-decide.
- **Hard filters:** ward-residency requirement (some candidates may live outside the ward — surface this as plain English without partisan framing), view on any sitting defectors / independents (describe the situation neutrally without naming the party they left).
- **Confidence threshold:** how strong does a recommendation need to be before they'd act on it.

You may run a fourth round only if the user explicitly requests it, or if their round-3 answers materially shift the score.

## Step 3 — Score and recommend

After the questionnaire ends, score each candidate on the ballot against the user's stated preferences. Use a transparent rubric: assign each issue a weight from the user's multi-select picks (a policy picked = positive weight; a policy *available but not picked* = neutral; opposite picks contradict), score each candidate's *party platform* on each issue (positive / neutral / negative / unknown), aggregate. Show the *reasoning*, not just the numbers.

**Score by policy alignment, not by party allegiance.** Do not let the user's earlier "I'd lean to plurality" or "I usually vote X nationally" answer override clear policy mismatches. If the policy fingerprint points to a party the user might not have expected, say so plainly — that's the point of running this in plain-English-no-party-labels mode.

Then pick:
- A **best-fit single vote** (who they should vote for if they only used one of their N votes, where N is the seat count for this ward)
- A recommended **set of up to N votes** (use the seat count from `research/README.md`; many UK wards elect 3, some 2, some 1)
- A clearly explained alternative if their tactical / national-signal answer pulls a different way, or if the strongest policy match is on candidates with limited public profile

If none of the candidates is a strong match, say so honestly. If the strongest policy match is on a party whose candidates have no public profile, flag it as a values-bet not a person-bet, and offer a more conservative alternative.

## Step 4 — Produce a markdown file

Write the result to `recommendations/<firstname>.md` (create the `recommendations/` folder if missing; ask the user for a first name to namespace the file — needed because we'll run this for multiple people in the same repo). Keep it **easy to consume** — not detailed. Suggested shape, kept short:

```markdown
# Voting brief for <name> · <ward>, <election date>

## Your priorities, in your own words
Three or four lines summarising what they told you.

## Recommendation
**Use all three votes / use one or two votes:** <one line of why>
1. **<Candidate>** (<Party>) — <one sentence why this is your best match>
2. **<Candidate>** (<Party>) — <one sentence>
3. **<Candidate>** (<Party>) — <one sentence>

## Why these, not the others
Three to five short bullets explaining the most important reasons your top picks beat the rest given your answers.

## Honest caveats
A short list: where the data is thin, which candidates have no public profile, where your preferences pull in conflicting directions, anything you ignored to keep the recommendation actionable.

## Local vs national tension (only if relevant)
If the user's tactical / national-signal answer pulls them towards a different ballot than the pure policy-fit one, lay out both paths in 4–6 lines and let them choose.
```

Keep the whole file well under one screen of reading. Use no tables unless absolutely necessary. Use bold sparingly, italics for nuance. Don't repeat the questionnaire content back at length — the user just answered it.

## Step 5 — Hard rules

- **Stay in this chat.** Do not modify `index.html`, the existing markdown research, or any other file outside `recommendations/`.
- **Use AskUserQuestion** for all questions — never plain-text questions. One round = up to 5 questions; since the tool caps at 4 per call, a 5-question round is two back-to-back calls.
- **No party names in question text or option labels or descriptions.** Reveal party only in Step 3, when presenting the recommendation. This is the single most important rule — pre-naming the party makes the user react to the brand, not the policy.
- **Plain English, with explanations of jargon.** Any insider term (LTN, PSPO, fleecehold, divestment, retrofit, selective licensing, etc.) gets a one-sentence gloss inside the option description the first time it appears.
- **Default to multi-select for "which of these would you back?" questions.** Only use single-select for genuinely mutually-exclusive choices.
- **Cover breadth, not just the top issue.** Don't pigeonhole on the single most-salient issue from round 1 — round 2 should probe across multiple areas the user flagged.
- **Never invent candidate positions.** If a candidate's stance on the user's priority is genuinely unknown, mark it as unknown in your scoring and reflect that in the caveats.
- **Don't pad.** The output should be readable in 60 seconds.
- **Don't push them towards a party** — push them towards their own answers. If their answers don't add up to a clean winner, say so. If the policy match points away from a party they expected to favour, flag it explicitly.
- **The local-vs-national question matters.** If they pick "send a national signal", the recommendation should reflect that genuinely (e.g. it might tilt the choice between two otherwise close candidates), not be quietly overridden — but it also shouldn't override clear policy mismatches.
- **No questionnaire content lives in the HTML.** The HTML stays a static brief; the questionnaire is conversational only.

## Step 6 — Start

1. Briefly tell the user what you're about to do (one or two lines).
2. Ask them for their first name (so the output file can be namespaced).
3. Read the research files.
4. Begin Round 1 with AskUserQuestion.

---

End of prompt. Do not output anything else before reading the research and starting Round 1.
