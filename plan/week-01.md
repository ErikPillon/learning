# Week 1 — setup, variables, functions, control flow

Rust: book ch. 1–3, Rustlings `variables`, `functions`, `if`, `primitive_types`.
Julia: juliaup, Euler 1 and 2.

Nothing was logged on Monday, so session 1 is whenever you start. The week ends
a day late or you double up on the weekend — your call, don't backfill the log
with a date you didn't work.

**Rustlings topic names below are unverified** (v6, not installed yet). Run
`rustlings list` once it is installed and correct this file if they differ.

---

## Session 1 — Setup

```sh
rustup update
cargo install rustlings && rustlings init
rustup doc --book     # ch. 1 and 2
```

`rustup update` first: Rustlings v6 needs a newer rustc than your 1.77.1, so
installing before updating will probably fail. Then work ch. 2, the guessing
game — type it out rather than reading it. It front-loads a dozen things you
won't understand yet (`match`, `Result`, shadowing, traits in scope), and that
is deliberate; the discomfort is the chapter's design. You're not meant to
finish ch. 1–3 today.

Log it: `platform = "project"`, `problem = "setup"`, `concepts = ["toolchain"]`.

## Session 2 — Rustlings `variables`, book ch. 3.1

Bindings are immutable by default and `mut` is the exception you opt into —
the inverse of Python, where everything is rebindable and immutability is the
special case. Shadowing (`let x = ...` twice in a scope) is a separate
mechanism from `mut`: it creates a *new* binding, so the type may change, which
is how you'd write Python's `s = int(s)`. `const` needs an explicit type, is
inlined at compile time, and lives anywhere including module scope. The
compiler will tell you which of the three you wanted; read the `help:` line.

## Session 3 — Rustlings `functions` and `if`, book ch. 3.3–3.5

Function signatures are never inferred: every parameter and the return type are
annotated, even though bodies infer freely. Rust separates *expressions* (yield
a value) from *statements* (don't), and the last expression in a block without
a semicolon is the block's value — adding a semicolon there changes the return
to `()` and is the single most common beginner error. Because blocks are
expressions, `if` is one too: `let n = if cold { 1 } else { 2 };` is idiomatic
where Python uses a ternary. Conditions must be an actual `bool` — there is no
truthiness, so `if 1` and `if some_vec` are compile errors.

## Session 4 — Rustlings `primitive_types`, then Exercism `leap`

```sh
exercism download --track=rust --exercise=leap
cd rust/exercism/leap && cargo test
```

Integers have explicit widths and signedness (`i32` default, `u8`, `usize`),
and overflow panics in debug builds instead of silently wrapping like Python's
unbounded ints. Tuples are fixed-length and mixed-type with `.0`/`.1` access;
arrays are fixed-length and single-type, which is why you'll reach for `Vec`
next week. `leap` is pure boolean logic — the interesting part is that the
whole thing is one expression, and Clippy will nudge you if you wrote it as a
chain of `if`s returning `true`/`false`. Run `cargo clippy` before you log it.

## Session 5 — juliaup, then Euler 1

```sh
curl -fsSL https://install.julialang.org | sh   # new shell afterwards
julia --version
$EDITOR julia/euler/p001.jl
julia --project=julia julia/euler/p001.jl
```

Ranges are lazy objects, not lists: `1:999` allocates nothing, and `1:2:9`
steps by two. Comprehensions build a concrete array — `[x^2 for x in 1:5]` —
while the same expression in parentheses is a lazy generator that streams
without materialising. Most reducers (`sum`, `count`, `maximum`) accept either,
so you rarely need the array. Julia is 1-indexed and ranges are inclusive at
both ends, which is the opposite of Python's `range(1, 999)` on both counts.

## Session 6 — Euler 2, then catch up the log and notes

`while cond ... end` with `break` and `continue`, no parentheses around the
condition and no colon. Multiple assignment evaluates the whole right-hand side
first, so `a, b = b, a` swaps without a temporary, exactly as in Python.
Integer literals default to `Int64` here, so nothing overflows at this scale —
but check `typemax(Int64)` so you know where the edge is, because week 7 walks
straight into it. Finish by writing the two concept notes if you've been
putting them off; the log is the only thing the website will ever see.

---

## Overflow, if a session runs short

- Exercism `raindrops` — same control-flow level as `leap`, a little string work.
- Exercism `collatz-conjecture` — loops, and an early taste of `Result`.

Both verified to exist on the Rust track. Don't start them at the cost of the
Julia days; Friday and the weekend are the ones that slip.

## Ask the tutor for

`Hint level 1 for euler 1` before level 2, and don't ask for a review until
the tests pass. `Explain expressions vs statements` is worth it after session 3
if the semicolon rule hasn't clicked.
