# Notes

One markdown file per concept, not per problem. Filenames are
`<language>-<concept>.md`, e.g. `rust-ownership.md`, `julia-comprehensions.md`.

Each file is written by the learner, in their own words. Suggested shape:

```markdown
# <concept>

## Mental model
One or two sentences. What is actually going on.

## Minimal example
The smallest snippet that shows the idea.

## Compared to Python
(and Julia, where relevant)

## Pitfalls
What bit me, and what the compiler/error message looked like.

## Seen in
- euler 1, exercism/<slug>, rustlings/<topic>
```

`log.toml` entries point at these files via their `notes` field, and
`tracker build` will link them from the public site.
