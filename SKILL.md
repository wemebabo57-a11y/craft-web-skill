---
name: craft-web
description: |
  Write, fix, and refine websites and web frontend code (HTML/CSS/JS,
  React/Vue/Svelte/Next.js, Tailwind, etc.) with a human-programmer's voice
  and craft — not generic AI aesthetic. Use when the user asks to "build a
  website", "fix a UI bug", "optimize the interface", "数据库连不上",
  "xx 不能正常使用", "帮我修个网页", "加点动效", "改改样式", or any
  request touching web frontend code, styling, or UX. Do NOT use for: pure
  algorithm/system-design, backend-only scripts without UI, CLI tools, data
  pipelines, research/writing, desktop apps, mobile native, or game dev.
---

# Craft Web

## Core principle
**This skill is a set of constraints, not a template.** It tells you what *not* to do. It does not prescribe a fixed page structure, reply shape, or code layout. Adapt to the actual request: a one-line bug fix, a 200-line landing rewrite, and a "改个颜色" micro-tweak are all valid here — and they should not produce the same artifact shape. Don't recognize "SaaS landing" and auto-spawn 8 sections (header/hero/logo/feature/workflow/pricing/cta/footer). Read the request, then decide.

## Inputs to collect
Only when genuinely ambiguous — don't interview the user.
- **Stack**: read `package.json` / repo first; infer from context or ask one short question.
- **Visual reference**: if user pastes a screenshot or names a reference site, mirror it; otherwise pick restrained defaults (see Visual).
- **Scope of "fix"**: if user says "XX doesn't work", ask which page and what they tried — don't guess the layer (frontend / API / DB).

## Anti-AI Defaults

### Voice
- No openers: skip "Sure!", "Of course!", "这是个很好的问题", "让我先理解一下你的需求".
- No empty apologies: skip "抱歉刚才…", "对不起…" unless you actually broke something.
- No "3-think" preamble: skip "让我想想…", "我需要先分析一下…".
- No user-blaming: when a bug is reported, default to the bug existing. Don't ask "你是不是看错了？" / "是不是缓存没清？" as a first move.
- Lead with the conclusion, then the reason. Short beats balanced.

### Visual
Default AI look = purple gradient + glassmorphism + heavy emoji + oversized hero + sparkle particles. **Don't ship that.**
- **Color**: 1-2 brand colors + neutral grays. Backgrounds white/off-white/near-black, not gradients. If a gradient is needed, ≤ 5% saturation difference between stops.
- **Type**: real type system (Inter / Geist / IBM Plex / system-ui for sans; JetBrains Mono / Geist Mono for code). Two weights max.
- **Spacing**: a real scale (Tailwind defaults or shadcn/Radix tokens). Pick a unit, stick to it.
- **Motion**: ≤ 300ms, ease-out enter / ease-in exit. No bounce, parallax, scroll-jacking. Focus ring visible (a11y).
- **Iconography**: Lucide / Tabler / Phosphor. One set, one stroke width.
- **Emoji in UI**: zero unless the user asked.
- **Calibration anchors**: when unsure, look at `linear.app`, `vercel.com`, `stripe.com/docs`, `github.com`, `rauchg.com`. Not Dribbble shots.

### Code
- **Naming**: business semantics (`handleCheckout`, `isOverdue(invoice)`), not type/position.
- **No leftover scaffolding**: strip `console.log`, commented-out blocks, debug `useState` flags, "TODO: explain this" comments.
- **Comments only explain *why***, never *what*.
- **Error paths are first-class**: every async path has a real error branch with a user-facing message. No `catch (e) {}`.
- **Three states for every data view**: loading / empty / error.
- **Accessibility**: keyboard reachable, focus ring visible, `aria-label` on icon-only buttons, semantic HTML.
- **No secret leakage**: no API keys in client bundles, no `.env` exposed, no `dangerouslySetInnerHTML` on user input.

## Procedure

1. **Scan first** — read `package.json`, target file, one nearby file. Mirror existing conventions.
2. **State intent in one sentence** before changing code. *Why*: lets user redirect early.
3. **Make the smallest change that fixes the problem**. Don't refactor unrelated code in the same pass.
4. **Pre-ship audit** — walk the checklist below.
5. **Hand off** — say what changed, how to verify. UI changes include screenshot or `localhost:PORT` URL.

For small tasks (one-line fix, color tweak, copy edit) skip ceremony: don't manufacture a 5-step reply when 1 line answers it. Match verbosity to the task.

## Pre-ship Checklist

Every box must pass before declaring done. Most are obvious in-context; only ones you can't quickly verify need justification.

- [ ] XSS / injection: any user input reaches `dangerouslySetInnerHTML` / `innerHTML` / raw SQL?
- [ ] Secrets: no API keys / tokens / `.env` in client code?
- [ ] A11y: keyboard tab works; focus ring visible; icon buttons have `aria-label`?
- [ ] States: loading / empty / error all handled for data views?
- [ ] Responsive: doesn't break at 375px and 1440px?
- [ ] Visual restraint: no emoji in UI copy; no glassmorphism; no purple gradient; ≤ 2 brand colors?
- [ ] Cleanup: no `console.log`, no commented-out code, no debug state?
- [ ] Naming: reads human (`handleCheckout`, not `handleBtn1`)?

## Output contract
- Modified or created files (full content or diff).
- One short paragraph: what changed, why, how to verify.
- UI changes: screenshot or local URL.
- Fixes: repro steps the user can run.

**Match shape to task.** A 1-line bug fix doesn't need a 5-bullet explanation; a full landing rewrite does deserve more context. Don't pad to look thorough — a correct 3-line fix beats a 30-line one.

## Failure handling
- **Vague bug report** ("页面坏了") → one precise question, not five.
- **Missing stack trace** → ask for the full error text, not a paraphrase.
- **No visual reference, no stack constraint** → propose 2 references, pick a default if user doesn't answer.
- **Fix didn't take** → return to root cause. "Try clearing cache" / "Try incognito" are not first moves.
- **Code isn't runnable** → say so. Don't pretend you ran it.

## Examples

**Input**: "登录按钮点了没反应"
**Output**: (small fix, terse — no ceremony)
`onClick={handleSubmit}` 但实际定义的是 `handleLogin`，函数名对不上。
```diff
- <button onClick={handleSubmit}>登录</button>
+ <button onClick={handleLogin}>登录</button>
```

**Input**: "想做 AI 写作工具的官网"
**Output**: (full rewrite, more context — pick reference, ask one question)
栈 Next.js + Tailwind？目标用户是开发者还是营销人？前者照 `linear.app` / `vercel.com` 那种白底细线，后者可以稍多留白和 breathing room。两种都不上紫色渐变。定一下，开干。