# AGENTS.md — ExactForm

## What this is
ExactForm is a TI-style scientific calculator for iOS + Android with exact-form math (real fraction bars, surds, symbolic π/e), step-by-step solutions (Pro), and graphing planned as a paid add-on. Offline-first, no ads, no accounts.

## Stack (exact — do not substitute)
- Flutter (Dart), single codebase for iOS + Android
- Symbolic engine: nerdamer (MIT) + mathjs (Apache 2.0) via flutter_js
- Math rendering: flutter_math_fork (pure Dart, no WebView)
- State: Riverpod · Persistence: drift (SQLite)
- IAP: in_app_purchase

## Docs (read before structural work)
- docs/01-vision-and-features.md — product spec / PRD
- docs/05-architecture.md — THE contract: modules, data models, flows, non-goals
- docs/06-ui-ux.md — screens and interactions
- docs/02-04 — research behind the decisions

## Commands
- flutter pub get
- flutter run
- flutter test
- flutter analyze

## Do not
- Never embed Giac/Xcas or any GPL code. MIT/Apache only.
- No ads, no accounts, no network calls in v1 — fully offline.
- lib/math/ must never import Flutter (pure Dart, unit-tested).
- No new dependencies without asking.

## How to work
- Plan before coding: explain the approach, wait for approval on anything structural.
- One micro-task at a time; tests first for engine code.
- Update docs/05-architecture.md when a decision changes.
