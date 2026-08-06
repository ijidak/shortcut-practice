# shortcut-practice

A self-contained keyboard-shortcut drill game. Open `index.html` in a browser — no build step,
no server, no dependencies, no tests. To try a change, just reload the file.

## Layout

- `index.html` — the entire app: CSS in one `<style>` block, all JS in one `<script>` block at the bottom.
- `mini-spec.md` — the original prompt that produced the app. Describes the intended game rules.
- `shortcut-game-creation-prompts.md` — log of follow-up prompts used to extend the app.

## How `index.html` is organized

The `<script>` is divided by `/* ===== Section ===== */` banners, in this order:

1. **Shortcut data** — `RAW`, an object keyed by app (`tmux`, `iterm`, `vscode`, `finder`). Each entry has
   `label`, `blurb` (HTML, shown on the idle screen), and `shortcuts: [{ name, keys }]`.
   `keys` is an array of chords; more than one element means a *sequence* (e.g. `["Ctrl+B", "%"]` for tmux).
   A chord is a `+`-joined string of modifiers plus one key: `Cmd`, `Ctrl`, `Option`/`Alt`, `Shift`,
   and symbols (`⌘⌃⌥⇧`) all parse. Arrows can be written `→ ← ↑ ↓`.
2. **Key parsing** — `parseChord` / `keyToCode` turn those strings into `{ctrl, meta, alt, shift, code, char}`,
   plus the display helpers (`chordCapHTML`, `seqWords`, `seqCompact`).
3. **Matching** — `chordMatches` compares a `keydown` against the expected chord. It matches on *either*
   `e.code` or the produced `e.key` character so it survives non-US layouts. Trailing chords in a
   sequence ignore a lingering Ctrl (people don't release the tmux prefix cleanly).
4. **Game state** — one state object per app in `games`, built by `buildGame`. `g()` returns the current one.
   Key knobs at the top: `ROUND_MS` (5s per prompt), `COOLDOWN_MS` (60s before a once-correct shortcut
   returns), `CELEBRATE_MS`.
5. **Round flow** — `startGame` → `nextRound` → `tick` → `onTimeout` / `handleCorrect`.
   `status` drives everything and is one of: `idle`, `running`, `reveal`, `celebrate`, `cooldownwait`,
   `mastered`, `empty`, `paused_running`, `paused_reveal`, `paused_cooldown`.
6. **Key handling** — a single `window` `keydown` listener; it `preventDefault`s everything while a round
   is active so browser shortcuts don't fire.
7. **Controls** — pause/resume/stop/reset, `switchApp`, and `disableAndSkip` (for combos the OS eats
   before the page sees them — drops the shortcut for the session and advances).
8. **Rendering** — `render()` rebuilds the stage, controls, and stats from `status` on every state change.
   `renderShortList()` draws the per-tab checklist below the card and short-circuits via
   `shortListSignature` when nothing visible changed.
9. **Audio / Confetti** — WebAudio tones and a canvas particle burst. Self-contained.

## Rules that shouldn't drift

- Two correct answers retire a shortcut for the session; one correct answer puts it on a 1-minute cooldown.
- A timeout reveals the answer and requires typing it to advance, but does **not** count as correct —
  the shortcut stays in rotation.
- Nothing persists across refreshes, by design.
- `Cmd+W` and `Cmd+Q` are deliberately absent from the data so a session can't be lost mid-game.

## Working in here

- Adding or removing a shortcut means editing the `RAW` array for that app and nothing else — the
  checklist, stats, and rotation all derive from it.
- Keep the `RAW` entries column-aligned the way the surrounding lines are.
- Everything stays in the single file; don't split out CSS/JS or add tooling.

## Committing

After finishing a requested edit, `git add`, commit with a short descriptive message, and push to
`origin main` without asking first. Work directly on `main` — no branches or PRs for this repo.
