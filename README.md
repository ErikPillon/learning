# learning

Rust (new) and Julia (refresher), 12 weeks, six ~30-minute sessions a week,
with an LLM acting as tutor. **All exercise code in this repo is written by
hand by the learner** — the tutor assigns, hints and reviews, and only helps
*design* the tracker.

Start date: 2026-10-05 · 0 of 72 sessions logged.

## Layout

```
rust/exercism/     Exercism Rust track solutions (Wed/Thu)
rust/leetcode/     LeetCode, Rust only (week 12)
julia/euler/       Project Euler: p001.jl, p002.jl, ... (Fri/weekend)
tracker/           Rust CLI, built in weeks 9-11
notes/             one markdown file per concept
site/              static site, output of `tracker build`
log.toml           single source of truth for all stats
LEARNING_PROJECT.md  the full plan and the tutor's briefing
SETUP.md           what is installed, what is missing, what is unverified
WORKFLOW.md        how to run a session from the terminal, start to commit
DEPLOY.md          how site/ reaches Vercel
tracker/DESIGN.md  architecture of the CLI, written before the code
```

Rustlings lives outside this repo; only completed topics get logged.

## Daily loop

Full version, with the commands, in [WORKFLOW.md](WORKFLOW.md).

1. Solve. Make the tests or the answer pass.
2. Rust days: `cargo fmt` && `cargo clippy`, fix the warnings.
3. Append a `[[solve]]` entry to `log.toml`, add a few lines in `notes/`.
4. `git commit -m "<platform> <problem> (<language>): <concept>"` and push.

## Stats

Stats come only from this repo — `log.toml`, `notes/`, git history. Nothing is
scraped from Exercism, Project Euler or LeetCode. Until `tracker stats` exists
(week 10), count by hand or read `log.toml`.

## Talking to the tutor

Point the tutor at `LEARNING_PROJECT.md` and `log.toml`, then use:
`Tasks for week N` · `Task for today` · `Review: <problem>` + code ·
`Hint level 1/2/3 for <problem>` · `Explain <concept>` · `Retro`.
