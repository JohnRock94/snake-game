# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file browser Snake game — no build tools, no dependencies, no server required. Open `snake.html` directly in a browser to play.

## Architecture

Everything lives in [`snake.html`](snake.html): inline CSS, a `<canvas>` element, and a `<script>` block.

**Key state variables** (all module-level `let`):
- `snake` — array of `{x, y}` cells, head at index 0
- `dir` / `nextDir` — current and queued direction `{x, y}`; `nextDir` is applied at the start of each tick to allow buffering one input
- `apple` — single `{x, y}` position
- `lava` — array of `{x, y}` positions that accumulate permanently
- `moveCount` — increments each tick; every 10 moves `placeLava()` is called
- `score`, `gameOver`, `started`, `ticker` (the `setInterval` handle)

**Game loop**: `setInterval(tick, 150)`. Each tick moves the snake, checks collisions (self and lava), handles apple eating, and calls `draw()`.

**Coordinate system**: grid units (`cols × rows`), derived from canvas pixel size divided by `CELL` (20 px). Snake wraps at edges.

**Spawn helpers**: `occupiedSet()` builds a `Set` of all blocked cells (snake + lava); `placeApple()` and `placeLava()` use it to avoid overlaps.

**Rendering order** in `draw()`: background → grid dots → lava → apple → snake (head drawn brighter than body).

## Controls

- **Keyboard**: arrow keys steer the snake; 180° reversals are blocked.
- **Touch/mobile**: swipe gestures via `touchstart`/`touchend` events. The swipe delta is compared on both axes; the dominant axis determines direction. Swipes shorter than 10px are ignored. Both keyboard and swipe start the game from the title screen.

## Security

A `Content-Security-Policy` meta tag (`default-src 'none'; style-src 'unsafe-inline'; script-src 'unsafe-inline'`) is set in `<head>` to block injected third-party scripts when served over HTTP.
