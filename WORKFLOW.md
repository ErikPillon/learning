# Working the repo from the terminal

Everything here runs locally. You should not need to open a chat to complete a
session — only to get a task, a hint, a review or a retro.

---

## 0. Where am I in the plan?

Until `tracker stats` exists (week 10), count by hand:

```sh
grep -c '^\[\[solve\]\]' log.toml          # sessions logged so far
tail -20 log.toml                          # what you did last
git log --oneline -5                       # same thing, faster to skim
```

Session *n* maps onto the schedule in order, six per week:

| Position in the week | Language | What |
| --- | --- | --- |
| 1 (Mon) | Rust | New concept: ~10 min book, ~20 min Rustlings |
| 2 (Tue) | Rust | Same concept, more Rustlings |
| 3 (Wed) | Rust | One Exercism exercise using the concept |
| 4 (Thu) | Rust | Second Exercism exercise, or finish Wednesday's |
| 5 (Fri) | Julia | One Project Euler problem |
| 6 (weekend) | Julia | Second Euler problem, then update log and notes |

Week number = `(sessions_logged / 6) + 1`. The full concept-by-week table is
section 4 of `LEARNING_PROJECT.md`.

---

## 1. Rust, Rustlings days (Mon/Tue)

Rustlings lives outside this repo — only the topics you complete get logged.

```sh
rustup doc --book          # offline book, opens in the browser
cd ~/rustlings && rustlings   # starts the watcher; it recompiles on save
```

The watcher prints the failing output and a key legend; hints are behind one of
those keys. Check the legend on screen or `rustlings --help` — the subcommands
changed between v5 and v6, so don't trust a remembered command.

Nothing from Rustlings is committed. The log entry is the record:

```toml
platform = "rustlings"
problem = "variables4"
```

## 2. Rust, Exercism days (Wed/Thu)

By default the Exercism CLI downloads into its own workspace directory, not
into this repo. Point it here once, so solutions land where they belong:

```sh
exercism workspace                      # prints the current workspace path
exercism configure --workspace=/Users/epillon/GitHub/learning/rust/exercism
```

Verify the flag name with `exercism configure --help` before relying on it; if
it is not supported, leave the workspace alone and copy the solution directory
into `rust/exercism/` when you finish.

Then, per exercise:

```sh
exercism download --track=rust --exercise=<slug>
cd rust/exercism/<slug>
cargo test                 # the exercise ships with the tests
cargo test -- --ignored    # later tests are #[ignore]d; unignore as you go
cargo clippy && cargo fmt
exercism submit            # optional — publishing to Exercism is up to you
```

## 3. Julia, Euler days (Fri/weekend)

One file per problem, zero-padded, in `julia/euler/`:

```sh
$EDITOR julia/euler/p001.jl
julia --project=julia julia/euler/p001.jl
```

First time you need a package, activate the shared environment and add it —
this creates `julia/Project.toml`, which is committed, and a `Manifest.toml`,
which is not:

```sh
julia --project=julia
# then press ] to enter pkg mode:
# (julia) pkg> add Primes
# backspace to leave pkg mode, Ctrl-D to quit
```

For a quick throwaway check without writing a file:

```sh
julia -e 'println(sum(1:10))'
```

Checking an Euler answer means pasting it on projecteuler.net — that is the one
unavoidable website. The code stays here.

## 4. End of every session

Four steps, always in this order:

**1. Make it pass.** Tests green, or the Euler answer accepted.

**2. Rust days only:**

```sh
cargo fmt && cargo clippy
```

Fix the warnings. Clippy is the closest thing you have to a free review.

**3. Log it.** Append one entry — by hand, until `tracker add` exists:

```sh
cat >> log.toml <<'ENTRY'

[[solve]]
date = 2026-10-05
platform = "rustlings"
problem = "variables"
language = "rust"
minutes = 28
concepts = ["bindings", "shadowing"]
notes = "notes/rust-bindings.md"
ENTRY
```

`platform` is one of `euler`, `exercism`, `rustlings`, `leetcode`, `project`.
`language` is `rust` or `julia`. `date` is unquoted. Drop the `notes` line if
you did not write any. Check you did not break the file:

```sh
python3 -c "import tomllib;tomllib.load(open('log.toml','rb'));print('ok')"
```

**4. Write up the concept, then commit.** One file per *concept*, not per
problem — append to the existing file when the concept repeats. Shape is in
`notes/README.md`.

```sh
$EDITOR notes/rust-bindings.md
git add -A
git commit -m "rustlings variables (rust): bindings"
git push
```

Commit message format: `<platform> <problem> (<language>): <concept>`.

---

## 5. Stuck, with no tutor in reach

In order, before asking anything:

```sh
cargo build 2>&1 | head -40      # read the whole error, not just line one
rustc --explain E0382            # long-form explanation of any error code
cargo doc --open                 # docs for your deps, offline
rustup doc --std                 # standard library, offline
```

Rust's errors are long on purpose and usually contain the fix. Read to the
bottom — the `help:` line is often literally the answer.

For Julia:

```sh
julia -e 'using Pkg; Pkg.status()'
# in the REPL: ?sum  for help, methods(sum) to see what it accepts
```

If you are still stuck after ~10 minutes, stop and log the session anyway with
what you tried. A logged failure beats an unlogged gap, and it gives the next
`Retro` something real to work with.

---

## 6. Asking for a task, hint or review

These are the only reasons to open a chat. Paste `log.toml` first, or point
the tutor at this repo.

| Say | Get |
| --- | --- |
| `Tasks for week N` | Six sessions with concrete exercises and primers |
| `Task for today` | The next session, based on `log.toml` |
| `Hint level 1 for <problem>` | A conceptual nudge, nothing more |
| `Hint level 2 for <problem>` | The algorithm or language feature that applies |
| `Hint level 3 for <problem>` | Pseudocode — never real Rust or Julia |
| `Review: <problem>` + your code | A review, after it already works |
| `Explain <concept>` | Mental model, minimal example, pitfalls, vs. Python |
| `Retro` | Progress summary, weak spots, plan adjustments |

Reviews happen *after* you have something working. Hints come one level at a
time, and only when you ask.

---

## 7. Optional shell shortcuts

Paste into `~/.zshrc` if they help:

```sh
export LEARN=~/GitHub/learning
alias l='cd $LEARN'
alias llog='tail -20 $LEARN/log.toml'
alias lcount='grep -c "^\[\[solve\]\]" $LEARN/log.toml'
alias lsite='python3 -m http.server 4173 --directory $LEARN/site'
```

`lsite` serves the generated site at http://localhost:4173 so you can look at
it before deploying.
