# RateLens — Narrative Layer

## The Why (1-pager)

I traded equities professionally. That job puts you in front of FX and rates more than the title suggests — hedges, cross-border flows, the macro backdrop to every equity call — and rates were always the part I wanted to go deeper on. What's the curve pricing in. Why is the long end moving. Is a given bond actually cheap, or does the spread just look wide because nobody's bothered to check it against the curve properly. That curiosity never had a dedicated outlet on an equity desk.

RateLens is that outlet, built deliberately as the second platform in the same buildout as EquityLens. EquityLens proved out the pattern: product definition, a real calc layer, an interactive demo, on equities and portfolio risk. RateLens applies the same process to fixed income — bond valuation, yield curve construction, and macro-driven rate scenarios — because the two platforms together say something EquityLens alone doesn't: this isn't one asset class I happen to know, it's a repeatable way of turning trading-desk questions into working analytical tools, regardless of which market they're pointed at.

The build itself mirrors how the questions actually get asked on a desk. Price a bond, get its yield and duration. Look at the curve, read whether it's telling you something about growth or about fiscal risk. Build a position, know its risk in dollars, not just in theory. Stress it against a scenario before finding out the hard way. Every metric in RateLens — DV01, Z-spread, the 2s10s spread, term premium — is something I'd want an honest answer on before putting on a trade, not a textbook definition for its own sake.

## What It Shows

- **Fixed income fluency that goes past "I know the formulas."** The platform bootstraps a real spot curve from par yields, solves YTM numerically rather than approximating it, and decomposes long-end yield moves into expected short-rate path versus term premium — the actual decomposition a rates desk uses, not a simplified stand-in for it.
- **The ability to translate market intuition into a working product.** Trading gives you the questions worth asking. Turning "is this curve signal actually good news" into a scenario tool someone else can use is a different skill, and it's the one this project is built to demonstrate.
- **A repeatable process, not a one-off.** Same shape both times: product definition doc, a calc layer built as reusable, documented skills, an interactive demo, a plain-English explanation of what the numbers mean. That process is the actual asset — it's what let RateLens go from decision to working portfolio tool in one focused build, and it's what would let a third platform (credit, derivatives, whatever's next) start from a running start instead of zero.

## LinkedIn Post Draft

I spent my time as an equity trader more curious about the bond market than I probably should have been. Every equity call has a rates backdrop — where's the curve, what's the Fed pricing in, is the long end moving because growth is strong or because the market's worried about something else entirely. I never had a proper outlet to build that curiosity into something concrete.

So I built RateLens: a fixed income analytics platform — bond valuation, yield curve construction, macro rate scenarios, portfolio risk — as the second piece in a self-directed buildout that started with EquityLens, an equity research and portfolio risk tool.

Same process both times: define the product, build a real calculation layer (not a mockup pretending to calculate), ship an interactive demo, write down what the numbers actually mean and why. The point isn't "I built two finance tools." It's that the process transfers — trading experience turns into the right questions, and the build turns those questions into something someone else can actually use.

Next up: extending it with live market data and a peer comparison view. Open to conversations with anyone working at the intersection of markets and product.

#FixedIncome #QuantFinance #ProductBuilding #CareerPivot

## Interview Talking Points

**The pitch, in one breath:** "I traded equities, kept getting pulled into rates and FX along the way, and built RateLens to turn that curiosity into a real tool — bond pricing, curve construction, and macro scenario analysis, with the same calc-layer-first process I used to build EquityLens."

**Why bonds, specifically, after an equity background:** Every equity desk lives downstream of the rate environment. Building RateLens was the chance to go from "I watch the curve because it affects my equity book" to "I can actually construct the curve, explain what moved it, and stress-test a position against it."

**What's actually hard about this, that a naive build would skip:** Getting YTM from a price requires solving numerically — there's no closed form, so it's Newton-Raphson with a bisection fallback for when it doesn't converge cleanly. Bootstrapping a spot curve from par yields is a sequential problem, not a lookup, and it's also the easiest place to introduce a silent unit-scaling bug — which happened during this build, was caught by testing the calc layer in isolation before wiring it into the UI, and got fixed in both the reusable skill and the product. That's a better interview story than claiming nothing went wrong.

**What I'd change or add if this were production:** Option-adjusted spread for callable bonds — flagged explicitly as out of scope rather than faked. Key rate duration instead of a single parallel-shift number, since real curve risk isn't parallel. Live data — Treasury.gov or a market data connector — in place of the mock curve, which the calc layer was built to swap in without any changes to the math. A peer comparison view, which is scoped but not yet built.

**How this connects to EquityLens:** Same shape, different market — product doc, calc-layer-as-skills, interactive demo, plain-English explainer. Two platforms in, the process itself is the more interesting artifact than either platform alone: it's evidence of a repeatable way to go from a trading-desk question to shipped analytical software.

**Anticipated pushback — "isn't this just a toy, since the data's mocked":** The data is mock; the math isn't. Every formula — YTM solve, duration, convexity, curve bootstrap, Z-spread, term premium decomposition — runs the same whether the input is a hardcoded number or a live feed, which was a deliberate design choice from the first line of code, not a retrofit.
