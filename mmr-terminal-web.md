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
        Quant Analysis Engine
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

      <!-- QUANT INPUTS -->
      <div class="iv-grid">
        <div class="field"><label>Spot</label><input id="spot" type="number" placeholder="e.g. 24800"></div>
        <div class="field"><label>Strike</label><input id="strike" type="number" placeholder="e.g. 24800"></div>

        <div class="full-width">
          <div class="triple-row">
            <div class="field">
              <label>DTE</label>
              <input id="dte" type="number" placeholder="Days to Expiry">
            </div>
            <div class="field">
              <label>IV %</label>
              <input id="iv" type="number" placeholder="e.g. 15.0">
            </div>
          </div>
        </div>

        <div class="full-width">
          <div class="triple-row">
            <div class="field">
              <label>Rate %</label>
              <input id="rate" type="number" placeholder="e.g. 9.0">
            </div>
            <div class="field">
              <label>Div Yield %</label>
              <input id="div" type="number" placeholder="e.g. 0.0">
            </div>
          </div>
        </div>
      </div>

      <button class="run-btn" onclick="runQuant()">Run Quant Analysis</button>

      <br><br>

      <!-- IV ESTIMATOR INPUTS -->
      <p class="subtext">
        Proprietary IV Estimator
      </p>

      <div class="iv-grid">
        <div class="field full-width">
          <label>Probable Resistance</label>
          <input id="ivestResistance" type="number" placeholder="e.g. 25000">
        </div>
      </div>

      <button class="run-btn" onclick="runIvEstimator()">Estimate IV</button>

    </div>
  </div>

  <!-- ================= RIGHT PANEL ================= -->
  <div class="mmr-right">

    <div class="mmr-card" id="box-input">
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Waiting for input...
    </div>

    <div class="mmr-card" id="box-model">
      <div class="mmr-card-title">[ QUANT ANALYSIS OUTPUT ]</div>
      Waiting for model to run...
    </div>

    <div class="mmr-card" id="box-ivest">
      <div class="mmr-card-title">
        [ IV ESTIMATOR OUTPUT ]
        <button class="btn-small" id="ivestTransferBtn" onclick="transferEstimatedIv()" disabled style="float:right;">&rarr; IV</button>
      </div>
      Waiting for estimator to run...
    </div>

  </div>

</div>
<div class="mmr-about-full">
  <div class="mmr-about-box">

    <div class="mmr-about-title">[ ABOUT TRADESTER PRO ]</div>

    <p>TradeSter Pro is a high-precision, IV-driven Quant Analysis engine - resistance/target 
    projection, Black-Scholes call/put pricing & delta, and a proprietary IV Estimator - built 
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

let lastEstimatedIv = null;

/* ================= DEFAULT STATE ================= */
function resetOutputPanels(){
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Waiting for input...
  `;

  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ QUANT ANALYSIS OUTPUT ]</div>
    Waiting for model to run...
  `;
}

