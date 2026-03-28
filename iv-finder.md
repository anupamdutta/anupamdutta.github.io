---
layout: page
title: IV Finder
permalink: /iv-finder/
---

<div class="iv-container">

  <p class="subtext">Estimate implied volatility from market price</p>

  <div class="iv-grid">

    <div>
      <label>Spot</label>
      <input id="spot" type="number">
    </div>

    <div>
      <label>Strike</label>
      <input id="strike" type="number">
    </div>

    <div>
      <label>DTE</label>
      <input id="dte" type="number">
    </div>

    <div>
      <label>Option Price</label>
      <input id="price" type="number">
    </div>

    <div>
      <label>Type</label>
      <select id="type">
        <option value="call">Call</option>
        <option value="put">Put</option>
      </select>
    </div>

  </div>

  <button class="run-btn" onclick="runIV()">Run IV Solver</button>

  <div id="result" class="result-box"></div>

</div>
<script>
async function runIV() {
  const data = {
    spot: document.getElementById("spot").value,
    strike: document.getElementById("strike").value,
    dte: document.getElementById("dte").value,
    price: document.getElementById("price").value,
    type: document.getElementById("type").value.toLowerCase()
  };

  const res = await fetch("https://script.google.com/macros/s/AKfycbyCLcqtV8YiPbuizHVQdaRhMmlI1LCZS6Z8qmo0XRslg9SY8kU9VtHVWdJ39AGcYlhM1g/exec", {
    method: "POST",
    body: JSON.stringify(data)
  });

  const json = await res.json();

  document.getElementById("result").innerText =
    "IV = " + json.iv.toFixed(2) + "%";
}

</script>
