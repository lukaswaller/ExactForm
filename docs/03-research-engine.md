# ExactForm Calculator — Math Engine and Rendering Research

*Research completed Oct 8, 2026. Core technical decisions for the app.*

## Math engines compared

| Engine | Language | Exact forms | Symbolic power | License | Mobile route | Verdict |
|---|---|---|---|---|---|---|
| **nerdamer** (+ maintained `nerdamer-prime` fork) | JS | ✅ exact fractions, keeps √2/π/e symbolic; `toTeX()` gives LaTeX directly | Medium — simplify, factor, diff, basic integrate, solve, limits | **MIT** ✅ | Anywhere JS runs (RN natively; Flutter via `flutter_js`); tiny, no server | **Best MVP pick** — purpose-built for this |
| **mathjs** | JS | ✅ `Fraction` datatype, BigNumber arbitrary precision | Low-medium — `simplify()` weaker than nerdamer | **Apache 2.0** ✅ | Same JS routes | Great *complement*: numerics, units, matrices, statistics |
| **Giac/Xcas** | C++ | ✅ full exact arithmetic | Very high — engine inside HP Prime + GeoGebra CAS | **GPL-3.0** ⚠️ copyleft | Compiles for Android NDK + iOS | Technically ideal, **legally toxic** for a closed paid app. Reference for feature parity only — never embed. |
| **SymPy** | Python | ✅ gold standard exact/symbolic | Highest of open options | BSD ✅ | Painful on mobile (heavy, slow startup) | Server-side/desktop only; skip for phone |
| **ExprTk / muParser** | C++ | ❌ numeric doubles only | ❌ none | MIT / BSD ✅ | Trivial via FFI | Numeric fallback only — can't deliver "exact form" |

## Recommended engine architecture

**nerdamer (MIT) as the symbolic core + mathjs (Apache 2.0) for numerics/units/matrices.** Both JS, permissively licensed, offline, tiny footprint.

**Long-term differentiator:** Lukas's C++ strength is an asset — write a custom exact-arithmetic core (BigInt rationals + surd/symbolic-π representation) and bind via Dart FFI once the app has traction. That's the defensible move; nerdamer gets v1 shipped.

## Rendering textbook-style math on mobile

Two problems: (a) *displaying* fraction bars/radicals/superscripts — solved; (b) *editing* them with cursor navigation — that's the real work and the differentiator.

- **Flutter: `flutter_math_fork`** — pure-Dart port of the KaTeX parser, native widgets, no WebView/JS, MIT, actively maintained. **This is the display answer.** Alternative: `orestesgaolin/katex` (newer Dart port, MIT, renders to SVG).
- **Avoid KaTeX-via-WebView** as the primary renderer — 50–150 MB RAM per view, sluggish for a calculator.
- **React Native:** no mature native renderer (WebView-based `react-native-katex`, or young RaTeX project — Rust core, no WebView).
- **SwiftUI:** WKWebView + KaTeX, or RaTeX's C ABI. No first-class native option.

## Input editing: the feel of a real calculator

Copy the hardware pattern — **template-based input**. Tapping the fraction key inserts a stacked fraction template with navigable numerator/denominator slots (exactly like a TI-84's n/d key); arrow keys move the cursor through the expression tree.

Under the hood: **expression AST → serialize to nerdamer string → evaluate → render result AST back through the math renderer.** Very buildable in Flutter with custom widgets — and precisely what makes it "feel like a real calculator" instead of a text box.

## Recommended stack: Flutter

For a solo dev with C++/Python background, no JS/Swift history, needing iOS + Android:

- **Flutter (Dart)** — single codebase ships both platforms (the iOS gap *is* the opportunity); custom-drawn widgets ideal for bespoke keypad + textbook display; `flutter_math_fork` for native math rendering; hot reload for fast solo iteration; `dart:ffi` path to a hand-written C++ engine later.
- **SwiftUI** — best iOS polish, but Android means a full rewrite; doubles solo work.
- **React Native** — nerdamer/mathjs run natively in the JS runtime (genuine advantage), but custom keypad + textbook UI is harder to nail, and math rendering options are immature.

**Verdict: Flutter.**

## Sources
- nerdamer evaluation: https://github.com/ggml-org/llama.cpp/pull/25948
- nerdamer-prime fork: https://socket.dev/npm/package/nerdamer-41fb3b2/overview/1.1.13-41fb3b2
- flutter_math_fork: https://github.com/cuplivo/cuplivo/blob/HEAD/dependencies/flutter_math_fork/README.md
- orestesgaolin/katex: https://github.com/orestesgaolin/katex/blob/HEAD/README.md
- RaTeX: https://github.com/ascarl/ratex
- WebView cost analysis: https://github.com/fullstackstudio-nl/hermie/blob/HEAD/docs/adr/0020-diagrams-and-math-without-a-webview.md
- Xcas/Giac (GPL): https://en.wikipedia.org/wiki/Xcas
