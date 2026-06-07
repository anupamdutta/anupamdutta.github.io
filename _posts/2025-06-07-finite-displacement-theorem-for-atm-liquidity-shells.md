---
layout: post
title: "Finite Displacement Theorem for ATM Liquidity Shells: A Measure-Theoretic Approach to Dealer Hedge Compression"
date: 2026-06-07
tags: [NIFTY50, Gamma, Dealer Hedging, Measure Theory, Functional Analysis, Market Microstructure]
---

# Finite Displacement Theorem for ATM Liquidity Shells: A Measure-Theoretic Approach to Dealer Hedge Compression

Most option traders analyze markets through payoff diagrams.

More sophisticated participants analyze volatility surfaces.

Market makers analyze inventory.

This framework starts from a different premise.

It assumes the option chain behaves as a discretized liquidity manifold whose local geometry is governed by strike fragmentation, dealer inventory redistribution, and nonlinear hedge propagation.

Under this interpretation, ATM structures are not merely collections of contracts.

They are bounded liquidity shells possessing finite displacement capacity.

The central result of this work is the **Finite Displacement Theorem**, which states that dealer-driven displacement inside a discrete ATM liquidity shell converges toward a finite upper bound despite arbitrarily large increases in hedge activity.

---

# Discrete Strike Topology

Let

$$
K_0
$$

denote the ATM strike.

Let

$$
k
$$

denote the minimum strike interval in the local option topology.

For NIFTY50,

$$
k=50
$$

The nearest neighboring strikes become

$$
K_0-k
$$

and

$$
K_0+k
$$

which define the fundamental ATM liquidity shell

$$
\Omega_1=[K_0-k,K_0+k]
$$

with measure

$$
\mu(\Omega_1)=2k
$$

For NIFTY50,

$$
\mu(\Omega_1)=100
$$

Thus the ATM shell represents the smallest closed liquidity neighborhood surrounding the synthetic equilibrium state.

---

# Synthetic Futures as the State Variable

Traditional frameworks define spot as the independent variable.

This assumption becomes increasingly inadequate once dealer inventory begins dominating local market behavior.

Instead define

$$
S_f
$$

as the Synthetic Future implied through put-call parity.

The state of the option manifold is therefore represented by

$$
\Psi(S_f,t)
$$

rather than

$$
\Psi(S,t)
$$

where

$$
S
$$

denotes the cash index.

The evolution process becomes

$$
\frac{\partial \Psi}{\partial t}
=
\mathcal{L}(\Psi,S_f)
$$

where

$$
\mathcal{L}
$$

represents the dealer inventory operator acting upon the discrete strike lattice.

This formulation places dealer hedging at the center of the system rather than treating it as a secondary consequence of spot movement.

---

# The Hedge Propagation Functional

Define a prime-normalized liquidity ordering

$$
p_n
$$

where

$$
p_n
$$

represents the nth liquidity node.

Now define the hedge propagation functional

$$
f(p)
=
\left(
\frac{p}{2(p+1)}
\right)k
$$

with

$$
f:\mathbb{R}^{+}\rightarrow\mathbb{R}
$$

The functional represents cumulative hedge displacement generated through liquidity redistribution inside the ATM shell.

Unlike conventional volatility models, displacement here emerges from the geometry of strike fragmentation itself.

---

# The Finite Displacement Theorem

Observe

$$
\lim_{p\to\infty}
f(p)
=
\frac{k}{2}
$$

This yields the central theorem.

## Finite Displacement Theorem

For any discrete ATM liquidity shell

$$
\Omega_1=[K_0-k,K_0+k]
$$

with strike interval

$$
k
$$

the cumulative hedge displacement generated through arbitrarily large liquidity fragmentation converges toward

$$
\frac{k}{2}
$$

and remains bounded above by this quantity.

Formally,

$$
\sup f(p)
=
\frac{k}{2}
$$

For NIFTY50,

$$
\sup f(p)
=
25
$$

For SENSEX,

$$
\sup f(p)
=
50
$$

This immediately implies that dealer-driven displacement remains finite despite unbounded increases in hedge activity.

The system admits infinite liquidity fragmentation but finite displacement.

This distinction is fundamental.

---

# Wing Decomposition

Define the call-wing operator

$$
\mathcal{C}
=
\{K_0-k,K_0+k\}_{CE}
$$

and the put-wing operator

$$
\mathcal{P}
=
\{K_0-k,K_0+k\}_{PE}
$$

For an ATM level of 25000:

$$
\mathcal{C}
=
\{24950CE,25050CE\}
$$

and

$$
\mathcal{P}
=
\{24950PE,25050PE\}
$$

Applying the Finite Displacement Theorem,

$$
\sup(\mathcal{C})
=
25
$$

and

