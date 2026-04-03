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
        <div class="field"><label>Rate %</label><input id="rate" type="number" placeholder="e.g. 6"></div>
        <div class="field"><label>DTE</label><input id="dte" type="number" placeholder="Days to Expiry"></div>
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

  const payload = {
    appKey: document.getElementById("appKey").value.trim(),
    appToken: document.getElementById("appToken").value.trim(),
    spot: Number(document.getElementById("spot").value),
    strikeDiff: Number(document.getElementById("strikeDiff").value),
    rate: Number(document.getElementById("rate").value),
    dte: Number(document.getElementById("dte").value)
  }

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

  /* ===== LOADING STATE (PRO UI) ===== */
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `

  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
    Running model...
  `

  

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
      Strike Diff: ${payload.strikeDiff}<br>
      Rate: ${payload.rate}<br>
      DTE: ${payload.dte}
    `

    /* ===== MODEL ===== */
    const residualColor = json.delta < 0.01 ? "#22c55e" : "#ef4444";
    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
      Iteration: ${json.iteration}<br>
      Strike: ${json.strike_final}<br>
      MMR Vol: ${json.mmr_pct.toFixed(3)}<br>
      Call: ${json.call.toFixed(3)}<br>
      Put: ${json.put.toFixed(3)}
      <br><br>

      <div style="color:#facc15;">[ CALIBRATION RESIDUAL ]</div>
      <div style="color:${residualColor};">
        L1 Error: ${json.delta.toFixed(6)}
      </div>
    `

    

  }catch(e){
    showError("Connection error")
  }
}

</script>
