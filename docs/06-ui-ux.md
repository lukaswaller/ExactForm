# ExactForm Calculator — UI and UX Plan

## Design language

- **Hardware soul, modern skin.** The keypad borrows TI's visual grammar (grouped function keys, 2nd-shift key) but with contemporary spacing, typography, and dark/light themes. It should feel like a *better* physical calculator, not a spreadsheet.
- **Textbook display:** real fraction bars, proper superscripts/subscripts, radicals that stretch — via `flutter_math_fork`. The display is the product; it must be beautiful.
- **Two-line display:** input line (editable, cursor-visible) above the result line. Result shows a small **exact ⇄ decimal toggle** — one tap flips forms.
- Mono font for values, clean sans for chrome. Generous touch targets (this is a keypad app; mis-taps are the enemy).

## Screens

1. **Calculator (home)** — the whole app, really:
   - Display area (top ~35%): input + result + form toggle.
   - Keypad (bottom ~65%): digits, operators, TI-style function grid (trig, log, powers, nCr), `2nd` shift key, fraction template key, ANS/history recall.
   - Haptic tick on every key. Long-press for alternate functions.
2. **History** — every calculation, searchable, grouped by day/workspace. Tap to recall into the editor as a *live expression* (not dead text). Swipe to delete; Pro: export workspace as PDF/image.
3. **Steps (Pro)** — after evaluating, "Show steps" reveals rule-by-rule derivation with before/after math rendering.
4. **Converter** — units + physical constants, searchable. (Engineering students live here.)
5. **Settings/Pro** — form default (exact/decimal), precision, angle mode, themes, and the single Pro upsell row. No nag screens.

## Key interactions

- **Template input:** fraction key inserts a stacked fraction with two navigable slots; arrow keys walk the expression tree. This is the #1 feel requirement — prototype it first.
- **Live evaluation:** result updates as you type (debounced). The calculator feels alive.
- **Form toggle:** the signature gesture. Exact by default; one tap for decimal.
- **ANS key** inserts the previous result as an exact value (not its decimal truncation) — chained exact computation.
- **Landscape mode:** extended function grid (matrices, statistics) — RealCalc Plus proved users pay for this.

## Onboarding (first 30 seconds)

No tutorial. Preload one expression: `√8 + 1/3`. The result shows `2√2 + 1/3` — the user *sees* exact form working before typing a thing. One hint pill: "Tap ≈ for decimal."

## Accessibility & polish

- Dynamic type in chrome; display scales with pinch.
- High-contrast theme; color is never the only signal for errors.
- Reduce-motion respected.

## Later UX (v2+)

- Graphing tab: function list → plot with trace, zoom, intersection finder (paid add-on).
- Custom function keys / programmable buttons.
- Share calculation as a rendered image.
