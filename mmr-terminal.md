---
layout: page
title: MMR Terminal
permalink: /mmr-terminal/
---

# MMR TERMINAL

> Market Maker Risk Engine  
> Volatility Surface Normalization

---

## Overview

MMR Terminal is a deterministic option pricing engine designed to map discrete market risk into a consistent implied volatility regime.

This is not a standard Black-Scholes wrapper.

It is built on a proprietary framework:

**NCP40 / RSEDAO**

The system reconstructs volatility structure under real market constraints, preserving forward consistency and surface alignment.

---

## What it does

- Estimates **risk-adjusted option prices** (European style)  
- Internally derives **Implied Volatility structure**  
- Normalizes volatility surface under market constraints  
- Detects mispricing and structural inefficiencies  

---

## Key Idea

Market prices are not always consistent.

MMR Terminal forces consistency.

It adjusts:

- Call-Put parity deviations  
- Strike-based distortions  
- Funding-driven dislocations  

to produce a structurally valid volatility surface.

---

## Interface Preview

<div style="text-align:center;">
  <img src="/assets/mmr-terminal-cover.png" width="800">
</div>

<div style="text-align:center;">
  <img src="/assets/mmr-terminal-ui.png" width="800">
</div>

---

## System Requirements

- Windows 10 or above  
- 64-bit system  

---

## Installation

1. Download the ZIP file  
2. Extract to Desktop  
3. Open folder  
4. Run the `.exe` file  

---

## If Windows Defender blocks it

This is common for unsigned executables.

- Click **More Info**  
- Click **Run Anyway**  

---

## Important

This is not a plug-and-play retail tool.

Proper usage requires:

- Understanding of volatility  
- Knowledge of option structure  
- Familiarity with pricing behavior  

Training is recommended.

---

## Download

👉 [Download MMR Terminal](https://drive.google.com/file/d/14wL_Fpe5ju2zbOb2RGCr4oD3RJ2hLP9H/view?usp=sharing)


---

## Framework

This tool is based on:

**NCP40 → GammaGrid Option Engine**

A structured approach to modeling:

- Market maker risk  
- Volatility normalization  
- Surface consistency  

---

## Developed by

Anupam Dutta  
GammaGrid Systems  

---

Trade less.  
Observe structure.  
Execute when pricing is wrong.
