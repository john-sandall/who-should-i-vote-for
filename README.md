# Who Should I Vote For?

A pair of prompts that turn an empty folder into a sourced, neutral voter's brief for any UK council ward, plus a personalised recommendation of which candidate on your ballot best fits your own policy preferences.

Built for myself on the morning of the 7 May 2026 Islington local elections, after realising I had no idea who 14 of the 16 names on my ballot paper were.

> ⚠️ **Note on accuracy.** These prompts research live council records, party manifestos, the official Statement of Persons Nominated PDF, and local press. They were designed with strict anti-hallucination rules. They are not infallible. Treat the output as a strong starting point, then verify anything that's about to change your vote.

---

## Non-partisan by design. Here's the actual guarantee.

This isn't vibe voting. It's the opposite.

- **Not affiliated with, endorsed by, or funded by any political party.**
- **Same template for every party.** Same depth. Same scrutiny. No party gets more flattering framing than another.
- **Anti-hallucination is the first rule of the prompt.** Every candidate name and party description has to trace back to the council's official Statement of Persons Nominated PDF. Every numerical claim gets cross-checked against at least two sources. Where information is genuinely missing for a smaller party, the brief says so.
- **The recommender hides every party label** during questioning. You answer plain-English policy questions and only see who said what after your answers are locked in. People are sometimes surprised by the result.
- **Your tactical preference is honoured.** If you'd rather vote on a national signal than on local fit, you say so up front and the recommendation reflects that genuinely.

The goal: help you vote on what actually matters to you for local decision-making, not by reflex along national party lines. It might end up being the same answer. It might not.

---

## Try it in 60 seconds (no install required)

Easiest path for non-technical users. Works in any decent AI chat tool.

