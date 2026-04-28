# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Olivia's State Quest** — a US states learning PWA for a child to play on mobile. The player taps the correct state on an interactive SVG map when prompted.

## No Build System

This is a dependency-free static site. There is no `package.json`, no npm, no bundler, and no transpiler. All app code lives in `index.html`.

**To deploy changes**: `git add . && git commit -m "..." && git push` — GitHub Pages auto-redeploys in ~30 seconds.

**To force installed PWAs to update**: Bump the cache version string in `sw.js` (e.g., `state-quest-v1` → `state-quest-v2`). Without this, service-worker-cached clients won't pick up changes.

## Architecture

Everything is in `index.html` (~1,450 lines):
- **CSS** — embedded in `<head>` using CSS custom properties for theming. Responsive via CSS Grid with `grid-template-areas` (portrait vs. landscape layouts) and `clamp()` for fluid typography.
- **SVG map** — 50 state `<path>` elements, each with an id like `s-ca`, `s-tx`, etc., and a `.state` class. Wrapped in `<g id="zoom-group">`.
- **App logic** — vanilla JS IIFE at the bottom. Key sections:
  - `state` object (lines ~742–756): runtime game state (weights, misses, streaks, current question)
  - Pan/zoom engine: viewBox-based SVG manipulation (no CSS transforms). Handles pinch, drag, mouse wheel, and zoom buttons.
  - Quiz engine: weighted random state selection (missed states get higher weight), mastery threshold of 2 consecutive correct answers per state.
  - Feedback: emoji burst, color flash, vibration on tap. On wrong answer, auto-zooms and highlights the correct state.

Supporting files:
- `sw.js` — cache-first service worker; caches app shell + Google Fonts
- `manifest.webmanifest` — PWA install metadata (name, icons, display mode)
- Icon files: `icon-192.png`, `icon-512.png`, maskable variants, `apple-touch-icon.png`, `favicon.png`

## Key Implementation Details

**State weighting**: Each state has a `weights[code]` value. Correct answers lower the weight; wrong answers raise it. States are selected by weighted random draw. Mastery is tracked in `consecCorrect[code]`; once a state reaches 2 it's "mastered" and excluded from future draws.

**SVG pan/zoom**: Uses viewBox math directly — no CSS `transform`. The `viewBox` attribute on the `<svg>` is updated on every pan/drag/zoom event. Pinch zoom calculates the midpoint between two touch points and zooms around it.

**Wrong-answer teaching**: When the player taps the wrong state, the correct state's path gets a highlight class and the map viewBox animates to center and zoom in on it.

**CSS custom properties** (theming):
```
--paper: #fdf6ec   (background)
--orange: #E87B30  (primary/accent)
--green:  #73A262  (correct)
--rose:   #d96a6a  (wrong)
--ink:    #2a2520  (text)
```

# Karpathy Guidelines

Behavioral guidelines to reduce common LLM coding mistakes, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.