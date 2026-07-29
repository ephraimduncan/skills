# HTML Report Format

One self-contained HTML file in the OS temp dir. Tailwind (Play CDN) + Mermaid from CDNs; no build step. The report must read like a **real developer tool** — dense, calm, high-contrast, **dark** — the register of Cursor / Linear / the GitHub PR view in dark mode (a quiet header toggle flips to light) — not a generic Tailwind landing page or an AI mockup. Visuals carry the weight — each chosen to fit its change; prose is plain and sparse. The report is **layered**: a plain story on the surface — read once, in about three minutes, headings and visuals alone carrying it — and the evidence underneath in collapsed blocks (annotated code excerpts, call shapes) for the curious glance or for grounding what the agent gets asked. The plain layer never depends on the collapsed layer, and the report must end with the reader knowing exactly what to check, in what order, and what to ask their coding agent.

**The look is fixed.** Do not improvise a new palette, font, or component style per report. Copy the token block and the rules below verbatim so every report is visually identical run to run. The only thing that changes between reports is the *content*.

## The five tells to kill

The output reads AI-generated when any of these slip in. The design system exists to prevent them; check the markup against this list before writing the file.

1. **Washed-out low-contrast gray text.** One flat muted gray for everything — subject line, descriptions, captions, labels. Real tools use a deliberate contrast ramp. **Prose is never lighter than `--secondary`.** `--tertiary` is for labels/meta only; `--faint` is for code-excerpt gutters and schematic diagram sublabels only — never a readable sentence.
2. **Default Inter with no OpenType features.** Plain Inter is the single most common AI-site fingerprint. Always enable the feature set (`cv02 cv03 cv04 cv11 ss01 ss03`) + optical sizing — it makes Inter look intentional. (uidotsh Typography guideline.)
3. **Uppercase-mono tinted pills.** `NEW MODULE` in a colored rounded tag = the templated badge tell. Change-type labels are a **quiet dot + sentence-case text** with a hairline ring. The one sanctioned pill is the header's numeric stat pill (`+3,985`) — numbers, never words.
4. **Default Play-CDN palette + unthemed diagrams.** No `slate`/`gray`/`stone`/`indigo`, no `bg-*-50`, no raw `fill:#ecfdf5` in Mermaid, no `rounded-lg` everywhere, no `font-serif`. Only the semantic tokens; Mermaid nodes are **outline-only**, themed to the tokens.
5. **Insider language that assumes the reader lives in the source.** Diagram labels, bullets, or summaries built from bare symbol names (`_wrapper_for`, `__aexit__`) or unexplained trade terms ("registers the global provider", "the normalizer's correlation keys"). The reader steers the agents that write the code and never reads the source — a sentence earns its place only if it would make sense said aloud to a smart friend who has never seen this codebase. Behavior in plain words leads; a name appears at most once, in parens, as an anchor to hand the agent. See the next section.

## Write for the person who steers the agents

The report's reader directs the coding agents that build this app; they do not read the source. They know the product cold — what it does, its features, screens, and commands — and they decide what gets built next by prompting. They do not carry the codebase's internal vocabulary, and stack jargon (provider, normalizer, middleware, span, seam) means nothing to them unless the report explains it on the spot.

The one test, applied to every sentence, heading, bullet, chip, and diagram label: **would it make sense said aloud to a smart friend who has never seen this codebase?** If not, rewrite it around behavior or add a plain-words gloss.

- **Behavior first, always.** Say what the app does differently — "cancelling a turn now shows up as cancelled instead of looking like a quick success" — never what the code is shaped like ("the proxy synthesizes a cancelled finish reason").
- **Every term of art is explained or dropped.** First use gets a short gloss in the same sentence: "a span — one timed record of one agent turn". A term the report can't afford to explain is a term the report can't afford to use. This applies to the stack's jargon (OpenTelemetry, webhook, migration) as much as the repo's own.
- **Architecture-speak stays in the analysis.** "Seam", "boundary", "surface", "deep module", "indirection" guide *your* reading of the diff; the report translates each finding into what the app now does differently.
- **Names are anchors, not prose.** Public function names and file paths may appear — in mono, once, in parens or a files line — because the reader pastes them to their agent. No sentence may *depend* on a name to parse; private helpers and dunders almost never appear at all.
- **Diagram node labels are things the reader can point at** — "your script", "the eve agent", "telemetry.dev dashboard" — or plain-words roles ("the timer that runs until the stream ends"). Never call chains, never mechanism boxes. The one exception: a public call the reader's own script writes (`send(message)`) is welcome.
- **Depth is layered, never deleted.** The specifics that make the story credible — counts ("18 kinds of events"), semantics ("first one wins"), public names once in parens — stay in the plain layer. The evidence — real code, call shapes — lives in collapsed blocks with plain-words summary lines, so the reader can ignore it, glance at it, or hand it to the agent. The plain layer must read complete with every collapsed block closed.
- **Sentence budgets govern the plain layer:** the summary ≤2 sentences; per card, What changed ≤3 sentences, Why it matters ≤1, ≤4 bullets; captions 1–2 sentences. Depth beyond the budget goes into a collapsed under-the-hood block or the agent conversation — never inline.
- **Never a mystery annotation.** A trailing `— fnA · fnB → fnC` after a file path explains nothing; if a relationship matters, say it in a sentence. If it doesn't, drop it.

## Visuals show the app's real behavior, at a glance

A visual earns its place only if the reader recognizes their own app in it and gets the point in seconds. One that needs study — or that could describe any codebase — subtracts. Cut it, redraw it, or replace it with a sentence.

**Pick the representation before drawing — fit decides, never habit or category:**

- Every panel chooses from the whole menu: a Mermaid flowchart or sequence diagram, hand-built boxes-and-arrows, a UI wireframe pair, a real screenshot pair, a rendered diff, a JSON tree of the actual record, a run-trace timeline, a small table — or no panel at all. The change's *category* never mandates a form: a UI change may land best as a wireframe pair, or as a short flow if what changed is how the screen gets its data; a flow change may land best as one table of round-trips. Judge each change on its own.
- The fit test, per panel: **does the picture show something the What-changed sentences alone could not?** A panel whose entire information content is one changed noun — same boxes, same arrows, one label swapped — fails it; cut the panel and let the sentences carry the fact. A card with zero panels is a normal card, not a lesser one.
- The recognition test: would this exact picture look different for any other change touching the same area? A generic exchange ("a developer's terminal → the registry → source comes back") that redraws identically for a hundred PRs is furniture, not evidence.
- When two forms genuinely fit, prefer the one closest to what the reader would point at: the screen itself (a wireframe) over a diagram about the screen; the record itself over a diagram of the pipeline that carries it.

