---
layout: page
title: IV Finder
permalink: /iv-finder/
---

<div class="iv-page">

  <div class="iv-layout">

    <!-- ================= LEFT OUTPUT ================= -->
    <div class="iv-output-panel">

      <div class="mmr-card" id="box-input">
        <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
        Waiting for input...
      </div>

      <div class="mmr-card" id="box-result">
        <div class="mmr-card-title">[ RESULT ]</div>
        Run solver to see IV
      </div>

    </div>

    <!-- ================= RIGHT INPUT ================= -->
    <div class="iv-input-panel">
      <div class="iv-container">

        <p class="subtext">
          This is a precision Implied Volatility solver designed for index options like Nifty50.
        </p>

        <div class="iv-grid">

          <div class="field">
            <label>Synthetic Future</label>
            <input id="spot" type="number" placeholder="e.g. 17427.95">
          </div>

          <div class="field">
            <label>Strike</label>
            <input id="strike" type="number" placeholder="e.g. 17350">
          </div>

          <div class="field">
            <label>DTE</label>
            <input id="dte" type="number" placeholder="Days to expiry">
          </div>

          <div class="field">
            <label>Option Price</label>
            <input id="price" type="number" placeholder="Market price">
          </div>

          <div class="field">
            <label>Type</label>
            <select id="type">
              <option value="call">Call</option>
              <option value="put">Put</option>
            </select>
          </div>

        </div>

        <button class="run-btn iv-btn" onclick="runIV()">Run IV Solver</button>

      </div>
    </div>

  </div>

</div>

<!-- ================= MODAL ================= -->
<div id="errorModal" class="modal">
  <div class="modal-content">
    <span class="close" onclick="closeModal()">&times;</span>
    <p id="modalText"></p>
  </div>
</div>

<script>

/* ================= MODAL ================= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ================= MAIN ================= */
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

  // ===== LOADING STATE =====
  document.getElementById("box-input").innerHTML = "Loading...";
  document.getElementById("box-result").innerHTML = "";

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

    /* ===== INPUT BOX ===== */
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      SF: ${S}<br>
      Strike: ${K}<br>
      DTE: ${DTE}<br>
      Option Price: ${P}<br>
      Type: ${type.toUpperCase()}
    `;

    /* ===== RESULT BOX ===== */
    const color = json.iv > 0 ? "#22c55e" : "#ef4444";

    document.getElementById("box-result").innerHTML = `
      <div class="mmr-card-title">[ RESULT ]</div>
      <div style="color:${color}; font-size:18px;">
        IV: ${json.iv.toFixed(4)} % <br>
        Risk Level* (SF^): ${json.risk_level.toFixed(2)} <br>
        Risk Level** (SF^^): ${json.risk_level_1.toFixed(2)} <br>
      </div>
    `;

  } catch (err) {
    showError("Connection error. Try again.");
  }
}

</script>
