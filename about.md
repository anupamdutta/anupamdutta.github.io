---
layout: page
title: About
permalink: /about/
---


I am **Anupam Dutta**. This is not a blog. This is a Quant Research Lab.

Trading is not just what I do. It is how I think.

I didn’t start in the markets.  
For over a decade, I worked as a Mechanical Engineer in multinational companies, solving technical problems and managing systems. But I wanted something different, the ability to make decisions and live with their outcomes.

In April 2019, I committed fully to the Indian equity index options market, with Nifty50 as my primary arena.

---

## What I built

My work focuses on:

- Open Interest shifts  
- Implied Volatility structure  
- Volatility surface behavior  
- Dealer positioning and gamma flows  

Over time, this evolved into structured frameworks like:

---

### MMR Terminal

I built the **MMR Terminal** as an extension of this research.

Its purpose is simple:

To quantify **Market Maker Risk (MMR)** through the volatility implied by option prices.

Instead of treating implied volatility as just another market statistic, the framework interprets it as a measure of the risk market makers must assume when quoting and hedging options.

This provides a market-maker-centric view of expected volatility, risk perception, and option pricing distortions.

<div style="margin-top:12px;">
  <a href="/mmr-terminal-web/"
     style="
       background:#020617;
       border:1px solid #38bdf8;
       color:#38bdf8;
       padding:8px 16px;
       border-radius:6px;
       text-decoration:none;
       font-family:monospace;
       box-shadow:0 0 8px rgba(56,189,248,0.3);
     ">
     Explore MMR Terminal →
  </a>
</div>

<br>

---

### Risk Strike Terminal

As my research expanded beyond volatility estimation, I developed the **Risk Strike Terminal** as a companion to the **MMR Terminal**.

While the MMR Terminal quantifies the level of **Market Maker Risk (MMR)** implied by option prices, the Risk Strike Terminal addresses a different but equally important question:

> **At what strike level are option market makers implicitly expecting risk while quoting an option premium?**

Using a proprietary quantitative framework, the Risk Strike Terminal estimates a **Risk Strike**—a risk-adjusted strike level inferred from option premiums, strike spacing, and synthetic forward relationships. Rather than predicting market direction, the model attempts to identify the level of risk embedded within the option pricing process.

The terminal computes:

- Risk Strike
- Risk-adjusted Put Price
- Risk-adjusted Call Price
- Synthetic Forward
- Market Risk Zone

Together, the **MMR Terminal** and **Risk Strike Terminal** form a complementary quantitative research suite for option market analysis. The MMR Terminal measures the **magnitude of market maker risk**, while the Risk Strike Terminal estimates the **location where that risk is expected to materialize**.

The tool is intended for advanced option traders, quantitative researchers, and derivatives professionals. To make practical use of its outputs, users should possess a sound understanding of:

- Black-Scholes Option Pricing
- Implied Volatility
- Put-Call Parity
- Option Greeks
- Option Market Structure

For traders who wish to understand the mathematical framework and practical application of these models, I also conduct a structured training program covering the underlying theory, quantitative concepts, and real-market implementation.

<div style="margin-top:12px;">
  <a href="/risk-terminal-web/"
     style="
       background:#020617;
       border:1px solid #38bdf8;
       color:#38bdf8;
       padding:8px 16px;
       border-radius:6px;
       text-decoration:none;
       font-family:monospace;
       box-shadow:0 0 8px rgba(56,189,248,0.3);
     ">
     Explore Risk Strike Terminal →
  </a>
</div>

<br>

---


## Philosophy

I don’t believe in prediction.

I believe in:

- Understanding what is priced in  
- Identifying where pressure builds  
- Acting only when asymmetry is visible  

Markets cannot be mastered.  
But a process can be built.

---

## Contact
- **Anupam Dutta**
  
- ☎️ +91-8240775462

- 📧 [dutta.anupam.02@gmail.com](mailto:dutta.anupam.02@gmail.com)
