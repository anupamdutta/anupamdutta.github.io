---
layout: home

---


> Market structure is not random.

---

<div style="display:flex; align-items:center; gap:20px; flex-wrap:wrap; margin-top:20px;">

  <img src="/assets/anupam.png"
       style="
         width:90px;
         height:90px;
         border-radius:50%;
         object-fit:cover;
         border:2px solid #38bdf8;
         box-shadow:0 0 12px rgba(56,189,248,0.4);
       ">

  <div>
    <strong>Hello, I am Anupam Dutta.</strong><br>
    I work on volatility structure, gamma positioning, and systematic option pricing.<br>
    Threads: <a href="https://www.threads.net/@gammagrid">@gammagrid</a>

    <div style="margin-top:10px;">
      <a href="/about/"
         style="
           background:#020617;
           border:1px solid #38bdf8;
           color:#38bdf8;
           padding:6px 14px;
           border-radius:6px;
           text-decoration:none;
           font-family:monospace;
           box-shadow:0 0 8px rgba(56,189,248,0.3);
         ">
         Read More →
      </a>
    </div>
  </div>

</div>
<br>

---

## Today’s Market Snapshot

<div style="margin-top:20px; text-align:center;">

  <img src="/assets/daily/{{ site.data.latest.image }}"
       style="
         width:100%;
         max-width:520px;
         border-radius:12px;
         box-shadow:0 10px 30px rgba(0,0,0,0.6);
         border:1px solid rgba(56,189,248,0.2);
       ">

  <p style="
      margin-top:10px;
      font-size:14px;
      color:#94a3b8;
      font-family:monospace;
    ">
    EOD Nifty50 Option Chain on {{ site.data.latest.date }}
  </p>
  <div style="margin-top:12px;">
  <a href="/live-nifty-option-chain.html"
     style="
       display:inline-block;
       background:#020617;
       border:1px solid #38bdf8;
       color:#38bdf8;
       padding:8px 16px;
       border-radius:8px;
       text-decoration:none;
       font-family:monospace;
       font-size:13px;
       box-shadow:0 0 10px rgba(56,189,248,0.3);
     ">
     View Live Nifty Option Chain →
  </a>
</div>

</div>
<br>


## What I actually study

I don’t predict direction.  
I track **pressure, positioning, and volatility expansion**.

- Implied Volatility behavior  
- Dealer Gamma positioning  
- Liquidity-driven moves  
- Structural inefficiencies  

---

## Framework

This is not discretionary noise.

This is a structured approach:

- Observe volatility regime  
- Map gamma exposure  
- Identify forced flows  
- Execute only when asymmetry exists  

---

## Core Areas

### ⟶ IV Analysis
Understanding expansion, compression, and mispricing.

### ⟶ Gamma Structure
Where dealers are forced to hedge. Where markets get pinned or pushed.

### ⟶ Trade Logs
Actual executions. No hindsight. No storytelling.

---

## Recent Logs

<ul class="post-list">
{% for post in site.posts %}
  <li>
    <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>

    <h3>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </h3>

    <div>
      {% for tag in post.tags %}
        <span class="tag">{{ tag }}</span>
      {% endfor %}
    </div>
  </li>
{% endfor %}
</ul>

---

## Operating Principle

Trade less.  
Observe more.  
Execute only when structure aligns.
