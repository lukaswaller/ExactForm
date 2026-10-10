# ExactForm Calculator — Architecture Document

*The contract Cursor builds from. Keep it current: when a decision changes, update this doc first.*

## 1. Tech stack

| Layer | Choice | Why |
|---|---|---|
| App framework | **Flutter (Dart)** | One codebase iOS + Android (the iOS gap is the opportunity); custom-drawn widgets for bespoke keypad + textbook display; hot reload for fast solo iteration |
| Symbolic engine | **nerdamer** (MIT, via `flutter_js`) | Exact fractions, symbolic √/π/e, simplify/factor/solve; `toTeX()` feeds the renderer directly |
| Numerics/units/matrices | **mathjs** (Apache 2.0, via `flutter_js`) | `Fraction`/`BigNumber`, unit conversions, matrices, statistics — complements nerdamer |
| Math rendering | **flutter_math_fork** (MIT, pure Dart) | Native-widget KaTeX-compatible rendering, no WebView (WebView costs 50–150MB per view) |
| State management | **Riverpod** | Standard, testable |
| Local persistence | **drift (SQLite)** | Calculation history, workspaces, settings — relational fits |
| IAP | **in_app_purchase** (storekit/billing) | Pro unlock + graphing add-on |
| Backend (v1) | **None** — fully offline | Core promise: no account, no internet needed |

**License guardrail:** never embed Giac/Xcas (GPL-3.0) in the shipped app. MIT/Apache engines only. Long-term: custom C++ exact-arithmetic core via `dart:ffi` (leverages Lukas's C++).

## 2. Module map

```
lib/
├── math/                    # Pure Dart, zero Flutter imports (unit-testable)
│   ├── engine.dart          # Bridge to nerdamer/mathjs via flutter_js; unified API
│   ├── ast.dart             # Expression AST: templates (fraction, power, root), cursor slots
│   ├── exact.dart           # Exact-form representation: rationals, surds, symbolic constants
│   ├── decimal.dart         # Exact→decimal conversion with precision control
│   └── steps.dart           # Step-by-step derivation (Pro): rule applications as data
├── input/                   # Template-based editing
│   ├── templates.dart       # Fraction/power/root/integral template insertion
│   └── cursor.dart          # Tree cursor: arrow-key navigation through the AST
├── ui/
│   ├── keypad.dart          # TI-style keypad, haptics, layout (portrait/landscape)
│   ├── display.dart         # Two-line display: input (editable) + result, via flutter_math_fork
│   └── history.dart         # History list, search, recall-into-expression, workspaces
├── features/
│   ├── converter.dart       # Units + physical constants (Pro: extended sets)
│   └── graphing/           # Phase 2: plotter (paid add-on) — stubbed behind feature flag
└── services/                # Persistence, IAP/entitlements, settings, analytics
```

**Rule:** `math/` never imports Flutter. The engine is the referee — UI renders what the engine says.

## 3. Data models

```dart
ExprNode { kind (number | fraction | power | sqrt | symbol | func | ...), children, value? }
ExactValue { sign, rational: BigInt/BigInt, surdCoeff?, symbolicConst? (pi | e) }
DecimalValue { value: double or BigDecimal, precision }
Evaluation { input: ExprNode, exact: ExactValue?, decimal: DecimalValue, steps: Step[]? }
Step { rule: String, before: TeX, after: TeX }       // Pro step-by-step
HistoryEntry { id, timestamp, inputTeX, exactTeX?, decimalStr, tags[], workspaceId }
Settings { formDefault: exact | decimal, precision, angleMode, theme, haptics }
Entitlements { isPro, hasGraphing }
```

## 4. Key flows

**Type → evaluate → toggle form (the core loop)**
1. Keypad taps insert into the AST via templates (fraction key → stacked fraction with two cursor slots).
2. On each edit, `engine.evaluate(ast)` runs (debounced, isolate if needed) → `Evaluation`.
3. Display shows input (textbook-rendered) + result in the default form; **≈/exact toggle** flips `exact` ↔ `decimal` per result.
4. Swipe/tap a history entry to recall it into the editor as a live AST (not a flat string).

**Step-by-step (Pro):** `steps.derive(ast)` returns rule applications (e.g. "combine like terms", "rationalize denominator"); UI renders before/after TeX per step.

**IAP:** `services/entitlements` gates `steps`, workspaces/export, graphing module. Free tier never nags — upsell lives in settings + a single "Pro" row.

## 5. API contracts

- v1: no backend, no API. All computation on-device.
- Later (optional cloud sync): Supabase — `HistoryEntry` sync by `workspaceId`. The AST serializes to a canonical string (nerdamer-compatible) — that string *is* the interchange format.

## 6. Coding conventions

- `math/` is pure Dart with unit tests against known exact results (`1/3+1/6 == 1/2`, `√8 == 2√2`).
- Tolerances explicit; never compare doubles with `==` in engine code.
- Every user-visible string localized from day one.
- Feature-flag `graphing` from the start so the paid module slots in cleanly.

## 7. Non-goals (v1)

- No graphing (Phase 2, paid add-on — but architecture must not block it).
- No CAS beyond nerdamer's built-in solve/simplify/diff (custom C++ core is post-traction).
- No accounts, no cloud, no social features.
- No camera input (that's the solvers' game).

## 8. Build order

1. AST + template input + keypad + `flutter_math_fork` display (it must *feel* right first).
2. nerdamer/mathjs bridge → exact evaluation + exact/decimal toggle.
3. History (drift) + search + recall + workspaces.
4. Units/constants converter.
5. Pro IAP + step-by-step + export.
6. Graphing module (paid add-on).

## Open technical questions

- `flutter_js` overhead per keystroke — measure; if slow, batch evaluations or move engine to an isolate.
- Exact-form canonical representation: how far to push the custom `ExactValue` model vs. nerdamer strings in v1.
- Landscape/tablet keypad layout.