$$
\sup(\mathcal{P})
=
25
$$

Therefore,

$$
\sup(\mathcal{C}\cup\mathcal{P})
=
50
$$

representing the total displacement capacity of the ATM shell.

The shell behaves as a bounded liquidity container whose aggregate displacement measure is finite.

---

# First Variation and Marginal Compression

Taking the first derivative,

$$
f'(p)
=
\frac{k}{2(p+1)^2}
$$

Observe that

$$
f'(p)>0
$$

for all

$$
p>0
$$

Thus displacement remains monotonic.

However,

$$
\lim_{p\to\infty}
f'(p)
=
0
$$

The implication is subtle.

Dealer hedging continues.

Inventory redistribution continues.

Yet marginal displacement approaches zero.

The system remains active while becoming progressively less responsive.

This phenomenon constitutes the mathematical definition of dealer hedge compression.

---

# Second Variation and Global Concavity

Differentiating once more,

$$
f''(p)
=
-\frac{k}{(p+1)^3}
$$

Since

$$
f''(p)<0
$$

for all

$$
p>0
$$

the functional is globally concave.

Consequently, every additional unit of hedge flow contributes less displacement than the preceding one.

From a market microstructure perspective:

- hedge activity persists,
- inventory rotation persists,
- displacement persists,

yet acceleration decays continuously.

The shell therefore cannot sustain explosive displacement indefinitely.

---

# Curvature of the Liquidity Manifold

Define local shell curvature

$$
\kappa(p)
=
\frac{|f''(p)|}
{
\left(
1+f'(p)^2
\right)^{3/2}
}
$$

Substituting,

$$
\kappa(p)
=
\frac{
\frac{k}{(p+1)^3}
}
{
\left(
1+\frac{k^2}{4(p+1)^4}
\right)^{3/2}
}
$$

Observe

$$
\lim_{p\to\infty}
\kappa(p)
=
0
$$

The shell progressively flattens.

Geometrically speaking, dealer hedging destroys curvature.

The liquidity manifold approaches a state of local Euclidean neutrality.

---

# Compression Energy Functional

Define total compression energy

$$
E
=
\int_0^\infty f'(p)^2\,dp
$$

Substituting,

$$
E
=
\int_0^\infty
\frac{k^2}
{4(p+1)^4}
\,dp
$$

which evaluates to

$$
E
=
\frac{k^2}{12}
$$

For NIFTY50,

$$
E
=
208.33
$$

Thus total hedge-compression energy remains finite despite liquidity order extending without bound.

The ATM shell therefore behaves as a dissipative system.

---

# Measure-Theoretic Formulation

Let

$$
\Omega
$$

denote the ATM shell.

Define the displacement measure

$$
\nu(A)
=
\int_A f'(p)\,d\mu
$$

for all measurable subsets

$$
A\subseteq\Omega
$$

Then

$$
\nu(\Omega)
=
\frac{k}{2}
$$

which recovers the Finite Displacement Theorem directly.

No measurable subset can generate displacement exceeding the total displacement measure available within the shell.

The ATM shell therefore behaves as a finite measure space under hedge propagation.

---

# Dealer Compression Fixed Point

Define

$$
p^\star
$$

such that

$$
|f'(p^\star)|
<
\epsilon
$$

for arbitrarily small

$$
\epsilon>0
$$

Then

$$
p^\star
=
\sqrt{\frac{k}{2\epsilon}}
-1
$$

Beyond this point additional hedge flow contributes negligible displacement.

The shell enters a compression-dominated regime.

This is precisely where market participants frequently mistake continued dealer activity for continued expansion potential.

The activity remains.

The displacement capacity does not.

---

# Empirical Interpretation

For NIFTY50:

$$
\Omega_1
=
[24950,25050]
$$

with

$$
\mu(\Omega_1)=100
$$

The shell decomposes into

$$
25
\rightarrow
\mathcal{C}
$$

and

$$
25
\rightarrow
\mathcal{P}
$$

yielding

$$
50
\rightarrow
\Omega_1
$$

of aggregate displacement capacity.

The implication is profound.

Increasing dealer hedging does not imply unbounded premium expansion.

Instead it implies convergence toward a finite displacement attractor determined entirely by strike topology.

---

# Conclusion

Most traders ask:

> Will dealer hedging continue?

The more important question is:

> Has the ATM liquidity shell exhausted its displacement measure?

Once the shell approaches its asymptotic compression state, dealer activity may continue increasing, inventory may continue rotating, and synthetic futures may continue migrating.

Yet the option manifold increasingly behaves as a dissipative system converging toward bounded displacement equilibrium.

The Finite Displacement Theorem suggests that this equilibrium is governed not by volatility itself, but by the geometry of the strike lattice and the finite displacement capacity embedded within the ATM liquidity shell.
