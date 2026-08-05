# RateLens — Bond Trader Feature List

Grounded in what Bloomberg (YAS, CRVF, WB, ALLQ), ICE, and Tradeweb treat as core desk infrastructure, the CFA-standard fixed-income return attribution framework, and RateLens's skill-based architecture — formulas ready now (`ratelens-bond-valuation`, `ratelens-yield-curve`, `ratelens-macro-rate-drivers`), data wiring TBD per item.

## Built (MVP + Tier 2 + Tier 3 additions)

- Bond lookup with clean/dirty price, YTM, duration, convexity, DV01
- Yield curve: par curve (live from Treasury.gov), bootstrapped spot, forward rates, 2s10s / 3m10y / butterfly readouts
- Relative value: bond vs. own curve (Z-spread) and vs. rating/sector peers (Comparison tab), plus a systematic relative-value ranking across all bonds (residual vs. rating-bucket typical spread)
- Portfolio-level duration, DV01, convexity, weighted YTM, VaR
- Key rate duration — per-tenor partial duration via cashflow mapping, reconciles exactly to total modified duration, portfolio-level bar chart + per-instrument table (Portfolio & Risk tab)
- Curve shock / scenario stress test (parallel, custom bp shock, plus a separate corporate spread shock) on a single bond or full portfolio
- Return attribution — carry + roll / curve effect / spread effect decomposition for every bull/base/bear/custom scenario, 3-month horizon
- Historical scenario replay — three real, sourced episodes (2022 Fed hiking cycle, March 2023 SVB shock, January 2026 JGB contagion) re-shaped as key-rate node deltas and priced through the KRD engine, not a single parallel shock
- Carry & roll-down analysis, and a full cashflow schedule, per bond (Bond Valuation tab)
- Execution & liquidity (illustrative) — bid-ask, days-to-liquidate, 3-dealer mock RFQ comparison, per bond
- Credit fundamentals — real, sourced leverage/coverage (AAPL, TSLA) and capital ratios (JPM, bank-appropriate metric) per corporate issuer
- Historical curve snapshots — real 6-month curve evolution and 2s10s trend, sourced live
- Illustrative credit spread curve by rating, overlaid with real computed corporate Z-spreads
- Watchlist alerts and upcoming Treasury auction calendar (Dashboard)
- Derivatives — Treasury futures (illustrative CTD/DV01) and par swap rates (real curve + illustrative but structurally-grounded swap spread), plus a portfolio DV01 hedge calculator (Derivatives tab)
- Inflation-linked bonds — real TIPS yield curve (sourced), curve-implied vs. directly-quoted breakeven inflation, two illustrative TIPS instruments priced on a real basis (Yield Curve tab)
- International curve overlay — US vs. JGB/Bund/Gilt at matching tenors, cross-market 10Y spreads, real sourced levels (Yield Curve tab)
- Benchmark-relative analytics — active duration/yield/spread and duration-based tracking error vs. an illustrative Agg-style benchmark, anchored to real sourced index characteristics (Portfolio & Risk tab)
- Multi-factor risk model — Level/Slope/Curvature/Spread factor exposures (reusing the key rate duration engine) with a correlation structure, showing genuine diversification benefit vs. naive risk summation (Portfolio & Risk tab)
- Monte Carlo / fat-tailed VaR — simulation-based VaR and CVaR/Expected Shortfall, drawn from the same factor covariance matrix via Cholesky decomposition, normal vs. fat-tailed side by side (Portfolio & Risk tab)
- "What This Means" contextual explainers on every tab

## Future Features — Ranked by Relevance

What's left, ordered by what a real bond trader or portfolio manager would reach for first, not by build difficulty.

1. **ESG / green bond coverage** — a full asset category with zero presence right now, and a growing one.
2. **Trade blotter** — log a trade, track realized vs. unrealized P&L over time. Different in kind from the multi-user/auth items already ruled out of scope; a single-user paper-trading log would close the gap between "analytics tool" and "workbench."
3. **OAS (option-adjusted spread)** for callable bonds — the single biggest gap vs. Bloomberg's YAS. Explicitly out of scope in the valuation skill; naming it as "here's what I'd build next" beats faking a number.
4. **Repo/financing rate integration** — bond desk P&L isn't just price return, it's price return net of funding cost.

If picking just one next: **trade blotter**. It's the cheapest of the four to build on what's already here, and it's the piece that would make this read as a workbench you actually use, not just one you analyze with.

## Deliberately Out of Scope

Different in kind from the list above — these are platform-maturity items worth naming as "if this were production," not worth building for a portfolio demo:

- Multi-user desk book / auth
- Real-time streaming prices vs. end-of-day snapshots
- Compliance/limits checking (position limits, concentration limits)

