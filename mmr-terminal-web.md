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
            <button class="btn-small" onclick="toggleToken()">Show</button>
          </div>
        </div>
      </div>

      <br>

      <!-- INPUTS -->
      <div class="iv-grid">
        <div class="field"><label>Spot</label><input id="spot" type="number"></div>
        <div class="field"><label>Strike</label><input id="strike" type="number"></div>
        <div class="field"><label>DTE</label><input id="dte" type="number"></div>
        <div class="field"><label>Strike Diff</label><input id="strikeDiff" type="number"></div>
        <div class="field"><label>Rate %</label><input id="rate" type="number"></div>
        <div class="field"><label>Mag Factor</label><input id="fac" type="number" value="1"></div>
      </div>

      <button class="run-btn" onclick="runMMR()">Run MMR Terminal</button>

    </div>
  </div>

  <!-- ================= RIGHT PANEL ================= -->
  <div class="mmr-right">

    <div class="mmr-card" id="box-input"></div>
    <div class="mmr-card" id="box-model"></div>

    <div class="mmr-card" id="box-original"></div>
    <div class="mmr-card" id="box-adjusted"></div>

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
function toggleToken(){
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
    strike: Number(document.getElementById("strike").value),
    dte: Number(document.getElementById("dte").value),
    strikeDiff: Number(document.getElementById("strikeDiff").value),
    rate: Number(document.getElementById("rate").value),
    fac: Number(document.getElementById("fac").value)
  }

  if(!payload.appKey) return showError("App Key required")
  if(!payload.appToken) return showError("Token required")
  if(payload.fac < 0) return showError("Mag Factor must not be negative")

  // Clear previous
  document.getElementById("box-input").innerHTML = "Loading..."
  document.getElementById("box-model").innerHTML = ""
  document.getElementById("box-original").innerHTML = ""
  document.getElementById("box-adjusted").innerHTML = ""

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

    // ===== INPUT =====
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      Spot: ${payload.spot}<br>
      Strike: ${payload.strike}<br>
      DTE: ${payload.dte}<br>
      Strike Diff: ${payload.strikeDiff}<br>
      Rate: ${payload.rate}<br>
      Mag Factor: ${payload.fac}
    `

    // ===== MODEL =====
    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
      Iteration: ${json.iteration}<br>
      MMR Vol: ${json.mmr_pct.toFixed(3)}<br>
      Call: ${json.call.toFixed(3)}<br>
      Put: ${json.put.toFixed(3)}
    `

    // ===== ORIGINAL =====
    document.getElementById("box-original").innerHTML = `
      <div class="mmr-card-title">[ ORIGINAL MODEL ]</div>
      Call: ${json.call_model.toFixed(3)}<br>
      Put: ${json.put_model.toFixed(3)}<br>
      IV Call: ${json.iv_call.toFixed(3)}<br>
      IV Put: ${json.iv_put.toFixed(3)}<br>
      SF: ${json.sf.toFixed(3)}
    `

    // ===== ADJUSTED + RESIDUAL =====
    const residualColor = json.delta < 0.01 ? "#22c55e" : "#ef4444";

    document.getElementById("box-adjusted").innerHTML = `
      <div class="mmr-card-title">[ ADJUSTED MODEL ]</div>
      Call: ${json.call_model.toFixed(3)}<br>
      Put: ${json.put_model_adj.toFixed(3)}<br>
      IV Call: ${json.iv_call.toFixed(3)}<br>
      IV Put: ${json.iv_put_adj.toFixed(3)}<br>
      SF: ${json.sf_adj.toFixed(3)}

      <br><br>

      <div style="color:#facc15;">[ RESIDUAL ]</div>
      <div style="color:${residualColor};">
        L1 Error: ${json.delta.toFixed(6)}
      </div>
    `

  }catch(e){
    showError("Connection error")
  }
}

</script>
