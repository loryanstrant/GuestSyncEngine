# Copilot instructions — GuestSyncEngine

> Canonical standards live in the `dev-standards` repo on SOUNDWAVE/Gitea.

## What this repo is

A **Microsoft 365 guest-synchronisation solution** distributed as a packaged
`.zip` alongside architecture/operational reference docs. Not a Home Assistant
component.

## Repo shape

- `GuestSynchronisationEngine_*.zip` — the packaged solution (binary; not
  unpacked into version control — agents can't read inside it).
- `Architecure reference for host tenant.md`, `Onboarding a new subsidiary.md`,
  `Operational workflow reference.md` — reference docs.

## Conventions

- M365 solution + docs: no HA component scaffolding applies.
- Core solution is zipped — to work on it meaningfully, unpack into the repo first.
- Keep the reference docs in step with the solution.
- Never commit tenant IDs, secrets, or real directory data.

## Never

- Don't commit secrets or tenant-specific identifiers.
