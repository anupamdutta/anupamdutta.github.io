---
layout: post
title: "IV Curve (Volatility Smile): Before You Buy an Option, Look at This"
date: 2026-10-25
tags: [IV, Volatility Smile, Options Trading, NIFTY]
---

## Before I buy an option, I want to know one thing. Am I paying too much?

Most option buyers look at the chart.

They identify support, resistance, a breakout, or a possible reversal. Then they decide whether to buy a Call or a Put.

Fair enough. Direction matters.

But there is another question I want answered before I put my money on the table:

**Am I buying this option at a reasonable implied volatility, or am I paying an inflated premium?**

That is where the **IV Curve, also called the Volatility Smile or Volatility Skew**, comes into the picture.

It tells me how implied volatility is distributed across different strike prices.

And if I am buying NIFTY options, I do not want to ignore that information.

Because sometimes the strike I want to buy is precisely the strike where the market is charging me an expensive volatility premium.

---

## What exactly is an IV Curve?

An IV Curve is a plot of implied volatility against option strike prices.

The horizontal axis represents the strike price. The vertical axis represents implied volatility, usually expressed as an annualised percentage.

Every point on the curve represents the implied volatility associated with a particular strike.

For example, imagine the following NIFTY IV readings:

- 22,000 strike: IV of 24%
- 23,000 strike: IV of 13.5%
- 23,400 strike: IV of 13.8%
- 24,000 strike: IV of 16%
- 25,000 strike: IV of 25%

These numbers are illustrative, apart from the values discussed in the chart below.

Notice something interesting.

IV is not the same at every strike.

The market may charge a much higher implied volatility for an out-of-the-money Put than for a near-the-money option. Calls can also become expensive at particular strikes, especially when traders aggressively demand upside protection or exposure.

The result is a curve rather than a straight line.

**The important point is this: two options on the same underlying, with the same expiry, can carry very different implied volatilities.**

That difference matters when I am deciding which strike to buy.

---

## Let's read the NIFTY IV Curve

![NIFTY Implied Volatility Curve showing strike-wise IV, spot price, ATM strike and selected strike](/assets/iv-smile-nifty.png)

*Figure 1: NIFTY IV Curve. The chart displays a strike-wise volatility snapshot, with spot near 23,366.70, the ATM strike at 23,400, and the selected 23,300 strike showing an IV of approximately 13.397%. The displayed trade date is 5 June 2026.*

Let me walk you through what I see here.

### 1. The lowest part of the curve

Look at the region around the 23,100 to 23,500 strikes.

IV is sitting close to 13.4% to 13.8%.

This is the flatter, lower-volatility region of the curve.

The selected 23,300 strike has an IV of approximately 13.397%, as indicated in the chart.

The spot price is around 23,366.70, and the ATM strike is 23,400.

So the selected strike is close to the current market level.

At first glance, an IV of 13.397% might look attractive to an option buyer. But I would not immediately conclude that the option is cheap.

Why?

Because **a low IV does not automatically mean a cheap option**.

I still need to compare that IV with the option's historical IV range, its own recent readings, the remaining time to expiry, and the volatility of comparable strikes.

If this option normally trades at 10% IV, then 13.397% might actually be expensive.

If it normally trades at 20%, the same reading might represent a relatively inexpensive volatility entry.

Context decides.

### 2. The left side of the curve

Now look towards the lower strikes.

IV rises sharply as we move from the region around 23,000 towards 22,000 and below.

At the far left, the curve climbs above 30%.

This is a substantial difference from the IV near the money.

In index options, this type of shape is often associated with **downside volatility skew**.

Why does this happen?

Market participants frequently pay up for downside protection. During periods of uncertainty, traders may rush to buy Put options, increasing their premiums and the implied volatility required to explain those prices.

There can also be differences in tail-risk expectations, hedging demand, liquidity, and the supply of options at different strikes.

The result is that lower-strike Puts can carry considerably higher IV than near-the-money options.

For an option buyer, this raises an important question.

Am I buying a Put because I expect a sharp fall, or am I buying it after the market has already made that downside protection expensive?

Both situations can look identical on a price chart.

But they are not identical trades.

### 3. The right side of the curve

Move towards the higher strikes.

IV begins rising again, eventually reaching approximately 27% near the 25,200 strike.

This gives the curve a smile-like shape, although the left side is much steeper than the right.

A volatility smile does not have to be perfectly symmetrical.

In equity indices, the curve often shows a pronounced downside skew. In other market conditions, demand for upside Calls can also push their IV higher.

The shape tells me that the market is not pricing every possible move equally.

Different strikes carry different volatility premiums.

That is exactly why I want to inspect the curve before choosing a strike.

---

## Why every option buyer should check the IV Curve

Let me make this practical.

Suppose NIFTY is trading around 23,400.

I expect a bullish move and decide to buy a Call.

I have several strikes available:

- An ITM Call
- An ATM Call
- A slightly OTM Call
- A far OTM Call

The far OTM Call may require less money upfront. That does not automatically make it a bargain.

If its IV is substantially higher than the IV of comparable strikes, I may be paying a premium for that particular exposure.

The option can still make money if NIFTY rises sufficiently. But I need to overcome the combined effects of the underlying movement, time decay, and any change in IV.

Now imagine that the ATM Call has an IV of 14%, while the far OTM Call has an IV of 27%.

That difference deserves investigation.

The far OTM Call may be expensive relative to the rest of the curve. Its low rupee premium may simply reflect its low probability of finishing in the money, while its IV may reflect demand for a large upside move.

**A cheap-looking option is not necessarily a cheap-volatility option.**

