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

      <div class="mmr-card" id="box-dist">
        <div class="mmr-card-title">[ PROBABILITY TABLE ]</div>
        Waiting for data...
      </div>
      
      <div class="mmr-card">
        <div class="mmr-card-title">[ DISTRIBUTION CURVE ]</div>
        <canvas id="pdfChart" height="120"></canvas>
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

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>

/* ================= MODAL ================= */
function showError(msg) {
  document.getElementById("modalText").innerText = msg;
  document.getElementById("errorModal").style.display = "flex";
}

function closeModal() {
  document.getElementById("errorModal").style.display = "none";
}

/* ================= CHART INSTANCE ================= */
let chart = null;

/* ================= MAIN ================= */
async function runIV() {

  let S = parseFloat(document.getElementById("spot").value);
  let K = parseFloat(document.getElementById("strike").value);
  let DTE = parseFloat(document.getElementById("dte").value);
  let P = parseFloat(document.getElementById("price").value);
  let type = document.getElementById("type").value.toLowerCase();

  if (!S || S <= 0) return showError("Invalid Spot value");
  if (!K || K <= 0) return showError("Invalid Strike value");
  if (!DTE || DTE <= 0) return showError("DTE must be greater than 0");
  if (!P || P <= 0) return showError("Invalid Option Price");

  document.getElementById("box-input").innerHTML = "Loading...";
  document.getElementById("box-result").innerHTML = "";
  document.getElementById("box-dist").innerHTML = "Loading...";

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
      return showError("IV calculation failed");
    }

    /* ===== INPUT ===== */
    document.getElementById("box-input").innerHTML = `
      <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
      SF: ${S}<br>
      Strike: ${K}<br>
      DTE: ${DTE}<br>
      Option Price: ${P}<br>
      Type: ${type.toUpperCase()}
    `;

    /* ===== RESULT ===== */
    document.getElementById("box-result").innerHTML = `
      <div class="mmr-card-title">[ RESULT ]</div>
      IV: ${json.iv.toFixed(4)} % <br>
      Risk Level* (SF^): ${json.risk_level.toFixed(2)} <br>
      Risk Level** (SF^^): ${json.risk_level_1.toFixed(2)}
    `;

    /* ===== PROBABILITY TABLE ===== */
    renderTable(json.distribution, S);

    /* ===== PDF CHART ===== */
    renderChart(json.pdf, S);

  } catch (err) {
    showError("Connection error");
  }
}


/* ================= TABLE ================= */
function renderTable(data, spot) {

  let html = `
    <div class="mmr-card-title">[ PROBABILITY TABLE ]</div>
    <table style="width:100%; font-size:12px;">
      <tr>
        <th>Strike</th>
        <th>d2</th>
        <th>Below</th>
        <th>Above</th>
        <th>Touch</th>
      </tr>
  `;

  data.forEach(row => {

    const highlight = Math.abs(row.strike - spot) < 25
      ? "background:#111;"
      : "";

    html += `
      <tr style="${highlight}">
        <td>${row.strike}</td>
        <td>${row.d2.toFixed(2)}</td>
        <td>${(row.probBelow * 100).toFixed(1)}%</td>
        <td>${(row.probAbove * 100).toFixed(1)}%</td>
        <td>${(row.touchProb * 100).toFixed(1)}%</td>
      </tr>
    `;
  });

  html += "</table>";

  document.getElementById("box-dist").innerHTML = html;
}


/* ================= CHART ================= */
function renderChart(pdfData, spot) {

  const labels = pdfData.map(p => p.price);
  const values = pdfData.map(p => p.density);

  if (chart) chart.destroy();

  const ctx = document.getElementById("pdfChart").getContext("2d");

  chart = new Chart(ctx, {
    type: "line",
    data: {
      labels: labels,
      datasets: [{
        label: "Probability Density",
        data: values,
        borderWidth: 2,
        pointRadius: 0,
        tension: 0.3
      }]
    },
    options: {
      responsive: true,
      plugins: {
        legend: { display: false }
      },
      scales: {
        x: {
          ticks: { maxTicksLimit: 8 }
        },
        y: {
          display: false
        }
      }
    }
  });
}

</script>