**Relatable — anchored in what the app actually does:**

- Every diagram traces a concrete scenario the app really performs — "user saves a settings change", "a streamed completion returns chunks" — starting from the entry point the reader knows: the page, command, endpoint, or public call. Never a free-floating module abstraction with no scenario attached.
- The Before diagram shows how the feature behaves **today on the base branch**, drawn from the base-side code — recognizable as the existing app, never a guessed straw-man. If the before-behavior can't be stated as one sentence a user of the app would confirm ("today, saving re-reads the whole config from disk"), it hasn't been established yet — read the base side first.
- Nodes use the product's and repo's own vocabulary — the feature, page, command, and module names the reader already uses — with roles in plain words. A generic `Handler → Service → Repo` shape that fits any codebase is the sign the diagram isn't grounded; redraw it around this feature.

**Glanceable — one idea, seconds to parse:**

- One idea per diagram. The bold narrative heading states the takeaway; the diagram is evidence for that sentence and nothing else. True-but-irrelevant nodes dilute — cut them.
- Budgets: ~7 nodes per flowchart, ~8 messages per sequence diagram (collapse repeated exchanges into one labelled message). Over budget → split into two small diagrams or move the detail into the caption.
- The ten-second test: heading + diagram alone must land the point before the caption is read. If the caption is needed to *decode* the diagram (rather than add nuance), redraw.
- Before/after pairs keep the same orientation and node placement; only the changed part appears, moves, or fades. The delta must pop from visual comparison alone — re-layouting the After forces a full re-parse and hides the change.
- Edges flow one direction (left→right or top→bottom, the direction of the action). Crossing edges usually mean the diagram carries two ideas — split it.

## Design system (tokens)

Define a semantic token layer once, then reference it everywhere — never raw `slate`/`gray`/`stone`/`indigo`. Neutral family is **zinc**, dark: a near-black zinc canvas, elevated surfaces one step lighter, recessed wells one step darker, and alpha-white hairlines (never opaque `#333`-style borders — alpha sits correctly on every surface). Text is a **four-step contrast ramp**, not one muted gray. Four hues with one job each: green = new/added, amber = modified, red = problem/removed — all three used rarely — and blue strictly for navigation/reference (file-path links, anchors, inline code refs), never as a semantic. **Dark by default, one-click light** — both palettes define the *same* custom properties in the token block; the header toggle flips `data-theme` on `<html>` and every utility and tint follows the vars.

**Critical (verified):** the Tailwind Play CDN does **not** emit named color utilities from an `@theme` block — `bg-canvas`/`text-primary` compile to nothing. So define tokens as plain `:root` custom properties in a **native `<style>`** (not `type="text/tailwindcss"`), and hand-define the color utility classes there too. Tailwind is used only for layout / spacing / typography. Static arbitrary values like `shadow-[0_0_0_1px_var(--hairline)]` also work; dynamically composed classes do not (JIT scans once at load). The CDN's runtime stylesheet also loads *after* the token block, so its `font-mono` utility would override the token stack — the token rule carries `!important` for exactly that reason (verified). The report is **dark by default with a persisted light override**: the pre-paint snippet in the token block reads `localStorage` and sets `data-theme="light"` on `<html>` before first paint (no flash); each palette block carries its own `color-scheme`.

Paste this block verbatim into `<head>`:

```html
<link rel="preconnect" href="https://rsms.me/" />
<link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&display=swap" />
<script src="https://cdn.tailwindcss.com"></script>
<script>try { if (localStorage.getItem("pr-canvas-theme") === "light") document.documentElement.dataset.theme = "light"; } catch {}</script>
<style>
  :root {
    color-scheme: dark;
    --canvas:#18181b; --chrome:#1f1f23; --well:#131316;          /* page / elevated / recessed */
    --hairline:rgb(255 255 255 / 0.09);                          /* ring + divider color */
    --primary:#f4f4f5;    /* zinc-100 — headings, key values, emphasized inline (~14.9:1) */
    --secondary:#d4d4d8;  /* zinc-300 — ALL body / descriptions / captions (~11:1) */
    --tertiary:#a1a1aa;   /* zinc-400 — labels, meta, legend, diagram edge notes (~6.8:1, AA) */
    --faint:#71717a;      /* zinc-500 — code-excerpt gutters + schematic diagram sublabels ONLY */
    --accent:#73b87e; --warn:#e0a458; --danger:#e06060;          /* new · modified · problem/removed */
    --link:#7aa2f7;                                              /* navigation/reference: paths, anchors, ic */
    --font-sans:"InterVariable",ui-sans-serif,system-ui,sans-serif;
    --font-mono:"IBM Plex Mono","SF Mono",ui-monospace,"JetBrains Mono",monospace;
  }
  :root[data-theme="light"] {
    color-scheme: light;
    --canvas:#ffffff; --chrome:#ffffff; --well:#f4f4f5;
    --hairline:rgb(9 9 11 / 0.08);
    --primary:#18181b; --secondary:#3f3f46; --tertiary:#71717a; --faint:#a1a1aa;
    --accent:#047857; --warn:#b45309; --danger:#b91c1c; --link:#1d4ed8;
  }
  html{font-feature-settings:"cv02","cv03","cv04","cv11","ss01","ss03";scroll-behavior:smooth}
  body{background:var(--canvas);color:var(--secondary);font-family:var(--font-sans);font-optical-sizing:auto;-webkit-font-smoothing:antialiased;text-rendering:optimizeLegibility}
  code,pre,.font-mono{font-family:var(--font-mono) !important;font-feature-settings:normal}
  .bg-canvas{background-color:var(--canvas)} .bg-chrome{background-color:var(--chrome)} .bg-well{background-color:var(--well)}
  .text-primary{color:var(--primary)} .text-secondary{color:var(--secondary)} .text-tertiary{color:var(--tertiary)} .text-faint{color:var(--faint)}
  .text-accent{color:var(--accent)} .text-warn{color:var(--warn)} .text-danger{color:var(--danger)} .text-link{color:var(--link)}
  .border-hairline{border-color:var(--hairline)} .decoration-hairline{text-decoration-color:var(--hairline)}
  .ring-hairline{box-shadow:0 0 0 1px var(--hairline)}
  .tint-accent{background:color-mix(in srgb, var(--accent) 10%, transparent)}
  .tint-warn{background:color-mix(in srgb, var(--warn) 10%, transparent)}
  .tint-danger{background:color-mix(in srgb, var(--danger) 10%, transparent)}
  .diff-add{background:color-mix(in srgb, var(--accent) 12%, transparent)}
  .diff-del{background:color-mix(in srgb, var(--danger) 10%, transparent)}
  .syn-kw{color:var(--danger);font-weight:600}
  .syn-fn{color:var(--accent);font-weight:600}
  .syn-type{color:var(--warn)}
  .syn-param{color:var(--primary)}
  .syn-op,.syn-punc{color:var(--tertiary)}
  .syn-str,.syn-num{color:var(--warn)}
  .removed{opacity:.45;text-decoration:line-through}
  .chip{display:inline-flex;align-items:center;gap:.375rem;border-radius:.25rem;padding:.125rem .4375rem;font-size:.75rem;line-height:1rem;font-weight:500;box-shadow:0 0 0 1px var(--hairline)}
  .chip::before{content:"";width:.375rem;height:.375rem;border-radius:9999px;background:currentColor;flex:none}
  .pill{display:inline-flex;align-items:center;font-family:var(--font-mono);font-size:.75rem;line-height:1rem;font-weight:500;padding:.125rem .5rem;border-radius:9999px;background:color-mix(in srgb, currentColor 12%, transparent)}
  .ic{font-family:var(--font-mono);font-size:.8125em;color:var(--link);background:var(--well);padding:.0625rem .3125rem;border-radius:.25rem}
  main section{scroll-margin-top:3.75rem}
  .navlink{color:var(--tertiary);transition:color .12s}
  .navlink:hover{color:var(--primary)}
  .ann{display:block;font-family:var(--font-sans);font-size:.8125rem;line-height:1.25rem;white-space:normal;max-width:62ch;color:var(--secondary);border-left:2px solid var(--accent);background:color-mix(in srgb, var(--accent) 8%, transparent);border-radius:.125rem;padding:.25rem .625rem;margin:.375rem 0 .375rem 2.25rem}
  .ann-warn{border-left-color:var(--warn);background:color-mix(in srgb, var(--warn) 9%, transparent)}
</style>
```

