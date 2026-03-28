---
layout: home

---


> Market structure is not random.

---

<div style="display:flex; align-items:center; gap:20px; flex-wrap:wrap; margin-top:20px;">

  <img src="/assets/images/anupam.png"
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
