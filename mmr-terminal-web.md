---
layout: page
title: MMR Terminal Web
permalink: /mmr-terminal-web/
---

<div class="iv-container">

  <p class="subtext">
    MMR Terminal - Auth Protected Option Pricing Engine
  </p>

  <!-- ================= AUTH ================= -->
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

  <!-- ================= INPUTS ================= -->
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
      <label>Strike Diff</label>
      <input id="strikeDiff" type="number">
    </div>

    <div class="field">
      <label>Rate %</label>
      <input id="rate" type="number">
    </div>

    <div class="field">
      <label>Mag Factor</label>
      <input id="fac" type="number" value="1" min="0">
    </div>

  </div>

  <div style="text-align:center;">
    <button class="run-btn" style="width:260px;" onclick="runMMR()">Run MMR Terminal</button>
  </div>

  <div id="result" class="result-box"></div>

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

  // ===== VALIDATION =====
  if(!payload.appKey) return showError("App Key required")
  if(!payload.appToken) return showError("Token required")
  if(payload.fac < 0) return showError("Mag Factor must not be negative")

  const out = document.getElementById("result")
  out.innerHTML = "Running..."

  try{

    const res = await fetch("https://script.google.com/macros/s/AKfycbyeRllRTjvMPaXRqDZJSnvj9FyBUl9Lhot0D-nsfW5zWztJjZO1pI3hGhXGQTrP9O2U/exec", {
      method: "POST",
      body: JSON.stringify(payload)
    })

    const json = await res.json()

    if(json.error){
      out.innerHTML = ""
      showError(json.message)
      return
    }

    out.innerHTML = `
<pre class="iv-output">
==============================================================
        MARKET MAKER RISK (MMR) OPTION PRICING
==============================================================

INPUT PARAMETERS

Spot Price               : ${payload.spot}
Strike Price             : ${payload.strike}
Days to Expiry (DTE)     : ${payload.dte}
Strike Difference        : ${payload.strikeDiff}
Implied Funding Rate (%) : ${payload.rate}
Mag Factor               : ${payload.fac}

--------------------------------------------------------------

MODEL OUTPUT

Solver Iteration         : ${json.iteration}
MMR Volatility (%)       : ${json.mmr_pct.toFixed(3)}
Black-Scholes Call       : ${json.call.toFixed(3)}
Black-Scholes Put        : ${json.put.toFixed(3)}

--------------------------------------------------------------

ORIGINAL MODEL (UNCONSTRAINED SURFACE)

Model Call               : ${json.call_model.toFixed(3)}
Model Put                : ${json.put_model.toFixed(3)}
Call Implied Vol (%)     : ${json.iv_call.toFixed(3)}
Put Implied Vol (%)      : ${json.iv_put.toFixed(3)}
Synthetic Forward (F*)   : ${json.sf.toFixed(3)}

--------------------------------------------------------------

ADJUSTED MODEL (VOL-SURFACE CONSISTENCY)

Model Call               : ${json.call_model.toFixed(3)}
Model Put                : ${json.put_model_adj.toFixed(3)}
Call Implied Vol (%)     : ${json.iv_call.toFixed(3)}
Put Implied Vol (%)      : ${json.iv_put_adj.toFixed(3)}
Synthetic Forward (F*)   : ${json.sf_adj.toFixed(3)}

--------------------------------------------------------------

Calibration Residual (L1): ${json.delta.toFixed(6)}

==============================================================
</pre>
    `

  }catch(e){
    out.innerHTML = ""
    showError("Connection error")
  }
}

</script>
