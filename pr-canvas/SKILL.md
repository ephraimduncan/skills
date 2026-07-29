---
name: pr-canvas
description: Present what a PR, the current PR branch, or any worktree changed vs its base — as a self-contained, plain-language HTML briefing with before/after visuals (diagrams, UI wireframes — whatever fits the change), written for someone who steers coding agents rather than reads diffs. Use when the user runs /pr-canvas, pastes a PR URL wanting the story of the change, or says "preview the changes on this branch compared to main", "what are the architectural changes here", "architecture report" — anything wanting understanding of a change rather than a line-by-line review.
---

# PR Canvas

Produce a single self-contained HTML report that explains what a change *does* — what's possible now, what behaves differently, where it sits in the app — for a reader who directs coding agents but never reads the source. Visuals carry the weight and are chosen per change — a flow diagram when flow is the story, a UI wireframe pair when the screen is, none when a sentence says it better; prose is plain and sparse; every sentence must survive the say-it-to-a-smart-friend test defined in `HTML-REPORT.md`. Open the report in the browser when done.

For a line-level diff walkthrough, that's the `pr-review-canvas` skill, not this one.

## Workflow

### 1. Resolve the subject

Accept any of, in this order of likelihood:

- **Current worktree/branch vs a base (the default)** — "preview the changes here compared to main". Resolve the base: the branch the user names, else the repo default (`git symbolic-ref refs/remotes/origin/HEAD`, falling back to `main`). Then:
  ```bash
  base=$(git merge-base origin/main HEAD)
  git log --oneline "$base"..HEAD
  git diff --stat "$base"          # vs working tree — includes uncommitted work
  git diff "$base" -- <paths>      # read full hunks for files that matter
  ```
  Diff against the **working tree**, not just HEAD — uncommitted and staged changes are part of the preview. Note in the report header if the delta includes uncommitted work.
- **PR URL or number** — fetch in parallel:
  ```bash
  gh api repos/{owner}/{repo}/pulls/{number} --jq '{title, body, user: .user.login, additions, deletions, changed_files, base: .base.ref, head: .head.ref, merged, state}'
  gh api repos/{owner}/{repo}/pulls/{number}/files --paginate --jq '.[] | {filename, status, additions, deletions, patch}'
  ```
  If the repo is checked out locally, prefer `git diff base...head` for full context over truncated API patches.
- **Explicit commit range or two branches** — same as the default case with the given endpoints.
- **Feature name** ("the export pipeline", "auth") — locate it: grep entry points, map the modules involved. If the user wants *how it changed*, find the commits that built it (`git log --follow`, `git log -S<symbol>`). If they want *how it works now*, skip before/after and present current structure only — the report becomes the big-picture map plus cards on how it works today.

### 2. Analyze architecturally

Read the diff for structure, not correctness. An architectural change is one of:

- **Module created / removed / split / merged**
- **Dependency edge added or removed** — imports that cross a module boundary; watch especially for new edges that reverse or shortcut an existing direction
- **Seam introduced or removed** — new interface + adapter, or indirection deleted
- **Interface widened / narrowed** — exported surface of a module grew or shrank
- **Responsibility moved** — logic relocated across a seam (caller → callee, handler → domain, app → library)
- **Flow changed** — sync→async, new round-trips, caching layer, queue inserted, fan-out added; schema/contract changes (migrations, API routes, event shapes) count here
- **What the user sees changed** — a screen, component, layout, visible state, or user-facing wording changed; a real change that gets its own card even when no module or flow moved

Everything else — formatting, renames without moves, test-only churn, version bumps, generated files — is **mechanical**. Collapse all of it into one line in the report. Never inflate mechanical churn into architecture.

The taxonomy above is your analysis lens, not the report's vocabulary — "seam", "boundary", "surface", "module depth" never appear in the output. The report translates each finding into what the app now does differently and what that lets the reader do.

**Honesty rule:** if the PR has no architectural change, the report says exactly that in the header, lists the mechanical summary, and stops. Do not invent structure.

For each real change, establish the before-state (read the base-side of the diff, or `git show base:path`) so before/after visuals are grounded, not guessed.

### 3. Build the report

Follow `HTML-REPORT.md` in this skill directory for the format, scaffold, visual patterns, and tone — **the visual style is fixed there; copy its token `<style>` block verbatim, do not improvise a per-report palette or font.** Two of its sections govern everything else: "Write for the person who steers the agents" — the reader never reads source code, so every sentence, heading, and diagram label must pass the say-it-to-a-smart-friend test, with every term of art glossed inline or dropped — and "Visuals show the app's real behavior, at a glance" — each card picks the representation that makes *that particular change* land (a Mermaid flow or sequence diagram, a UI wireframe pair, a real screenshot pair, an annotated screenshot with callouts pointing at the changed region, a rendered diff, a JSON tree of the actual record, a run-trace timeline — or no panel at all; fit decides, never habit or category), every visual traces a concrete scenario this app performs (Before = base-branch behavior, read from base-side code), carries one idea within the budgets, and before/after pairs keep the same layout so the delta pops without study. The report is **layered**: a plain story on the surface — readable in about three minutes with every collapsed block closed, headings and visuals alone carrying it — and the evidence underneath in collapsed blocks (annotated code excerpts, call shapes) for the curious glance or for grounding what the agent gets asked. The five-tells list is also load-bearing: contrast ramp, Inter features on, quiet sentence-case chips, semantic zinc tokens with outline-only themed Mermaid, and no insider language. It is a dense dark-theme developer tool (Cursor / Linear / GitHub-dark register), and it must end with the reader knowing exactly what to check, in what order, and what to ask their coding agent. Structure:

1. **Header** — sans-serif title stating the outcome in plain words; mono meta line (`<hash> · <repo> · <date>` or `<branch> → <base>`); numeric stat pills (`+added` / `−removed` / files); theme toggle (top-right, scaffold-owned); legend
2. **What this changes** — ≤2 plain sentences (what's possible now, how it lands) + quiet change-type chips
3. **The big picture** — one small map (≤5 boxes, one direction) of where the change sits on the path from a visible action to the result the reader cares about; changed pieces highlighted; setup mechanics never appear here
4. **What changed** — one card per real change; stacked Before/After panels with narrative headings a user of the app would confirm ("Before: the agent records everything, but the records go nowhere") as centrepiece — each panel in whatever representation makes that change land at a glance (flow/sequence diagram, UI wireframe or screenshot pair, annotated screenshot, rendered diff, JSON tree, run trace — or no panel when the sentences alone carry it); flow changes highlight the changed subpath directly in the diagram
5. **How you'd use it** — new/changed functions, commands, hooks, or options as one-sentence plain entries with file paths as anchors, plus optionally the call shape (the one line your script would write); never type signatures or diffs; skipped when nothing user-facing changed
6. **Under the hood** — 2–4 collapsed code excerpts, each ≤ ~20 lines with plain-words margin annotations and a summary line that stands alone; deep-linked to the PR Files tab when the subject is a PR; always optional to the story; skipped when the cards already show everything
7. **Also in this change** — one collapsed line of mechanical churn
8. **What to check** — a plain quality read; a check-in-this-order list (linked steps, worth-a-close-look vs skim); risks phrased as consequences the reader would observe; and "Ask your agent" items — each with a one-click copy button holding a complete, self-contained instruction to paste at the coding agent

### 4. Deliver

Write the file to the OS temp dir and open it:

```bash
open "$TMPDIR/pr-canvas-<subject>.html"
```

One file, no build step, no local assets — Tailwind and Mermaid come from CDNs.
