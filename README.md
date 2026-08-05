# RateLens

Fixed income analytics workbench — bond valuation, yield curve construction, macro rate scenarios, and portfolio risk in one screen. The fixed income sibling to **EquityLens**, built as the second platform in the same self-directed buildout, same process, different market.

## View the Demo

Open `RateLens_Workbench.html` in any browser. No server, no build step, no dependencies beyond a Google Fonts import (falls back to system fonts offline). Everything — pricing, duration, curve bootstrapping, spreads, scenarios — is computed live in the browser from the mock bond and curve data baked into the page.

## What's in This Project

| File | What it is |
|---|---|
| `RateLens_Product_Definition_Doc.md` | Problem statement, user workflow, data model, competitive context |
| `RateLens_Workbench.html` | **The main artifact.** Interactive demo — Dashboard, Bond Valuation, Yield Curve, Portfolio & Risk, Scenarios, Comparison |
| `RateLens_Landing_Page.html` | Early visual concept, superseded by the workbench as the primary deliverable — kept for reference |
| `RateLens_Trader_Feature_List.md` | Tiered feature roadmap (what's built, what's next, what's deliberately out of scope) |
| `RateLens_Technical_Glossary.md` | Plain-English explanation of every metric on the platform and what it signals about the economy |
| `RateLens_Narrative.md` | Why this was built, what it demonstrates, interview talking points |
| `ratelens-bond-valuation` (skill) | Bond pricing, YTM, duration, convexity, DV01, spreads — formulas + reference code |
| `ratelens-yield-curve` (skill) | Curve bootstrapping, forwards, T-bill conventions, curve shape signals |
| `ratelens-macro-rate-drivers` (skill) | Term premium decomposition, deficit sensitivity, scenario/shock framework |

The three skills are saved separately (not files in this folder) and are what the workbench's calc layer is built from — they're reusable on their own for any future RateLens work.

## Features (built)

- **Dashboard** — today's Treasury curve and a bond watchlist across sovereigns, a T-bill, and three corporates; watchlist alerts and the upcoming Treasury auction calendar
- **Bond Valuation** — clean price, YTM (solved numerically), current yield, Macaulay/modified duration, convexity, DV01, nominal spread, Z-spread, carry & roll-down, full cashflow schedule, illustrative execution/liquidity estimates, and real sourced credit fundamentals per corporate issuer
- **Yield Curve** — par curve, bootstrapped spot curve, implied forward rates, 2s10s / 3m10y / butterfly signals, T-bill discount-to-BEY conversion, 6-month real curve history, real TIPS yield curve with breakeven inflation, and an international curve comparison (US vs. JGB/Bund/Gilt)
- **Portfolio & Risk** — editable position weights, weighted YTM, portfolio effective duration, portfolio DV01, duration-based 95% VaR, key rate duration (per-tenor partial duration, reconciles exactly to total duration), benchmark-relative analytics (active duration/yield/spread, tracking error), a multi-factor risk model (Level/Slope/Curvature/Spread with genuine diversification benefit), and Monte Carlo/fat-tailed VaR with CVaR
- **Scenarios** — bull/base/bear yield scenarios, custom parallel + corporate spread shocks, return attribution (carry / curve / spread decomposition), and historical scenario replay against three real episodes (2022 hiking cycle, March 2023 SVB, January 2026 JGB contagion)
- **Comparison** — side-by-side fundamentals across a selected peer set, curve-positioning chart, Z-spread comparison, systematic relative-value ranking across all bonds, credit spread curve by rating
- **Derivatives** — Treasury futures (illustrative CTD/DV01 per contract) and par swap rates (real curve + illustrative but structurally-grounded swap spread), plus a portfolio DV01 hedge calculator — the tools to actually manage rate risk, not just measure it

Remaining roadmap (OAS, repo/financing) is in `RateLens_Trader_Feature_List.md`.

## Architecture

Every calculation is a pure function — plain numbers and arrays in, no ticker/CUSIP lookups or API calls baked into the math. That's deliberate: the same formulas run unchanged whether the input is today's hardcoded mock data or tomorrow's live feed. This is the same pattern EquityLens uses (see its `equitylens-risk-kpis` skill), applied here to fixed income:

```
Mock data (today)  ──┐
                      ├──▶  Pure calc functions (never change)  ──▶  UI
Live data (later)  ──┘
```

Mock bond and curve data is seeded from real Treasury par yields (3M through 30Y, live from Treasury.gov) plus 13 illustrative corporates spanning AAA to B+: Microsoft, Johnson & Johnson (AAA), Berkshire Hathaway, Walmart, ExxonMobil (AA), JPMorgan, Bank of America (A-), Verizon (BBB+), Tesla, Ford Motor Credit, Occidental Petroleum (BB+), and Carnival (B+). Real, named, widely-traded issuers across financials, tech, healthcare, retail, energy, telecom, auto, and leisure — wide enough to build a genuinely different 5-bond portfolio each time and see the platform's metrics actually respond differently. Prices are derived (not guessed) from each bond's target YTM against the live curve; spread levels and credit fundamentals are researched and sourced per issuer (see `RateLens_Trader_Feature_List.md` for citations).

## Known Simplifications

Documented in full in each skill and in `RateLens_Technical_Glossary.md`; the short version:

- No option-adjusted spread — callable bonds aren't modeled, flagged rather than faked
- Curve shocks are stylized (parallel, linear steepener/flattener), not historically-shaped
- Portfolio VaR is duration-based and parametric — assumes normal yield moves and a parallel shift, understates true tail risk
- Yield curve bootstrap uses linear interpolation between quoted tenors — small residual noise where tenors are sparse (e.g., 10Y to 30Y)
- Term premium is a single scalar, not its own term structure

## Technical Roadmap

Not product features — engineering improvements worth naming rather than leaving implicit:

- **No checked-in test suite.** Every verification pass so far has been an ad-hoc Node script (extract the `<script>` block, run it in a DOM-shim, assert on output) written fresh in a scratch sandbox each session and thrown away afterward. It's caught real bugs (a sign error in the derivatives hedge calculator, a yield-curve bootstrap scaling bug, a corporate roll-down miscalculation), but it isn't repeatable. A companion test file shipped alongside the workbench would turn "verified today" into "provably still correct."
- **Single 130KB+ file, calc/data/render all interleaved.** A deliberate original tradeoff (no server, no build step, no dependencies) that's still right for a single-file portfolio demo, but if this keeps growing, splitting calc/data/render into separate files behind a minimal bundler would make it easier to test and reason about — at the cost of the current zero-dependency simplicity.
- **No persistence.** Global mutable state (portfolio weights, comparison selection, hedge inputs) resets on reload. Since this runs as a real local file in your own browser rather than a sandboxed artifact, localStorage is actually available here and would be a cheap add.
- **Thin input validation.** Bad input in a weight or value field mostly produces a silent `NaN%` rather than a message.
- **Data vintage is scattered.** Every new section (TIPS, international curves, credit fundamentals) tracks its own "as of" date inline — honest in the moment, but adding up to a lot of different dates in different corners of the file. A single consolidated vintage panel would make "how stale is this" a one-glance answer.

## Roadmap

Done: README, Comparison view, live Treasury.gov government curve, historical curve chart, credit spread curve by rating, contextual explainers on every tab, carry & roll-down, cashflow schedule, relative value ranking, watchlist alerts, auction calendar, return attribution, key rate duration, historical scenario replay, execution/liquidity estimates, credit fundamentals, derivatives overlay (futures/swaps/hedge calculator), TIPS/inflation breakevens, international curve overlay, benchmark-relative analytics, multi-factor risk model, Monte Carlo/fat-tailed VaR.

What's left — full detail and rationale in `RateLens_Trader_Feature_List.md`: ESG/green bond coverage, a trade blotter, OAS for callable bonds (needs an actual optionality model, not a cosmetic add), and repo/financing rate integration.

Formal skill evals via skill-creator remain optional, if the calc layer ever needs to be load-bearing for something beyond this demo.

## Author

Rodrigo Martell Velasco. Built as the fixed income counterpart to EquityLens — see `RateLens_Narrative.md` for the full story.