## What NOT to Build, and Why That's a Feature

Full OAS modeling, multi-dealer RFQ/execution, and real compliance infrastructure are what actually separate a demo from a production system. Naming them explicitly — "out of scope, here's why, here's what I'd add" — reads as more sophisticated in an interview than quietly shipping a shallow version of all three.

## Sources Consulted

- [Bloomberg Fixed Income Trading](https://professional.bloomberg.com/products/trading/electronic-markets/fixed-income/)
- [Bloomberg Functions List — Wall Street Oasis](https://www.wallstreetoasis.com/resources/data/bloomberg/bloomberg-functions-shortcuts-list)
- [Using Bloomberg analytics for pre-trade decisions](https://www.bloomberg.com/professional/insights/trading/using-bloomberg-analytics-to-make-better-pre-trade-decisions/)
- [Japan's Bond Market Crash — CNBC](https://www.cnbc.com/2026/07/14/japan-bond-jgb-yields-.html)
- [Fixed-Income Return Attribution — AnalystPrep (CFA Level III)](https://analystprep.com/study-notes/cfa-level-iii/fixed-income-return-attribution/)
- [Key Rate Duration — AnalystPrep (CFA)](https://analystprep.com/cfa-level-1-exam/fixed-income/key-rate-duration-2/)
- [Attributing Return for Fixed Income Portfolios — FactSet](https://insight.factset.com/hubfs/White%20Papers/%20Attributing_Return_for_FI_Portfolios_WP.pdf)
- [The Many Interest Rates in 2022 — St. Louis Fed](https://www.stlouisfed.org/on-the-economy/2023/jan/many-interest-rates-2022)
- [2-year Treasury yield posts biggest 3-day decline since 1987 stock crash — CNBC](https://www.cnbc.com/2023/03/13/us-treasury-yields-investors-assess-the-state-of-the-economy.html)
- [Japan's bond market is back in play after decades in the wilderness — CNBC](https://www.cnbc.com/2026/07/14/japan-bond-jgb-yields-.html)
- [Apple (AAPL) Debt-to-EBITDA — GuruFocus](https://www.gurufocus.com/term/debt-to-ebitda/AAPL)
- [Tesla (TSLA) Debt-to-EBITDA — GuruFocus](https://www.gurufocus.com/term/debt-to-ebitda/TSLA)
- [JPMorgan Chase Q1 2026 capital ratios — StockTitan 8-K filing](https://www.stocktitan.net/sec-filings/JPM/8-k-jpmorgan-chase-co-reports-material-event-ff61953e159c.html)
- [Japan 10-Year / 30-Year Government Bond Yield — Trading Economics](https://tradingeconomics.com/japan/government-bond-yield)
- [Germany 10-Year / 2-Year Bond Yield — Trading Economics](https://tradingeconomics.com/germany/government-bond-yield)
- [UK 10-Year / 2-Year / 30-Year Gilt Yield — Trading Economics](https://tradingeconomics.com/united-kingdom/government-bond-yield)
- [10-Year / 30-Year TIPS Real Yield (DFII10, DFII30) — FRED](https://fred.stlouisfed.org/series/DFII10)
- [10-Year Breakeven Inflation Rate (T10YIE) — FRED](https://fred.stlouisfed.org/series/T10YIE)
- [5-Year TIPS Auction Results — TIPSwatch](https://tipswatch.com/2026/06/18/5-year-tips-auction-gets-a-real-yield-of-1-955-a-good-result-for-investors/)
- [Swap Spreads: Analysis & Outlook — Amundi Research Center](https://research-center.amundi.com/article/swap-spreads-analysis-challenges-and-outlook)
- [Negative Swap Spreads — Josephine Smith, NYU Stern](https://www.stern.nyu.edu/sites/default/files/assets/documents/con_034334.pdf)
- [BlackRock Aladdin — fixed income risk, order management, attribution](https://www.blackrock.com/aladdin)
- [Monte Carlo VaR under Basel III](https://www.mdpi.com/2227-9091/13/8/146)
- [Fixed income tracking error — Vanguard](https://corporate.vanguard.com/content/corporatesite/us/en/corp/articles/whats-secret-tight-bond-index-fund-tracking.html)
- [Bloomberg PORT / MAC3 multi-asset factor risk models](https://www.bloomberg.com/professional/products/risk/mac3)
- [ESG/green bond sustainability rating methodology — ISS](https://insights.issgovernance.com/posts/the-sustainability-bond-rating-and-the-current-gsss-market/)
- [iShares Core U.S. Aggregate Bond ETF (AGG) — duration/yield/OAS benchmark stats](https://www.ishares.com/us/products/239458/ishares-core-total-us-bond-market-etf)
