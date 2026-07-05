# Contributing

Guide for working on the `harness-coding` template itself — not for projects scaffolded from it.

## Setup

Clone the repo and open it in the devcontainer like any other target project (`Ctrl+Shift+P → Dev Containers: Reopen in Container`). Branch from `main`; do not push directly to `main`.

## Development-only files

`justfile.private` exists only in this repo. It holds the template's own E2E tests and is never scaffolded into target projects — `cli.sh` removes it from any project that does have it (via `DEPRECATED_FILES`). Never add development/test logic to a REPLACE or MARKER file; it would ship to every target project on their next `cli.sh update`.

## Testing changes

```bash
just test-scaffold        # scaffold a fresh project into a temp dir, validate structure/syntax
just test-scaffold-build   # test-scaffold + full docker compose build (slow, needs network)
```

Both live in `justfile.private` and run against the tracked + untracked-not-ignored file set — i.e. exactly what a project created from this template would receive.

## File contract

This template distinguishes five file categories, enforced by `cli.sh` and documented in `README.md`'s file contract table:

- **REPLACE** — fully template-managed, overwritten on every `update`
- **MARKER** — only the `[harness-coding:START/END]` block is template-managed; content outside it is preserved
- **DIFF-ONLY** — shown as a diff, never auto-applied (`--force` to override)
- **NEVER-TOUCH** — created once if missing, never modified afterward
- **DEPRECATED** — removed automatically from target projects on `update`

When adding, renaming, or removing a template file, update both `cli.sh` (the `process_*` calls) and the README table in the same change — they must always agree; that's the whole point of the contract.

## Rule: template repo vs. target project

Files here that should never propagate to a scaffolded project belong in `justfile.private` or the DEPRECATED list, not in a REPLACE/MARKER file. This repo never runs `cli.sh update` against itself, so `justfile.private` is safe from its own deprecation.
