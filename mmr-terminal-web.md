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
        Auth Protected Option Pricing Engine
      </p>

      <!-- AUTH -->
      <div class="iv-grid">
        <div class="field">
          <label>App Key</label>
          <input id="appKey">
        </div>

        <div class="field">
          <label>App Token</label>
          <div class="auth-row">
            <input id="appToken" type="password">
            <button class="btn-small" onclick="toggleToken(event)">Show</button>
          </div>
        </div>
      </div>

      <br>

      <!-- INPUTS -->
      <div class="iv-grid">
        <div class="field"><label>Spot</label><input id="spot" type="number" placeholder="e.g. 23190.40"></div>
        <div class="field"><label>Strike Diff</label><input id="strikeDiff" type="number" placeholder="e.g. 50"></div>
        <div class="field">
          <label>Auto Strike</label>
          <div class="toggle-row">
            <label class="switch">
              <input type="checkbox" id="autoStrike" checked onchange="toggleStrikeInput()">
              <span class="slider"></span>
            </label>
            <span id="toggleLabel">ON</span>
          </div>
        </div>
        
        <div class="field">
          <label>Manual Strike</label>
          <input id="manualStrike" type="number" placeholder="Enter strike" disabled>
        </div>
         <div class="full-width">
          <div class="triple-row">
        
            <div class="field">
              <label>Rate %</label>
              <input id="rate" type="number" placeholder="e.g. 6">
            </div>
        
            <div class="field">
              <label>DTE</label>
              <input id="dte" type="number" placeholder="Days to Expiry">
            </div>
        
            <div class="field">
              <label>Mag Factor</label>
              <select id="fac">
                <option value="0">0</option>
                <option value="1">1</option>
              </select>
            </div>
        
          </div>
        </div>
        </div>

      <button class="run-btn" onclick="runMMR()">Run MMR Terminal</button>

    </div>
  </div>

  <!-- ================= RIGHT PANEL ================= -->
  <div class="mmr-right">

    <div class="mmr-card" id="box-input">
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Waiting for input...
    </div>

    <div class="mmr-card" id="box-model">
      <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
      Waiting for model to run...
    </div>

    <div class="mmr-card" id="box-surface-raw">
      <div class="mmr-card-title">[ UNCONSTRAINED VOLATILITY SURFACE ]</div>
      Waiting...
    </div>
    
    <div class="mmr-card" id="box-surface-adj">
      <div class="mmr-card-title">[ VOLATILITY SURFACE CONSISTENCY ]</div>
      Waiting...
    </div>

  </div>
  
</div>
<div class="mmr-about-full">
  <div class="mmr-about-box">
    
    <div class="mmr-about-title">[ ABOUT MMR TERMINAL ]</div>

    <p>MMR Terminal is a high-precision Market Maker Risk (MMR) based option pricing engine 
    built for advanced derivatives analysis and model-driven decision making.</p>

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

function toggleStrikeInput() {
  const isAuto = document.getElementById("autoStrike").checked;
  const manualField = document.getElementById("manualStrike");
  const label = document.getElementById("toggleLabel");

  if (isAuto) {
    manualField.disabled = true;
    label.innerText = "ON";
  } else {
    manualField.disabled = false;
    label.innerText = "OFF";
  }
}

  

/* ================= TOKEN TOGGLE ================= */
function toggleToken(event){
  const f = document.getElementById("appToken")
  const btn = event.target

  if(f.type === "password"){
    f.type = "text"
    btn.innerText = "Hide"
  }else{
    f.type = "password"
    btn.innerText = "Show"
  }
}

/* ================= MODAL ================= */
function showError(msg){
  document.getElementById("modalText").innerText = msg
  document.getElementById("errorModal").style.display = "flex"
}

function closeModal(){
  document.getElementById("errorModal").style.display = "none"
}

