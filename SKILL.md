---
name: craft-web
description: |
  Build, fix, or refine web frontend work the way a real maintainer would — read the actual request, decide the shape, ship code that looks like it was meant to live there for years. Triggers on "build a website", "fix this UI bug", "优化界面", "AI 味太重", "加点动效", "改改样式", "帮我做个登录页", "数据库连不上", or any web frontend ask touching HTML/CSS/JS, React/Vue/Svelte/Next.js, Tailwind. Not for pure algorithm questions, backend-only scripts, CLI tools, research/writing tasks, or native desktop/mobile.
---

# Craft Web

## TL;DR
A set of constraints, not a template. The skill tells the model what *not* to do and what *good* looks like — it does not prescribe a fixed page structure or reply shape. A one-line color tweak and a 200-line landing rewrite are both valid; they should not produce the same artifact.

---

## What this is

The most common AI failure on web work is **template matching**: see "SaaS landing" → auto-spawn hero / logo-bar / features / workflow / pricing / CTA / footer. This skill actively forbids that. Read the request, decide the structure, then write.

Three layers of constraint:
- **Visual** — how it looks (the deepest layer, most likely to slip)
- **Voice** — how the model talks about the work
- **Code** — how the code is written

If you only remember one thing: **the visual recipe below**. Everything else is housekeeping.

---

## Visual Recipe

Default AI look = purple gradient + glassmorphism + emoji in UI copy + oversized hero + sparkle particles. Don't ship that. The recipe below is the **constructive** counterpart — what to ship instead.

### Background and surface

Pick a paper color that isn't pure white. Pure `#FFFFFF` reads as "unfinished"; a tiny off-white reads as "designed".

| Mode | Paper | Ink | When |
|---|---|---|---|
| Light | `#FAFAF7` (warm cream) or `#F8F8F5` | `#0A0A0A` | Default for SaaS, docs, marketing |
| Light cool | `#F7F8FA` | `#0B0D12` | Dashboards, dev tools |
| Dark | `#0A0A0A` (not `#000`) | `#EDEDEA` | Dark mode default; never pure black |
| Dark rich | `#0E0F12` | `#E8E8E5` | Premium feel (Linear dark, Vercel) |

Borders and dividers: `rgba(0,0,0,0.08)` on light, `rgba(255,255,255,0.08)` on dark. Never solid 1px gray.

### Type system

Two faces max (sans + optional mono). Real weights, not faux-bold via CSS.

**Sans candidates (pick one family, use it everywhere)**:
- Inter / Geist / Inter Tight — default for SaaS, dashboards
- IBM Plex Sans — for dev tools, technical
- Söhne / Switzer — for premium (paid fonts)
- system-ui — only when zero asset budget

**Mono candidates (when code appears in UI)**:
- JetBrains Mono / Geist Mono / IBM Plex Mono