1. Open [Claude.ai](https://claude.ai) or [ChatGPT](https://chatgpt.com). Free tiers work, but a paid account with web browsing gives noticeably better results.
2. Open the prompt you want, copy the contents:
   - 👉 [**PROMPT.md**](PROMPT.md) builds the brief for any UK ward
   - 👉 [**RECOMMEND.md**](RECOMMEND.md) runs the policy questionnaire and writes a short personalised recommendation
3. Paste it into the chat. The agent will ask you for your ward or postcode (or your priorities, in the recommender's case) and take it from there.

When the brief finishes, the agent will hand you the HTML in chat. Copy it into a file called `index.html` on your computer and double-click to open it in a browser. The recommendation comes back as a short markdown summary you can read straight in the chat.

> 💡 **For best results in the chat path,** turn web browsing on in the model's settings. Without it, the agent has to fall back on its training-data knowledge for council records and that's where hallucinations creep in.

### Want to share your brief with friends? Use Netlify Drop.

Once you have `index.html` on your computer, the fastest way to put it on the public internet is [Netlify Drop](https://app.netlify.com/drop). Literally drag the file onto the page in your browser and you get an instant URL like `https://breezy-otter-1234.netlify.app/` to share with anyone.

- **No account needed for a quick test**, but anonymous sites are deleted after 24 hours. Sign up (free) before dragging if you want it to persist and to be able to update it later by dropping new versions in.
- Drag the *whole folder* (the one containing `index.html` plus the `research/` subfolder) if you want the linked markdown research files to be reachable too. Drag just `index.html` if you don't.
- Free SSL, free custom subdomain. Good enough for sharing in a group chat or in a comment.

---

## Best results: Claude Code CLI + Playwright MCP

If you're comfortable in a terminal, this is meaningfully better. The agent can fetch council PDFs directly, drive a real browser to bypass scraping blocks, run text extraction on PDFs locally, and write the brief and the research markdown straight to your filesystem. Less copy-paste, fewer omissions.

### One-time install

```bash
# Claude Code itself
brew install --cask claude-code        # macOS, Homebrew
# or
npm install -g @anthropic-ai/claude-code  # any platform with Node 18+

# Then sign in
claude
```

[Official install docs](https://docs.claude.com/en/docs/claude-code/setup) cover Windows and other paths.

### Add Playwright MCP (recommended)

Playwright MCP lets the agent drive a real browser. Some council sites block plain HTTP fetches. With this installed, those just work.

```bash
claude mcp add playwright npx @playwright/mcp@latest
```

Verify with `claude mcp list`. When you start the agent, mention "use Playwright MCP" in your first message so it knows to reach for it. ([reference](https://github.com/microsoft/playwright-mcp))

### Then run

```bash
git clone https://github.com/john-sandall/who-should-i-vote-for.git
cd who-should-i-vote-for
claude
```

Paste the contents of `PROMPT.md` (everything between the `---` rules). The agent asks for your ward or postcode and goes to work. Expect 5 to 15 minutes depending on how many parties are standing.

When the brief is done, paste `RECOMMEND.md` into the same Claude Code session for the personalised recommendation. It saves to `recommendations/<yourname>.md` so multiple people in the same household can each get their own without overwriting each other.

### Other CLI agents

The prompts are tool-agnostic. Tested on Claude Code. Should work in OpenAI Codex CLI and similar agentic CLIs that have web browsing and a file system. The only Claude-Code-specific tool reference is in the optional Playwright section above.

---

## What's in this repo

| File / folder | What it is |
|---|---|
| `index.html` | Worked example: a single-page voter's brief for Bunhill ward, Islington (May 2026). Open it in any browser to see what the prompt produces. |
| `research/` | The long-form sourced markdown the brief is built from. Per-party deep dives, candidate notes, council scorecard, all sources. |
| [`PROMPT.md`](PROMPT.md) | Prompt #1. Builds the brief for any UK ward. |
| [`RECOMMEND.md`](RECOMMEND.md) | Prompt #2. Runs the chat questionnaire and writes the recommendation. Requires the brief to exist first. |

---

## What it actually produces

**The brief** is a self-contained HTML page styled like a civic broadsheet: every candidate listed in ballot order, every party's pledges side by side, a graded report card on the incumbent council's last manifesto vs delivery, polling-share projections, and where to actually vote.

**The recommendation** is a short markdown file (under a screen of reading) with your top one-to-three picks, why they fit your stated priorities, and what to ignore. The questionnaire is around 10 multiple-choice questions across two or three rounds. No party labels appear until your answers are locked in.

---

## A bit more on the design

Three principles shaped everything.

**Anti-hallucination first.** The prompts hard-code a four-tier source hierarchy. Tier 1 is the council's official Statement of Persons Nominated PDF, the only authoritative candidate list. Tier 2 is parties' own primary materials. Tier 3 is local press of record. Tier 4 is Wikipedia and aggregators, useful for orientation but never the sole source for a load-bearing claim. The agent maintains a verification log and isn't allowed to publish until every claim has been re-checked against at least one primary source.

**Same template for every party.** No party gets more space, more sympathetic framing, or more scrutiny than another. If a smaller party hasn't published a local manifesto, that's stated. If a controlling group has a Housing Ombudsman special report against them, that's stated too. The reader does the weighing.

**The questionnaire hides party labels.** Rounds 1 and 2 ask only about specific policies in plain English. The party-to-policy mapping is only revealed in the final recommendation. The result is sometimes the party you'd usually pick, sometimes not.

---

## Limitations

- **Built for UK council elections.** The structure (Statement of Persons Nominated, ward boundaries, council scorecard) is specifically British. Adapting for other countries would need new source patterns.
- **Verification by a human pair of eyes is still useful.** I caught small errors during my own pass that the agent had introduced from secondary sources. The prompts now warn against those specifically, but doing a final check before voting still matters.
- **Smaller-party transparency varies.** Some independents and minor parties don't publish much. The brief reflects that honestly rather than padding.
- **The polling projections are someone else's estimates** (PollCheck, in the Bunhill example), not original analysis. Treat them as one signal among several.
- **The chat-tool path is more lossy than the CLI path.** Without local file writing, you'll need to copy and paste the HTML into a file yourself. Without browser tooling, fetches occasionally fail on protected council pages.

---

## Contributing or adapting

Fork freely. If you adapt this for your own city, country, or election type, I'd genuinely love to hear about it. Open an issue with a link.

If you spot a factual error in the Bunhill example or a hole in the prompt's anti-hallucination rules, please open an issue.

---

## Licence

MIT. Use, fork, adapt for your own area or election.

— John Sandall · [@john-sandall](https://github.com/john-sandall)
