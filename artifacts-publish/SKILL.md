---
name: artifacts-publish
description: "Publish a generated HTML file (pr-canvas report, any self-contained page) to https://artifacts.duncan.land for a shareable public link. Use when the user says \"publish this\", \"share this report\", \"put this online\", or after generating an HTML report they want linked in a PR. Also holds operator notes for running the artifacts service."
---

# Publish to artifacts.duncan.land

Duncan's own HTML host (Cloudflare Worker + R2, repo at `~/dev/artifacts`, GitHub `ephraimduncan/artifacts`, CLI on npm as `@ephraimduncan/artifacts`). One self-contained HTML file per slug, 25 MB max. Artifacts are unlisted — everyone keeps their own link.

## Publish

Preferred — the CLI is on PATH and handles auth automatically (admin token from `~/.config/artifacts/token`):

```bash
artifacts push <file.html> [--name <slug>]
```

Prints the public URL (also copied to clipboard). Slug defaults to the slugified filename; re-pushing the same slug overwrites in place, so the link stays stable while iterating.

Other commands: `artifacts list` (admin only), `artifacts rm <slug>`, `artifacts open <slug>`.

## Typical flow

After generating a pr-canvas report:

```bash
artifacts push "$TMPDIR/pr-canvas-<subject>.html" --name pr-canvas-<repo>-<pr>
```

Then paste the URL into the PR comment/description.

## Without the CLI

```bash
curl -sS -T <file.html> -H "Authorization: Bearer $(cat ~/.config/artifacts/token)" https://artifacts.duncan.land/<slug>
```

## Cautions

- Anyone with a link can read it — warn before publishing anything sensitive.
- Never print the contents of `~/.config/artifacts/token`.
- Slug rules: `[a-z0-9._-]`, first char alphanumeric, ≤128 chars. 403 on a slug means someone else owns it — pick another name.

## Operator notes (Duncan's service internals — keep out of public docs)

- The `artifacts.duncan.land` custom domain is provisioned by the `routes` entry in `wrangler.jsonc`; deploys propagate to the edge with ~30–60 s lag, so re-verify before assuming a bad deploy.
- The duncan.land zone strips `ETag` on responses; conditional requests work via `Last-Modified` instead.
- Everything on this origin is public, user-supplied HTML. Never host anything credentialed (logged-in dashboards, cookies) on this origin.
- `robots.txt` disallows all crawling; flip it in `src/index.ts` to allow indexing.
- Public listing is disabled by design: `GET /` JSON requires the admin token; the homepage never enumerates artifacts.
