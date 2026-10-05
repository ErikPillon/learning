# Deploying the site

The site is static. `tracker build` (week 11) writes `site/`, and Vercel
serves it. No Rust toolchain runs on Vercel — that is the point.

Until the tracker exists, `site/index.html` is a hand-written placeholder, so
everything below can be set up and tested today.

## Option A — deploy the folder directly (what the plan recommends)

No build step, no guessing how Vercel's build environment behaves:

```bash
npm i -g vercel
cd site && vercel        # first run: links/creates the project, asks a few questions
cd site && vercel --prod # subsequent deploys
```

The first `vercel` run requires signing in through the browser and answering
prompts about the project name and framework — answer **Other** / no
framework, since this is plain HTML. Do this yourself; it is an interactive
login.

Day-to-day, after a session:

```bash
cargo run --manifest-path tracker/Cargo.toml -- build   # once the tracker exists
cd site && vercel --prod
```

## Option B — connect the GitHub repo

`vercel.json` at the repo root is already configured for this: no build
command, no install command, output directory `site`. Import the repo at
vercel.com, and the committed `site/` is served as-is.

**Unverified**: whether Vercel honours `outputDirectory` for a project with no
build step, and whether the dashboard overrides the file. Check the deployment
log on the first push. If it serves the repo root instead of `site/`, use
Option A — it sidesteps the question entirely.

Because this option serves what is committed, `site/` must stay in git. It is
not in `.gitignore`, deliberately.

## Option C — GitHub Pages (fallback)

A workflow that runs `tracker build` and publishes `site/` with
`actions/deploy-pages`. Not set up; only worth doing if Vercel disappoints.
Needs the Rust toolchain in CI, which is why it is the fallback and not the
default.

## Notes

- Vercel CLI commands and settings change; verify against current Vercel docs
  rather than trusting this file. Last checked: never.
- The site has no runtime dependencies — no JS framework, no CDN, no fonts
  fetched at load. Keep it that way; it is why the page is fast and why there
  is nothing to break.