function resetIvEstPanel(){
  document.getElementById("box-ivest").innerHTML = `
    <div class="mmr-card-title">
      [ IV ESTIMATOR OUTPUT ]
      <button class="btn-small" id="ivestTransferBtn" onclick="transferEstimatedIv()" disabled style="float:right;">&rarr; IV</button>
    </div>
    Waiting for estimator to run...
  `;
  lastEstimatedIv = null;
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

/* ================= QUANT ANALYSIS ================= */
async function runQuant(){

  const payload = {
    action: "quant",
    appKey: document.getElementById("appKey").value.trim(),
    appToken: document.getElementById("appToken").value.trim(),
    spot: Number(document.getElementById("spot").value),
    strike: Number(document.getElementById("strike").value),
    dte: Number(document.getElementById("dte").value),
    iv: Number(document.getElementById("iv").value),
    rate: Number(document.getElementById("rate").value),
    div: Number(document.getElementById("div").value)
  };

  // ===== VALIDATION =====
  if (!payload.appKey) return showError("App Key required");
  if (!payload.appToken) return showError("Token required");

  if (!payload.spot || payload.spot <= 0)
    return showError("Spot must be greater than 0");

  if (!payload.strike || payload.strike <= 0)
    return showError("Strike must be greater than 0");

  if (!payload.dte || payload.dte <= 0)
    return showError("DTE must be greater than 0");

  if (payload.dte > 3650)
    return showError("DTE too large (check input)");

  if (!payload.iv || payload.iv <= 0)
    return showError("IV must be greater than 0");

  if (isNaN(payload.rate))
    return showError("Rate is required (use 0 if not applicable)");

  if (isNaN(payload.div))
    return showError("Div Yield is required (use 0 if not applicable)");

  /* ===== LOADING STATE ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `

  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ QUANT ANALYSIS OUTPUT ]</div>
    Running model...
  `

  try{

    const res = await fetch(ENDPOINT, {
      method: "POST",
      body: JSON.stringify(payload)
    })

    const json = await res.json()

    if(json.error){
      showError(json.message)
      resetOutputPanels()
      return
    }

    /* ===== INPUT ===== */
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Spot: ${payload.spot}<br>
      Strike: ${payload.strike}<br>
      DTE: ${payload.dte}<br>
      IV: ${payload.iv}%<br>
      Rate: ${payload.rate}%<br>
      Div Yield: ${payload.div}%
    `;

    /* ===== MODEL ===== */
    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ QUANT ANALYSIS OUTPUT ]</div>

      Strike: ${json.strike}<br>
      BS Call Price: ${json.bsCallPrice}<br>
      BS Put Price: ${json.bsPutPrice}<br>
      Model Call Price: ${json.modelCallPrice}<br>
      Model Put Price: ${json.modelPutPrice}<br>

      <br>
      <div style="font-size:12px; color:#facc15; line-height:1.4;">
        ⚠ Model output is for educational purposes only.
      </div>
    `;

  }catch(e){
    showError("Connection error")
    resetOutputPanels()
  }
}

/* ================= IV ESTIMATOR ================= */
async function runIvEstimator(){

  const payload = {
    action: "ivEstimator",
    appKey: document.getElementById("appKey").value.trim(),
    appToken: document.getElementById("appToken").value.trim(),
    spot: Number(document.getElementById("spot").value),
    dte: Number(document.getElementById("dte").value),
    resistance: Number(document.getElementById("ivestResistance").value),
    rate: Number(document.getElementById("rate").value),
    div: Number(document.getElementById("div").value)
  };

  // ===== VALIDATION =====
  if (!payload.appKey) return showError("App Key required");
  if (!payload.appToken) return showError("Token required");

  if (!payload.spot || payload.spot <= 0)
    return showError("Spot must be greater than 0 (see Quant Inputs)");

  if (!payload.dte || payload.dte <= 0)
    return showError("DTE must be greater than 0 (see Quant Inputs)");

  if (payload.dte > 3650)
    return showError("DTE too large (check input)");

  if (!payload.resistance || payload.resistance <= 0)
    return showError("Probable Resistance must be greater than 0");

  if (isNaN(payload.rate))
    return showError("Rate is required (use 0 if not applicable)");

  if (isNaN(payload.div))
    return showError("Div Yield is required (use 0 if not applicable)");

  /* ===== LOADING STATE ===== */
  document.getElementById("box-ivest").innerHTML = `
    <div class="mmr-card-title">[ IV ESTIMATOR OUTPUT ]</div>
    Estimating...
  `
  lastEstimatedIv = null;

  try{

    const res = await fetch(ENDPOINT, {
      method: "POST",
      body: JSON.stringify(payload)
    })

    const json = await res.json()

    if(json.error){
      showError(json.message)
      resetIvEstPanel()
      return
    }

    lastEstimatedIv = json.iv;

    document.getElementById("box-ivest").innerHTML = `
      <div class="mmr-card-title">
        [ IV ESTIMATOR OUTPUT ]
        <button class="btn-small" id="ivestTransferBtn" onclick="transferEstimatedIv()" style="float:right;">&rarr; IV</button>
      </div>
      Estimated IV: <b>${json.iv}%</b>
    `;

  }catch(e){
    showError("Connection error")
    resetIvEstPanel()
  }
}

/* ================= TRANSFER ESTIMATED IV -> QUANT IV FIELD ================= */
function transferEstimatedIv(){
  if (lastEstimatedIv === null) return;
  const ivField = document.getElementById("iv");
  ivField.value = lastEstimatedIv;
  ivField.focus();
  const original = ivField.style.backgroundColor;
  ivField.style.backgroundColor = "#22c55e";
  setTimeout(() => { ivField.style.backgroundColor = original; }, 350);
}

</script>