/* ================= MAIN ================= */
async function runMMR(){

  const isAutoStrike = document.getElementById("autoStrike").checked;
  const manualStrikeVal = Number(document.getElementById("manualStrike").value);
  
  const payload = {
    appKey: document.getElementById("appKey").value.trim(),
    appToken: document.getElementById("appToken").value.trim(),
    spot: Number(document.getElementById("spot").value),
    strikeDiff: Number(document.getElementById("strikeDiff").value),
    rate: Number(document.getElementById("rate").value),
    dte: Number(document.getElementById("dte").value),
  
    autoStrike: isAutoStrike,
    strike: isAutoStrike ? null : manualStrikeVal
  };

  // ===== VALIDATION =====
  if (!payload.appKey) return showError("App Key required");
  if (!payload.appToken) return showError("Token required");
  
  if (!payload.spot || payload.spot <= 0)
    return showError("Spot must be greater than 0");
  
  
  if (!payload.strikeDiff || payload.strikeDiff <= 0)
    return showError("Strike Diff must be greater than 0");
  
  if (!payload.rate || payload.rate <= 0)
    return showError("Rate must be greater than 0");
  
  if (!payload.dte || payload.dte <= 0)
    return showError("DTE must be greater than 0");
  
  if (payload.dte > 3650)
    return showError("DTE too large (check input)");

  if (!isAutoStrike) {
    if (!manualStrikeVal || manualStrikeVal <= 0) {
      return showError("Manual Strike must be greater than 0");
    }
  }

  /* ===== LOADING STATE (PRO UI) ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `

  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
    Running model...
  `

  document.getElementById("box-surface-raw").innerHTML = `
    <div class="mmr-card-title">[ UNCONSTRAINED VOLATILITY SURFACE ]</div>
    Loading...
  `;

  document.getElementById("box-surface-adj").innerHTML = `
    <div class="mmr-card-title">[ VOLATILITY SURFACE CONSISTENCY ]</div>
    Loading...
  `;

  

  try{

    const res = await fetch("https://script.google.com/macros/s/AKfycbyeRllRTjvMPaXRqDZJSnvj9FyBUl9Lhot0D-nsfW5zWztJjZO1pI3hGhXGQTrP9O2U/exec", {
      method: "POST",
      body: JSON.stringify(payload)
    })

    const json = await res.json()

    if(json.error){
      showError(json.message)
      return
    }

    /* ===== INPUT ===== */
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Spot: ${payload.spot}<br>
      Strike Mode: ${payload.autoStrike ? "AUTO" : "MANUAL"}<br>
      ${!payload.autoStrike ? `Strike: ${payload.strike}<br>` : ""}
      Strike Diff: ${payload.strikeDiff}<br>
      Rate: ${payload.rate}<br>
      DTE: ${payload.dte}
    `;

    /* ===== MODEL ===== */

    // 🎯 COLOR BASED ON CALIB SIGN
    let residualColor;
    
    if (json.calib_sign === 1) {
      residualColor = "#22c55e";   // GREEN
    } else if (json.calib_sign === -1) {
      residualColor = "#ef4444";   // RED
    } else {
      residualColor = "#facc15";   // YELLOW
    }
    const signSymbol = json.calib_sign === 1 ? "▼/●" :
                   json.calib_sign === -1 ? "▲" : "●";
    
    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
      Iteration: ${json.iteration}<br>
      Strike: ${json.strike_final}<br>
      MMR Vol: ${json.mmr_pct.toFixed(3)}<br>
      <div style="color:#facc15;">[ CALIBRATION RESIDUAL ]</div>
      <div style="color:${residualColor}; font-weight:600;">
        L1 Error: ${json.delta.toFixed(6)} ${signSymbol}
      </div>
    `;

    /* ===== UNCONSTRAINED SURFACE ===== */
    document.getElementById("box-surface-raw").innerHTML = `
      <div class="mmr-card-title">[ UNCONSTRAINED VOLATILITY SURFACE ]</div>
    
      Call Model: ${Number(json.call_model).toFixed(3)}<br>
      Put Model: ${Number(json.put_model).toFixed(3)}<br>
      Call IV (%): ${Number(json.call_iv).toFixed(3)}<br>
      Put IV (%): ${Number(json.put_iv).toFixed(3)}<br>
      SF(F*): ${Number(json.sf).toFixed(3)}
    `;
    
    /* ===== CONSISTENT SURFACE ===== */
    document.getElementById("box-surface-adj").innerHTML = `
      <div class="mmr-card-title">[ VOLATILITY SURFACE CONSISTENCY ]</div>
    
      Call Model Adj: ${Number(json.call_model).toFixed(3)}<br>
      Put Model Adj: ${Number(json.put_model_adj).toFixed(3)}<br>
      Call IV Adj (%): ${Number(json.call_iv).toFixed(3)}<br>
      Put IV Adj (%): ${Number(json.iv_put_adj).toFixed(3)}<br>
      SF Adj(F**): ${Number(json.sf_adj).toFixed(3)}
    `;
    

  }catch(e){
    showError("Connection error")
  }
}

</script>
