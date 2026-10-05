# Setup

State of this machine, checked 2026-10-05. Re-run the commands in
"Verify" after any toolchain update.

## Installed

| Tool | Version found | Note |
| --- | --- | --- |
| rustup | 1.27.0 | |
| rustc / cargo | 1.77.1 (2024-03-27) | **Old.** Run `rustup update` before installing Rustlings. |
| clippy, rustfmt | installed as components | `cargo clippy`, `cargo fmt` work |
| rust-docs | installed | offline book available: `rustup doc --book` |
| git | /usr/bin/git | |
| gh | /opt/homebrew/bin/gh | |
| node / npm | /opt/homebrew | only needed for the Vercel CLI |

## Missing — install in this order

1. **Update Rust first.** Rustlings v6 declares a minimum supported
   rustc newer than 1.77; installing it on 1.77.1 will probably fail.
   ```sh
   rustup update
   ```
2. **Rustlings** (week 1, Mon/Tue):
   ```sh
   cargo install rustlings
   rustlings init          # creates a ./rustlings directory; can live outside this repo
   rustlings               # starts the watcher
   ```
   The exact subcommands changed between Rustlings v5 and v6. If `rustlings init`
   is rejected, run `rustlings --help` and follow that output rather than this file.
3. **juliaup + Julia** (week 1, Fri):
   ```sh
   curl -fsSL https://install.julialang.org | sh
   # new shell, then:
   juliaup status
   julia --version
   ```
4. **Exercism CLI** (week 1, Wed) — install via Homebrew or the official
   binary, then:
   ```sh
   exercism configure --token=<token-from-exercism.org/settings/api_cli>
   exercism download --track=rust --exercise=<slug>
   ```
5. **Vercel CLI** — not needed until week 11. `npm i -g vercel`.

## Verify

```sh
rustc --version && cargo --version
rustup doc --book          # opens the offline book
julia --version
rustlings --help
exercism version
```

## Section 9 checklist — current state

| Item | State |
| --- | --- |
| Rust book chapter numbers from ch. 15 on | **Verified against the local book** shipped with rustc 1.77.1: ch. 15 = Smart Pointers, ch. 16 = Fearless Concurrency. Matches the roadmap. Re-check after `rustup update`: newer editions insert an async chapter at 17 and shift 17–20, but 1–16 are unaffected. |
| Rustlings folder names / install steps (v6) | **Unverified** — Rustlings is not installed. Confirm topic names from `rustlings list` once installed, and correct the week-by-week table if they differ. |
| Project Euler publication rule | **Not found.** The About page and the problem archive carry no such notice, and `/faq` returns 404. The roadmap only uses problems 1–24, so the "beyond 100" question does not arise in these 12 weeks. Before committing any solution above #100, check an individual problem page above 100 while logged in. |
| LeetCode terms on publishing solutions | **Unverified.** `rust/leetcode/` is empty; nothing to publish before week 12. |
| Primes.jl / BenchmarkTools.jl / Combinatorics.jl | **Unverified** — Julia not installed. Check with `] add <Pkg>` in the project environment once juliaup is in place. |
| Vercel CLI commands / static deploy settings | **Unverified** — not needed until week 11. |
