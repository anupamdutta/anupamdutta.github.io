---
layout: page
title: Forward Calculator
permalink: /forward-calculator/
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
          Forward Price Calculator using continuous compounding.
        </p>

        <div class="iv-grid">

          <div class="field">
            <label>Spot (S)</label>
            <input id="spot" type="number" placeholder="e.g. 22500">
          </div>

          <div class="field">
            <label>DTE (T)</label>
            <input id="dte" type="number" placeholder="Days to expiry">
          </div>

          <div class="field">
            <label>Funding Rate % (R)</label>
            <input id="rate" type="number" placeholder="e.g. 6.5">
          </div>

          <div class="field">
            <label>Tick Size (d)</label>
            <input id="tick" type="number" placeholder="e.g. 0.05">
          </div>

        </div>

        <button class="run-btn iv-btn" onclick="runForward()">Calculate Forward</button>

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

/* ========= MODAL ========= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ========= MAIN ========= */
function runForward() {

  let S = parseFloat(document.getElementById("spot").value);
  let T = parseFloat(document.getElementById("dte").value);
  let R = parseFloat(document.getElementById("rate").value);
  let d = parseFloat(document.getElementById("tick").value);

  /* ===== VALIDATION ===== */
  if (!S || S <= 0) return showError("Invalid Spot");
  if (!T || T <= 0) return showError("Invalid DTE");
  if (T > 3650) return showError("DTE too large");
  if (R === null || isNaN(R)) return showError("Invalid Rate");
  if (!d || d <= 0) return showError("Invalid Tick Size");

  /* ===== CALC ===== */
  let t = T / 365;
  let r = R / 100;

  let f = S * Math.exp(r * t);

  /* ===== ROUND UP TO TICK ===== */
  let rounded = Math.ceil(f / d) * d;

  /* ===== OUTPUT ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Spot: ${S}<br>
    DTE: ${T}<br>
    Rate: ${R}%<br>
    Tick: ${d}
  `;

  document.getElementById("box-result").innerHTML = `
    <div class="mmr-card-title">[ RESULT ]</div>
    Raw Forward: ${f.toFixed(4)}<br>
    <div style="color:#22c55e; font-size:18px;">
      Rounded Forward: ${rounded.toFixed(4)}
    </div>
  `;
}

</script>
