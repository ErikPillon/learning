# tracker — design

Design only. Per tutor rule 4, the architecture, data structures and module
layout are collaborative; **the code is written by hand by the learner** in
weeks 9–11. There is intentionally no Rust in this file.

Design principle from the plan: stats come only from the local repo
(`log.toml`, `notes/`, git history). Nothing is scraped.

---

## 1. Module layout

```
tracker/
├── Cargo.toml
└── src/
    ├── main.rs      arg parsing, dispatch, exit codes. No logic.
    ├── log.rs       the Log type, load/save, validation
    ├── stats.rs     pure functions: Log -> Summary. No I/O, no printing.
    ├── streak.rs    date-run arithmetic (own module: it is the fiddly part)
    ├── render.rs    Summary -> HTML string
    └── site.rs      write index.html, copy assets/
```

Two rules that make this testable:

- **`stats.rs` and `streak.rs` do no I/O and no printing.** They take data,
  return data. Everything in them is unit-testable with a hand-built `Log`
  value — which is exactly what week 7 (tests) is for.
- **`render.rs` returns a `String`, it does not write files.** `site.rs` owns
  the filesystem. You can assert on rendered HTML without touching disk.

Build order: `log.rs` → `add` (week 9) → `stats.rs`/`streak.rs` → `stats`
(week 10) → `render.rs`/`site.rs` → `build` (week 11).

## 2. Data structures

### Parsed from `log.toml`

| Type | Field | Shape | Notes |
| --- | --- | --- | --- |
| `Log` | `meta` | `Meta` | |
| | `solve` | sequence of `Solve` | TOML `[[solve]]` maps to a `Vec`; the field name is `solve`, not `solves` |
| `Meta` | `start_date` | date | |
| | `weeks`, `sessions_per_week`, `target_sessions` | integers | |
| `Solve` | `date` | date | TOML local date; `chrono` support is behind a cargo feature — look up which |
| | `platform` | enum: euler, exercism, rustlings, leetcode, project | |
| | `language` | enum: julia, rust | |
| | `problem` | string | |
| | `minutes` | integer | |
| | `concepts` | sequence of strings | |
| | `notes` | optional string | absent in early entries |

Two things to work out rather than guess:

- Making `platform` and `language` real enums instead of strings is the whole
  point of week 3. Deserializing a lowercase TOML string into an enum variant
  needs a serde attribute — find it in the serde docs, don't guess it.
- `Option<String>` for `notes` is the week-5 concept arriving exactly on time.

### Produced by `stats.rs`

| Type | Field | Notes |
| --- | --- | --- |
| `Summary` | `sessions` | count, and `target` from meta |
| | `minutes_total` | |
| | `by_platform` | counts keyed by platform |
| | `by_language` | counts keyed by language |
| | `current_streak`, `longest_streak` | in days, from `streak.rs` |
| | `concepts` | concept → the solves that touched it, and the notes path |
| | `timeline` | solves in date order |

Keep `Summary` the single thing both `stats` (terminal) and `build` (HTML)
consume. If one of them needs a field the other does not, that is a hint the
field belongs in the renderer, not the summary.

## 3. Streaks

The only genuinely tricky logic, hence its own module. Definition to settle
before coding, because the tests depend on it:

- A streak is a run of **consecutive calendar days with at least one solve**.
  Two solves on one day do not extend it.
- The plan is six days a week, so a deliberate rest day breaks a strict
  streak. Decide now: strict consecutive days, or "no gap longer than one
  day". Write the choice here before writing a test.
- `current_streak` counts back from today, not from the last logged solve —
  otherwise it never decays and the number is a lie.

Pseudocode, as a level-3 hint:

```
dates  <- unique calendar dates of all solves, sorted
runs   <- split dates wherever the gap to the previous date exceeds the allowed gap
longest <- length of the longest run
current <- length of the final run, but only if its last date is today or yesterday,
           else 0
```

## 4. CLI surface

`clap` with the derive feature. Three subcommands:

| Command | Behaviour | Exit |
| --- | --- | --- |
| `tracker add` | prompts for platform, problem, minutes, concepts, notes; validates; appends one `[[solve]]` to `log.toml` | non-zero on invalid input, with the reason |
| `tracker stats` | prints the summary to stdout | non-zero if `log.toml` is unreadable |
| `tracker build` | writes `site/index.html` and copies `site/assets/` | non-zero on write failure |

Decisions worth making early:

- **Appending must not reformat the rest of the file.** Serializing the whole
  `Log` back out will destroy the comment header in `log.toml`. Append the
  rendered entry as text instead, then re-parse to confirm the file is still
  valid. Parse-after-write is the cheap insurance.
- `add` takes optional flags for every prompt, so it is scriptable and so the
  prompting path and the flag path share one validation function.
- Accept `--log <path>` and `--out <dir>` with sensible defaults. Tests need
  to point at a fixture, not at the real repo.

Crate shortlist to verify with `cargo search` at the time: `clap`, `serde`,
`toml`, `chrono`. Nothing else should be needed; the site has no runtime deps.

## 5. The HTML contract

`site/assets/style.css` is hand-written and **not generated**. `render.rs`
emits markup against its class names and `site.rs` copies the file unchanged,
so styling never has to round-trip through Rust string literals.

Available classes: `.wrap`, `.site-title`, `.lede`, `.progress-label`,
`.progress-track`, `.progress-fill`, `.tiles`, `.tile`, `.tile-value`,
`.tile-label`, `.empty`, `.chip` with `.chip-rust` / `.chip-julia`, plus
`code`. Tokens (spacing, type, color) are CSS variables on `:root`, with a
dark-mode block — use the variables, never new hex values.

Escape before interpolating. Concept names and note titles end up in HTML,
and `&` in a concept name should not break the page.

### What goes on the page

The plan lists totals, streaks, concepts and a timeline. All four on first
paint is a wall. Ranked, with a budget of four tiles and one chart:

1. **Headline**: sessions completed out of 72, as the progress bar.
2. **Four tiles**: current streak · problems solved · hours spent · concepts
   covered. Not more — longest streak and per-platform splits are detail.
3. **One chart**: solves over time. An inline SVG of one small square per
   day, Rust and Julia distinguished by *shape as well as fill* so it reads
   without color. No second chart.
4. **Concepts**, as a plain list linking to `notes/`. A list beats a chart
   when the question is "what have I covered".
5. **Timeline**, last ~20 solves, with the rest behind a details element.

Per-platform and per-language breakdowns belong in `tracker stats` on the
terminal, where you are the only reader. The site has an audience of
strangers; it answers "is this person actually doing it" in one screen.

Accessibility, cheaply: one `<h1>`, real `<table>` for the timeline, an
`aria-label` on the SVG stating the same fact in words, and a text total
beside every bar. A visitor using a screen reader should get the numbers.

## 6. Open design questions

- [ ] Strict streaks or one-day-gap tolerant? (section 3)
- [ ] Does `add` ask for the date, or always use today?
- [ ] Should `build` fail or warn when a `notes` path does not exist on disk?
- [ ] Minutes: trust the logged number, or cross-check against git commit times?
