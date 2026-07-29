---
name: annotate-screenshots
description: Capture beautiful retina screenshots with rose-red DOM-injected annotations (no image editing) for PR bodies and reviews. Use when the user wants screenshots for a PR, says "annotate screenshots", "add screenshots to the PR", "screenshot this page/feature", or wants before/after visuals of UI changes. Produces the consistent house style: 1440×900 @2x viewport, #e11d48 outline boxes with label chips, published to artifacts.duncan.land.
---

# Annotated screenshots (house style)

Screenshots are captured from a real browser at retina resolution. Annotations are **DOM elements injected into the live page before the shot** — never image post-processing. This keeps labels crisp at any zoom, perfectly aligned to the UI, and identical in style across every PR.

## The style (do not deviate)

- Rose red `#e11d48` everywhere. One accent color, always the same.
- Box: `3px solid #e11d48`, `border-radius: 10px`, soft halo `box-shadow: 0 0 0 4px rgba(225,29,72,0.15)`, 8px padding around the target's bounding rect.
- Chip: solid `#e11d48` background, white text, `600 13px/1.4 ui-sans-serif, system-ui`, `4px 10px` padding, `6px` radius, `white-space: nowrap`. Placed above the box by default; `bottom` or `right` when the top would clip or cover content.
- Label text: short noun phrases describing what's new — `New "agents" category`, `Install command`, not sentences.
- Max 2–3 annotations per shot. More = noise; take another shot instead.

## Workflow

### 1. Serve the production build

Never screenshot the dev server (HMR overlays, unminified flashes, dev banners). Build first, then serve the output via `hub start` (never a bash background job):

```
vp build   # or the project's build command
```

```
hub start:
  name: web-preview
  application: /bin/sh
  args: ["-c", "set -a; . ./.env.local; set +a; PORT=4823 exec node .output/server/index.mjs"]
  cwd: apps/web
  ready: { port: 4823, timeout: 30 }
```

Adapt the serve command to the project (any static/prod server works). Add dummy env vars if config validation demands them. Smoke-test with `curl -s -o /dev/null -w '%{http_code}'` before opening the browser.

### 2. Open a retina tab

This is what makes the shots beautiful — a 2x device scale factor yields 2880×1800 PNGs with razor-sharp text:

```
browser open:
  url: http://localhost:4823/<page>
  viewport: { width: 1440, height: 900, scale: 2 }
```

### 3. Inject the annotation helper

Run once per page load (navigation wipes it — redefine after every `tab.goto`):

```js
await tab.evaluate(() => {
  window.__annotate = (el, label, opts = {}) => {
    const r = el.getBoundingClientRect();
    const pad = opts.pad ?? 8;
    const box = document.createElement('div');
    box.className = '__annotation';
    Object.assign(box.style, {
      position: 'absolute',
      left: `${r.left + scrollX - pad}px`,
      top: `${r.top + scrollY - pad}px`,
      width: `${r.width + pad * 2}px`,
      height: `${r.height + pad * 2}px`,
      border: '3px solid #e11d48',
      borderRadius: '10px',
      boxShadow: '0 0 0 4px rgba(225,29,72,0.15)',
      zIndex: 99999,
      pointerEvents: 'none',
    });
    const chip = document.createElement('div');
    chip.textContent = label;
    const base = {
      position: 'absolute',
      background: '#e11d48',
      color: '#fff',
      font: '600 13px/1.4 ui-sans-serif, system-ui',
      padding: '4px 10px',
      borderRadius: '6px',
      whiteSpace: 'nowrap',
    };
    if (opts.chip === 'right') {
      Object.assign(chip.style, base, { left: '100%', top: '50%', transform: 'translateY(-50%)', marginLeft: '10px' });
    } else if (opts.chip === 'bottom') {
      Object.assign(chip.style, base, { top: '100%', left: '0', marginTop: '8px' });
    } else {
      Object.assign(chip.style, base, { bottom: '100%', left: '0', marginBottom: '8px' });
    }
    box.appendChild(chip);
    document.body.appendChild(box);
  };
  window.__clearAnnotations = () =>
    document.querySelectorAll('.__annotation').forEach(d => d.remove());
});
```

### 4. Target, annotate, settle, shoot

Find elements **semantically** — stable attributes and text content, never brittle CSS chains:

```js
await tab.evaluate(() => {
  const nav = [...document.querySelectorAll('nav a')].find(a => a.getAttribute('href') === '#tag-agents');
  const card = [...document.querySelectorAll('a[aria-label]')]
    .find(a => a.getAttribute('aria-label')?.includes('OpenClaw'))?.closest('div.relative');
  const install = [...document.querySelectorAll('pre')].find(p => p.textContent.includes('plugins install'));
  window.__annotate(nav, 'New "agents" category', { chip: 'right' });
  window.__annotate(card, 'New OpenClaw integration card', { chip: 'bottom' });
  card.scrollIntoView({ block: 'center' });
});
await new Promise(r => setTimeout(r, 400));   // let scroll + fonts settle
return await tab.screenshot();
```

Rules:
- `scrollIntoView({ block: 'center' })` the primary subject before shooting.
- Always wait ~400 ms after annotating/scrolling.
- Boxes are positioned with `scrollX/scrollY` at call time — annotate **after** any scrolling, or re-annotate if you scroll again.
- Full-page shots (`tab.screenshot({ fullPage: true })`) stay **unannotated** — they show the whole change; annotated shots zoom into specifics.
- `__clearAnnotations()` between different shots on the same page.

### 4b. Crop to the section when the full viewport adds nothing

A tight crop of the annotated section often beats a full-viewport shot — the reviewer only needs enough surrounding context to see what changed and where. Use viewport shots for layout-level changes (new nav category, moved sections); use crops for local changes (one config block, one button, one card).

Crop with a `clip` rect computed from the annotated element, expanded for context and the chip. Don't use `tab.screenshot({ selector })` — annotations live on `document.body`, outside the element, so a selector shot drops them:

```js
const rect = await tab.evaluate(() => {
  const el = [...document.querySelectorAll('pre')].find(p => p.textContent.includes('plugins install'));
  window.__annotate(el, 'Install command');
  const r = el.getBoundingClientRect();
  return { x: r.left + scrollX, y: r.top + scrollY, width: r.width, height: r.height };
});
await new Promise(r => setTimeout(r, 400));
const pad = { top: 72, side: 48, bottom: 48 };   // extra top room for the chip
const out = `${process.env.TMPDIR}/annotated-crop.png`;
await page.screenshot({
  path: out,   // raw page.screenshot returns a buffer; give it a path so the PNG lands on disk
  clip: {
    x: Math.max(0, rect.x - pad.side),
    y: Math.max(0, rect.y - pad.top),
    width: rect.width + pad.side * 2,
    height: rect.height + pad.top + pad.bottom,
  },
});
return out;
```

- `clip` is in CSS pixels; the 2x device scale still applies, so crops stay retina-sharp.
- Keep ~48px of context on each side (72px above for the chip). A crop so tight the reader can't tell what page they're on is too tight.
- One section per crop. Two distant annotated areas → two crops, not one tall shot.

### 5. Publish (content-type gotcha)

Upload PNGs to artifacts.duncan.land with an **explicit `Content-Type: image/png`** — the plain `artifacts push` CLI serves them as `text/html` and GitHub's camo proxy silently refuses to render them:

```bash
curl -sS -X PUT --data-binary @shot.png -H "Content-Type: image/png" \
  -H "Authorization: Bearer $(cat ~/.config/artifacts/token)" \
  https://artifacts.duncan.land/<slug>.png
```

Slug format: `<topic>-<what>.png`, e.g. `openclaw-integration-index.png`. Verify before embedding:

```bash
curl -sI https://artifacts.duncan.land/<slug>.png | grep -i '^content-type'   # must be image/png
```

See `skill://artifacts-publish` for auth details and slug rules.

### 6. Embed in the PR body

One `###` heading per shot; annotated shots say so in heading and alt text; full page goes last:

```markdown
## Screenshots

### Integrations index — new category + card

![Integrations index with new agents category and OpenClaw card annotated](https://artifacts.duncan.land/<slug>-index.png)

### Setup section — annotated

![Setup section with install command and config annotated](https://artifacts.duncan.land/<slug>-setup.png)

### Full page

![Full page](https://artifacts.duncan.land/<slug>-detail.png)
```

### 7. Clean up

Stop the preview server (`hub stop web-preview`) and close the tab (`browser close`) when done.