Markup uses only the semantic classes: `bg-chrome`, `bg-well`, `text-primary/secondary/tertiary/faint`, `text-accent/warn/danger/link` (`text-link` strictly for navigation/reference — file paths, anchors — never a semantic), `border-hairline`, `ring-hairline` (the hairline elevation), `chip` (+ `text-accent`/`text-warn`/`text-danger` for its hue; the dot inherits via `currentColor`), `pill` (numeric header stats only — pair with a hue class; the tint derives from `currentColor`), `ic` (inline code reference in prose, `--link` hue), `tint-*`, `diff-add`/`diff-del` (code-excerpt line backgrounds), `ann`/`ann-warn` (margin-note rows inside code excerpts), `syn-*` (code-token spans — the scaffold highlighter owns them), and `navlink` (copy buttons and quiet action links — the Play CDN cannot variant custom classes, so hover color lives in the token block, never `hover:text-*`). Everything else is normal Tailwind layout/spacing/type. The raw Tailwind palette must not appear.

**Contrast ramp — the rule that kills the washed-out look:**
- `text-primary` — headings, the header title, key stat values, emphasized inline spans.
- `text-secondary` — **every readable sentence**: the summary, section subtitles, "what changed"/"why it matters", diagram captions, effect bullets, check-section prose, annotation rows inside code excerpts.
- `text-tertiary` — labels only: stat captions, the legend, file-path lines, diagram edge annotations. Legible but recessive.
- `text-faint` — decoration only: code-excerpt line gutters and schematic diagram sublabels. Never a sentence.

**Surface rules** (uidotsh Surfaces / Shadows):
- Content sits directly on `bg-canvas`. Separate siblings with the **lightest** treatment that works: whitespace → hairline divider (`h-px bg-hairline` or `divide-hairline`) → recessed well → card. Never jump straight to cards.
- An elevated block is a **hairline ring, never border+shadow**: `bg-chrome ring-hairline`. Reserve it for genuinely distinct units (each change card). Never `border border-slate-200`, never a card darker than its canvas.
- Diagram panels are **wells**: `bg-well`, no ring. A recessed panel may be darker than canvas; an elevated one may not.
- Near-square corners: `rounded-md` default, `rounded-sm` for small nested elements. Nested rounded element goes one step smaller (concentric).

**Change-type labels — quiet, not pills.** A dot in the label's own hue + sentence-case text + hairline ring. Never uppercase, never mono, never a loud tint fill:

```html
<span class="chip text-accent">new module</span>   <!-- green dot -->
<span class="chip text-warn">flow change</span>    <!-- amber dot -->
<span class="chip text-tertiary">ports &amp; adapters</span>  <!-- neutral dot -->
```

**Typography** (uidotsh Typography): headings `font-semibold`/`font-medium`, never `font-bold`; `tracking-tight` above `text-xl`; no `leading-*` on headings; `text-balance` on headings, `text-pretty` on paragraphs. Body is `text-sm`/`text-base` — `text-xs` only for code-excerpt gutters and schematic diagram labels, never prose. Numbers get `tabular-nums`.

**Mermaid**, themed to the tokens via the scaffold's **theme module** — it owns diagrams end to end: stores each `pre.mermaid` source at load, renders with `startOnLoad:false` for the active theme, and re-renders on toggle. Outline nodes, crisp arrows, readable labels. Diagrams sit in a `bg-well` panel, so every stroke must hold contrast against the well in **both** themes — dark: node borders `#a1a1aa` (never `#3f3f46`, invisible on the dark well), cluster fills `#1f1f23` (never `#131316`); light (mapped automatically): borders `#71717a`, cluster fills white (never `#f4f4f5`). Sequence diagrams inherit ugly defaults unless the actor variables are set — the module sets them per theme:

