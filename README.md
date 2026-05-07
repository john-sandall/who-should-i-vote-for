# Who Should I Vote For?

A pair of prompts that turn an empty folder into a serious, sourced voter's brief for any UK council ward, and then into a personalised "which one to vote for" recommendation, all from your laptop.

Built for myself on the morning of the 7 May 2026 Islington local elections, after realising I had no idea who 14 of the 16 names on my ballot paper were. Sharing in case anyone else's local turnout looks anything like ours did last time (30%).

> **Note on accuracy:** these prompts research live council records, party manifestos, the Statement of Persons Nominated PDF, and local press. They were designed with strict anti-hallucination rules. They are not infallible. Treat the output as a strong starting point, then verify anything that's about to change your vote.

---

## What's in here

| File / folder | What it is |
|---|---|
| `index.html` | The finished example: a single-page voter's brief for **Bunhill ward, Islington** (May 2026). Open it in any browser. |
| `research/` | The long-form sourced research that the brief is built from. Per-party deep dives, candidate notes, council scorecard, all sources. |
| `PROMPT.md` | **Prompt #1.** Tells an AI agent how to research and build a brief like this for any other UK ward. |
| `RECOMMEND.md` | **Prompt #2.** Runs an interactive policy questionnaire in chat (no party names visible until the end) and writes a one-page personalised recommendation. |

---

## What it actually produces

**The brief** is a self-contained HTML page styled like a civic broadsheet: every candidate listed in ballot order with what's publicly known about them, every party's pledges side-by-side, a graded report card on the incumbent council's last manifesto vs delivery, vote-share polling, and where to actually go and vote. It's neutral. Every party gets the same template.

**The recommendation** is a short markdown file with your top one-to-three picks, why they fit your stated priorities, and what to ignore. It runs as a 10-question chat: which areas you care about, which specific policies you'd back (in plain English, no party labels), and a tactical question about whether you're voting on local fit alone or want to send a national signal too.

---

## How to use it

You'll need [Claude Code](https://docs.claude.com/claude-code) (Anthropic's terminal-based AI coding tool) and an Anthropic account.

### 1. Get Claude Code

Quickest path:

```bash
# macOS / Linux (with Homebrew)
brew install --cask claude-code

# or via npm
npm install -g @anthropic-ai/claude-code
```

Then run `claude` once to sign in.

If those don't work for you, the [official install docs](https://docs.claude.com/en/docs/claude-code/setup) cover Windows and other paths.

### 2. Clone or download this repo

```bash
git clone https://github.com/john-sandall/who-should-i-vote-for.git
cd who-should-i-vote-for
```

(Or click the green "Code" button on GitHub and download as zip, then unzip.)

### 3. Build a brief for your ward

In your terminal, inside the repo folder, run:

```bash
claude
```

When the prompt appears, paste the contents of `PROMPT.md` (everything between the `---` rules). The agent will ask you for your ward or postcode, then go and do the research. Expect it to take 5–15 minutes depending on how many parties are standing.

When it's done you'll have a fresh `index.html` (open it in a browser) and a new `research/` folder of sourced markdown.

### 4. Get a personalised recommendation

Once the brief is built, paste the contents of `RECOMMEND.md` into the same Claude Code session. It runs an adaptive questionnaire (around 10 questions across 2–3 rounds) using multiple-choice answers, then writes your recommendation to `recommendations/<yourname>.md`.

Each person can run this without overwriting anyone else's recommendation.

---

## Less technical? Read this.

If the terminal stuff sounds intimidating, here's the short version of what's happening.

Claude Code is a tool that lets an AI agent read files, browse the web, and write files on your computer. It runs in a terminal because that gives it permission to do those things. You don't have to be a developer to use it. You sign in once, paste a long prompt, and watch it work.

The two prompts in this repo are long because they spell out, very carefully, exactly how the agent should research things and what it must not invent. That's deliberate. AI models will happily fabricate councillor names and manifesto pledges if you let them. The whole point of these prompts is to stop that from happening.

If you want to try this and you're stuck at any step, open an issue on the repo and I'll help.

---

## Why I built it this way

Three principles drove the design.

**Anti-hallucination first.** Every candidate name and party description has to trace back to the council's official Statement of Persons Nominated PDF. Every numerical claim gets cross-checked against at least two sources before it's written down. Where information is genuinely missing for a smaller party, the brief says so explicitly rather than papering over the gap. This is not optional in the prompts.

**Same template for every party.** No party gets more space, more flattering framing, or more scrutiny than another. If a smaller party hasn't published a local manifesto, that's stated. If a controlling group has a Housing Ombudsman special report against them, that's stated too. The reader does the weighing.

**The questionnaire hides party labels.** Round one and two of the recommender ask about specific policies in plain English. You only see which party owns which position after your answers are locked in. The result is sometimes the party you'd usually pick. Sometimes it isn't.

---

## Limitations and caveats

- **Built for UK council elections.** The structure of the prompts (Statement of Persons Nominated, ward boundaries, council scorecard) is specifically British. Adapting them for other countries would need new source patterns.
- **A pair of researcher's eyes is still useful.** I caught several small errors during my own verification pass that the agent had introduced from secondary sources. The prompts now warn against those specifically, but verification before voting still matters.
- **Smaller-party transparency varies.** Some independents and minor parties don't publish much. The brief reflects that honestly rather than padding.
- **The polling projections are someone else's estimates** (PollCheck), not original analysis. Treat as one signal among several.

---

## Licence

MIT. Use, fork, adapt for your own area or election. If you build something on top of this I'd genuinely love to know.

— John Sandall
