---
layout: post
title: "Dealer Hedging, Gamma Compression, and the Hidden Structure of Option Premiums"
date: 2026-05-07
tags: [Options, Gamma, Market Microstructure, Volatility, Dealer Hedging]
---


## Most option traders think price moves premiums.

Sometimes it does.

But near heavily concentrated strikes, especially when dealers are trapped in short gamma exposure, premiums stop behaving normally.

At that point, dealer hedging itself becomes part of the movement.

That changes everything.

---

## The idea started from something I kept observing

There were moments where:

- Spot barely moved
- Option premiums expanded aggressively
- IV jumped
- Hedging activity accelerated across nearby strikes

Traditional pricing logic explained parts of it, but never the full structure.

The more I watched option chains closely, the clearer it became:

> Premium movement near important strikes is heavily influenced by dealer positioning and hedge flow reflexivity.

Not just direction.

Not just volatility.

Positioning.

---

## The MMR Terminal

To study this behavior, I built the **[MMR Terminal](https://anupamdutta.github.io/mmr-terminal-web/)**.

The purpose of the tool is simple:

> Find the approximate market maker risk price where dealer inventory pressure becomes structurally important.

I call this level:

$$
m = \text{MMR Risk Price}
$$

Near this region, option premiums often become extremely sensitive to hedging activity.

This is where the chain starts behaving differently.

Not statistically different.

Structurally different.



---

## Liquidity is not continuous

Most pricing models assume smooth liquidity.

Real markets do not behave that way.

Strikes are discrete.

Dealer inventories cluster unevenly.

Hedging happens in intervals.

Liquidity becomes fragmented across the chain.

And under short gamma conditions, every move in spot forces additional hedge adjustment.

Those hedge adjustments then feed back into premiums themselves.

That reflexive loop is what creates violent premium expansion near certain strikes.

---

## The displacement framework

After observing these behaviors repeatedly, I started modeling the minimum premium displacement required before dealer hedging becomes self reinforcing.

The result became:

$$
f(p)=\left(\frac{p}{2(p+1)}\right)k
$$

where:

- $p$ = prime liquidity order
- $k$ = local strike spacing
- $f(p)$ = minimum premium displacement threshold

This is not a pricing formula.

It does not predict direction.

It describes the minimum structural displacement required before dealer hedging starts materially influencing premium behavior.

---

## Why prime order?

Because liquidity fragmentation across the option chain is not uniform.

Composite structures create overlapping hedge behavior.

Prime fragmentation isolates cleaner liquidity intervals.

In practice, this means certain strike relationships behave more independently while others become structurally entangled through dealer positioning.

The prime constraint was my way of modeling cleaner separation inside the chain.

---

## The important part is the derivative

To understand how displacement changes as liquidity fragmentation increases:

$$
\frac{df}{dp}=\frac{k}{2(p+1)^2}
$$

The derivative stays positive:

$$
\frac{df}{dp}>0
$$

but decays asymptotically:

$$
\lim_{p\to\infty}\frac{df}{dp}=0
$$

That decay matters.

A lot.

Because it suggests something traders intuitively feel during heavy gamma environments:

> Initially, dealer hedging expands premiums aggressively.  
> Eventually, the impact of additional hedging begins compressing.

That compression effect becomes visible near crowded strikes where positioning is heavily distributed across neighboring contracts.

---

## Gamma expansion eventually becomes gamma compression

This was the most interesting observation.

At first:

- hedging amplifies movement
- premiums expand rapidly
- IV rises
- dealer flow accelerates

But as liquidity becomes fragmented across nearby strikes, each additional hedge adjustment contributes less incremental expansion.

The chain starts absorbing part of the pressure itself.

That creates a bounded system.

Mathematically:

$$
\lim_{p\to\infty}f(p)=\frac{k}{2}
$$

Meaning:

> Premium displacement cannot expand indefinitely relative to local strike spacing.

That is the core idea behind the framework.

---

## What this means in practice

This changes how I think about option movement near ATM regions.

Instead of asking:

> “Where will price go?”

I increasingly think:

> “Where is dealer inventory concentrated, and how aggressively will hedging react if price approaches that region?”

Sometimes the biggest premium expansion happens not because price moved far...

but because dealer positioning became unstable.

---

## What I believe most traders miss

Most retail traders still think:

- direction first
- Greeks second
- positioning later

In reality, positioning often comes first.

Especially in index options.

Spot movement matters.

But dealer inventory structure often determines how violently premiums respond to that movement.

---

## Final thought

The market is not just price reacting to information.

Sometimes the market is dealers reacting to themselves.

And when enough short gamma pressure builds near concentrated strikes, the option chain starts behaving less like a smooth pricing system and more like a reflexive feedback structure driven by inventory stress, liquidity fragmentation, and hedge acceleration.

That is the behavior this framework attempts to describe.

