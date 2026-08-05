# RateLens — Technical Concepts Glossary

What each number on the platform means, why it moves, and what it signals about the economy — not just the formula. Written for README/documentation inclusion; pairs with the three calc skills (`ratelens-bond-valuation`, `ratelens-yield-curve`, `ratelens-macro-rate-drivers`), which hold the formulas and code. This doc holds the "so what."

## Price, Yield, and Why They Move Opposite Ways

A bond pays a fixed coupon. If market interest rates rise after you buy it, new bonds get issued with higher coupons, so your fixed-coupon bond becomes less attractive — its price falls until its yield matches what the market now demands. That's the entire mechanism behind the golden rule of fixed income: **price and yield move in opposite directions.** Everything else in this glossary is a refinement of how much and how fast.

**Yield to Maturity (YTM)** is the single discount rate that makes a bond's future cash flows (coupons + principal) equal its current price. It's the return you'd earn if you bought the bond today, held it to maturity, and reinvested every coupon at that same rate — that reinvestment assumption is the main thing that makes YTM an approximation of realized return rather than a guarantee of it.

**Current yield** (annual coupon ÷ price) is a cruder, quicker number — it ignores the time value of money and any capital gain or loss to par. Useful for a fast eyeball check, not for real comparison across bonds.

**Clean vs. dirty price:** the quoted ("clean") price excludes interest that has accrued since the last coupon. The actual cash you'd pay to settle the trade ("dirty" price) adds that **accrued interest** back in. This trips people up mainly because two bonds with the same clean price can require different cash at settlement depending on where they sit in their coupon cycle.

## Duration — How Sensitive Is This Bond to Rate Moves

**Macaulay Duration** is the weighted-average time (in years) until you receive a bond's cash flows, where each cash flow is weighted by its present value. Conceptually it's the "center of mass" of the bond's payments — a 10-year bond with a fat coupon has a Macaulay duration meaningfully less than 10 years, because you're getting real money back well before maturity. A zero-coupon bond's Macaulay duration exactly equals its maturity, since there's only one cash flow. Macaulay's original point in inventing this (1938) wasn't rate-risk measurement — it was **immunization**: at a horizon equal to Macaulay duration, price risk (rates rise, bond price falls) and reinvestment risk (rates rise, but you reinvest coupons at the new higher rate) roughly offset. That's still the deepest reason the concept matters, even though in practice everyone uses it as a risk number.

**Modified Duration** converts that time-based number into a price-sensitivity number: the approximate percentage price change for a 1% (100bp) move in yield. A modified duration of 8 means "if yields rise 1%, expect this bond's price to fall about 8%." Higher duration = more interest-rate risk, full stop. This is why long-maturity, low-coupon bonds (more cash flow weight pushed out in time) are the most rate-sensitive instruments in fixed income, and why the RateLens mock UST 30Y (duration ~15.6) moves so much more than the UST 2Y (duration ~1.9) for the same yield shock.

**DV01 (Dollar Value of 1bp)**, sometimes PV01, is modified duration translated into actual dollars: the price change for a 1 basis point (0.01%) yield move. This is the number a trading desk actually watches day to day — duration is the textbook percentage, DV01 is "how many dollars did I just make or lose." **A low DV01 means low dollar sensitivity per basis point** — either because the position is short-duration (a T-bill or 2-year note has an inherently low DV01 per unit face value) or because the position size itself is small. Low DV01 isn't inherently "good" or "bad" — it's a statement about risk appetite: a low-DV01 book is defensive and stable but also captures less upside if you have a strong view that rates are about to fall. Desks size positions in DV01 terms precisely so risk is comparable across bonds of very different maturities and coupons.

**Convexity** is the second-order correction to duration. Duration alone assumes a straight-line relationship between yield and price, but the real relationship curves — bond prices rise more than duration predicts when yields fall, and fall less than duration predicts when yields rise. That asymmetry is *positive convexity*, and it's a feature, not a flaw: everything else equal, more convexity is better, because it means the bond's payoff profile is skewed in your favor. Convexity matters most for long-duration bonds and for large yield moves — for small moves on short bonds it's a rounding error; for the UST 30Y under a 100bp shock it meaningfully changes the answer.

