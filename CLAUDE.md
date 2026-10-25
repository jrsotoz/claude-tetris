# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no build, no tests, no linter. README and UI text are in Spanish.

## Run

- `open index.html`, or serve locally (e.g. `python3 -m http.server`) and open `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + canvases `#board` 300x600, `#next-canvas` 120x120, HUD, `#overlay`), `style.css`, `game.js` (all logic, loaded as classic script, `'use strict'`).

`game.js` specifics:
- State is module-level `let` globals (`board`, `current`, `next`, `score`, `lines`, `level`, `dropInterval`, ...), reset in `init()`. Restart button calls `init()`.
- Board: `ROWS x COLS` 2D array of ints; `0` = empty, `1-7` index into both `COLORS` and `PIECES` (piece type id == color id).
- Game loop: `requestAnimationFrame` `loop(ts)` accumulates `dropAccum` and gravity-drops at `dropInterval`; `togglePause`/`endGame` cancel the frame via `animId`.
- Piece lifecycle: `lockPiece()` = `merge()` → `clearLines()` → `spawn()`; `spawn()` promotes `next` to `current` and triggers `endGame()` on spawn collision.
- Rotation: `rotateCW` on matrix + horizontal wall kicks `[0,-1,1,-2,2]` in `tryRotate` (no SRS).
- Scoring: `LINE_SCORES[cleared] * level`; soft drop +1/cell, hard drop +2/cell; level = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)`.
- Input: single `keydown` listener (arrows, `X` rotate, `Space` hard drop, `P` pause).
- Piece selection is pure `Math.random()` (no 7-bag).
