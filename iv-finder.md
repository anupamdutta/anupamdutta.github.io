---
layout: page
title: IV Finder
permalink: /iv-finder/
---

<div class="iv-container">


  <p class="subtext">
    This is a precision Implied Volatility solver designed for index options like Nifty50.  
    Unlike basic calculators, it is structured to reflect market-consistent pricing by accounting for 
    implied funding conditions rather than relying purely on static interest rate assumptions.  
    Built as a lightweight interface, it represents the foundational layer of a more advanced volatility framework.
  </p>
  <br>

  <div class="iv-grid">

    <div class="field">
      <label>Spot</label>
      <input id="spot" type="number">
    </div>

    <div class="field">
      <label>Strike</label>
      <input id="strike" type="number">
    </div>

    <div class="field">
      <label>DTE</label>
      <input id="dte" type="number">
    </div>

    <div class="field">
      <label>Option Price</label>
      <input id="price" type="number">
    </div>

    <div class="field">
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

<!-- MODAL -->
<div id="errorModal" class="modal">
  <div class="modal-content">
    <span class="close" onclick="closeModal()">&times;</span>
    <p id="modalText"></p>
  </div>
</div>

<script>

function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

async function runIV() {

  let S = parseFloat(document.getElementById("spot").value);
  let K = parseFloat(document.getElementById("strike").value);
  let DTE = parseFloat(document.getElementById("dte").value);
  let P = parseFloat(document.getElementById("price").value);
  let type = document.getElementById("type").value.toLowerCase();

  // ===== VALIDATION =====
  if (!S || S <= 0) return showError("Invalid Spot value");
  if (!K || K <= 0) return showError("Invalid Strike value");
  if (!DTE || DTE <= 0) return showError("DTE must be greater than 0");
  if (!P || P <= 0) return showError("Invalid Option Price");

  if (DTE > 3650) return showError("DTE too large (check input)");

  // ===== API CALL =====
  try {
    const res = await fetch("https://script.google.com/macros/s/AKfycbyCLcqtV8YiPbuizHVQdaRhMmlI1LCZS6Z8qmo0XRslg9SY8kU9VtHVWdJ39AGcYlhM1g/exec", {
      method: "POST",
      body: JSON.stringify({
        spot: S,
        strike: K,
        dte: DTE,
        price: P,
        type: type
      })
    });

    const json = await res.json();

    if (!json.iv || isNaN(json.iv)) {
      return showError("IV calculation failed. Check inputs.");
    }

    document.getElementById("result").innerHTML = `
    <pre class="iv-output">
    ========================================
            IV SOLVER OUTPUT
    ========================================
    
    INPUT PARAMETERS
    
    Spot Price        : ${S}
    Strike Price      : ${K}
    Days to Expiry    : ${DTE}
    Option Type       : ${type.toUpperCase()}
    
    ----------------------------------------
    
    RESULT
    
    Implied Volatility : ${json.iv.toFixed(4)} %
    
    ========================================
    </pre>
    `;

  } catch (err) {
    showError("Connection error. Try again.");
  }
}

</script>
