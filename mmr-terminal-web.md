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
        IV Finder &amp; Greeks
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

      <!-- IV FINDER INPUTS -->
      <div class="iv-grid">

        <div class="field"><label>SF</label><input id="sf" type="number" placeholder="e.g. 24800"></div>
        <div class="field"><label>Strike</label><input id="strike" type="number" placeholder="e.g. 24800"></div>

        <div class="full-width">
          <div class="triple-row">
            <div class="field">
              <label>Level Type <span style="color:#f87171;">*</span></label>
              <select id="levelType" onchange="updateLevelLabel()">
                <option value="R">Resistance</option>
                <option value="S">Support</option>
              </select>
            </div>
            <div class="field">
              <label id="levelLabel">Resistance (R)</label>
              <input id="level" type="number" placeholder="e.g. 25000">
            </div>
          </div>
        </div>

        <div class="full-width">
          <div class="field">
            <label>DTE</label>
            <input id="dte" type="number" placeholder="Days to Expiry">
          </div>
        </div>

        <div class="full-width">
          <div class="triple-row">
            <div class="field">
              <label>r %</label>
              <input id="r" type="number" value="0" placeholder="e.g. 0">
            </div>
            <div class="field">
              <label>q %</label>
              <input id="q" type="number" value="0" placeholder="e.g. 0">
            </div>
          </div>
        </div>

      </div>

      <button class="run-btn" onclick="runIvGreeks()">Find IV &amp; Greeks</button>

    </div>
  </div>

  <!-- ================= RIGHT PANEL ================= -->
  <div class="mmr-right">

    <div class="mmr-card" id="box-input">
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Waiting for input...
    </div>

    <div class="mmr-card" id="box-model">
      <div class="mmr-card-title">[ IV &amp; GREEKS OUTPUT ]</div>
      Waiting for model to run...
    </div>

  </div>

</div>
<div class="mmr-about-full">
  <div class="mmr-about-box">

    <div class="mmr-about-title">[ ABOUT MMR TERMINAL ]</div>

    <p>MMR Terminal is a high-precision IV Finder - deriving implied volatility from a support or resistance
    level, then computing Black-76 call/put prices and Greeks (delta, gamma, vega, theta) for any strike - built
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
    <div class="mmr-card-title">[ IV &amp; GREEKS OUTPUT ]</div>
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

/* ================= LEVEL LABEL ================= */
function updateLevelLabel() {
  const isR = document.getElementById("levelType").value === "R";
  document.getElementById("levelLabel").innerText = isR ? "Resistance (R)" : "Support (S)";
}

/* ================= MODAL ================= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ================= IV FINDER + GREEKS ================= */
async function runIvGreeks() {

  const payload = {
    action: "ivGreeks",
    appKey:    document.getElementById("appKey").value.trim(),
    appToken:  document.getElementById("appToken").value.trim(),
    sf:        Number(document.getElementById("sf").value),
    strike:    Number(document.getElementById("strike").value),
    levelType: document.getElementById("levelType").value,
    level:     Number(document.getElementById("level").value),
    dte:       Number(document.getElementById("dte").value),
    r:         Number(document.getElementById("r").value),
    q:         Number(document.getElementById("q").value)
  };

  const isR = payload.levelType === "R";
  const lvlName = isR ? "Resistance" : "Support";

  // ===== VALIDATION =====
  if (!payload.appKey)   return showError("App Key required");
  if (!payload.appToken) return showError("Token required");

  if (!payload.sf || payload.sf <= 0)
    return showError("SF must be greater than 0");

  if (!payload.strike || payload.strike <= 0)
    return showError("Strike must be greater than 0");

  if (!payload.level || payload.level <= 0)
    return showError(lvlName + " must be greater than 0");

  if (isR && payload.level <= payload.sf)
    return showError("Resistance must be greater than SF");

  if (!isR && payload.level >= payload.sf)
    return showError("Support must be less than SF");

  if (!payload.dte || payload.dte <= 0)
    return showError("DTE must be greater than 0");

  if (payload.dte > 3650)
    return showError("DTE too large (check input)");

  if (isNaN(payload.r))
    return showError("r is required (use 0 if not applicable)");

  if (isNaN(payload.q))
    return showError("q is required (use 0 if not applicable)");

  /* ===== LOADING STATE ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `;
  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ IV &amp; GREEKS OUTPUT ]</div>
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
      SF: ${payload.sf}<br>
      Strike: ${payload.strike}<br>
      ${lvlName}: ${payload.level}<br>
      DTE: ${payload.dte}<br>
      r: ${payload.r}%<br>
      q: ${payload.q}%
    `;

    /* ===== OUTPUT ===== */
    const f = (v, d) => (v === null || v === undefined) ? "N/A" : Number(v).toFixed(d);
    const block = (name, o) => `
      <b>${name}</b><br>
      Price: ${f(o.price, 2)}<br>
      Delta: ${f(o.delta, 4)}<br>
      Gamma: ${f(o.gamma, 6)}<br>
      Vega: ${f(o.vega, 2)}<br>
      Theta: ${f(o.theta, 2)}<br>
    `;

    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ IV &amp; GREEKS OUTPUT ]</div>

      IV (${lvlName}): <b>${f(json.iv, 3)}%</b><br>
      <br>
      ${block("CALL @ " + payload.strike, json.call)}
      <br>
      ${block("PUT @ " + payload.strike, json.put)}
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