```html
<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  const FONT = { fontFamily: "IBM Plex Mono, ui-monospace, monospace", fontSize: "14px" };
  const DARK = { ...FONT,
    primaryColor: "#1f1f23", primaryTextColor: "#f4f4f5", primaryBorderColor: "#a1a1aa",
    lineColor: "#d4d4d8", tertiaryColor: "#18181b",
    clusterBkg: "#1f1f23", clusterBorder: "#71717a",
    actorBkg: "#1f1f23", actorBorder: "#a1a1aa", actorTextColor: "#f4f4f5", actorLineColor: "#71717a",
    signalColor: "#d4d4d8", signalTextColor: "#d4d4d8",
    labelBoxBkgColor: "#1f1f23", labelBoxBorderColor: "#a1a1aa", labelTextColor: "#f4f4f5", loopTextColor: "#f4f4f5",
    noteBkgColor: "#1f1f23", noteBorderColor: "#e0a458", noteTextColor: "#f4f4f5",
    edgeLabelBackground: "#131316" };
  const LIGHT = { ...FONT,
    primaryColor: "#ffffff", primaryTextColor: "#18181b", primaryBorderColor: "#71717a",
    lineColor: "#3f3f46", tertiaryColor: "#ffffff",
    clusterBkg: "#ffffff", clusterBorder: "#a1a1aa",
    actorBkg: "#ffffff", actorBorder: "#71717a", actorTextColor: "#18181b", actorLineColor: "#a1a1aa",
    signalColor: "#18181b", signalTextColor: "#18181b",
    labelBoxBkgColor: "#ffffff", labelBoxBorderColor: "#71717a", labelTextColor: "#18181b", loopTextColor: "#18181b",
    noteBkgColor: "#ffffff", noteBorderColor: "#b45309", noteTextColor: "#18181b",
    edgeLabelBackground: "#ffffff" };
  const HEX = { "1f1f23": "#ffffff", "f4f4f5": "#18181b", "73b87e": "#047857", "e0a458": "#b45309", "e06060": "#b91c1c" };
  const toLight = (s) => s
    .replace(/#(1f1f23|f4f4f5|73b87e|e0a458|e06060)\b/gi, (_, h) => HEX[h.toLowerCase()])
    .replace(/rgba\(115, 184, 126, 0\.12\)/g, "rgba(4, 120, 87, 0.09)");
  const graphs = [...document.querySelectorAll("pre.mermaid")];
  for (const g of graphs) g.dataset.graph = g.textContent;
  const KEY = "pr-canvas-theme", root = document.documentElement;
  async function render() {
    const light = root.dataset.theme === "light";
    mermaid.initialize({ startOnLoad: false, securityLevel: "loose", theme: "base", themeVariables: light ? LIGHT : DARK });
    for (const g of graphs) { g.removeAttribute("data-processed"); g.textContent = light ? toLight(g.dataset.graph) : g.dataset.graph; }
    if (graphs.length) await mermaid.run({ nodes: graphs });
    for (const btn of document.querySelectorAll("[data-theme-toggle]")) btn.textContent = light ? "Dark" : "Light";
  }
  for (const btn of document.querySelectorAll("[data-theme-toggle]"))
    btn.addEventListener("click", () => {
      if (root.dataset.theme === "light") delete root.dataset.theme; else root.dataset.theme = "light";
      try { localStorage.setItem(KEY, root.dataset.theme || "dark"); } catch {}
      render();
    });
  render();
</script>
```

`classDef`s are **outline-only** — chrome fill (`#1f1f23`), token stroke, bright label. Never a raw tinted fill like `fill:#ecfdf5` (an emerald-50 tell), never a white/light fill authored by hand. Author every diagram hex in the **dark palette only**; the theme module maps fills, strokes, and highlight rects to the light palette at render time:

```
classDef new  fill:#1f1f23,stroke:#73b87e,stroke-width:1.5px,color:#f4f4f5;
classDef mod  fill:#1f1f23,stroke:#e0a458,stroke-width:1.5px,color:#f4f4f5;
classDef prob fill:#1f1f23,stroke:#e06060,stroke-width:1.5px,color:#f4f4f5;
```

**Code highlighter** — the third paste-verbatim block, also in `<head>`. Code lines in "Under the hood" and call shapes in "How you'd use it" are authored as **plain escaped text** after the gutter span; this script tokenizes that text into the `syn-*` spans at load. Never hand-write per-token spans — hand-authored highlighting is exactly how code ships half-highlighted. The script only touches text nodes inside `pre code .block:not(.ann)`, so Mermaid blocks, gutters, annotation rows, and prose are untouched, and a misclassified token still renders in a legible token color.

