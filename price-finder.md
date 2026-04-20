---
layout: page
title: Price Finder
permalink: /price-finder/
---

<div class="iv-page">

  <div class="iv-layout">

    <!-- LEFT OUTPUT -->
    <div class="iv-output-panel">

      <div class="mmr-card" id="box-input">
        <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
        Waiting for input...
      </div>

      <div class="mmr-card" id="box-result">
        <div class="mmr-card-title">[ RESULT ]</div>
        Run calculator
      </div>

    </div>

    <!-- RIGHT INPUT -->
    <div class="iv-input-panel">
      <div class="iv-container">

        <p class="subtext">
          Option Pricing using Synthetic Future (Black-Scholes based).
        </p>

        <div class="iv-grid">

          <div class="field">
            <label>Synthetic Future (F)</label>
            <input id="fut" type="number" placeholder="e.g. 22500">
          </div>

          <div class="field">
            <label>Strike (K)</label>
            <input id="strike" type="number" placeholder="e.g. 22500">
          </div>

          <div class="field">
            <label>IV %</label>
            <input id="iv" type="number" placeholder="e.g. 18">
          </div>

          <div class="field">
            <label>DTE</label>
            <input id="dte" type="number" placeholder="Days to expiry">
          </div>

          <div class="field">
            <label>Rate %</label>
            <input id="rate" type="number" placeholder="e.g. 6">
          </div>

        </div>

        <button class="run-btn iv-btn" onclick="runPrice()">Calculate Price</button>

      </div>
    </div>

  </div>

</div>

<!-- MODAL -->
<div id="errorModal" class="modal">
  <div class="modal-content">
    <span class="close" onclick="closeModal()">&times;</span>
    <p id="modalText"></p>
  </div>
</div>

<script>

/* ========= NORMAL CDF ========= */
function normCDF(x) {
  let k = 1 / (1 + 0.2316419 * Math.abs(x));
  let a1=0.319381530, a2=-0.356563782, a3=1.781477937, a4=-1.821255978, a5=1.330274429;

  let poly = a1*k + a2*k*k + a3*Math.pow(k,3) + a4*Math.pow(k,4) + a5*Math.pow(k,5);

  let approx = 1 - (1 / Math.sqrt(2*Math.PI)) * Math.exp(-0.5*x*x) * poly;

  return x >= 0 ? approx : 1 - approx;
}

/* ========= MODAL ========= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ========= MAIN ========= */
function runPrice() {

  let F = parseFloat(document.getElementById("fut").value);
  let K = parseFloat(document.getElementById("strike").value);
  let IV = parseFloat(document.getElementById("iv").value);
  let DTE = parseFloat(document.getElementById("dte").value);
  let R = parseFloat(document.getElementById("rate").value);

  /* ===== VALIDATION ===== */
  if (!F || F <= 0) return showError("Invalid Synthetic Future");
  if (!K || K <= 0) return showError("Invalid Strike");
  if (!IV || IV <= 0) return showError("Invalid IV");
  if (!DTE || DTE <= 0) return showError("Invalid DTE");
  if (DTE > 3650) return showError("DTE too large");
  if (R === null || isNaN(R)) return showError("Invalid Rate");

  /* ===== CALC ===== */
  let T = DTE / 365;
  let sigma = IV / 100;
  let r = R / 100;

  let d1 = (Math.log(F / K) + 0.5 * sigma * sigma * T) / (sigma * Math.sqrt(T));
  let d2 = d1 - sigma * Math.sqrt(T);

  let call = Math.exp(-r * T) * (F * normCDF(d1) - K * normCDF(d2));
  let put  = Math.exp(-r * T) * (K * normCDF(-d2) - F * normCDF(-d1));

  /* ===== OUTPUT ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    F: ${F}<br>
    K: ${K}<br>
    IV: ${IV}%<br>
    DTE: ${DTE}<br>
    Rate: ${R}%
  `;

  document.getElementById("box-result").innerHTML = `
    <div class="mmr-card-title">[ RESULT ]</div>
    Call Price: ${call.toFixed(2)}<br>
    <div style="color:#22c55e; font-size:18px;">
      Put Price: ${put.toFixed(2)}
    </div>
  `;
}

</script>
