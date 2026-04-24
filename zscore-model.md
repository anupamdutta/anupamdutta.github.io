---
layout: page
title: Model Z Score
permalink: /zscore-model/
---

<div class="mmr-layout">

  <!-- LEFT PANEL -->
  <div class="mmr-left">
    <div class="iv-container">

      <p class="subtext">Auth Protected Z-Score Engine</p>

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

        <div class="field">
          <label>Synthetic Future (SF)</label>
          <input id="sf" type="number" placeholder="e.g. 23190.40">
        </div>

        <div class="field">
          <label>MMR %</label>
          <input id="mmrVol" type="number" placeholder="e.g. 12.5">
        </div>

        <div class="field">
          <label>IV Type</label>
          <select id="ivType">
            <option value="put">Put</option>
            <option value="call">Call</option>
          </select>
        </div>

        <div class="field">
          <label>Tick Size</label>
          <input id="tick" type="number" placeholder="e.g. 0.05">
        </div>

      </div>

      <button class="run-btn" onclick="runZ()">Run Z Score Model</button>

    </div>
  </div>

  <!-- RIGHT PANEL -->
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

<!-- ABOUT -->
<div class="mmr-about-full">
  <div class="mmr-about-box">
    <div class="mmr-about-title">[ ABOUT Z-SCORE MODEL ]</div>

    <p>Z Score Model is a volatility-derived pricing framework that converts MMR into option structure using statistical normalization.</p>

    <p>⚠ Intended for advanced users. Use with understanding.</p>

    <p>
      <b>Developer:</b> Anupam Dutta<br>
    </p>
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

const API = "https://script.google.com/macros/s/AKfycbyzXsVeHUlngh-uAQ1e02_-5Fs-dr7iEjsWbSCmLLSGg968HdBF6vUAbk2dOTeSUhWfaw/exec";

/* TOKEN TOGGLE */
function toggleToken(e){
  const f = document.getElementById("appToken");
  const b = e.target;
  if(f.type === "password"){ f.type="text"; b.innerText="Hide"; }
  else { f.type="password"; b.innerText="Show"; }
}

/* MODAL */
function showError(msg){
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}
function closeModal(){
  document.getElementById("errorModal").style.display = "none";
}

/* MAIN */
async function runZ(){

  const payload = {
    appKey: document.getElementById("appKey").value.trim(),
    appToken: document.getElementById("appToken").value.trim(),
    sf: Number(document.getElementById("sf").value),
    mmrVol: Number(document.getElementById("mmrVol").value),
    tick: Number(document.getElementById("tick").value),
    ivType: document.getElementById("ivType").value
  };

  // VALIDATION
  if (!payload.appKey) return showError("App Key required");
  if (!payload.appToken) return showError("Token required");
  if (!payload.sf || payload.sf <= 0) return showError("Invalid SF");
  if (!payload.mmrVol || payload.mmrVol <= 0) return showError("Invalid MMR %");
  if (!payload.tick || payload.tick <= 0) return showError("Invalid Tick");

  // LOADING UI
  document.getElementById("box-input").innerHTML = `
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading input...
  `;

  document.getElementById("box-model").innerHTML = `
    <div class="mmr-card-title">[ MODEL OUTPUT ]</div>
    Running model...
  `;

  try {

    const res = await fetch(API, {
      method: "POST",
      body: JSON.stringify(payload)
    });

    const json = await res.json();

    if (json.error) {
      showError(json.message);
      return;
    }

    // INPUT PANEL
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      SF: ${payload.sf}<br>
      MMR %: ${payload.mmrVol}<br>
      IV Type: ${payload.ivType}<br>
      Tick: ${payload.tick}
    `;

    // OUTPUT PANEL
    document.getElementById("box-model").innerHTML = `
      <div class="mmr-card-title">[ MODEL OUTPUT ]</div>

      IV Type: ${json.ivType}<br>
      Strike: ${json.strike}<br>

      Call Price: ${json.call_price}<br>
      Put Price: ${json.put_price}<br>

      Call Delta: ${json.call_delta.toFixed(4)}<br>
      Put Delta: ${json.put_delta.toFixed(4)}<br>
    `;

  } catch (e) {
    showError("Connection error");
  }
}

</script>