And the reverse is equally important.

An ATM option with a higher rupee premium can have a lower IV than a far OTM option. The ATM option costs more in absolute terms because it has a different relationship with the underlying, not necessarily because its volatility is overpriced.

This is why I separate two questions:

1. How much money am I paying for this option?
2. How expensive is its implied volatility relative to an appropriate benchmark?

The IV Curve helps answer the second question.

---

## A practical example: Direction right, trade still wrong

Suppose I buy a NIFTY Call when:

- NIFTY spot is 23,400
- The selected Call has an IV of 24%
- The option premium is ₹150

These are hypothetical numbers for illustration.

Now NIFTY moves in my favour.

But the market becomes calmer, and the option's IV falls from 24% to 16%.

My Call loses some of its volatility premium.

If the underlying move is not large enough to compensate for that decline, along with time decay, my option may deliver a disappointing return despite the correct directional view.

This is one of the most frustrating experiences in option buying.

The market moves in the direction I expected, yet my P&L does not respond as strongly as I anticipated.

The IV Curve gives me a way to investigate whether the strike I am considering carries an unusually high volatility premium in the first place.

It does not tell me exactly what IV will do next. But it helps me understand what I may be paying for today.

---

## Cheap IV versus expensive IV

This is where I want to be careful.

I do not classify an option as cheap or expensive simply by looking at the height of one point on the curve.

I use three comparisons.

### 1. Compare the strike with its own history

Suppose a particular NIFTY strike usually trades around 12% to 16% IV, but today it is trading at 25%.

That is a reason to investigate.

Has a major event increased uncertainty? Is there aggressive demand for protection? Is the market anticipating a larger move?

If I am buying, I want to understand why the volatility premium has expanded.

Likewise, if the strike is trading below its normal range, I want to know whether volatility is genuinely subdued or whether the market is underpricing an upcoming risk.

Historical comparisons should use comparable maturities and market conditions wherever possible.

### 2. Compare it with neighbouring strikes

This is where the IV Curve becomes particularly useful.

Suppose the ATM IV is 14%, the next OTM strike is 16%, and a farther OTM strike is 25%.

The far OTM strike stands out.

That does not automatically make it a bad purchase. The higher IV may reflect legitimate demand or a particular risk embedded in that strike.

But it tells me not to assume that all strikes offer the same volatility value.

I can compare alternatives before committing my capital.

### 3. Compare it with the volatility environment

I also want to understand the broader volatility regime.

What is India VIX doing? Is the market approaching an event? Is the entire volatility surface rising, or is the increase concentrated in particular strikes?

If all strikes are experiencing a broad volatility expansion, a single strike's elevated IV may have a different meaning than if that strike alone is becoming unusually expensive.

**Relative value matters more than a number viewed in isolation.**

---

## The IV Curve is not a prediction of market direction

This distinction is important.

A steep downside skew does not mean NIFTY must fall.

A rising IV on higher-strike Calls does not mean a rally is guaranteed.

The curve reflects the implied volatility associated with option prices at a particular point in time. Those prices are influenced by supply and demand, hedging activity, market expectations, liquidity, and other factors.

The curve tells me how volatility is distributed across strikes.

It does not tell me where the underlying must go next.

I still need my directional view, market structure, and trade setup.

The IV Curve is an additional layer of information, not a replacement for analysis.

---

## One important limitation: The curve is only a snapshot

The curve in the image shows a particular volatility snapshot. It is not a permanent characteristic of NIFTY.

IV changes throughout the trading session. The shape can change as spot moves, time passes, order flow changes, and traders adjust their positions.

There is another detail I would not ignore.

The curve should ideally compare options with the same expiry and consistent pricing inputs. Different expiries can have different volatility levels because the market prices different event risks and time horizons.

For practical analysis, I also want to distinguish a genuine change in volatility from a misleading reading caused by a stale quote, a wide bid-ask spread, or an illiquid strike.

A curve built from unreliable option prices can tell a convincing story that is simply wrong.

Data quality matters.

---

## My pre-buy checklist

Before buying a NIFTY Call or Put, I want answers to these questions:

- **Direction:** Why do I expect the underlying to move in my favour?
- **IV level:** Is the selected strike's IV high or low relative to its own history?
- **IV Curve:** Is my chosen strike unusually expensive compared with neighbouring strikes?
- **Expiry:** Am I comparing options with the same expiry?
- **Event risk:** Is a scheduled event affecting volatility pricing?
- **Liquidity:** Are the quotes reliable, with a reasonable bid-ask spread?
- **Risk and reward:** Is the potential payoff worth the premium and the risk of time decay or IV contraction?

I do not need every answer to point in the same direction. I need to understand the trade I am entering.

An option can be expensive and still be the right trade if I expect a sufficiently large move.

An option can have low IV and still be a poor trade if the underlying is unlikely to move enough before expiry.

The goal is not to buy the lowest IV available.

The goal is to avoid paying an unjustified volatility premium for the exposure I want.

---

## Final thought

I look at the price chart to understand where NIFTY might go.

I look at the option chain to understand what I will pay.

And I look at the IV Curve to understand how that volatility premium changes from one strike to another.

That third step is easy to overlook, especially when the market is moving fast and a breakout looks tempting.

But the option with the smallest premium is not necessarily the cheapest, and the option with the lowest IV is not necessarily undervalued.

**Before buying an option, I want to know not just what I am paying, but how that price compares with the volatility the market is charging elsewhere.**

That is what makes the IV Curve worth checking.

Trade the setup. Respect the premium. Never confuse a low option price with a good deal.