**Type scale (use this exact set unless there's a strong reason not to)**:

```
display    56–72px  / 600 weight / -0.02 to -0.04em tracking / line-height 1.05–1.1
h1         40–48px  / 600 / -0.015em / 1.1–1.2
h2         28–32px  / 600 / -0.01em  / 1.2–1.3
h3         20–24px  / 500–600 / -0.005em / 1.3
body       15–16px  / 400            / 1.5–1.6
caption    13–14px  / 500            / 1.4
mono       13–14px  / 400            / 1.5
```

Two weights is the rule of thumb; three is fine (400 / 500 / 600). Four is too many — pick the right weights before adding more.

### Color palette

The pattern: **1 brand + 1 accent + ink + paper + 2 grays**. That's 6. Anything more is chaos.

**Brand color**: a saturated but not neon value. AI defaults to purple → don't. Try ink-blue (`#1E40AF`), forest (`#166534`), or a muted coral (`#DC2626`). Saturation 60–80%, lightness 35–50%.

**Accent**: subtler than brand. Often a darker/lighter shade of brand or a near-neutral with a hint of hue.

**Grays**: one warm (`#737373` family), one cool (`#6B7280` family). Use warm for paper-on-light UIs, cool for dev tools and dashboards.

Recipe example (Linear-style):
```
brand    #5E6AD2    (a real purple-blue, not Tailwind purple-600)
accent   #4F46E5
ink      #0A0A0A
paper    #FAFAF7
gray-w   #737373
gray-c   #6B7280
```

Recipe example (Vercel-style):
```
ink      #000000
paper    #FFFFFF
accent   #0070F3
gray     #666666
```

### Spacing

4-base scale, used consistently:
```
4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 / 96 / 128 / 160 / 192
```

Section-to-section vertical rhythm: **96–160px**. Inside a section: **24–48px** between groups, **8–16px** between siblings. Hero to first content block: **64–96px**.

Tightness is a design choice, not a default. Linear is dense. Vercel is roomy. Pick one and apply it everywhere — mixed density reads as inconsistent.

### Density

Three levels, chosen per surface:

- **Compact** (Linear, dashboard, data table): row height 32–36px, padding 8–12px, font 13–14px. Use when users scan many rows.
- **Medium** (Notion, app shell): row 40–48px, padding 12–16px, font 15px. Default for most apps.
- **Loose** (Vercel docs, marketing hero, blog): padding 24–48px, font 16–18px, line-height generous. Use for reading surfaces.

### Motion

- ≤ 300ms. Default 150–200ms.
- ease-out for enter, ease-in for exit. ease-in-out only for state flips.
- No bounce. No parallax. No scroll-jacking.
- Focus ring is motion: visible on `:focus-visible`, 2px offset, brand color at 40% alpha.

### Iconography

One set, one stroke width, throughout:
- **Lucide** (default, 1.5px stroke) — most things
- **Tabler** (2px stroke) — denser icons
- **Phosphor** (regular weight) — when more visual presence needed

Never mix. Never reach for emoji to substitute an icon.

### Components that signal "real"

These are the markers a real maintainer uses; AI defaults miss them:
- **Real focus ring** (not just `outline: none`)
- **Hover states** that move slightly (1px translate, subtle bg shift) — not just color flip
- **Active/pressed** state distinct from hover
- **Disabled** state with reduced opacity AND a `cursor: not-allowed`
- **Skeleton loaders** matching real layout (not generic gray rectangles)
- **Empty states** with a small illustration or icon + a real next-step CTA, not "No data"
- **Error states** with what went wrong + what to do, not a stack trace
- **Keyboard shortcuts** shown in tooltips on hover (⌘K, etc.)

---

## Voice

- No openers: skip "Sure!", "Of course!", "这是个很好的问题", "让我先理解一下你的需求".
- No empty apologies: skip "抱歉刚才…" unless you actually broke something.
- No "let me think" preamble: skip "让我想想…" / "我需要先分析一下…".
- No user-blaming on bug reports: default to the bug existing. "你是不是清缓存了？" is not a first move.
- Lead with the conclusion, then the reason. One sentence beats five balanced ones.
- Match verbosity to the task. A one-line bug fix doesn't get a five-bullet explanation. A full landing rewrite does.

---

## Code

- **Naming**: business semantics. `handleCheckout`, `isOverdue(invoice)`, not `handleBtn1` or `processData2`.
- **No leftover scaffolding**: strip `console.log`, commented-out blocks, debug `useState` flags, "TODO: explain this" comments.
- **Comments only explain *why***, never *what*. Code says what; comments say why it had to be that way.
- **Error paths are first-class**: every async path has a real error branch with a user-facing message. No `catch (e) {}`.
- **Three states for every data view**: loading / empty / error. All three rendered.
- **Accessibility**: keyboard reachable, focus ring visible (see Visual Recipe), `aria-label` on icon-only buttons, semantic HTML (`<button>` not `<div onClick>`).
- **No secret leakage**: no API keys in client bundles, no `.env` exposed, no `dangerouslySetInnerHTML` on user input.

---

## Procedure

1. **Scan first**. Read `package.json`, target file, one nearby file. Mirror existing conventions.
2. **State intent in one sentence** before changing code. *Why*: lets the user redirect early.
3. **Make the smallest change that fixes the problem**. Don't refactor unrelated code in the same pass.
4. **Pre-ship audit** — walk the checklist below.
5. **Hand off**. Say what changed, why, how to verify. UI changes include screenshot or `localhost:PORT` URL.

Small tasks (one-line fix, color tweak, copy edit) skip the ceremony: don't manufacture a 5-step reply when 1 line answers it.

---

## Pre-ship Checklist

Every box must pass before declaring done. Most are obvious in-context; only ones you can't quickly verify need justification.

- [ ] XSS / injection: any user input reaching `dangerouslySetInnerHTML` / `innerHTML` / raw SQL?
- [ ] Secrets: no API keys / tokens / `.env` in client code?
- [ ] A11y: keyboard tab works; focus ring visible; icon buttons have `aria-label`?
- [ ] States: loading / empty / error all handled for data views?
- [ ] Responsive: doesn't break at 375px and 1440px?
- [ ] Visual restraint: no emoji in UI copy; no glassmorphism; no purple gradient; recipe from Visual section followed?
- [ ] Cleanup: no `console.log`, no commented-out code, no debug state?
- [ ] Naming: reads human (`handleCheckout`, not `handleBtn1`)?

---

## Calibration anchors — what to learn from each

Don't just visit these sites; learn the **specific thing** each one is good at.

- **linear.app** — density control and typographic rhythm. The compact data UI is a masterclass. Right column width matters. Button height consistency.
- **vercel.com** + **vercel.com/docs** — restrained black/white palette, generous spacing, hierarchy through size not color. The hero is the entire brand.
- **stripe.com/docs** — code samples that look like real code (not code blocks with `lorem ipsum`). Sidebar navigation as design object. Table layouts.
- **github.com** — functional UI where visual restraint serves usability. The "real maintainer" baseline: ship what's needed, nothing decorative.
- **rauchg.com** — prose-first personal site: typography, line-length, vertical rhythm. The opposite of marketing-template.
- **geist-ui.dev** — when you need a component library that already follows this recipe (Geist + Geist Mono + the spacing scale above).

Not Dribbble. Not "AI landing page inspiration" boards. Those are how AI defaults get trained.

---

## Examples

### Input: "登录按钮点了没反应"
**Output** (small fix, terse — no ceremony):

`onClick={handleSubmit}` 但实际定义的是 `handleLogin`，函数名对不上。
```diff
- <button onClick={handleSubmit}>登录</button>
+ <button onClick={handleLogin}>登录</button>
```

### Input: "想做 AI 写作工具的官网"
**Output** (full rewrite, more context — pick reference, ask one question):

栈 Next.js + Tailwind？目标用户是开发者还是营销人？前者照 `linear.app` / `vercel.com` 那种白底细线 + 紧凑层级，后者可以稍多留白和 breathing room。两种都不上紫色渐变。定一下，开干。

### Input: "优化这个页面，AI 味太重"
**Output** (apply the recipe, be concrete):

之前大概率是 purple gradient hero + 居中大标题 + emoji 装饰 + 三个 feature card + 一个 pricing 表 + 一个 footer CTA。这套组合直接拆掉。

按 Visual Recipe 重排：cream paper `#FAFAF7`、Inter Tight 标题 56px / -0.03em tracking、Linear-style 紧凑导航、h2 段间距 96px、feature 用 2 列 grid 而不是 3 列等宽 card。组件列表见上方「components that signal real」。

具体怎么改贴一下当前 HTML / 截图，我直接出 diff。

---

## What this skill is not

- Not a design system. Don't install shadcn just because this skill mentions it.
- Not a Tailwind preset. The recipe is portable to vanilla CSS, CSS Modules, Vue, whatever.
- Not a substitute for user judgment. When the user has a strong preference (e.g., "match our brand book"), follow that — the recipe is a default, not a law.