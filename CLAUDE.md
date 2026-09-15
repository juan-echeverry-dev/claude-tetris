# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A vanilla JavaScript Tetris implementation — no build step, no dependencies, no package.json. Three files: `index.html` (DOM/canvas structure), `style.css` (dark/retro arcade theme), `game.js` (all game logic, ~300 lines). README is in Spanish.

## Running

No install/build. Either open `index.html` directly in a browser, or serve it with any static server (e.g. `python3 -m http.server 8000`, `npx serve .`). There is no test suite, linter, or build tooling in this repo.

## Architecture

Everything lives in `game.js` as top-level functions and module-scope mutable state (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) — there are no classes or modules.

- **Board model**: `board` is a `ROWS × COLS` matrix; each cell is `0` (empty) or a color index `1–7` identifying which piece type locked there.
- **Pieces**: `PIECES` are defined as square matrices (index 0 unused). Rotation (`rotateCW`) is a transpose + row-reverse, not per-piece rotation tables.
- **Collision** (`collide`): checks board bounds and overlap with locked cells; used both for movement and for projecting the ghost piece.
- **Wall kicks** (`tryRotate`): after rotating, tries offsets `[0, -1, 1, -2, 2]` and takes the first that doesn't collide.
- **Game loop** (`loop`): driven by `requestAnimationFrame`, accumulates elapsed time in `dropAccum` and advances the piece one row once `dropAccum >= dropInterval`; also calls `draw()` every frame.
- **Locking/spawning**: `lockPiece()` → `merge()` (writes piece into `board`) → `clearLines()` → `spawn()` (promotes `next` to `current`, generates a new `next`; if the new `current` immediately collides, calls `endGame()`).
- **Line clearing** (`clearLines`): scans bottom-up, splices out full rows and unshifts empty rows at the top; re-checks the same row index after a splice (`r++` inside the loop).
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` multiplied by `level`; hard drop adds 2 points per row dropped, soft drop adds 1 point per row.
- **Leveling/speed**: level = `floor(lines / 10) + 1`; `dropInterval = max(100, 1000 - (level - 1) * 90)` ms.
- **Ghost piece** (`ghostY`): projects `current` straight down until collision, drawn via `draw()` with `globalAlpha = 0.2`.
- **Rendering**: `draw()` redraws the whole board canvas every frame (grid, locked cells, ghost, current piece); `drawNext()` renders the next-piece preview on a separate canvas (`#next-canvas`).
- **Input**: a single `keydown` listener switches on `e.code` (arrows, `KeyX` for rotate, `Space` for hard drop, `KeyP` for pause), gated on `paused`/`gameOver`.

## Tunable constants (in `game.js`)

`COLS`, `ROWS`, `BLOCK` (cell size in px), `COLORS`, `LINE_SCORES`, `dropInterval` (initial). If `COLS`/`ROWS`/`BLOCK` change, update the `#board` canvas `width`/`height` in `index.html` to match (`COLS × BLOCK`, `ROWS × BLOCK`).
