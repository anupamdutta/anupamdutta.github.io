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

      <div class="mmr-card">
        <div class="mmr-card-title">[ PROBABILITY TABLE ]</div>
        <div id="prob-grid">Waiting for data...</div>
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
  <div class="mmr-card mmr-about" id="box-about">
  <div class="mmr-card-title">[ ABOUT ]</div>

  <div class="about-content">

    <p><b>Implied Volatility (IV)</b></p>
    <p>
      I don’t treat IV as just another number on the screen. For me, IV is the
      market’s collective expectation of future movement. It reflects how much
      traders believe price can expand or contract before expiry.
    </p>
    <p>
      When IV is elevated, the market is pricing in larger swings, uncertainty,
      or event-driven moves. When IV is low, it is expecting stability and range-bound behavior.
      So instead of asking “is IV high or low”, I ask “what kind of move is being priced in?”
    </p>

    <p><b>Range (SF^ and SF^^)</b></p>
    <p>
      The levels shown above are not traditional support or resistance zones.
      They are statistically derived ranges based on current volatility.
    </p>
    <p>
      SF^ represents roughly the expected lower bound, and SF^^ represents the upper bound
      for the given time to expiry. Most of the time, price tends to remain within this range,
      not because it must, but because that is what the current volatility implies.
    </p>
    <p>
      Think of this as a probability envelope. Price can break it, but when it does,
      you are entering tail-event territory.
    </p>

    <p><b>Probability Table</b></p>
    <p>
      Instead of predicting direction, I focus on distribution. The table maps where price
      is likely to end up and how far it is likely to travel.
    </p>
    <p>
      “Below” tells you the probability of expiry finishing below a certain level.
      “Touch” tells you the probability of price reaching that level at any point
      before expiry, even if it does not stay there.
    </p>
    <p>
      This distinction is critical. Markets often travel further than where they finally settle.
      Understanding this difference is where most traders gain or lose edge.
    </p>

    <p><b>Real Use in Trading</b></p>
    <p>
      I don’t use this to guess direction. I use it to understand structure.
    </p>
    <p>
      If touch probability is high, I assume price can reach that level and plan risk accordingly.
      If it is low, I treat that zone as a tail event, useful for positioning or hedging.
    </p>
    <p>
      This framework helps in strike selection, premium selling, and risk management.
      Instead of reacting to price, I operate within a probabilistic map.
    </p>

    <p><b>How to Think About It</b></p>
    <p>
      Most traders ask, “Will the market go up or down?”  
      I ask, “How far can the market move, and what is the probability of that move?”
    </p>
    <p>
      That shift, from direction to distribution, is where consistency starts to build.
    </p>

    <hr>

    <p><b>Want to Go Deeper?</b></p>
    <p>
      If you want to truly understand how IV translates into real trades, how probability
      interacts with option pricing, and how to structure positions based on expectation,
      I cover all of this step by step in my premium IV Decoding course.
    </p>
    <p>
      This is not theory-heavy content. It is built around practical application,
      real market behavior, and decision-making frameworks.
    </p>

    <p>
      To join or learn more:<br>
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

  document.getElementById("box-input").innerHTML = "Loading...";
  document.getElementById("box-result").innerHTML = "";
  document.getElementById("prob-grid").innerHTML = "Loading...";

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
