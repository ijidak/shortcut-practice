# Shortcut Practice Game — Creation Prompts

This file documents the prompts used (with Claude Code) to build the shortcut
trainer in `index.html`. They are listed in the order they were given, with a
short note on what each one produced.

---

## 1. Initial build

> Create an HTML game that I can run locally in my browser to practice tmux shortcuts, iterm2 shortcuts, and Cursor / VS Code shortcuts.
>
> The game should be the same on each tab, but each tab should be practice for a different app.
>
> The game should show me the name of a commonly needed command or action and give me five seconds to get the shortuct correct.
>
> When I get the shortcut correct it should play a happy sound and show confetti that lasts one second before moving to the next shortcut.
>
> When I get the shortcut wrong it should beep.
>
> After my five seconds are up, if I haven't gotten the shortcut right, the shortcut should be displayed and I should have to keypress the shortcut displayed on the screen to dismiss the display and move to the next shortcut.
>
> The game should not end until I press the stop button.
>
> There should be a pause button that I can press to pause the game.
>
> When I get a shortcut correct, that shortcut should not show again for at least one minute so that I can focus on practicing shortcuts I haven't gotten correct.
>
> For shortcuts I didn't get correct, they should re-enter the rotation so that I can pratice them again.
>
> For shortcut I get write twice, those should no longer come up in the game session.
>
> There is no need to save the game session between plays and browser refreshes. When I refresh the browser it is okay that the game resets.

**Produced:** the initial single-file `index.html` — three tabs (tmux, iTerm2,
Cursor/VS Code), 5-second timed rounds, Web Audio success/fail sounds,
canvas confetti, timeout reveal-and-retype flow, Start/Pause/Stop controls, the
1-minute cooldown, re-entry of missed shortcuts, and retire-after-two-correct.

---

## 2. Multi-key sequence not accepted (tmux prefix)

> I'm trying to type 'Ctrl + B + Left Arrow' and the game is not accepting my input to let me advance

**Produced:** fix + explanation. tmux shortcuts are a *prefix sequence* (press
`Ctrl+B`, release, then the next key), and on macOS holding `Ctrl` while pressing
an arrow is captured by Mission Control / Spaces before the page sees it. Added
leniency for a lingering `Ctrl` on trailing chords, clearer tmux guidance, and a
per-round "multi-key" hint.

---

## 3. A specific letter shortcut not registering

> That fixed it! But Ctrl+B then D doesn't work

**Produced:** made key matching layout-independent. Matching now succeeds on
**either** the physical key (`e.code`) **or** the produced character (`e.key`),
so it no longer breaks with alternate keyboard layouts, input methods, or a
modifier mangling the physical-key report. Shifted symbols like `%` and `"`
remain correctly disambiguated.

---

## 4. Add Finder

> Add a tab for Finder shortcuts

**Produced:** a fourth **Finder** tab (30 macOS Finder shortcuts). Browser-
destructive combos (`⌘W`, `⌘Q`, `⌘M`, Empty Trash `⌘⇧⌫`) were deliberately
excluded, and the browser-reserved-keys note was expanded to cover `⌘1`–`⌘9`
tab switching.

---

## 5. Per-tab shortcut checklist with enable/disable

> For each tab, below the current content, add a list of all the shortcuts that the game will cycle through for that tab.
>
> Next to each shortcut add a check box that I can un-check to remove the shortcut from the current session of the game.
>
> For a given shortcut, I should be able to un-check or check the box at any time to add or remove the shortcut from the practice cycle.
>
> For shortcuts I get write twice, those should no longer come up in the game session.

**Produced:** a per-tab checklist below the card listing every shortcut with a
checkbox, compact key display, and status badge (`in cycle` / `1 / 2` /
`mastered` / `off`). Toggling takes effect immediately — even mid-round;
unchecking the on-screen shortcut advances to the next, and unchecking
everything shows a "No shortcuts selected" state. Added Check all / Uncheck all
buttons and updated the bottom stats to `in rotation / got once / mastered / off`.

---

## 6. This document

> Document the prompts we used to create this shortcut HTML game and put them in a file shortcut-game-creation-prompts.md

**Produced:** this file.

---

## Notes

- The entire game lives in a single self-contained `index.html` (no build step,
  no dependencies). Open it directly in a browser.
- Logic was verified throughout with headless Node test harnesses (key parsing,
  layout-independent matching, and the round/toggle state machine).