```html
<script type="module">
  const KW = new Set("abstract as async await case catch class const def del elif else enum except export extends fn for from func function if impl implements import in interface is lambda let match new not of or override pass private protected pub public raise readonly return self static struct super this throw trait try type typeof union use var where with yield".split(" "));
  const PRIM = new Set("Any None True False Self bool bytes dict float frozenset int list object set str tuple any bigint boolean false never null number string symbol true undefined unknown void byte error rune i8 i16 i32 i64 u8 u16 u32 u64 f32 f64 isize usize".split(" "));
  const TOKEN = /(\s+)|("(?:[^"\\]|\\.)*"?|'(?:[^'\\]|\\.)*'?|`(?:[^`\\]|\\.)*`?)|(\d[\w.]*)|([A-Za-z_$][\w$]*)|(->|=>|[^\sA-Za-z_$\d]+)/g;
  const span = (cls, text) => Object.assign(document.createElement("span"), { className: cls, textContent: text });
  for (const line of document.querySelectorAll("pre code .block:not(.ann)")) {
    for (const node of [...line.childNodes]) {
      if (node.nodeType !== Node.TEXT_NODE || !node.textContent.trim()) continue;
      const text = node.textContent, out = document.createDocumentFragment();
      let m, prev = "";
      while ((m = TOKEN.exec(text))) {
        if (m[1]) { out.append(m[1]); continue; }
        if (m[2]) out.append(span("syn-str", m[2]));
        else if (m[3]) out.append(span("syn-num", m[3]));
        else if (m[4]) {
          const next = (text.slice(TOKEN.lastIndex).match(/^\s*(\S)/) || [])[1];
          const typePos = /(?:->|=>|[:|<&])$/.test(prev) || ["extends", "implements", "as", "new", "impl"].includes(prev);
          out.append(span(
            KW.has(m[4]) ? "syn-kw"
            : typePos || PRIM.has(m[4]) || /^[A-Z]/.test(m[4]) ? "syn-type"
            : next === "(" ? "syn-fn"
            : "syn-param", m[4]));
        } else out.append(span(/^[()[\]{}.,;]*$/.test(m[5]) ? "syn-punc" : "syn-op", m[5]));
        prev = m[0];
      }
      node.replaceWith(out);
    }
  }
</script>
```

**Copy buttons** — the fourth paste-verbatim block, also in `<head>`. Any element with a `data-copy` attribute becomes a one-click copy control; the scaffold script owns the behavior — never hand-roll per-button JS. Used on the "Ask your agent" items, where the payload is a complete instruction ready to paste at the coding agent — file paths included, expected outcome stated — so a concern becomes an agent task in one click.

```html
<script type="module">
  for (const btn of document.querySelectorAll("[data-copy]")) {
    btn.addEventListener("click", async () => {
      await navigator.clipboard.writeText(btn.dataset.copy);
      const prev = btn.textContent;
      btn.textContent = "Copied";
      setTimeout(() => (btn.textContent = prev), 1200);
    });
  }
</script>
```

## Scaffold

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>PR Canvas — {{subject}}</title>
    <!-- Inter + IBM Plex Mono + Tailwind + token <style> block (above) -->
    <!-- theme module (Mermaid + toggle) + code highlighter + copy-button script (above) -->
  </head>
  <body class="antialiased">
    <main class="isolate mx-auto max-w-4xl px-6 py-10 space-y-10">
      <header>...</header>
      <section id="at-a-glance">...</section>
      <section id="big-picture">...</section>
      <section id="changes" class="space-y-8">...</section>
      <section id="usage">...</section>
      <section id="under-the-hood">...</section>
      <section id="mechanical">...</section>
      <section id="checks">...</section>
    </main>
  </body>
</html>
```

Section headings are `h2.text-lg.font-semibold.tracking-tight.text-primary` with a hairline underline — `border-b border-hairline pb-2` on the heading — the section divider that keeps a long dark page scannable (dense, tool-like — not `text-2xl` landing-page scale). Card titles are `h3.text-base.font-semibold.text-primary`, no underline.

## Header

The title is a **sans-serif narrative headline**, not a mono log line: `h1` with `text-xl sm:text-2xl font-semibold tracking-tight text-balance text-primary`, stating the outcome in plain words — "Eve agents can now report to telemetry.dev", "Saving settings no longer rewrites the whole config" — never a chain of symbol names, never structure-speak ("three entry points over one seam"). A code identifier inside the title may sit in `font-mono` at the same size. Under it, one mono meta line in `text-sm text-tertiary`: `{{short-hash}} · {{repo}} · {{date}}` — a branch delta shows `{{branch}} → {{base}}` in place of the hash; say here if uncommitted work is included.

Then the stats as **numeric pills** — mono numbers on a `currentColor` tint, the one sanctioned pill (change types stay `chip`s): `pill text-tertiary` for files/commits, `pill text-accent` for added, `pill text-danger` for removed. The theme toggle sits top-right of the header, on the title row: `<button class="navlink font-mono text-sm" data-theme-toggle></button>` — the scaffold's theme module sets its label ("Light"/"Dark") and behavior; never hand-roll toggle JS.

```html
<header class="space-y-4">
  <div class="flex items-start justify-between gap-4">
    <div class="space-y-1.5">
      <h1 class="text-xl font-semibold tracking-tight text-balance text-primary sm:text-2xl"><span class="font-mono">lib/text-measure</span> extracted from typist</h1>
      <p class="font-mono text-sm text-tertiary">499c1aef · duncan/typist · 2026-07-05 · includes uncommitted work</p>
    </div>
    <button class="navlink font-mono text-sm" data-theme-toggle></button>
  </div>
  <div class="flex flex-wrap items-center gap-2">
    <span class="pill text-tertiary">28 files</span>
    <span class="pill text-accent">+3,985</span>
    <span class="pill text-danger">−1,240</span>
    <span class="pill text-tertiary">12 commits</span>
  </div>
</header>
```

Legend is a compact mono caption row, dots not boxes: green = new, amber = changed, red = removed / problem, blue = link. **`text-sm text-tertiary` minimum — never `text-xs`**; if the legend shares a row with anything else and reads cramped, use two lines rather than shrinking. No intro paragraph.

## What this changes

Two sentences maximum, `text-secondary text-pretty max-w-[70ch]`: first, what the product can do now or does differently — "Agents built on eve can now send everything they do to telemetry.dev"; second, at most, how it lands in one plain clause — "a new package does the connecting on the agent side, and the server now understands what eve sends". No jargon, no mechanism narration — a reader who stops here must still leave with the right one-line story.

Then a row of quiet change-type chips (the `chip` dot style above):

```html
<div class="flex flex-wrap gap-2">
  <span class="chip text-accent">new package</span>
  <span class="chip text-accent">new capability</span>
  <span class="chip text-warn">behavior change</span>
</div>
```

Types — plain words only: `new package` / `new capability` / `API change` / `behavior change` / `data change` / `code moved` / `wiring change` / `removed` / `nothing structural`. Accent hue for additive (new package / new capability), warn for changes to existing behavior or shape (API / behavior / data / moved / wiring), tertiary for `removed` and the honest `nothing structural`. If `nothing structural` applies, say so here, render "Also in this change", and stop — never inflate churn into a story.

## The big picture

One small Mermaid flowchart in a well answering exactly one question: **where does this change sit on the path from an action the reader can see to the result they care about?** Start from the actor or action they know ("your script", "a user saves settings") and end at the visible outcome ("the dashboard shows the turn").

Hard rules — this map confuses fastest when it tries to say two things:

- **≤5 boxes.** Every box is something the reader can point at: an app, a service, a package, a screen, an outside system. Never a mechanism ("global provider", "one-shot init"), never a pattern name.
- **One flow, one direction** (left→right). No dashed setup/registration edges alongside solid data edges — how a thing gets wired up is a card's story, never the map's. Edge labels ≤3 plain words, and only where the arrow alone is ambiguous.
- **Changed pieces highlighted** via the outline `classDef`s (accent = new, warn = updated, `.removed` faded = removed), marked in the label ("(new)").
- **Caption above, one sentence** in `text-sm text-secondary` stating the takeaway ("the new connector rides inside the agent and forwards everything to telemetry.dev; the receiving side was taught to read it").
- If an honest map needs more than 5 boxes, it's zoomed out too far — cut to the neighbourhood the change actually touches.

```html
<div class="rounded-md bg-well p-4">
  <pre class="mermaid">
flowchart LR
  S["your script"] --> A["eve agent"]
  A --> P["@telemetry-dev/eve (new)"]
  P --> I["telemetry.dev ingest (updated)"]
  I --> D["dashboard"]
  classDef new fill:#1f1f23,stroke:#73b87e,stroke-width:1.5px,color:#f4f4f5;
  classDef mod fill:#1f1f23,stroke:#e0a458,stroke-width:1.5px,color:#f4f4f5;
  class P new
  class I mod
  </pre>
</div>
```

## Change card

One `<article>` per real change — an elevated hairline-ring block on canvas. Visuals carry the weight where a picture fits; prose is plain and sparse either way.

```html
<article class="space-y-4 rounded-md bg-chrome p-6 ring-hairline">…</article>
```

- **Title** — `h3.text-base.font-semibold.text-primary`, the change as an outcome in product terms ("You can now time whole conversations from the calling side"), never structure-speak ("client proxy over the session seam").
- **Label row** — the change-type `chip`(s).
- **Files** — `font-mono text-sm text-secondary`. New `+` in accent, deleted `−` in danger, moved `→`; line counts in `text-tertiary`. These are anchors the reader pastes to their agent; any annotation after a path is a plain-words phrase ("the timer lives here"), never a chain of function names.
- **Before / After** — the centrepiece when the change has a picture worth drawing. Stacked full-width well panels with narrative bold headings (`p.font-medium.text-primary`) that a user of the app would confirm as true sentences — `Before: the agent records what it does, but the records go nowhere` / `After: records flow to telemetry.dev, wired up in any of three places`. Never a bare "Before"/"After" label. Each panel holds whichever representation fits *this* change ("Pick the representation before drawing") — a flow or sequence diagram, a UI wireframe pair, a record strip; a change whose picture would only restate one changed word gets **no panels**, and the What changed sentences lead the card instead. Side-by-side only when both panels are small enough to read unscrolled. Feature with no before-state → one panel: `How it works`.
- **Diagram caption** — for any diagram with more than ~6 nodes/messages, one or two `text-secondary` sentences directly under the well saying how to read it and what to notice. Small diagrams whose heading already says it all can skip this.
- **What changed** — ≤3 sentences, `text-secondary` with `<span class="font-medium text-primary">What changed</span> —` lead-in: what a user or caller experiences differently, every term of art glossed inline. Keep the concrete specifics — counts ("18 kinds of events"), semantics ("first one wins"), public names once in parens — they are what make the story credible.
- **Why it matters** — one sentence, the practical consequence ("because the connector only points and never translates, most eve updates won't touch it") — not praise.
- **Effects** — `<ul role="list">`, `text-sm text-secondary`, ≤4 bullets. Each a complete standalone sentence that passes the friend test; budgets are ceilings, not quotas.
- **Watch out** (if applicable) — one line in a warn-tinted well (`rounded-md p-3 text-sm text-warn tint-warn`), phrased as the consequence the reader would observe ("if your app already sets up OpenTelemetry, eve's records silently go there and the dashboard stays empty").

Prose stays lean but never cryptic. If a diagram needs a paragraph to be understood, redraw it; if the flow is genuinely that involved, keep the diagram simple and put the nuance in the caption sentence.

## Visual patterns

The representation-fit rules above decide *which kind* of evidence a card gets (or none); these patterns are how each kind is drawn. Pick the pattern that fits; mix them; don't make every visual look the same. All nodes outline-only per the `classDef`s above. **Node labels and messages are plain words** (see "Write for the person who steers the agents") — a node is "the timer that rides the stream", not `_InstrumentedStream`; a message is "replies stream back", not `stream_chunk_builder(chunks, messages)`. The one exception: a public call the reader's own script writes (`send(message)`) is welcome. Every visual must also pass the glance test above — a real scenario of this app, one idea, within budget, stable before/after layout.

### Mermaid graph (workhorse for dependency changes)
`flowchart` in a well. Node labels stay plain and pointable; `subgraph` containers with tertiary caps labels may group a product area (`DASHBOARD`, `AGENT PROCESS`); classDef **outlines** — accent for new/consolidated, danger for problem nodes in Before, `.removed` for removed; short edge-label pills only where the arrow alone is ambiguous (`reads`, `saves to`). Sequence diagrams for flow changes ("before: 6 round-trips; after: 1"; "before: waits; after: queued") — cap a sequence diagram at ~8 messages; beyond that, split the story or collapse repeated exchanges into one labelled message ("replies stream through, each forwarded to your script").

For **flow changes**, the changed subpath must be visually marked, not just described in the surrounding prose — an unmarked flow diagram is an unfinished flow card. The marks are token-derived and fixed:

- **Flowcharts** — accent outline (`classDef new`) on the changed node(s), and the changed edge(s) in accent via `linkStyle`: `linkStyle 2,4 stroke:#73b87e,stroke-width:2.5px`. Edge indexes count every edge in declaration order, zero-based — count them, don't guess. Unchanged edges stay on the default line color.
- **Sequence diagrams** — wrap the changed messages in `rect rgba(115, 184, 126, 0.12)` … `end` (the accent at ~12% opacity; never a raw palette fill like `rgb(236, 253, 245)` — that is emerald-50, tell #4) with a `Note` inside naming the changed path.
- **Caption** — the sentence under the well points at the mark ("the highlighted segment is the changed part: the timer now stops after the last reply, not when the call returns").

If Mermaid cannot highlight the exact segment cleanly, use hand-built boxes-and-arrows instead of accepting an unmarked flow.

### UI wireframe pair (when the screen itself is the story)
When what changed is what the reader sees — layout, components appearing or moving, a new visible state — and a picture of the screen beats any diagram about it, the Before/After panels hold **simplified wireframes of the real UI**, hand-built from the token system inside the usual `bg-well` panel. Rebuild each side from the actual markup (base side for Before) — real section order, real labels and button text, real placement — never from memory or a generic app shape. Keep it schematic:

- The screen or component is a `bg-chrome ring-hairline rounded-md` frame; regions inside are hairline-divided rows or nested `bg-well rounded-sm` blocks.
- Real text where it identifies (button labels, tab names, headings) in `text-xs text-secondary`; filler content as skeleton bars: `<div class="h-2 w-24 rounded-sm" style="background:var(--hairline)"></div>`.
- The delta wears the usual hues: a new element gets an accent outline (`style="box-shadow:0 0 0 1.5px var(--accent)"`), a removed element stays in Before with `.removed`, a moved or restyled element gets a warn outline in After.
- Both frames share size and region layout so the delta pops from comparison alone — the stable before/after layout rule applies to wireframes too.
- A wireframe answers "what does this screen look like now vs before". If the screen looks the same and only what happens behind it changed, this is the wrong pattern.

```html
<div class="rounded-md bg-well p-4">
  <div class="mx-auto max-w-sm space-y-2 rounded-md bg-chrome p-3 ring-hairline">
    <div class="flex items-center justify-between">
      <span class="text-xs text-secondary">Export</span>
      <div class="h-2 w-10 rounded-sm" style="background:var(--hairline)"></div>
    </div>
    <div class="h-2 w-full rounded-sm" style="background:var(--hairline)"></div>
    <div class="flex gap-2">
      <div class="rounded-sm px-2 py-1 text-xs text-secondary ring-hairline">Copy link</div>
      <div class="rounded-sm px-2 py-1 text-xs text-accent" style="box-shadow:0 0 0 1.5px var(--accent)">Export as PDF</div>
    </div>
  </div>
</div>
```

### Screenshot pair (real pixels, when you have them)
When the change is visual and a real rendering exists — screenshots in the PR description, a before/after captured from the running app, a generated chart — real pixels beat a wireframe, and a wireframe beats a diagram about the screen. Embed each image **into the file** as a base64 data URI (`<img src="data:image/png;base64,…" alt="…">`) so the report stays one self-contained file — downscale to display size first, a full-resolution screenshot balloons the file; when the subject is a PR, the PR's own hosted image URLs are also fine. The `<img>` sits inside the usual `bg-well` panel with `rounded-md ring-hairline`, a one-sentence `text-sm text-secondary` caption under it naming what to look at. Before/after screenshots share width and crop so the delta pops; point at the changed region in the caption's words (or an accent-outlined overlay box) — never make the reader play spot-the-difference.

### Rendered diff (when the lines are the story)
For the rare change whose clearest evidence is the lines themselves — a one-line behavior fix, a flipped default, a config swap — a small diff can be a card panel instead of prose about the lines. Same markup as an Under-the-hood excerpt, promoted to a panel: `bg-chrome ring-hairline` block with a mono file-path header row, lines as plain escaped text after a `select-none text-faint` gutter, `diff-del`/`diff-add` line backgrounds, the scaffold highlighter coloring tokens automatically, and `.ann` margin rows carrying the plain-words meaning. Unified by default; split (two aligned columns, `grid grid-cols-2 divide-x divide-hairline`) only when line-for-line correspondence is the point. Budget ≤ ~20 lines — a diff that needs more belongs collapsed in Under the hood, not on a card.

### JSON tree (when the record is the story)
For data-shape changes, show the actual record as a collapsible tree — native nested `<details>`/`<summary>`, no script: objects and arrays are `<details>` (summary = key plus a `text-faint` count), primitives are inline rows. Mono `text-sm/6`; keys `syn-param`, strings `syn-str`, numbers `syn-num`, punctuation `syn-punc` — authored by hand here, and kept **outside `pre code`** so the scaffold highlighter leaves it alone. Open exactly the path to the changed leaves and collapse everything else; mark changed rows with `diff-add`/`diff-del` backgrounds (old value also `.removed`). Before/after records share key order; better yet, one tree with both rows. The tree shows the path to what matters, never the whole payload.

```html
<div class="rounded-md bg-well p-4 font-mono text-sm/6">
  <details open>
    <summary class="cursor-pointer text-secondary">block.json <span class="text-faint">· 8 keys</span></summary>
    <div class="border-l border-hairline pl-4">
      <div><span class="syn-param">"name"</span><span class="syn-punc">:</span> <span class="syn-str">"dialog-01"</span></div>
      <div class="diff-del"><span class="syn-param">"style"</span><span class="syn-punc">:</span> <span class="syn-str removed">"new-york"</span></div>
      <div class="diff-add"><span class="syn-param">"style"</span><span class="syn-punc">:</span> <span class="syn-str">"base-nova"</span></div>
      <details>
        <summary class="cursor-pointer text-secondary">"dependencies" <span class="text-faint">· 3 items</span></summary>
        <div class="border-l border-hairline pl-4">…</div>
      </details>
    </div>
  </details>
</div>
```

### Run trace (when an agent run is the story)
When the change alters what an agent or pipeline *does* over a run — a step added, removed, reordered, or made cheaper — show the run as a **step timeline**: an `<ol>` against a hairline left border, one `<details>` per step; the summary is a mono `text-faint` step index, the step in plain words, and a `tabular-nums text-tertiary` duration on the right; the body is the detail (the call made, an output excerpt, a plain-words note). Collapse repeats into one step ("retries the upload ×12"). Hues as everywhere: accent for steps new in After, `.removed` for steps that are gone, warn for changed ones; before/after traces keep the same step order so the inserted or removed step pops. Cap ~10 visible steps.

```html
<ol class="space-y-1 border-l border-hairline pl-4">
  <li><details>
    <summary class="cursor-pointer text-sm text-secondary"><span class="font-mono text-faint">01</span> reads the config <span class="float-right font-mono text-xs text-tertiary tabular-nums">12 ms</span></summary>
    <div class="py-1 pl-6 text-sm text-secondary">…what happened in this step, in plain words, with the one call or excerpt that proves it…</div>
  </details></li>
</ol>
```

### Hand-built boxes-and-arrows (when Mermaid's layout fights you)
Modules as token-styled `<div>`s (`bg-chrome ring-hairline`), arrows as inline SVG over a relative container. Reach for this when "after" needs to feel like one deep block with faded internals — Mermaid won't render that weight.

### Cross-section (layer changes)
Stack horizontal bands (`h-12 border-l-4 border-hairline`) for the layers a call threads. Before: 5 bands. After: 3, removed ones faded and struck (`.removed`).

### Call-graph collapse / explode
Before: a tree of calls as nested blocks. After: collapsed into one with internals faded — or the reverse for an extraction: one block explodes into two with a new labelled connection point.

## How you'd use it

Only when the change adds or changes something the reader — or their agent, on their behalf — will call, run, or configure: new functions, commands, hooks, options, environment variables, endpoints. One elevated block per item, or one block with a short list, whichever reads lighter.

Each entry: a **chip** (`text-accent` new / `text-warn` changed / `text-tertiary` removed), the name in mono `text-primary` with its file path in mono `text-tertiary` beside it, and **one plain sentence** — what it does and when it runs, phrased as advice ("wrap the client your script already uses; every message you send becomes one timed record"). Optionally, below the sentence, the **call shape** — the one line the reader's script (or their agent) would actually write, as plain escaped mono text auto-highlighted by the scaffold's code highlighter. Call shape, never type signature — parameter names only, no type annotations, no return types, no diffs.

```html
<article class="overflow-hidden rounded-md bg-chrome ring-hairline">
  <div class="flex items-center gap-2 border-b border-hairline px-4 py-2">
    <span class="chip text-accent">new</span>
    <span class="font-mono text-sm text-primary">wrapEveClient</span>
    <span class="font-mono text-sm text-tertiary">packages/eve/src/client.ts</span>
  </div>
  <p class="px-4 py-3 text-sm text-secondary">Wrap the eve client your script already uses; every message you send becomes one timed record — duration, time to first reply, token usage, and how the turn ended.</p>
  <pre class="border-t border-hairline px-4 py-2.5 font-mono text-sm text-secondary"><code><span class="block">const client = wrapEveClient(createClient(), options?)</span></code></pre>
</article>
```

Nothing user-facing changed → skip the section entirely.

## Under the hood

The plain layer tells the story; this section holds the proof — the few lines of real code that carry the change, annotated in plain words. **Collapsed by default and always optional: the reader never needs it to follow the report; it exists for the curious glance and for grounding what the agent gets asked.** Budget: 2–4 excerpts, each ≤ ~20 lines — the line that does the connecting, the block where the risky behavior lives, the table entry that changed. If the cards already show everything, skip the section entirely.

One `<details>` per excerpt, each with an `id` so check-order steps can link to it. The `<summary>` is the plain-words takeaway — "The connector runs once; whoever gets there first wins" — with the mono `path · lines a–b` beside it, linked to the PR Files tab when the subject is a PR: `{{pr-url}}/files#diff-{{sha256-of-path}}` (the anchor is the sha256 hex of the file path — `printf '%s' "src/foo.ts" | shasum -a 256`). A reader who never opens a single one still gets every takeaway from the summaries alone.

Inside: code lines as plain escaped text after a `select-none text-faint` gutter span, `diff-add`/`diff-del` backgrounds where the excerpt is a diff, auto-highlighted by the scaffold's code highlighter. Annotations are **`.ann` margin-note rows interleaved directly under the line they explain** — one plain sentence saying what the line means, never a restatement of the code; `ann ann-warn` for "this is the part to verify". Two or three annotations per excerpt maximum — the annotations carry the meaning; the code is evidence.

```html
<details id="uth-once">
  <summary class="cursor-pointer rounded-md bg-chrome px-4 py-2.5 text-sm ring-hairline">
    <span class="font-medium text-primary">The connector runs once — whoever gets there first wins</span>
    <a class="ml-2 font-mono text-link" href="{{pr-url}}/files#diff-{{sha256}}">packages/eve/src/config.ts · 17–31</a>
  </summary>
  <pre class="mt-2 overflow-x-auto rounded-md bg-chrome p-4 font-mono text-sm/5 text-secondary ring-hairline"><code><span class="block diff-add"><span class="select-none text-faint">21 </span>  if (initialized) return;</span><span class="block diff-add"><span class="select-none text-faint">22 </span>  initialized = true;</span><span class="block ann">runs once on purpose — a process can only claim its telemetry destination once, so a second setup with different options could never take effect anyway</span></code></pre>
</details>
```

## Also in this change

One collapsed `<details>` in a well (`rounded-md bg-well px-4 py-3 text-sm text-secondary`), summary in `text-primary`, listing the churn in plain words: "Also here: a 28-file runnable example app, a README rewrite, and a lockfile bump." Never a card; never inflated into a story.

## What to check

One elevated block (`rounded-md bg-chrome p-6 ring-hairline`). This is where the report turns into the reader's next move — never a "go read the diff" path (the reader won't read code; their agent will). Four parts, in this order:

- **Quality read** — one or two plain sentences: does the shape look sound, and where is the soft spot ("the complicated parts hide behind three one-line entry points; the one fragile spot is the client wrapper's reliance on a piece eve marks internal"). No "deep/shallow module" vocabulary — say what it means instead.
- **Check in this order** — a numbered list (≤5 steps) walking the concerns in the order that settles them fastest: the load-bearing decision first, then the risky behavior, then the edges. Each step is one line — where to look, linked to its change card or under-the-hood excerpt, and what should be true, in plain words ("the timer must stop on failures and cancels too, not just clean finishes"). Tag each step with a chip: `text-warn` "worth a close look" or `text-tertiary` "skim". End with the mechanical line ("everything else is the example app — trust the summary"). With only one or two concerns, fold them into Risks and skip the list.
- **Risks** — ≤3 bullets, each phrased as the consequence the reader would actually observe, trigger in plain words ("an eve update can quietly break the client wrapper — wrapped scripts just stop reporting turns"). A risk that can't be stated as an observable consequence isn't a risk, it's trivia.
- **Ask your agent** — ≤3 items, the report's exit ramp. Each is a one-line concern anchor-linked to its change card (`text-link underline decoration-hairline underline-offset-2`) plus a copy button (`<button class="navlink font-mono text-xs shrink-0" data-copy="…">Copy</button>`). The payload is a **complete, self-contained instruction to the coding agent**: file paths, what to check or change, and what outcome proves it — pasteable with zero editing. When the subject is someone else's PR, phrase the payload as a review comment instead and say so in the item.

## Final check

Before writing the file, scan the markup once:

1. **The friend test passes everywhere** — every sentence, heading, bullet, chip, diagram label, and excerpt summary would make sense said aloud to a smart friend who has never seen this codebase; every term of art is glossed on first use or gone; symbols appear only as mono anchors (once, in parens, or in files lines), never as the subject of a sentence.
2. **The plain layer reads complete in ~3 minutes with every collapsed block closed** — budgets respected (≤2-sentence summary; per card ≤3 what-changed sentences, ≤1 why, ≤4 bullets); headings + visuals alone tell the whole story top to bottom; nothing in the story depends on opening a `<details>`.
3. **Contrast ramp respected** — no readable sentence in `text-tertiary`/`text-faint`; the header title is sans-serif `text-primary` with its mono meta line `text-sm text-tertiary`; legend row is `text-sm text-tertiary` minimum; `text-faint` appears only on code-excerpt gutters and schematic diagram sublabels.
4. **Inter features on** — `html{font-feature-settings:…}` + `body{font-optical-sizing:auto}` present.
5. **No uppercase-mono pills** — change labels are `chip` + quiet dot, sentence-case; the only pill is the header's numeric stat.
6. **No palette drift** — no raw `slate`/`gray`/`stone`/`indigo`, no `bg-*-50`, no `rounded-lg`/`font-serif` defaults, every Mermaid node uses the outline-only token theme, and `--link` blue appears only on navigation/reference (paths, anchors, `ic`) — never as a semantic hue.
7. **Every panel earns its place and wears the right form** — representation chosen per change by fit (diagram, wireframe or screenshot pair, rendered diff, JSON tree, trace timeline, record strip — or no panel where the sentences carry it; never a panel whose only content is one swapped word); the big picture has ≤5 pointable boxes, one direction, no setup/registration edges, changed pieces highlighted, a one-sentence caption; every card visual traces a real scenario (Before = base-branch behavior, verified against the base side), one idea, within budget, before/after pairs share layout; flow changes mark the changed subpath in accent with a caption naming it.
8. **Under the hood stays optional and mechanical** — 2–4 excerpts, every one collapsed, each summary a plain-words takeaway that stands alone; code lines are plain escaped text after the gutter (no hand-written `syn-*` spans — the scaffold highlighter owns them); annotation rows carry `.ann` so the highlighter skips them, two or three per excerpt.
9. **The exit ramp is wired** — every in-report anchor (check-order steps, ask-your-agent links) resolves to a real id; every `data-copy` payload is a complete, self-contained agent instruction (file paths + expected outcome) pasteable with zero editing.
10. **Theme toggle works** — dark renders by default; the header's `data-theme-toggle` button flips to light and back, the choice persists across reloads (`localStorage` + the pre-paint snippet), and every diagram re-renders for the active theme. Mermaid hexes are authored in the dark palette only — the theme module owns the light mapping.
