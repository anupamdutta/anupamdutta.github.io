---
layout: page
title: IV Finder
permalink: /iv-finder/
---

<div style="max-width:600px; font-family:monospace;">

<h2>IV Finder</h2>

<p style="color:#64748b;">
Estimate implied volatility from market price.
</p>

---

<label>Spot Price</label><br>
<input id="spot" type="number"><br><br>

<label>Strike Price</label><br>
<input id="strike" type="number"><br><br>

<label>Days to Expiry</label><br>
<input id="dte" type="number"><br><br>

<label>Option Price</label><br>
<input id="price" type="number"><br><br>

<label>Option Type</label><br>
<select id="type">
  <option value="call">Call</option>
  <option value="put">Put</option>
</select><br><br>

<button onclick="calcIV()">Find Implied Volatility</button>

---

<h3 id="result"></h3>

</div>

<script>

function normCDF(x) {
  return (1 + erf(x / Math.sqrt(2))) / 2;
}

function erf(x) {
  let sign = (x >= 0) ? 1 : -1;
  x = Math.abs(x);

  let a1 = 0.254829592, a2 = -0.284496736,
      a3 = 1.421413741, a4 = -1.453152027,
      a5 = 1.061405429, p = 0.3275911;

  let t = 1.0 / (1.0 + p * x);
  let y = 1.0 - (((((a5 * t + a4) * t) + a3) * t + a2) * t + a1)
      * t * Math.exp(-x * x);

  return sign * y;
}

function blackScholes(S, K, T, r, sigma, type) {
  let d1 = (Math.log(S/K) + (r + 0.5*sigma*sigma)*T) / (sigma*Math.sqrt(T));
  let d2 = d1 - sigma*Math.sqrt(T);

  if (type === "call") {
    return S*normCDF(d1) - K*Math.exp(-r*T)*normCDF(d2);
  } else {
    return K*Math.exp(-r*T)*normCDF(-d2) - S*normCDF(-d1);
  }
}

function vega(S, K, T, r, sigma) {
  let d1 = (Math.log(S/K) + (r + 0.5*sigma*sigma)*T) / (sigma*Math.sqrt(T));
  return S * Math.sqrt(T) * (1/Math.sqrt(2*Math.PI)) * Math.exp(-0.5*d1*d1);
}

function calcIV() {

  let S = parseFloat(document.getElementById("spot").value);
  let K = parseFloat(document.getElementById("strike").value);
  let T = parseFloat(document.getElementById("dte").value) / 365;
  let marketPrice = parseFloat(document.getElementById("price").value);
  let type = document.getElementById("type").value;

  let r = 0.05;
  let sigma = 0.2;

  for (let i = 0; i < 100; i++) {
    let price = blackScholes(S, K, T, r, sigma, type);
    let v = vega(S, K, T, r, sigma);

    let diff = price - marketPrice;

    if (Math.abs(diff) < 0.0001) break;

    sigma = sigma - diff / v;
  }

  document.getElementById("result").innerText =
    "IV = " + (sigma * 100).toFixed(2) + "%";
}

</script>
