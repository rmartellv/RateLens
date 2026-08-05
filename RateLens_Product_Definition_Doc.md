# RateLens — Product Definition Doc

**Sibling platform to EquityLens.** Independent codebase, same build discipline: mockup-driven, MVP scope, portfolio artifact demonstrating fixed income domain expertise + full-stack thinking.

## Problem & User

Fixed income analysts and PMs price and monitor bonds across fragmented tools — a terminal for reference data, a spreadsheet for curve/spread analysis, another for portfolio-level risk (duration, DV01, VaR). No single lightweight workspace ties bond-level fundamentals, relative value vs. the curve/peers, and portfolio risk into one view. Bloomberg (FIT/YAS), Aladdin, and ICE solve this at enterprise scale and cost; nothing exists as a fast, standalone screener for an analyst who wants one number quickly.

## Day in the Life

An analyst opens RateLens each morning to check where curves moved overnight, then screens a specific bond by CUSIP/ISIN — pulling coupon, maturity, YTM, duration, and spread. She checks relative value: is this bond rich or cheap vs. its own curve (z-spread) and vs. rating/sector peers? If it looks attractive, she adds it to the portfolio view with a position size and watches portfolio duration, DV01, and VaR update in real time. Before a client call, she pulls up the comparison panel to justify the trade against three peer bonds side by side.

## MVP Scope

**Bond Fundamentals Panel**
- Input CUSIP/ISIN or issuer + tenor → coupon, maturity, YTM, modified duration, convexity, credit rating, issuer sector
- Price/yield history chart
- Curve context: spread to benchmark (Treasury/swap), z-spread

**Relative Value Screen**
- Bond vs. own curve (rich/cheap by z-spread)
- Bond vs. rating/sector peer group (spread percentile)

**Portfolio Manager**
- Add/remove bonds with position sizing (% or $)
- Portfolio-level: weighted YTM, effective duration, DV01, convexity, VaR (95%, parametric)
- Sector / rating / duration-bucket breakdown

**Comparison View**
- Side-by-side fundamentals for a peer group (e.g., IG corporates, EM sovereigns)
- Spread comps, curve positioning chart

## Data Model

*Inputs (per bond):* CUSIP/ISIN, issuer, coupon, maturity date, call schedule, currency, rating, sector, price/yield history, benchmark curve reference.

*Calculated:* YTM, modified duration, convexity, DV01, z-spread, curve percentile. Portfolio layer aggregates these position-weighted, plus VaR and diversification (HHI by sector/rating).

Data source: mock data for MVP (5–10 real bonds, static). Live sourcing — LSEG connector (bond pricing, curves, reference data) vs. another API — to be evaluated once the MVP proves out the calc logic; formulas are data-source agnostic so the swap is additive, not a rebuild.

## Competitive Context

Bloomberg FIT/YAS and Aladdin own this workflow at the institutional level — full reference data, real-time pricing, compliance layers. RateLens isn't competing there. The angle: a narrow, fast, single-analyst tool that proves the underlying logic (duration/convexity math, spread decomposition, portfolio risk aggregation) is understood and can be built, not just consumed from a vendor terminal. Same differentiation thesis as EquityLens — this reads as "understands fixed income, can build the tech, thinks about product," aimed at buy-side, IB fixed income, and risk-focused interviews specifically (distinct audience from EquityLens's equity-research angle).

## Deliverables (in order)

1. Product Definition Doc — this document
2. Wireframes/mockups (dashboard, bond detail, portfolio & risk view, comparison panel)
3. Interactive demo (React artifact, mock data, 5–10 bonds)
4. Narrative layer (1-pager / LinkedIn post — why built, what it signals, interview talking points)

## Out of Scope (MVP)

Live data integration, deployment/hosting, auth/multi-user, OAS/option-adjusted spread modeling for callables, factor-based risk attribution.
