# ExactForm Calculator — Market and Competitor Research

*Research completed Oct 8, 2026.*

## The gap

Nobody combines: (a) TI-style textbook input with real fraction bars/surds, (b) true exact-form answers (√2 stays √2), (c) step-by-step solutions, (d) a modern ad-free design, (e) cross-platform iOS + Android.

**The wedge:** *"the calculator HiPER users wish existed on iPhone, with Symbolab-style steps built in."* Also note: iOS 18+ Math Notes raised the bar — exact symbolic display is now table stakes on iPhone.

## Competitors

| App | Strengths | Weaknesses / gaps to exploit |
|---|---|---|
| **HiPER Scientific Calculator** (Android, 4.8★, 36K reviews) | Symbolic algebra, calculus, 2D/3D graphs, 100-decimal precision in Pro, huge feature depth | **Android-only** — huge iOS gap; free version has ads and shares device IDs; dated UI; weak simplification |
| **RealCalc / RealCalc Plus** (Android) | Most authentic Casio FX feel, RPN, no data collection | Cramped dated UI; fraction features paywalled; Android-only |
| **NCalc+** (Android) | Equations, derivatives, integrals, linear algebra in one app | Small audience, limited polish |
| **Calc84** (TI-84 emulator) | Nostalgia/authenticity for TI users | Emulator niche, legally gray, ugly |
| **PCalc** (iOS/Mac, ~$9.99) | Desktop-grade quality, decades of refinement, RPN + programmer modes | No true symbolic/exact-form CAS; iOS-only; no step-by-step |
| **GeoGebra Scientific/CAS** (both, free) | Textbook fraction input, exam mode; CAS view via Xcas/Giac; offline | No step-by-step; data-collection concerns; solver UX not calculator-first |
| **Photomath / Symbolab / WolframAlpha** | Camera input, step-by-step pedagogy, strongest engines | Require internet + accounts; subscriptions ($7–10/mo); they're *solvers*, not calculators — clunky for quick exact-form computation |

## What this means for ExactForm

- **iOS is the opportunity.** HiPER owns the Android power-user niche but leaves iPhone entirely open. Ship both platforms from day one (Flutter) and the iOS side is nearly uncontested.
- **Don't fight the solvers at solving** — beat them at *calculating*. Instant, offline, exact-form answers with zero friction is the use case they serve badly.
- **Steps are the upsell**, not the core. Photomath/Symbolab proved people pay for step-by-step; bundle it as the Pro hook.
- **No ads, ever.** The "better than Apple Calculator" premium positioning collapses the moment a banner appears.

## Sources
- https://macmyths.com/12-best-free-android-calculator-apps-and-widgets/
- https://play.google.com/store/apps/details?id=cz.hipercalc.pro
- https://xdaforums.com/t/the-best-scientific-graphing-and-cas-calculator-apps-for-android-comprehensive-comparative-review.4594851/post-88631847
- https://www.marketingscoop.com/tech/the-10-best-calculator-apps-for-solving-complex-mathematics/