## Spreads — What the Market Is Charging for Risk

**Nominal spread** is the crudest comparison: a bond's YTM minus a benchmark government yield at the same maturity. It's a single-point comparison and ignores the shape of the curve between coupon dates.

**Z-spread** is the more honest version: the constant spread you'd need to add to *every point* on the government zero (spot) curve so that discounting the bond's actual cash flows at "curve rate + spread" reproduces its market price. Because it uses the whole curve rather than one point, it's the standard way credit and relative-value desks actually quote spread. In the RateLens mock portfolio, a AA-rated corporate like the Apple bond shows a tight Z-spread (~30bp) — the market barely charges extra for that credit risk — while a BB+ name like the Tesla bond shows a much wider spread (~280bp), reflecting real default risk, lower liquidity, and less certainty about the credit over the life of the bond. **Spread is compensation for risk the government curve doesn't carry: default risk, liquidity risk, and structural/optionality risk.** Widening spreads on a specific credit are an early warning sign; tightening spreads across the board (as seen in 2026's investment-grade market broadly) signal risk appetite is high and credit fundamentals are viewed as solid — worth watching for complacency as much as comfort.

## The Yield Curve — Reading the Market's Own Forecast

**Par curve** is simply the yields quoted in the market at each maturity — what you'd actually see on a screen.

**Spot (zero) curve** is derived by bootstrapping: solving for the discount rate that applies to a single cash flow at each maturity, stripping out the effect of coupons paid along the way. When the par curve is upward-sloping (as it is now), the zero curve sits slightly *above* it at the long end — a bond's later cash flows have to be discounted a bit harder than its earlier ones to reconcile with a rising curve. This isn't a market quirk, it's arithmetic, and it's the correct textbook direction to check against if a bootstrap ever looks wrong.

**Forward curve** answers a different question: "given today's curve, what future short rate is implied, arbitrage-free?" A 5Y5Y forward rate isn't the market's *prediction* of where the 5-year rate will be in five years — it's what has to be true for a 10-year bond and a rolled sequence of 5-year bonds to earn the same return today, given no arbitrage. It's a useful anchor, but calling it a "forecast" overstates what it actually is; it's closer to a breakeven.

### Curve shape as an economic signal

**2s10s spread** (10-year yield minus 2-year yield) is the most-watched curve signal. A **positive** spread (normal curve) means the market expects the economy to need higher rates further out than right now — consistent with continued growth. A **negative** spread (inversion) has preceded every US recession in the past 50 years, because it signals the market expects the Fed to cut rates in the future in response to weakening growth, pulling expected short rates below current ones. As of the current RateLens curve, 2s10s sits at roughly **+45bp — positive, having un-inverted from the 2022–2024 inversion.** On its face that reads as less immediate recession risk than two years ago.

The nuance worth having ready in an interview: *not all steepening is the same signal.* A "bull steepener" happens when the front end falls faster than the back end — usually because the Fed is cutting, which is generally read as a controlled, disinflationary path (a genuinely reassuring signal). A "bear steepener" happens when the back end rises faster than the front end — usually because the market is pricing in more term premium, more fiscal risk, or more long-run inflation risk, even while the Fed holds. The current environment — an elevated front end (Fed funds still at 3.63%) alongside a 30-year yield near 5.3% and a rising term premium — leans toward the bear-steepener read. **The curve un-inverting isn't automatically good news; it matters *why* it un-inverted.**

**3m10y spread** is the Fed's own preferred recession-signal spread (referenced explicitly in Fed research), because it's less prone to some of the false signals that can affect 2s10s. Same interpretation logic: negative = warning sign, positive = normal.

**Butterfly (2s5s10s)** measures the curve's curvature independent of its level or slope — is the belly of the curve (5Y) rich or cheap relative to a straight line between the 2Y and 10Y. A pronounced hump or dip usually reflects a specific supply/demand imbalance or a market view concentrated at one part of the curve (e.g., heavy 5-year issuance, or a market view that cuts happen faster than the 10-year is pricing).

## Term Premium — Why the Highest 30-Year Rate in Years Is a Genuinely Mixed Signal

A long-term bond yield can be decomposed into two pieces: the **expected average path of short-term rates** over the bond's life, plus a **term premium** — extra compensation investors demand for the risk and uncertainty of locking up money for decades instead of rolling short-term paper. As of the current data, the 10-year term premium sits above **0.80%, the highest since 2011**, and accounts for more than half of the recent rise in the 10-year yield. That's a specific, identifiable claim: the move isn't primarily "the market expects higher average short rates" (which would be a growth/inflation story) — it's "investors want more compensation to hold long-duration government debt at all" (a risk/uncertainty story), tied to the trajectory of US deficits (5.8% of GDP in 2026, projected to reach 6.7% by 2036 per the CBO).

**Why a high 30-year rate is "bad," and why that's not the whole story:**

- **For the government:** every dollar of new and rolled-over debt refinances at the higher rate, so debt service costs compound the deficit problem that pushed rates up in the first place — a feedback loop worth naming explicitly.
- **For asset valuations broadly:** the long-end yield is the discount rate applied to far-future cash flows. Higher long rates mechanically lower the present value of anything priced on long-duration cash flows — long-duration growth stocks, real estate, infrastructure. This is the direct equity-market read-through of a bond-market move.
- **For borrowers generally:** mortgage rates, long-term corporate borrowing costs, and municipal financing costs all key off this part of the curve. Higher 30-year yields raise the cost of capital economy-wide for anything financed long.
- **For savers and fixed-income buyers:** a higher yield is *more income*, not less — a 30-year bond bought today locks in a better rate than the same bond bought two years ago. This is the case where "bad for the economy" and "bad for me personally" diverge, and it's worth being precise about which one is being asked.
- **The genuinely ambiguous part:** rising long rates *can* reflect strong expected growth (a "good" reason rates rise) rather than fiscal risk (a "concerning" reason). The term premium decomposition above is exactly how you tell the two apart — and in the current data, more than half the move is term premium, which leans toward the fiscal-risk reading rather than the strong-growth reading.

## T-Bills — A Different Quoting Convention Entirely

T-bills don't pay a coupon; they're sold at a discount to face value and mature at par. They're quoted on a **bank discount basis** — `(Face − Price)/Face × (360/DaysToMaturity)` — which uses a 360-day year and divides by face value rather than price. This convention **understates the true return**, sometimes by 15–25bp depending on the bill's maturity, because it uses the wrong denominator (face instead of price paid) and the wrong day count (360 instead of 365). Converting to **bond-equivalent yield (BEY)** is what makes a T-bill's return comparable, apples-to-apples, to a coupon note or bond. Skipping this conversion is a classic way to make a T-bill look cheaper than it actually is.

## Portfolio-Level Risk

**Weighted YTM** and **portfolio effective duration** are simply the position-weighted averages of the bond-level numbers above — they tell you the blended income and the blended rate sensitivity of the whole book, not any single holding.

**Value at Risk (VaR)** answers "how much could this portfolio plausibly lose over a given horizon, at a given confidence level" — RateLens uses a duration-based parametric VaR: `1.645 × Portfolio Duration × Assumed Daily Yield Volatility × Portfolio Value` for a 95% one-day estimate. The honest caveats: this assumes yield moves are normally distributed (real yield moves have fatter tails, so this understates true tail risk) and it assumes a parallel curve shift (a steepening or flattening move that isn't parallel will produce a different, and possibly worse, outcome than this number implies). VaR tells you a plausible loss threshold; it doesn't tell you what happens in the tail beyond that threshold — that's what CVaR/Expected Shortfall is for, and it's a reasonable next addition once the basic VaR is trusted.

## Quick Signal Reference

| Metric | Rising / widening usually means | Falling / narrowing usually means |
|---|---|---|
| Yield (any tenor) | Price falling; higher cost of capital at that maturity | Price rising; cheaper cost of capital |
| Duration / DV01 | More rate sensitivity, more risk (and more upside if rates fall) | Less rate sensitivity, more defensive |
| Z-spread on a specific credit | Market pricing in more default/liquidity risk on that name | Improving credit view, or excess risk appetite (watch for complacency) |
| 2s10s spread | Curve normal/steepening — read the *why* before calling it good news | Flattening toward inversion — historically a recession watch signal |
| Term premium | Investors demanding more compensation for duration/uncertainty risk (often fiscal-driven) | Confidence in long-run stability; cheaper long-term financing |
| 30Y yield | Higher long-run cost of capital economy-wide; pressure on long-duration asset valuations | Easier long-run financing conditions; supportive of long-duration assets |

## Tab-by-Tab Guide: What's There, What It's For, and When to Worry

The sections above explain what each metric *is*. This section is the operator's guide — what's on each screen, what question it answers, what a buy/sell (or hold/trim) signal actually looks like, and what level separates "normal" from "something's wrong." Reference levels below are the same ones wired into the platform's own watchlist rules and typical-spread benchmarks, not arbitrary round numbers.

### Dashboard

**What's there:** today's par curve chart, 10Y/30Y yield, 2s10s spread, 10Y term premium, a watchlist across all seven instruments, a Watchlist Alerts table, and an upcoming Treasury auction calendar.

**What it's for:** the morning check — where the curve sits today, whether anything on the book needs attention, and what supply is coming that could move prices.

**Signal:** this page isn't a buy/sell screen by itself; it's a triage screen. A row in Watchlist Alerts marked **TRIGGERED** means act or investigate now; **WATCH** means it's approaching a level worth planning around; **CLEAR** means no action. Heavy issuance clustering on the auction calendar (multiple large sizes in the same week) is a mild bearish signal into the auction date itself — more supply hitting the market pressures price down / yield up short-term, independent of any change in fundamentals.

**Levels that matter:**

| Signal | Watch | Triggered / distress |
|---|---|---|
| 10Y yield | > 4.30% | > 4.50% |
| 30Y yield | > 4.80% | > 5.00% |
| 2s10s spread | < 25bp | < 0bp (inversion) |
| Corporate Z-spread (per name, e.g. TSLA7Y) | > 200bp | > 250bp |
| Investment-grade Z-spread (per name, e.g. JPM5Y) | > 80bp | > 100bp |

### Bond Valuation

**What's there:** a single-instrument deep dive — clean price, YTM, current yield, DV01, Macaulay/modified duration, convexity, nominal and Z-spread, the Carry & Roll-Down breakdown, and the full cashflow schedule.

**What it's for:** answering "is this specific bond fairly priced, what does holding it actually earn me, and when does the cash show up."

**Signal:** the real buy/sell read here is Z-spread against your own view of fair compensation for the credit. If Z-spread is wider than you think the credit risk justifies, the bond is cheap — a buy candidate. If it's tighter than your view, it's rich — trim or avoid. Carry & Roll-Down tells you the return for doing nothing: on an upward-sloping curve, positive coupon carry plus a positive roll return is the default expectation. A bond showing a **negative** roll return while the curve is upward-sloping is a flag to double-check the inputs (own maturity vs. curve tenor, spread assumption) before trusting the number.

**Levels that matter:** use the rating-bucket typical spreads as your "is this normal for the rating" anchor — AAA ~20bp, AA ~35bp, A ~70bp, BBB ~115bp, BB ~220bp, B ~380bp (5Y benchmark). A bond trading 50-75bp or more above its bucket without an obvious explanation is either genuine relative value or the market pricing in something the rating hasn't caught up to yet — exactly what a credit-fundamentals check (leverage, coverage, covenants) is for, which is on the roadmap, not yet built.

### Yield Curve

**What's there:** the par and bootstrapped zero curve overlay, 2s10s / 3m10y / butterfly readouts, the par-vs-zero rate table, implied forward rates, the T-bill discount-to-BEY conversion, and six months of real curve history (level + 2s10s trend).

**What it's for:** the macro backdrop that everything else on the platform gets read against — not a single trade idea, but the regime you're trading within.

**Signal:** curve shape doesn't say "buy this bond," it says what kind of trade is even attractive right now. A steep curve makes roll-down carry trades (buying a point on the curve and letting it roll toward a lower-yielding, shorter tenor) genuinely profitable. A flat or inverted curve kills that trade — coupon income becomes the only reliable source of positive carry, and extending duration gets riskier for less reward. A pronounced butterfly (the 5Y meaningfully off a straight line between 2Y and 10Y) flags a relative-value opportunity in the belly of the curve, not just noise.

**Levels that matter:** 2s10s and 3m10y — positive is the normal, healthy state; inside +25bp is worth watching; negative (inversion) has preceded every US recession in the past 50 years. Butterfly readings under ~5bp are noise from interpolation; above ~15-20bp reflects a real supply/demand distortion or market view concentrated at that tenor.

### Portfolio & Risk

**What's there:** editable position weights, portfolio value, weighted YTM, effective duration, portfolio DV01, and 95% 1-day VaR.

**What it's for:** whole-book risk, not any single holding — how much could this book plausibly lose, and how rate-sensitive is it in aggregate.

**Signal:** this page isn't buy/sell either — it's a "should I rebalance or de-risk" screen. VaR climbing as a % of NAV without a deliberate weight change means duration or convexity shifted underneath you (bonds aged, prices moved) and the book got riskier without you choosing that.

**Levels that matter:** there's no universal "good" VaR number — it has to be sized against mandate and risk tolerance — but as a rule of thumb, a 1-day 95% VaR consistently running above roughly 1-2% of NAV is aggressive for what's supposed to be a conservative fixed-income book (equity books routinely run higher, which is the point of comparison). Watch the trend more than the level: a jump between rebalances is the actual signal.

### Scenarios

**What's there:** bull/base/bear portfolio price-impact buttons, a custom parallel-shock input, and a per-bond breakdown of the resulting price move.

**What it's for:** stress-testing the current book before committing to it, or answering "what happens to me if my rate view is wrong."

**Signal:** if the bear-case impact on the book is larger than you can tolerate, that's the trigger to trim duration or rotate into shorter maturities *before* it happens, not after. Convexity means the bull-case gain is always larger than the bear-case loss for the same basis-point move — so between two bonds with similar duration, the more convex one is the better asymmetric bet if you're genuinely uncertain which way rates move next.

**Levels that matter:** the platform's bear/bull scenarios (+56bp / -64bp) aren't arbitrary — they're anchored to the deficit/term-premium framework in the macro section above. Use your own bear-case number as the real test: if that price impact breaches what your mandate or comfort level allows, that's the actionable signal, not the specific bp figure shown.

### Comparison

**What's there:** a multi-instrument selector, a curve-positioning scatter (cheap/rich vs. the par curve), a Z-spread bar chart, side-by-side fundamentals, a Relative Value Ranking across all bonds (residual vs. typical spread for the rating), and a credit spread curve by rating with the platform's actual bonds overlaid.

**What it's for:** choosing among alternatives — which of these bonds is the better relative value right now, not just whether any one of them looks fairly priced in isolation.

**Signal:** a dot above the dashed par curve, or a wider bar than peers of similar credit quality, is cheap — more yield than the curve or the peer group implies for that risk, a buy candidate. Below the line or narrower than peers is rich — a candidate to trim or avoid. The Relative Value Ranking's residual column is the sharper version of the same read: a **positive residual** means cheap versus its own rating bucket specifically, which can disagree with "looks cheap in absolute terms" — a BB+ bond with a wide-looking 280bp spread can still be *rich* if BB+ paper typically trades even wider.

**Levels that matter:** same rating-bucket anchors as Bond Valuation (AAA 20bp / AA 35bp / A 70bp / BBB 115bp / BB 220bp / B 380bp). A residual beyond roughly ±50bp from the bucket typical is worth a second look — either a real opportunity or a sign the rating hasn't caught up to something the market already knows.

## Sources for the current-market anchors referenced above

- [CBO Budget & Economic Outlook 2026-2036](https://www.cbo.gov/publication/62105) — deficit and debt-to-GDP path
- [FRED / term premium research](https://fredblog.stlouisfed.org/2025/05/the-term-premium) — 10Y term premium level and interpretation
- [Federal Reserve H.15](https://www.federalreserve.gov/releases/h15/) — Fed funds effective rate
- [Goldman Sachs — US fiscal concerns and Treasury yields](https://www.goldmansachs.com/insights/articles/how-us-fiscal-concerns-are-affecting-bonds-currencies-stocks)
