---
layout: page
title: MMR Terminal Web
permalink: /mmr-terminal-web/
---

<div class="mmr-layout">

  <!-- ================= LEFT PANEL ================= -->
  <div class="mmr-left">
    <div class="iv-container">

      <p class="subtext">
        Option Risk Analyzer
      </p>

      <!-- AUTH -->
      <div class="iv-grid">
        <div class="field">
          <label>App Key <span style="color:#f87171;">*</span></label>
          <input id="appKey" required autocomplete="off">
        </div>
        <div class="field">
          <label>App Token <span style="color:#f87171;">*</span></label>
          <div class="auth-row">
            <input id="appToken" type="password" required autocomplete="off">
            <button class="btn-small" onclick="toggleToken(event)">Show</button>
          </div>
        </div>
      </div>

      <br>

      <!-- RISK INPUTS -->
      <div class="iv-grid">

        <div class="field full-width">
          <label>Option Type <span style="color:#f87171;">*</span></label>
          <select id="optionType">
            <option value="call">CALL</option>
            <option value="put">PUT</option>
          </select>
        </div>

        <div class="field"><label>Strike</label><input id="strike" type="number" placeholder="e.g. 24800"></div>
        <div class="field"><label>Call Price</label><input id="callPrice" type="number" placeholder="e.g. 120"></div>
        <div class="field"><label>Put Price</label><input id="putPrice" type="number" placeholder="e.g. 95"></div>

        <div class="full-width">
          <div class="field">
            <label>DTE</label>
            <input id="dte" type="number" placeholder="Days to Expiry">
          </div>
        </div>

      </div>

      <button class="run-btn" onclick="runRisk()">Run Risk Analysis</button>

    </div>
  </div>

  <!-- ================= RIGHT PANEL ================= -->
  <div class="mmr-right">

    <div class="mmr-card" id="box-input">
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Waiting for input...
    </div>

    <div class="mmr-card" id="box-model">
      <div class="mmr-card-title">[ RISK ANALYSIS OUTPUT ]</div>
      Waiting for model to run...
    </div>

  </div>

</div>
<div class="mmr-about-full">
  <div class="mmr-about-box">

    <div class="mmr-about-title">[ ABOUT MMR TERMINAL ]</div>

    <p>MMR Terminal is a high-precision Option Risk Analyzer — computing synthetic futures, stop-loss levels,
    estimated prices, and implied volatility using a Black-76 model — built
    for advanced derivatives analysis and model-driven decision making.</p>

    <p>⚠️ This is an advanced system intended for experienced users. Proper understanding
    is strongly recommended before usage.</p>

    <p>Access requires a valid <b>App Key</b> and <b>Token</b>, available via subscription.
    Monthly charges apply.</p>

    <p>
      <b>Developer:</b> Anupam Dutta<br>
      📞 +91-8240775462
    </p>

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

const ENDPOINT = "https://script.google.com/macros/s/AKfycbyeRllRTjvMPaXRqDZJSnvj9FyBUl9Lhot0D-nsfW5zWztJjZO1pI3hGhXGQTrP9O2U/exec";

/* ================= DEFAULT STATE ================= */
function resetOutputPanels() {
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Waiting for input...
  `;
  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ RISK ANALYSIS OUTPUT ]</div>
    Waiting for model to run...
  `;
}

/* ================= TOKEN TOGGLE ================= */
function toggleToken(event) {
  const f = document.getElementById("appToken");
  const btn = event.target;
  if (f.type === "password") {
    f.type = "text";
    btn.innerText = "Hide";
  } else {
    f.type = "password";
    btn.innerText = "Show";
  }
}

/* ================= MODAL ================= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ================= RISK ANALYSIS ================= */
async function runRisk() {

  const payload = {
    action: "risk",
    appKey:     document.getElementById("appKey").value.trim(),
    appToken:   document.getElementById("appToken").value.trim(),
    optionType: document.getElementById("optionType").value,
    strike:     Number(document.getElementById("strike").value),
    callPrice:  Number(document.getElementById("callPrice").value),
    putPrice:   Number(document.getElementById("putPrice").value),
    dte:        Number(document.getElementById("dte").value)
  };

  // ===== VALIDATION =====
  if (!payload.appKey)   return showError("App Key required");
  if (!payload.appToken) return showError("Token required");

  if (!payload.strike || payload.strike <= 0)
    return showError("Strike must be greater than 0");

  if (isNaN(payload.callPrice) || payload.callPrice < 0)
    return showError("Call Price must be 0 or more");

  if (isNaN(payload.putPrice) || payload.putPrice < 0)
    return showError("Put Price must be 0 or more");

  if (!payload.dte || payload.dte <= 0)
    return showError("DTE must be greater than 0");

  if (payload.dte > 3650)
    return showError("DTE too large (check input)");

  /* ===== LOADING STATE ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `;
  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ RISK ANALYSIS OUTPUT ]</div>
    Running model...
  `;

  try {

    const res = await fetch(ENDPOINT, {
      method: "POST",
      body: JSON.stringify(payload)
    });

    const json = await res.json();

    if (json.error) {
      showError(json.message);
      resetOutputPanels();
      return;
    }

    /* ===== INPUT ECHO ===== */
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Option Type: ${json.optionType.toUpperCase()}<br>
      Strike: ${payload.strike}<br>
      Call Price: ${payload.callPrice}<br>
      Put Price: ${payload.putPrice}<br>
      DTE: ${payload.dte}
    `;

    /* ===== OUTPUT ===== */
    const fmt = v => (v === null || v === undefined) ? "N/A" : v;

    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ RISK ANALYSIS OUTPUT ]</div>

      Synthetic Future: ${fmt(json.sf)}<br>
      <br>
      Call SL: ${fmt(json.callSL)}<br>
      Put SL: ${fmt(json.putSL)}<br>
      <br>
      Est. Call Price: ${fmt(json.estCall)}<br>
      Est. Put Price: ${fmt(json.estPut)}<br>
      <br>
      IV (Call): ${fmt(json.ivCall)}%<br>
      IV (Put): ${fmt(json.ivPut)}%<br>
      Level IV: ${fmt(json.levelIv)}%<br>
      <br>
      Optimal Price: ${fmt(json.optimal)}<br>
      Target Price: ${fmt(json.target)}<br>
      <br>
      <div style="font-size:12px; color:#facc15; line-height:1.4;">
        ⚠ Model output is for educational purposes only.
      </div>
    `;

  } catch(e) {
    showError("Connection error");
    resetOutputPanels();
  }
}

</script>
