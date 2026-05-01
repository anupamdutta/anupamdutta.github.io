---
layout: page
title: IV Finder + Decoder
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

      <div class="mmr-card">
        <div class="mmr-card-title">[ PROBABILITY TABLE ]</div>
        <div id="prob-grid">Waiting for data...</div>
      </div>
      
      <div class="mmr-card">
        <div class="mmr-card-title">[ DISTRIBUTION CURVE ]</div>
        <canvas id="pdfChart" height="100"></canvas>
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
  <div class="mmr-about-full">

    <div class="mmr-about-box">
  
      <div class="mmr-about-title">[ ABOUT ]</div>
  
      <p class="about-heading">Implied Volatility (IV)</p>
      <p>
        I don’t treat IV as just a number. It is the market’s expectation of how much
        price can move before expiry.
      </p>
      <p>
        High IV → larger expected swings.  
        Low IV → range-bound expectation.
      </p>
  
      <p class="about-heading">Range (SF^ and SF^^)</p>
      <p>
        These are statistical boundaries, not support/resistance.
        Most of the time, price stays inside this range.
      </p>
  
      <p class="about-heading">Probability Table</p>
      <p>
        “Below” → where expiry can happen.  
        “Touch” → how far price can travel before expiry.
      </p>
  
      <p class="about-heading">Real Use</p>
      <p>
        High touch → price can reach.  
        Low touch → tail risk.
      </p>
  
      <hr>
  
      <p class="about-heading">Want to go deeper?</p>
      <p>
        I teach IV, probability, and real trade application in my premium course.
      </p>
  
      <p>
        <b>Anupam Dutta</b><br>
        📞 +91-8240775462
      </p>
  
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

  // ===== RESET ALL PANELS =====
  document.getElementById("box-input").innerHTML = "Fetching Input Data...";
  document.getElementById("box-result").innerHTML = "Calculating...";
  document.getElementById("prob-grid").innerHTML = "Loading...";
  
  // 🔥 DESTROY OLD CHART IMMEDIATELY
  if (chart) {
    chart.destroy();
    chart = null;
  }
  
  // 🔥 CLEAR CANVAS (important)
  const ctx = document.getElementById("pdfChart").getContext("2d");
  ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);

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
    if (json.distribution) {
      renderTable(json.distribution);
    } else {
      document.getElementById("prob-grid").innerHTML = "No distribution data";
    }
    
    /* ===== PDF CHART ===== */
    if (json.pdf) {
      renderChart(json.pdf, S);
    }

  } catch (err) {
    showError("Connection error");
  }
}


/* ================= TABLE ================= */
function renderTable(data) {

  let html = `
    <div class="prob-header">
      <div>Level</div>
      <div>Strike</div>
      <div>Below</div>
      <div>Touch</div>
    </div>
  `;

  data.forEach(row => {

    let cls = "prob-row";

    // Highlight center
    if (row.label === "0σ") cls += " atm";
    else if (row.label === "+1σ" || row.label === "-1σ") cls += " zone";
    else cls += " tail";

    html += `
      <div class="${cls}">
        <div>${row.label}</div>
        <div>${row.strike}</div>
        <div>${(row.probBelow * 100).toFixed(1)}%</div>
        <div>${(row.touchProb * 100).toFixed(1)}%</div>
      </div>
    `;
  });

  document.getElementById("prob-grid").innerHTML = html;
}

/* ================= CHART ================= */
function renderChart(pdfData, spot) {

  const labels = pdfData.map(p => p.price);
  const raw = pdfData.map(p => p.density);

  const max = Math.max(...raw);
  const values = raw.map(v => v / max);

  if (chart) chart.destroy();

  const ctx = document.getElementById("pdfChart").getContext("2d");

  // ===== GRADIENT =====
  const gradient = ctx.createLinearGradient(0, 0, 0, 200);
  gradient.addColorStop(0, "rgba(56,189,248,0.4)");
  gradient.addColorStop(1, "rgba(56,189,248,0)");

  chart = new Chart(ctx, {
    type: "line",
    data: {
      labels: labels,
      datasets: [{
        data: values,
        borderColor: "#38bdf8",
        backgroundColor: gradient,
        fill: true,
        tension: 0.4,
        pointRadius: 0,
        borderWidth: 2
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: true,
      interaction: {
        mode: "index",
        intersect: false
      },
      plugins: {
        legend: { display: false },
        tooltip: {
          enabled: true,
          backgroundColor: "#020617",
          borderColor: "#38bdf8",
          borderWidth: 1,
          titleColor: "#38bdf8",
          bodyColor: "#cbd5e1",
          callbacks: {
            title: (ctx) => `Price: ${ctx[0].label}`,
            label: (ctx) => {
              const prob = (ctx.raw * 100).toFixed(2);
              return `Density: ${prob}%`;
            }
          }
        }
      },
      scales: {
        x: {
          ticks: {
            maxTicksLimit: 6,
            color: "#64748b"
          },
          grid: {
            color: "rgba(255,255,255,0.05)"
          }
        },
        y: {
          display: false
        }
      }
    }
  });
}

</script>
